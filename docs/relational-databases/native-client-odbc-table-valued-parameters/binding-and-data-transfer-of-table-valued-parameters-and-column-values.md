---
title: Table-Valued パラメーターのデータ転送
description: Table-Valued パラメーターのデータ転送について説明します
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.technology: native-client
ms.topic: reference
ms.assetid: 0a2ea462-d613-42b6-870f-c7fa086a6b42
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: ''
ms.date: 07/01/2020
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0a7a3ea0c3448de104a2742f7255c026946b405b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752432"
---
# <a name="binding-and-data-transfer-of-table-valued-parameters-and-column-values"></a>テーブル値パラメーターおよび列の値のバインドとデータ転送

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

他のパラメーターと同様に、テーブル値パラメーター (TVP) は、サーバーに渡される前にバインドされている必要があります。 アプリケーションは、他のパラメーターをバインドするのと同じ方法で、テーブル値パラメーターをバインドします。 SQLBindParameter または同等の SQLSetDescField または SQLSetDescRec の呼び出しを使用します。 テーブル値パラメーター用のサーバーのデータ型は SQL_SS_TABLE です。 C 型は SQL_C_DEFAULT または SQL_C_BINARY として指定できます。  

[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降では、入力のテーブル値パラメーターのみがサポートされています。 そのため、SQL_DESC_PARAMETER_TYPE を SQL_PARAM_INPUT 以外の値に設定しようとすると、SQLSTATE = HY105 で SQL_ERROR が返され、"パラメーターの型が無効です" というメッセージが表示されます。  

属性 SQL_CA_SS_COL_HAS_DEFAULT_VALUE を使用すると、テーブル値パラメーターのすべての列に既定値を割り当てることができます。 ただし、テーブル値パラメーターの個々の列値には、SQLBindParameter で *StrLen_or_IndPtr* の SQL_DEFAULT_PARAM を使用して既定値を割り当てることはできません。 SQLBindParameter で *StrLen_or_IndPtr* で SQL_DEFAULT_PARAM を使用すると、テーブル値パラメーター全体を既定値に設定することはできません。 これらの規則に従わないと、SQLExecute または SQLExecDirect は SQL_ERROR を返します。 診断レコードは SQLSTATE = 07S01 というメッセージで生成されます。 "パラメーターの既定のパラメーターが正しく使用されていませ \<p> \<p> ん。" は、クエリステートメント内の tvp の序数です。  

> [!Note]
> テーブル値パラメーターには、既定値が設定されていません。 SQL_DEFAULT_PARAM は行がないことを示しているためです。 行が存在しない場合は、バインドする列がありません。

テーブル値パラメーターをバインドしたら、アプリケーションでは、次に、テーブル値パラメーターの各列をバインドする必要があります。 これを行うには、アプリケーションはまず SQLSetStmtAttr を呼び出して、テーブル値パラメーターの序数に SQL_SOPT_SS_PARAM_FOCUS を設定します。 アプリケーションは、SQLBindParameter、SQLSetDescRec、および SQLSetDescField の各ルーチンを呼び出して、テーブル値パラメーターの列をバインドします。 SQL_SOPT_SS_PARAM_FOCUS を0に設定すると、通常の最上位レベルのパラメーターを操作するときに、SQLBindParameter、SQLSetDescRec、および SQLSetDescField の通常の効果が復元されます。

> [!Note]
> UnixODBC 2.3.1 が2.3.4 の Linux および Mac ODBC ドライバーの場合、SQLSetDescField を使用して TVP 名を SQL_CA_SS_TYPE_NAME 記述子フィールドに設定すると、unixODBC は、という正確な関数 (SQLSetDescFieldA/SQLSetDescFieldW) に応じて、ANSI 文字列と Unicode 文字列を自動的に変換しません。 TVP 名を設定するには、常に Unicode (UTF-16) 文字列で SQLBindParameter または SQLSetDescFieldW を使用する必要があります。

テーブル値パラメーター自体の実際のデータは送受信されませんが、テーブル値パラメーターを構成する各列のデータは送受信されます。 テーブル値パラメーターは擬似列であるため、SQLBindParameter のパラメーターは、次のように他のデータ型とは異なる属性を参照します。  

| パラメーター | テーブル値以外のパラメーターの型に関連する属性 (列を含む) | テーブル値パラメーターに関連する属性 |
|-----------|---------------------------------------------------------------------------|-----------------------------------------------|
|*InputOutputType*|IPD の SQL_DESC_PARAMETER_TYPE。<br /><br /> テーブル値パラメーターの列の場合、これはテーブル値パラメーター自体の設定と同じである必要があります。|IPD の SQL_DESC_PARAMETER_TYPE。<br /><br /> これは SQL_PARAM_INPUT である必要があります。|  
|*ValueType*|APD の SQL_DESC_TYPE、SQL_DESC_CONCISE_TYPE。|APD の SQL_DESC_TYPE、SQL_DESC_CONCISE_TYPE。<br /><br /> これは SQL_C_DEFAULT または SQL_C_BINARY である必要があります。|  
|*ParameterType*|IPD の SQL_DESC_TYPE、SQL_DESC_CONCISE_TYPE。|IPD の SQL_DESC_TYPE、SQL_DESC_CONCISE_TYPE。<br /><br /> これは SQL_SS_TABLE である必要があります。|  
|*ColumnSize*|IPD の SQL_DESC_LENGTH または SQL_DESC_PRECISION。<br /><br /> これは、 *ParameterType* の値によって異なります。|SQL_DESC_ARRAY_SIZE<br /><br /> テーブル値パラメーターにフォーカスが設定されている場合は SQL_ATTR_PARAM_SET_SIZE を使用して設定することもできます。<br /><br /> テーブル値パラメーターの場合、これはテーブル値パラメーターの列バッファー内の行数です。|  
|*DecimalDigits*|IPD の SQL_DESC_PRECISION または SQL_DESC_SCALE。|未使用。 これは 0 である必要があります。<br /><br /> このパラメーターが0でない場合、SQLBindParameter は SQL_ERROR を返し、"有効桁数または小数点以下桁数が無効です" というメッセージで SQLSTATE = HY104 の診断レコードが生成されます。|  
|*ParameterValuePtr*|APD の SQL_DESC_DATA_PTR。|SQL_CA_SS_TYPE_NAME。<br /><br /> これは、ストアドプロシージャの呼び出しでは省略可能であり、必要でない場合は NULL を指定できます。 プロシージャ呼び出しではない SQL ステートメントに対して指定する必要があります。<br /><br /> このパラメーターは、可変の行バインドの使用時にアプリケーションがテーブル値パラメーターを特定するための一意の値としても機能します。 詳細については、「テーブル値パラメーターの可変の行バインド」を参照してください。<br /><br /> SQLBindParameter の呼び出しでテーブル値パラメーターの型名を指定する場合は、ANSI アプリケーションとしてビルドされたアプリケーションであっても、Unicode 値として指定する必要があります。 パラメーター *StrLen_or_IndPtr* に使用される値は、SQL_NTS または名前の文字列の長さに (WCHAR) のサイズを乗算した値である必要があります。|  
|*BufferLength*|APD の SQL_DESC_OCTET_LENGTH。|テーブル値パラメーターの型名の長さ (バイト単位)。<br /><br /> 型名が null で終わる場合は SQL_NTS、テーブル値パラメーターの型名が不要な場合は0になります。|  
|*StrLen_or_IndPtr*|APD の SQL_DESC_OCTET_LENGTH_PTR。|APD の SQL_DESC_OCTET_LENGTH_PTR。<br /><br /> テーブル値パラメーターの場合、これはデータ長ではなく行数です。|  
||||

テーブル値パラメーターでは、固定の行バインドと可変の行バインドという 2 つのデータ転送モードがサポートされています。  

## <a name="fixed-table-valued-parameter-row-binding"></a>テーブル値パラメーターの固定の行バインド

固定の行バインドの場合、アプリケーションでは、使用可能なすべての入力列の値に対して十分な大きさのバッファー (バッファー配列) を割り当てます。 このアプリケーションによって次の処理が行われます。  

1. SQLBindParameter、SQLSetDescRec、または SQLSetDescField の呼び出しを使用して、すべてのパラメーターをバインドします。  

    1. SQL_DESC_ARRAY_SIZE に各テーブル値パラメーターの転送可能な最大行数を設定します。 これは、SQLBindParameter の呼び出しで行うことができます。  

2. SQLSetStmtAttr を呼び出して、各テーブル値パラメーターの序数に SQL_SOPT_SS_PARAM_FOCUS を設定します。  

    1. テーブル値パラメーターごとに、SQLBindParameter、SQLSetDescRec、または SQLSetDescField の呼び出しを使用して、テーブル値パラメーターの列をバインドします。  

    2. 既定値を持つテーブル値パラメーターの列ごとに、SQLSetDescField を呼び出して SQL_CA_SS_COL_HAS_DEFAULT_VALUE を1に設定します。  

3. SQLSetStmtAttr を呼び出して、SQL_SOPT_SS_PARAM_FOCUS を0に設定します。 これは、SQLExecute または SQLExecDirect が呼び出される前に実行する必要があります。 それ以外の場合は SQL_ERROR が返され、"無効な属性値、SQL_SOPT_SS_PARAM_FOCUS (実行時には0でなければなりません)" というメッセージで SQLSTATE = HY024 の診断レコードが生成されます。  

4. 行を含まないテーブル値パラメーターの SQL_DEFAULT_PARAM に *StrLen_or_IndPtr* または SQL_DESC_OCTET_LENGTH_PTR を設定します。または、テーブル値パラメーターに行がある場合は、sqlexecute または SQLExecDirect の次回の呼び出し時に転送される行の数を設定します。 テーブル値パラメーターが null 許容ではないため、 *StrLen_or_IndPtr* または SQL_DESC_OCTET_LENGTH_PTR をテーブル値パラメーターの SQL_NULL_DATA に設定することはできません (ただし、テーブル値パラメーターの構成列には null 値が許容される可能性があります)。 この値が無効な値に設定されている場合、SQLExecute または SQLExecDirect は SQL_ERROR を返し、"パラメーターの文字列またはバッファーの長さが正しくありません" というメッセージが表示され \<p> ます。ここで、p はパラメーター番号です。

5. SQLExecute または SQLExecDirect を呼び出します。

    入力テーブル値パラメーターの列の値は、 *StrLen_or_IndPtr* が SQL_LEN_DATA_AT_EXEC (*長さ*) に設定されているか、列の SQL_DATA_AT_EXEC に設定されている場合に、部分的に渡すことができます。 これは、パラメーターの配列を使用するときに値を個別に渡す場合と似ています。 実行時データのすべてのパラメーターと同様に、SQLParamData は、ドライバーがデータを要求している配列の行を示していません。アプリケーションでは、このことに対処する必要があります。 アプリケーションは、ドライバーが値を要求する順序について、想定を行うことはできません。  

## <a name="variable-table-valued-parameter-row-binding"></a>テーブル値パラメーターの可変の行バインド  

可変行バインドでは、実行時に行がバッチで転送され、アプリケーションは必要に応じて行をドライバーに渡します。 これは、各パラメーター値の実行時データに似ています。 可変の行バインドでは、アプリケーションによって次の処理が行われます。  

1. 前のセクションの手順 1. ~ 3. で説明したように、パラメーターとテーブル値パラメーターの列をバインドします。前のセクション「固定 Table-Valued パラメーターの行バインド」を参照してください。  

2. 実行時に SQL_DATA_AT_EXEC に渡すテーブル値パラメーターの *StrLen_or_IndPtr* または SQL_DESC_OCTET_LENGTH_PTR を設定します。 どちらも設定されていない場合は、前のセクションで説明したように、パラメーターが処理されます。  

3. SQLExecute または SQLExecDirect を呼び出します。 これは、実行時データパラメーターとして処理される SQL_PARAM_INPUT または SQL_PARAM_INPUT_OUTPUT パラメーターがある場合に SQL_NEED_DATA を返します。 この場合、アプリケーションによって次の処理が行われます。  

    - SQLParamData を呼び出します。 これにより、実行時データパラメーターの *Parametervalueptr* 値と SQL_NEED_DATA のリターンコードが返されます。 すべてのパラメーターデータがドライバーに渡されると、SQLParamData は SQL_SUCCESS、SQL_SUCCESS_WITH_INFO、または SQL_ERROR を返します。 実行時データパラメーターの場合、 *Parametervalueptr* は、記述子フィールド SQL_DESC_DATA_PTR と同じですが、値が一意に必要なパラメーターを識別するためのトークンと見なすことができます。 この "トークン" は、バインド時にアプリケーションからドライバーに渡され、実行時にアプリケーションに返されます。  
  
4. Null テーブル値パラメーターのテーブル値パラメーターの行データを送信するには、テーブル値パラメーターに行がない場合、アプリケーションは *StrLen_or_Ind* を SQL_DEFAULT_PARAM に設定して SQLPutData を呼び出します。  

    NULL 以外の TVP については、アプリケーションによって次の処理が行われます。

    - すべてのテーブル値パラメーター列の *Str_Len_or_Ind* を適切な値に設定し、実行時データパラメーターではないテーブル値パラメーター列のデータバッファーを設定します。 通常のパラメーターを個別にドライバーに渡すことができるように、テーブル値パラメーターの列の実行時データを使用することができます。  

    - は、サーバーに送信される行の数に設定された *Str_Len_or_Ind* を使用して SQLPutData を呼び出します。 0 ~ SQL_DESC_ARRAY_SIZE または SQL_DEFAULT_PARAM の範囲外の値はエラーとなり、"文字列またはバッファーの長さが無効です" というメッセージと共に SQLSTATE HY090 が返されます。 0は、すべての行が送信され、テーブル値パラメーターのデータがないことを示します (この一覧の2番目の箇条書き項目を参照)。 SQL_DEFAULT_PARAM を使用できるのは、ドライバーがテーブル値パラメーターのデータを最初に要求するときだけです (この箇条書きの 1 番目の項目参照)。  

5. すべての行が送信されると、は *Str_Len_or_Ind* 値が0のテーブル値パラメーターの SQLPutData を呼び出した後、上記の手順3a に進みます。  

6. SQLParamData を再度呼び出します。 テーブル値パラメーターの列の間に実行時データパラメーターがある場合は、SQLParamData によって返される値 *Valueptrptr* によって識別されます。 すべての列値を使用できる場合、SQLParamData はテーブル値パラメーターの *Parametervalueptr* 値を返し、アプリケーションは再度開始します。  

## <a name="next-steps"></a>次のステップ

[テーブル値パラメーターの ODBC](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)