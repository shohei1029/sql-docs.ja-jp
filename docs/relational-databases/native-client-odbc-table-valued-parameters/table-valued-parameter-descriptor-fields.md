---
description: テーブル値パラメーターの記述子フィールド
title: Table-Valued パラメーター記述子のフィールド |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- table-valued parameters (ODBC), descriptor fields
ms.assetid: 4e009eff-c156-4d63-abcf-082ddd304de2
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6c45999c04377bb88f48e7f97a85b46020673eaf
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755052"
---
# <a name="table-valued-parameter-descriptor-fields"></a>テーブル値パラメーターの記述子フィールド
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  テーブル値パラメーターのサポートには、ODBC アプリケーション パラメーター記述子 (APD) および実装パラメーター記述子 (IPD) の新しい [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有のフィールドが含まれます。  
  
## <a name="remarks"></a>解説  
  
|名前|場所|Type|説明|  
|----------|--------------|----------|-----------------|  
|SQL_CA_SS_TYPE_NAME|IPD|SQLTCHAR*|テーブル値パラメーターのサーバーの型名です。<br /><br /> SQLBindParameter の呼び出しでテーブル値パラメーターの型名を指定する場合は、ANSI アプリケーションとしてビルドされたアプリケーションであっても、常に Unicode 値として指定する必要があります。 パラメーター *StrLen_or_IndPtr* に使用される値は、SQL_NTS か、名前の文字列の長さに SIZEOF (WCHAR) を掛けたものである必要があります。<br /><br /> SQLSetDescField を使用してテーブル値パラメーターの型名を指定する場合は、アプリケーションのビルド方法に準拠したリテラルを使用して指定できます。 ODBC ドライバー マネージャーで、必要な Unicode 変換を実行します。|  
|SQL_CA_SS_TYPE_CATALOG_NAME (読み取り専用)|IPD|SQLTCHAR*|型が定義されているカタログです。|  
|SQL_CA_SS_TYPE_SCHEMA_NAME|IPD|SQLTCHAR*|型が定義されているスキーマです。|  
  
 アプリケーションでは、テーブル値パラメーターの SQL_CA_SS_TYPE_CATALOG_NAME を設定できません。 これを設定すると、SQL_ERROR が返され、"記述子フィールドの識別子が正しくありません" というメッセージで SQLSTATE = HY091 の診断レコードが記録されます。  
  
 次のステートメント属性および記述子のヘッダー フィールドは、テーブル値パラメーターにフォーカスが設定されている場合に適用されます。  
  
|名前|場所|Type|説明|  
|----------|--------------|----------|-----------------|  
|SQL_ATTR_PARAMSET_SIZE<br /><br /> (APD の SQL_DESC_ARRAY_SIZE に相当)|APD|SQLUINTEGER|テーブル値パラメーターのバッファー配列のサイズです。 これは、バッファーが保持できる最大行数、または行内のバッファーのサイズです。テーブル値パラメーターの値自体には、バッファーで保持できるよりも多い (または少ない) 行が含まれる場合があります。 既定値は 1 です。<br /><br /> 注: SQL_SOPT_SS_PARAM_FOCUS が既定値の0に設定されている場合、SQL_ATTR_PARAMSET_SIZE はステートメントを参照し、パラメーターセットの数を指定します。 SQL_SOPT_SS_PARAM_FOCUS がテーブル値パラメーターの序数に設定されている場合は、テーブル値パラメーターを参照し、テーブル値パラメーターのパラメーター セットごとの行数を指定します。|  
|SQL_ATTR_PARAM _BIND_TYPE|APD|SQLINTEGER|既定値は SQL_PARAM_BIND_BY_COLUMN です。<br /><br /> 行方向のバインドを選択するには、このフィールドに、構造体の長さ、または一連のテーブル値パラメーターの行のバインドされるバッファーのインスタンスの長さが設定されます。 この長さには、バインドされたすべての列の領域と、構造体またはバッファーの埋め込みを含める必要があります。 これにより、バインドされた列のアドレスが指定の長さでインクリメントされると、結果は、次の行の同じ列の先頭を指すようになります。 ANSI C で **sizeof** 演算子を使用すると、この動作は保証されます。|  
|SQL_ATTR_PARAM_BIND_OFFSET_PTR|APD|SQLINTEGER*|既定値は NULL ポインターです。<br /><br /> このフィールドが NULL 以外の場合、ドライバーはポインターを逆参照し、逆参照された値を記述子レコードの遅延された各フィールド (SQL_DESC_DATA_PTR、SQL_DESC_INDICATOR_PTR、および SQL_DESC_OCTET_LENGTH_PTR) に追加して、新しいポインター値を使用してデータ値にアクセスします。|  
  
 これらのフィールドは、テーブル値パラメーターでのみ有効です。それ以外のデータ型の場合は無視されます。  
  
 ストアド プロシージャを呼び出す場合、SQL_CA_SS_TYPE_NAME は省略可能です。 サーバー側でテーブル値パラメーターの型を特定できるようなプロシージャの呼び出しでない場合は、SQL ステートメントで SQL_CA_SS_TYPE_NAME を指定する必要があります。  
  
 型名が必要で、テーブル値パラメーターのテーブル型がストアド プロシージャとは異なるスキーマで定義されている場合、SQL_CA_SS_TYPE_SCHEMA_NAME を実装パラメーター記述子 (IPD) で指定する必要があります。 指定しないと、サーバーでテーブル値パラメーターの型を特定できません。 この場合、SQLExecute または SQLExecDirect を呼び出すとエラーが発生します。 エラーは SQLSTATE= 07006 で、メッセージは "データ型の属性に関する制限に違反しました" になります。  
  
 テーブル値パラメーターの列では、行方向のバインドまたは列方向のバインドを使用できます。 既定では、列方向のバインドが使用されます。 行方向のバインドは、SQL_ATTR_PARAM_BIND_TYPE および SQL_ATTR_ PARAM_BIND_OFFSET_PTR を設定することで指定できます。 これは、列およびパラメーターの行方向のバインドと似ています。  
  
 SQL_CA_SS_TYPE_CATALOG_NAME および SQL_CA_SS_TYPE_SCHEMA_NAME を使用すると、CLR ユーザー定義型パラメーターに関連付けられたカタログとスキーマも取得できます。 これらは、CLR ユーザー定義型の既存の型固有のカタログ スキーマの属性の代わりに使用します。  
  
## <a name="see-also"></a>参照  
 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)  
  
  
