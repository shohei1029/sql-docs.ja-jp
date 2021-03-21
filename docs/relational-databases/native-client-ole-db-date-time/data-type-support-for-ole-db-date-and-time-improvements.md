---
description: 日付と時刻の機能強化に対するデータ型のサポート (Native Client OLE DB プロバイダー)
title: 日付と時刻の機能強化に対するデータ型のサポート (Native Client OLE DB プロバイダー) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
helpviewer_keywords:
- date/time [OLE DB], data type support
- OLE DB, date/time improvements
ms.assetid: d40e3fd6-9057-4371-8236-95cef300603e
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7500e311e3c1694bcb958048c21ca12e154a8752
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748392"
---
# <a name="sql-server-native-client-data-type-support-for-ole-db-date-and-time-improvements"></a>OLE DB の日付と時刻の機能強化のためのデータ型のサポート SQL Server Native Client
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 日付/時刻データ型をサポートする OLE DB (Native Client) 型について説明 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。  
  
## <a name="data-type-mapping-in-rowsets-and-parameters"></a>行セットとパラメーターでのデータ型マッピング  
 OLE DB には、新しいサーバーの種類をサポートする 2 つの新しいデータ型 (DBTYPE_DBTIME2 と DBTYPE_DBTIMESTAMPOFFSET) が用意されています。 次の表に、完全なサーバーの型マッピングを示します。  
  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型|OLE DB データ型|値|  
|-----------------------------------------|----------------------|-----------|  
|DATETIME|DBTYPE_DBTIMESTAMP|135 (oledb.h)|  
|smalldatetime|DBTYPE_DBTIMESTAMP|135 (oledb.h)|  
|date|DBTYPE_DBDATE|133 (oledb.h)|  
|time|DBTYPE_DBTIME2|145 (sqlncli)|  
|datetimeoffset|DBTYPE_DBTIMESTAMPOFFSET|146 (sqlncli)|  
|datetime2|DBTYPE_DBTIMESTAMP|135 (oledb.h)|  
  
## <a name="data-formats-strings-and-literals"></a>データ形式 : 文字列とリテラル  
  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型|OLE DB データ型|クライアントで変換した場合の文字列の形式|  
|-----------------------------------------|----------------------|------------------------------------------|  
|DATETIME|DBTYPE_DBTIMESTAMP|'yyyy-mm-dd hh:mm:ss[.999]'<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、datetime における秒の小数部の桁数を 3 桁までサポートします。|  
|smalldatetime|DBTYPE_DBTIMESTAMP|'yyyy-mm-dd hh:mm:ss'<br /><br /> このデータ型の精度は 1 分です。 秒の部分は、出力時には 0 になり、入力時にはサーバーによって丸められます。|  
|date|DBTYPE_DBDATE|'yyyy-mm-dd'|  
|time|DBTYPE_DBTIME2|'hh:mm:ss[.9999999]'<br /><br /> 秒の小数部には、必要に応じて最大 7 桁まで指定できます。|  
|datetime2|DBTYPE_DBTIMESTAMP|'yyyy-mm-dd hh:mm:ss[.fffffff]'<br /><br /> 秒の小数部には、必要に応じて最大 7 桁まで指定できます。|  
|datetimeoffset|DBTYPE_DBTIMESTAMPOFFSET|'yyyy-mm-dd hh:mm:ss[.fffffff] +/-hh:mm'<br /><br /> 秒の小数部には、必要に応じて最大 7 桁まで指定できます。|  
  
 日付リテラルまたは時刻リテラルのエスケープ シーケンスに変更はありません。  
  
 結果に含まれる秒の小数部にはコロン (:) ではなくドット (.) を使用します。  
  
 アプリケーションに返される文字列値は、指定された列に対して常に同じ長さになります。 年、月、日、時、分、および秒の各部分は、最大幅に合わせて先頭にゼロが埋め込まれます。 日付と時間の間、および時間とタイムゾーン オフセットの間には空白が 1 つずつ入ります。 タイム ゾーン オフセットの前には常に符号を指定します。 オフセットが 0 の場合は、正符号 (+) になります。 符号とオフセット値の間に空白はありません。 秒の小数部では、必要に応じて、列に定義されている有効桁数になるまで後ろにゼロが埋め込まれますが、その有効桁数を超過することはありません。 datetime 列の場合、秒の小数部は 3 桁になります。 smalldatetime 列の場合、秒の小数部がなく、秒は常にゼロになります。  
  
 アプリケーションによって指定された文字列値を変換すると、柔軟性が向上し、各部分の値を最大幅より小さくすることができます。 年は 1 ～ 4 桁になります。 月、日、時、分、および秒は 1 桁または 2 桁になります。 日付と時刻の間および時刻とタイムゾーン オフセットの間には、任意の空白を含めることができます。 0 時 0 分のオフセットの符号は、正符号と負符号のどちらでもかまいません。 秒の小数部では、最大 9 桁になるように末尾にゼロを含めることができます。 時刻部分は、小数点で終了して、秒の小数部を省略することができます。  
  
 空の文字列は、有効な日付リテラルまたは時間リテラルではありません。また、NULL 値を表すものでもありません。 空の文字列を日付または時刻の値に変換しようとすると、SQLState 22018 のエラーが発生し、"キャストした文字コードが正しくありません" というメッセージが表示されます。  
  
