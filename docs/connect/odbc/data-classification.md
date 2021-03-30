---
description: ODBC ドライバーでデータ分類を使用する方法、およびデータ保護ポリシーを ODBC アプリケーションに組み込む方法について説明します。
title: データ分類 ODBC ドライバーの使用
ms.custom: ''
ms.date: 07/26/2018
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: v-daenge
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- driver
ms.assetid: f78b81ed-5214-43ec-a600-9bfe51c5745a
author: v-makouz
ms.author: v-makouz
manager: kenvh
ms.openlocfilehash: f940b688e015a9e9e32ac5b8e7953c9eed098052
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633147"
---
# <a name="data-classification"></a>データ分類

[!INCLUDE[Driver_ODBC_Download](../../includes/driver_odbc_download.md)]

## <a name="overview"></a>概要

機密データを管理するために、SQL Server と Azure SQL Server では、クライアント アプリケーションがさまざまな種類の機密データ (健康、財務など) をデータ保護ポリシーに沿って処理できるようにする、機密度メタデータをデータベース列に提供する機能が導入されました。

分類を列に割り当てる方法の詳細については、「[SQL データの検出と分類](../../relational-databases/security/sql-data-discovery-and-classification.md)」を参照してください。

Microsoft ODBC Driver 17.2 では、このメタデータを、SQL_CA_SS_DATA_CLASSIFICATION フィールド識別子を使用して SQLGetDescField を介して取得できます。

## <a name="format"></a>Format

SQLGetDescField の構文は次のとおりです。

```cpp
SQLRETURN SQLGetDescField(  
     SQLHDESC        DescriptorHandle,  
     SQLSMALLINT     RecNumber,  
     SQLSMALLINT     FieldIdentifier,  
     SQLPOINTER      ValuePtr,  
     SQLINTEGER      BufferLength,  
     SQLINTEGER *    StringLengthPtr);  
```

*DescriptorHandle*  
 [入力] IRD (実装行記述子) ハンドル。 SQL_ATTR_IMP_ROW_DESC ステートメント属性を使用して SQLGetStmtAttr を呼び出すことによって取得できます
  
 *RecNumber*  
 [入力] 0
  
 *FieldIdentifier*  
 [入力] SQL_CA_SS_DATA_CLASSIFICATION
  
 *ValuePtr*  
 [出力] 出力バッファー
  
 *BufferLength*  
 [入力] 出力バッファーの長さ (バイト単位)

 *StringLengthPtr* [出力] *ValuePtr* で返すことができるバイト数の合計を返すバッファーへのポインター。

> [!NOTE]
> バッファーのサイズが不明な場合、*ValuePtr* を NULL として指定して SQLGetDescField を呼び出し、*StringLengthPtr* の値を調べることによって判断できます。

データ分類情報を使用できない場合、"*記述子フィールドが正しくない*" ことを示すエラーが返されます。

SQLGetDescField の呼び出しが成功すると、*ValuePtr* によってポイントされたバッファーに次のデータが含まれます。

 `nn nn [n sensitivitylabels] tt tt [t informationtypes] cc cc [c columnsensitivitys]`

> [!NOTE]
> `nn nn`、`tt tt`、および `cc cc` はマルチバイト整数です。これらは、最下位のアドレスに最下位バイトで格納されます。

*`sensitivitylabel`* と *`informationtype`* は両方とも次の形式です

 `nn [n bytes name] ii [i bytes id]`

*`columnsensitivity`* は次の形式です

 `nn nn [n sensitivityprops]`

列 *(c)* ごとに、*n* 個の 4 バイト *`sensitivityprops`* が存在します。

 `ss ss tt tt`

s - *`sensitivitylabels`* 配列内のインデックス。ラベルが付けられていない場合は `FF FF`

t - *`informationtypes`* 配列内のインデックス。ラベルが付けられていない場合は `FF FF`

<br><br>
データの形式は、次の擬似構造体として表現できます。

