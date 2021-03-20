---
description: ステートメントの準備と実行 (ODBC)
title: ステートメントの準備と実行 (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- statement execution
- statement preparation
ms.assetid: 0adecc63-4da5-486c-bc48-09a004a2fae6
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 94270caf9aae58a48ec9905062b7f8f317cdfeb3
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753722"
---
# <a name="prepare-and-execute-a-statement-odbc"></a>ステートメントの準備と実行 (ODBC)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

    
### <a name="to-prepare-a-statement-once-and-then-execute-it-multiple-times"></a>ステートメントを 1 回だけ準備して複数回実行するには  
  
1.  [SQLPrepare 関数](../../../odbc/reference/syntax/sqlprepare-function.md)を呼び出して、ステートメントを準備します。  
  
2.  必要に応じて、 [Sqlnumparams](../../../odbc/reference/syntax/sqlnumparams-function.md) を呼び出して、準備されたステートメント内のパラメーターの数を確認します。  
  
3.  必要に応じて、準備されたステートメント内の各パラメーターに対して次の操作を行います。  
  
    -   [SQLDescribeParam](../../../relational-databases/native-client-odbc-api/sqldescribeparam.md)を呼び出して、パラメーター情報を取得します。  
  
    -   [SQLBindParameter](../../../relational-databases/native-client-odbc-api/sqlbindparameter.md)を使用して、各パラメーターをプログラム変数にバインドします。 実行時データ パラメーターをセットアップします。  
  
4.  準備されたステートメントの各実行に対して次の操作を行います。  
  
    -   ステートメントにパラメーター マーカーがある場合は、バインドされたパラメーター バッファーにデータ値を格納します。  
  
    -   [Sqlexecute](../../../odbc/reference/syntax/sqlexecute-function.md)を呼び出して、準備されたステートメントを実行します。  
  
    -   実行時データ入力パラメーターが使用されている場合、 [Sqlexecute](../../../odbc/reference/syntax/sqlexecute-function.md) は SQL_NEED_DATA を返します。 [Sqlparamdata](../../../odbc/reference/syntax/sqlparamdata-function.md)と[sqlparamdata](../../../relational-databases/native-client-odbc-api/sqlputdata.md)を使用して、データをチャンク単位で送信します。  
  
### <a name="to-prepare-a-statement-with-column-wise-parameter-binding"></a>列方向のパラメーターのバインドを使用してステートメントを準備するには  
  
1.  [SQLSetStmtAttr](../../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)を呼び出して、次の属性を設定します。  
  
    -   SQL_ATTR_PARAMSET_SIZE に、パラメーターのセット数 (S) を設定します。  
  
    -   SQL_ATTR_PARAM_BIND_TYPE を SQL_PARAMETER_BIND_BY_COLUMN に設定します。  
  
    -   SQL_ATTR_PARAMS_PROCESSED_PTR 属性を、処理されたパラメーターの数を格納する SQLUINTEGER 変数を指すように設定します。  
  
    -   SQL_ATTR_PARAMS_STATUS_PTR を、パラメーターの状態インジケーターを格納する SQLUSSMALLINT 変数の配列[S] を指すように設定します。  
  
2.  SQLPrepare を呼び出して、ステートメントを準備します。  
  
3.  必要に応じて、 [Sqlnumparams](../../../odbc/reference/syntax/sqlnumparams-function.md) を呼び出して、準備されたステートメント内のパラメーターの数を確認します。  
  
4.  必要に応じて、準備されたステートメントの各パラメーターに対して SQLDescribeParam を呼び出して、パラメーター情報を取得します。  
  
5.  各パラメーター マーカーについて、次の操作を行います。  
  
    -   データ値を格納する S パラメーター バッファーの配列を割り当てます。  
  
    -   データの長さを格納する S パラメーター バッファーの配列を割り当てます。  
  
    -   SQLBindParameter を呼び出して、パラメーターのデータ値とデータ長の配列をステートメントパラメーターにバインドします。  
  
    -   パラメーターが実行時データの text または image パラメーターである場合は、それをセットアップします。  
  
    -   実行時データ パラメーターを使用する場合は、それらをセットアップします。  
  
6.  準備されたステートメントの各実行に対して次の操作を行います。  
  
    -   S データ値と S データ長をバインドされたパラメーター配列に格納します。  
  
    -   SQLExecute を呼び出して、準備されたステートメントを実行します。  
  
    -   実行時データ入力パラメーターが使用されている場合、SQLExecute は SQL_NEED_DATA を返します。 SQLParamData と Sqlparamdata を使用して、データをチャンク単位で送信します。  
  
### <a name="to-prepare-a-statement-with-row-wise-bound-parameters"></a>行方向にバインドされたパラメーターを使用してステートメントを準備するには  
  
1.  構造体の配列[S] を割り当てます。この S は行パラメーターのセット数です。 構造体には各パラメーターについて 1 つの要素があり、各要素は次の 2 つの部分で構成されています。  
  
    -   最初の部分は、パラメーター データを格納する適切なデータ型の変数です。  
  
    -   2 つ目の部分は、状態インジケーターを格納する SQLINTEGER 変数です。  
  
2.  [SQLSetStmtAttr](../../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)を呼び出して、次の属性を設定します。  
  
    -   SQL_ATTR_PARAMSET_SIZE に、パラメーターのセット数 (S) を設定します。  
  
    -   SQL_ATTR_PARAM_BIND_TYPE を、手順 1. で割り当てた構造体のサイズに設定します。  
  
    -   SQL_ATTR_PARAMS_PROCESSED_PTR 属性を、処理されたパラメーターの数を格納する SQLUINTEGER 変数を指すように設定します。  
  
    -   SQL_ATTR_PARAMS_STATUS_PTR を、パラメーターの状態インジケーターを格納する SQLUSSMALLINT 変数の配列[S] を指すように設定します。  
  
3.  SQLPrepare を呼び出して、ステートメントを準備します。  
  
4.  各パラメーターマーカーについて、SQLBindParameter を呼び出して、手順 1. で割り当てた構造体の配列の最初の要素にあるパラメーターのデータ値とデータ長ポインターをポイントします。 パラメーターが実行時データ パラメーターである場合は、そのパラメーターをセットアップします。  
  
5.  準備されたステートメントの各実行に対して次の操作を行います。  
  
    -   バインドされたパラメーターのバッファー配列にデータ値を入力します。  
  
    -   SQLExecute を呼び出して、準備されたステートメントを実行します。 ドライバーによって、S 回 (パラメーターのセットごとに 1 回) SQL ステートメントが効率よく実行されます。  
  
    -   実行時データ入力パラメーターが使用されている場合、SQLExecute は SQL_NEED_DATA を返します。 SQLParamData と Sqlparamdata を使用して、データをチャンク単位で送信します。  
  
## <a name="see-also"></a>参照  
 [クエリの実行方法に関するトピック &#40;ODBC&#41;](../../../relational-databases/native-client-odbc-how-to/execute-queries/executing-queries-how-to-topics-odbc.md)  
  