## <a name="data-formats-data-structures"></a>データ形式 : データ構造体  
 次に説明する OLE DB 固有の構造体では、OLE DB は ODBC と同じ制約に準拠しています。 これらはグレゴリオ暦から取得されます。  
  
-   月の範囲は 1 ～ 12 です。  
  
-   日フィールドの範囲は 1 からその月の日数までです。この範囲は、うるう年を考慮して、年フィールドおよび月フィールドと一貫性を保つ必要があります。  
  
-   時刻の範囲は 0 ～ 23 です。  
  
-   分の範囲は 0 ～ 59 です。  
  
-   秒の範囲は 0 ～ 59 です。 この範囲では、恒星時との同期を維持するために最大 2 秒のうるう秒が許可されています。  
  
 次の既存の OLE DB 構造体の実装は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の新しい日付と時刻のデータ型をサポートするように変更されました。 ただし、定義は変更されていません。  
  
-   DBTYPE_DATE (オートメーション DATE 型です。 内部では **double** として表されます。 整数部分は 1899 年 12 月 30 日からの日数で、小数部分は 1 日の端数になります。 この型の精度は 1 秒なので、有効な小数点以下桁数は 0 です。)  
  
-   DBTYPE_DBDATE  
  
-   DBTYPE_DBTIME  
  
-   DBTYPE_DBTIMESTAMP (小数フィールドは、OLE DB で 10 億分の 1 秒 (ナノ秒) 単位の数値として定義され、範囲は 0 ～ 999,999,999 になります。)  
  
-   DBTYPE_FILETIME  
  
### <a name="dbtype_dbtime2"></a>DBTYPE_DBTIME2  
 この構造体では、32 ビットと 64 ビットの両方のオペレーティング システムで、12 バイトまで埋め込みが行われます。  
  
```  
typedef struct tagDBTIME2 {  
    USHORT hour;  
    USHORT minute;  
    USHORT second;  
    ULONG fraction;  
    } DBTIME2;  
```  
  
### <a name="dbtype_-dbtimestampoffset"></a>DBTYPE_ DBTIMESTAMPOFFSET  
  
```  
typedef struct tagDBTIMESTAMPOFFSET {  
    SHORT year;  
    USHORT month;  
    USHORT day;  
    USHORT hour;  
    USHORT minute;  
    USHORT second;  
    ULONG fraction;  
    SHORT timezone_hour;  
    SHORT timezone_minute;  
    } DBTIMESTAMPOFFSET;  
```  
  
 `timezone_hour` が負の値の場合、`timezone_minute` は負の値または 0 である必要があります。 `timezone_hour` が正の値の場合、`timezone minute` は正の値または 0 である必要があります。 `timezone_hour` が 0 の場合、`timezone minute` は -59 ～ +59 の値を保持できます。  
  