```cpp
struct IDnamePair {
 BYTE nameLen;
 USHORT name[nameLen];
 BYTE idLen;
 USHORT id[idLen];
};

struct SensitivityProp {
 USHORT labelIdx;
 USHORT infoTypeIdx;
};

USHORT nLabels;
struct IDnamePair labels[nLabels];
USHORT nInfoTypes;
struct IDnamePair infotypes[nInfoTypes];
USHORT nColumns;
struct {
 USHORT nProps;
 struct SensitivityProp[nProps];
} columnClassification[nColumns];
```

## <a name="code-sample"></a>コード サンプル

データ分類メタデータの読み取り方法を示すテスト アプリケーション。 Windows では、`cl /MD dataclassification.c /I (directory of msodbcsql.h) /link odbc32.lib` を使用してコンパイルし、接続文字列および (分類された列を返す) SQL クエリをパラメーターとして指定して実行できます。

```cpp
#ifdef _WIN32
#include <windows.h>
#endif
#include <sql.h>
#include <sqlext.h>
#include <msodbcsql.h>
#include <stdio.h>
SQLHANDLE env, dbc, stmt;
void checkRC_exit(SQLRETURN rc, SQLHANDLE hand, SQLSMALLINT htype, int retcode, char *action)
{
    if ((rc == SQL_ERROR || rc == SQL_SUCCESS_WITH_INFO) && hand)
    {
        char msg[1024], state[6];
        int i = 0;
        SQLRETURN rc2;
        SQLINTEGER err;
        SQLSMALLINT lenout;
        while ((rc2 = SQLGetDiagRec(htype, hand, ++i, state, &err, msg, sizeof(msg), &lenout)) == SQL_SUCCESS ||
            rc2 == SQL_SUCCESS_WITH_INFO)
            printf("%d (%d)[%s]%s\n", i, err, state, msg);
    }
    if (rc == SQL_ERROR && retcode)
    {
        printf("Error occurred%s%s\n", action ? " upon " : "", action ? action : "");
        exit(retcode);
    }
}
void printLabelInfo(char *type, char **pptr)
{
    char *ptr = *pptr;
    unsigned short nlabels;
    printf("----- %s(%u) -----\n", type, nlabels = *(unsigned short*)ptr);
    ptr += sizeof(unsigned short);
    while (nlabels--)
    {
        int namelen, idlen;
        char *nameptr, *idptr;
        namelen = *ptr++;
        nameptr = ptr;
        ptr += namelen * 2;
        idlen = *ptr++;
        idptr = ptr;
        ptr += idlen * 2;
        wprintf(L"Name: \"%.*s\" Id: \"%.*s\"\n", namelen, nameptr, idlen, idptr);
    }
    *pptr = ptr;
}
int main(int argc, char **argv)
{
    unsigned char *dcbuf;
    unsigned int dclen = 0;
    SQLRETURN rc;
    SQLHANDLE ird;
    if (argc < 3)
    {
        fprintf(stderr, "usage: dataclassification connstr query\n");
        return 1;
    }
    checkRC_exit(SQLAllocHandle(SQL_HANDLE_ENV, 0, &env), 0, 0,
        2, "allocate environment");
    checkRC_exit(SQLSetEnvAttr(env, SQL_ATTR_ODBC_VERSION, (SQLPOINTER)SQL_OV_ODBC3, 0), env, SQL_HANDLE_ENV,
        3, "set ODBC version");
    checkRC_exit(SQLAllocHandle(SQL_HANDLE_DBC, env, &dbc), env, SQL_HANDLE_ENV,
        4, "allocate connection");
    checkRC_exit(SQLDriverConnect(dbc, 0, argv[1], SQL_NTS, 0, 0, 0, SQL_DRIVER_NOPROMPT), dbc, SQL_HANDLE_DBC,
        5, "connect to server");
    checkRC_exit(SQLAllocHandle(SQL_HANDLE_STMT, dbc, &stmt), dbc, SQL_HANDLE_DBC,
        6, "allocate statement");
    checkRC_exit(SQLExecDirect(stmt, argv[2], SQL_NTS), stmt, SQL_HANDLE_STMT,
        7, "execute query");
    checkRC_exit(SQLGetStmtAttr(stmt, SQL_ATTR_IMP_ROW_DESC, (SQLPOINTER)&ird, SQL_IS_POINTER, 0), stmt, SQL_HANDLE_STMT,
        8, "get IRD handle");
    rc = SQLGetDescFieldW(ird, 0, SQL_CA_SS_DATA_CLASSIFICATION, dcbuf, 0, &dclen);
    if (rc != SQL_SUCCESS && rc != SQL_SUCCESS_WITH_INFO)
    {
        checkRC_exit(rc, ird, SQL_HANDLE_DESC, 0, 0);
        printf("Error reading SQL_CA_SS_DATA_CLASSIFICATION IRD field. Ensure that the driver and\n"
            "server both support the Data Classification feature, and that the query returns columns\n"
            "with classification information.\n");
    }
    else
    {
        SQLINTEGER dclenout;
        unsigned char *dcptr;
        unsigned short ncols;
        printf("Data Classification information (%u bytes):\n", dclen);
        if (!(dcbuf = malloc(dclen)))
        {
            printf("Memory Allocation Error");
            return 9;
        }
        checkRC_exit(SQLGetDescFieldW(ird, 0, SQL_CA_SS_DATA_CLASSIFICATION, dcbuf, dclen, &dclenout),
            ird, SQL_HANDLE_DESC, 10, "reading SQL_CA_SS_DATA_CLASSIFICATION");
        dcptr = dcbuf;
        printLabelInfo("Labels", &dcptr);
        printLabelInfo("Information Types", &dcptr);
        printf("----- Column Sensitivities(%u) -----\n", ncols = *(unsigned short*)dcptr);
        dcptr += sizeof(unsigned short);
        while (ncols--)
        {
            unsigned short nprops = *(unsigned short*)dcptr;
            dcptr += sizeof(unsigned short);
            while (nprops--)
            {
                unsigned short labelidx, typeidx;
                labelidx = *(unsigned short*)dcptr; dcptr += sizeof(unsigned short);
                typeidx = *(unsigned short*)dcptr; dcptr += sizeof(unsigned short);
                printf(labelidx == 0xFFFF ? "(none) " : "%u ", labelidx);
                printf(typeidx == 0xFFFF ? "(none)\n" : "%u\n", typeidx);
            }
            printf("-----\n");
        }
        if (dcptr != dcbuf + dclen)
        {
            printf("Error: unexpected parse of DATACLASSIFICATION data\n");
            return 11;
        }
        free(dcbuf);
    }
    return 0;
}
```