### <a name="ssvariant"></a>SSVARIANT  
 この構造体は、新しい構造体 DBTYPE_DBTIME2 と DBTYPE_DBTIMESTAMPOFFSET を含み、適切な型に対しては秒の小数部の桁数が追加されています。  
  
```  
struct SSVARIANT {  
   SSVARTYPE vt;  
   DWORD dwReserved1;  
   DWORD dwReserved2;  
   union {  
// ...  
      DBTIMESTAMP tsDateTimeVal;  
      DBDATE dDateVal;  
      struct _Time2Val {  
         DBTIME2 tTime2Val;  
         BYTE bScale;  
      } Time2Val;  
      struct _DateTimeVal {  
         DBTIMESTAMP tsDateTimeVal;  
         BYTE bScale;  
      } DateTimeVal;  
      struct _DateTimeOffsetVal {   
         DBTIMESTAMPOFFSET tsoDateTimeOffsetVal;  
         BYTE bScale;  
      } DateTimeOffsetVal;  
// ...  
   };  
};  
```  
  
 さらに、列挙の型を決定する、SSVARIANT の型のエンコーディングに関連付けられた列挙は、次のように拡張されます。  
  
```  
enum SQLVARENUM {  
// ...  
   // Datetime  
   VT_SS_DATETIME      = DBTYPE_DBTIMESTAMP,  
   VT_SS_SMALLDATETIME = 206,  
  
   VT_SS_DATE = DBTYPE_DBDATE,  
   VT_SS_TIME2 = DBTYPE_DBTIME2,  
   VT_SS_DATETIME2 = 212  
   VT_SS_DATETIMEOFFSET = DBTYPE_DBTIMESTAMPOFFSET  
};  
```  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **Sql_variant** を使用し、 **datetime** の制限付き精度に依存するネイティブクライアントに移行するアプリケーションは、基になるスキーマが **datetime** ではなく **datetime2** を使用するように更新された場合に更新する必要があります。  
  
 また、SSVARIANT へのアクセス マクロが拡張され、次の部分が追加されました。  
  
```  
#define V_SS_DATETIME2(X)       V_SS_UNION(X, DateTimeVal)  
#define V_SS_TIME2(X)           V_SS_UNION(X, Time2Val)  
#define V_SS_DATE(X)            V_SS_UNION(X, dDateVal)  
#define V_SS_DATETIMEOFFSET(X)  V_SS_UNION(X, DateTimeOffsetVal)  
```  
  
## <a name="data-type-mapping-in-itabledefinitioncreatetable"></a>ITableDefinition::CreateTable でのデータ型マッピング  
 次の型マッピングは、ITableDefinition::CreateTable で使用される DBCOLUMNDESC 構造体で使用されます。  
  
|OLE DB データ型 (*wType*)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型|メモ|  
|----------------------------------|-----------------------------------------|-----------|  
|DBTYPE_DBDATE|date||  
|DBTYPE_DBTIMESTAMP|**datetime2**(p)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、秒の小数部の有効桁数を決定するために、 *DBBFILE Desc bscale* メンバーを検査します。|  
|DBTYPE_DBTIME2|**time**(p)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、秒の小数部の有効桁数を決定するために、 *DBBFILE Desc bscale* メンバーを検査します。|  
|DBTYPE_DBTIMESTAMPOFFSET|**datetimeoffset**(p)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、秒の小数部の有効桁数を決定するために、 *DBBFILE Desc bscale* メンバーを検査します。|  
  
 アプリケーションで *wType* に DBTYPE_DBTIMESTAMP を指定した場合、*pwszTypeName* に型名を指定することで、**datetime2** へのマッピングをオーバーライドします。 **datetime** を指定する場合、*bScale* は 3 にする必要があります。 **smalldatetime** を指定する場合、*bScale* は 0 にする必要があります。 *bScale* が *wType* および *pwszTypeName* と一致しない場合、DB_E_BADSCALE が返されます。  
  
## <a name="see-also"></a>参照  
 [日付と時刻の強化機能 &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-date-time/date-and-time-improvements-ole-db.md)  
  
  