## <a name="supported-version"></a><a name="bkmk-version"></a>サポートされているバージョン

Microsoft ODBC Driver 17.2 では、`FieldIdentifier` が `SQL_CA_SS_DATA_CLASSIFICATION` (1237) に設定されている場合、`SQLGetDescField` を使用してデータの分類情報を取得できます。

Microsoft ODBC Driver 17.4.1.1 からは、サーバーでサポートされているデータ分類のバージョンを、`SQL_CA_SS_DATA_CLASSIFICATION_VERSION` (1238) フィールド識別子を使用して `SQLGetDescField` を介して取得できるようになりました。 17.4.1.1 では、サポートされているデータ分類のバージョンは "2" に設定されています。

17.4.2.1 以降、データ分類の既定バージョンは、"1" に設定され、ドライバーから SQL Server にサポート対象として報告されるバージョンとなります。 新しい接続属性 `SQL_COPT_SS_DATACLASSIFICATION_VERSION` (1400) を使用すると、アプリケーションで、サポートされているデータ分類のバージョンを "1" からサポート対象の最大バージョンにまで変更できます。

例:

バージョンを設定するには、この呼び出しを、SQLConnect または SQLDriverConnect の呼び出しの直前に行う必要があります。

```cpp
ret = SQLSetConnectAttr(dbc, SQL_COPT_SS_DATACLASSIFICATION_VERSION, (SQLPOINTER)2, SQL_IS_INTEGER);
```

現在サポートされているデータ分類のバージョンの値は、SQLGetConnectAttr 呼び出しを使用して取得できます。

```cpp
ret = SQLGetConnectAttr(dbc, SQL_COPT_SS_DATACLASSIFICATION_VERSION, (SQLPOINTER)&dataClassVersion, SQL_IS_INTEGER, 0);
```
