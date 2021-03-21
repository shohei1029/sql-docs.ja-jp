---
description: データ型の変換 (データベース エンジン)
title: データ型の変換 (データベース エンジン) | Microsoft Docs
ms.custom: ''
ms.date: 07/23/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- CAST function
- converting data types [SQL Server]
- CONVERT function
- data types [SQL Server], converting
- implicit data type conversions
- explicit data type conversions [SQL Server]
- converting data types [SQL Server], about converting data types
ms.assetid: ffacf45e-a488-48d0-9bb0-dcc7fd365299
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2a14b2a30a41dcc5528059fc7e77f47646e4121c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735652"
---
# <a name="data-type-conversion-database-engine"></a>データ型の変換 (データベース エンジン)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

データ型は、以下のシナリオで変換される場合があります。
-   あるオブジェクトのデータを他のオブジェクトのデータに移動、比較、または結合する場合は、あるオブジェクトのデータ型から他のオブジェクトのデータ型への変換が必要な場合があります。  
-   [!INCLUDE[tsql](../../includes/tsql-md.md)] 結果列、リターン コード、または出力パラメーターのデータをプログラム変数に移動する場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のシステム データ型から変数のデータ型にデータを変換する必要があります。  
  
アプリケーション変数と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の結果セット列、リターン コード、パラメーター、またはパラメーター マーカーの間でデータ型を変換する場合、サポートされるデータ型変換はデータベース API によって決まります。
  
## <a name="implicit-and-explicit-conversion"></a>暗黙的な変換と明示的な変換
データ型は、暗黙的または明示的に変換できます。
  
暗黙的な変換はユーザーが意識する必要はありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がデータのデータ型を自動的に変換します。 たとえば、**smallint** 型を **int** 型と比較する場合、比較を実行する前に、**smallint** 型から **int** 型に暗黙的に変換されます。
  
**GETDATE()** は、暗黙的に日付スタイル 0 に変換します。 **SYSDATETIME()** は、暗黙的に日付スタイル 21 に変換します。
  
明示的な変換では、CAST 関数または CONVERT 関数を使用します。
  
[CAST と CONVERT](../../t-sql/functions/cast-and-convert-transact-sql.md) の各関数は、値 (ローカル変数、列、または他の式) のデータ型を変換します。 たとえば、次の `CAST` 関数は数値 `$157.27` を文字列 `'157.27'` に変換します。
  
```sql
CAST ( $157.27 AS VARCHAR(10) )  
```  
  
[!INCLUDE[tsql](../../includes/tsql-md.md)] のプログラム コードを ISO に準拠させる場合は、CONVERT ではなく CAST を使います。 CONVERT のスタイル機能を利用する場合は、CAST ではなく CONVERT を使用します。
  
次の図は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システムで提供されるデータ型に許可されている、すべての明示的および暗黙的なデータ型変換です。 **xml**、**bigint**、**sql_variant** が含まれます。 代入時に **sql_variant** データ型からの暗黙的な変換は行われませんが、**sql_variant** への暗黙的な変換は行われます。
  
![データ型変換テーブル](../../t-sql/data-types/media/lrdatahd.png "データ型変換テーブル")

上のグラフは、SQL Server で許可されているすべての明示的および暗黙的な変換を示していますが、変換の結果のデータ型を示すものではありません。 SQL Server で明示的な変換が実行されると、ステートメント自体によって結果のデータ型が決定されます。 暗黙的な変換の場合、変数の値の設定や列への値の挿入などの代入ステートメントは、変数宣言または列定義によって定義されたデータ型になります。 比較演算子または他の式の場合、結果のデータ型は、データ型の優先順位の規則によって異なります。

例として、次のスクリプトでは型 `varchar` の変数が定義され、`int` 型の値が変数に代入された後、文字列型の変数の連結が選択されます。

```sql
DECLARE @string VARCHAR(10);
SET @string = 1;
SELECT @string + ' is a string.'
```

`1` の `int` 値は `varchar` に変換されるため、`SELECT` ステートメントからは値 `1 is a string.` が返されます。

次の例は、代わりに `int` 変数を使用した同様のスクリプトを示しています。

```sql
DECLARE @notastring INT;
SET @notastring = '1';
SELECT @notastring + ' is not a string.'
```

この場合、`SELECT` ステートメントからは次のエラーがスローされます。

`Msg 245, Level 16, State 1, Line 3`
`Conversion failed when converting the varchar value ' is not a string.' to data type int.`

式 `@notastring + ' is not a string.'` を評価するために、SQL Server では、式の結果を計算する前に、データ型の優先順位の規則に従って暗黙的な変換を完了します。 `int` は `varchar` よりも優先順位が高いため、SQL Server では文字列の整数への変換が試行され、その文字列を整数に変換できないため失敗します。 式に変換可能な文字列が指定されている場合は、ステートメントが成功します。次に例を示します。

```sql
DECLARE @notastring INT;
SET @notastring = '1';
SELECT @notastring + '1'
```

この場合、文字列 `1` を整数値 `1` に変換できるため、この `SELECT` ステートメントからは値 `2` が返されます。 指定されたデータ型が整数である場合、`+` 演算子は連結ではなく加算になることに注意してください。

## <a name="data-type-conversion-behaviors"></a>データ型変換の動作

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] オブジェクト間のデータ型の変換を行う場合、暗黙的または明示的なデータ型変換がサポートされない場合があります。 たとえば、**nchar** 型の値を **image** 型の値に変換することはできません。 **nchar** は明示的な変換によってのみ **binary** に変換できます。**binary** への暗黙的な変換はサポートされません。 ただし、**nchar** は暗黙的、明示的のどちらでも **nvarchar** に変換できます。
  
次のトピックでは、対応するデータ型変換の動作について説明しています。
  
 - [binary と varbinary &#40;Transact-SQL&#41;](../../t-sql/data-types/binary-and-varbinary-transact-sql.md)  
 - [datetime2 &#40;Transact-SQL&#41;](../../t-sql/data-types/datetime2-transact-sql.md)  
 - [money および smallmoney &#40;Transact-SQL&#41;](../../t-sql/data-types/money-and-smallmoney-transact-sql.md)  
 - [bit &#40;Transact-SQL&#41;](../../t-sql/data-types/bit-transact-sql.md)  
 - [datetimeoffset &#40;Transact-SQL&#41;](../../t-sql/data-types/datetimeoffset-transact-sql.md)  
 - [smalldatetime &#40;Transact-SQL&#41;](../../t-sql/data-types/smalldatetime-transact-sql.md)  
 - [char および varchar &#40;Transact-SQL&#41;](../../t-sql/data-types/char-and-varchar-transact-sql.md)  
 - [decimal 型と numeric 型 &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)  
 - [sql_variant &#40;Transact-SQL&#41;](../../t-sql/data-types/sql-variant-transact-sql.md)  
 - [date &#40;Transact-SQL&#41;](../../t-sql/data-types/date-transact-sql.md)  
 - [float と real &#40;Transact-SQL&#41;](../../t-sql/data-types/float-and-real-transact-sql.md)  
 - [time &#40;Transact-SQL&#41;](../../t-sql/data-types/time-transact-sql.md)  
 - [datetime &#40;Transact-SQL&#41;](../../t-sql/data-types/datetime-transact-sql.md)  
 - [int、bigint、smallint、tinyint &#40;Transact-SQL&#41;](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md)  
 - [一意識別子 &#40;Transact-SQL&#41;](../../t-sql/data-types/uniqueidentifier-transact-sql.md)  
  
###  <a name="converting-data-types-by-using-ole-automation-stored-procedures"></a>OLE オートメーション ストアド プロシージャを使用したデータ型の変換  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は [!INCLUDE[tsql](../../includes/tsql-md.md)] のデータ型を使用し、OLE オートメーションは [!INCLUDE[vbprvb](../../includes/vbprvb-md.md)] のデータ型を使用するため、OLE オートメーション ストアド プロシージャでは両方の間で渡されるデータの型を変換する必要があります。
  
次の表は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型から [!INCLUDE[vbprvb](../../includes/vbprvb-md.md)] データ型への変換を示します。
  
|SQL Server のデータ型|Visual Basic のデータ型|  
|--------------------------|----------------------------|  
|**char**、**varchar**、**text**、**nvarchar**、**ntext**|**String**|  
|**decimal**、**numeric**|**String**|  
|**bit**|**Boolean**|  
|**binary**、**varbinary**、**image**|1 次元 **Byte()** 配列|  
|**int**|**Long**|  
|**smallint**|**整数**|  
|**tinyint**|**Byte**|  
|**float**|**Double**|  
|**real**|**Single**|  
|**money**、 **smallmoney**|**通貨**|  
|**datetime**、**smalldatetime**|**日付**|  
|上記以外は NULL に設定|null 値に設定された **Variant**|  
  
**binary**、**varbinary**、**image** の各型の値を除いて、1 つの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 値はすべて 1 つの [!INCLUDE[vbprvb](../../includes/vbprvb-md.md)] 値に変換されます。 これらの値は [!INCLUDE[vbprvb](../../includes/vbprvb-md.md)] の 1 次元 **Byte()** 配列に変換されます。 この配列の範囲は、**Byte(** 0 から _length_ 1 **)** です。*length* は、 の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **binary**、**varbinary**、または **image** の値のバイト数です。
  
次の表は、[!INCLUDE[vbprvb](../../includes/vbprvb-md.md)] データ型から [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型への変換を示しています。
  
|Visual Basic のデータ型|SQL Server のデータ型|  
|----------------------------|--------------------------|  
|**Long**、**Integer**、**Byte**、**Boolean**、**Object**|**int**|  
|**Double**、**Single**|**float**|  
|**通貨**|**money**|  
|**日付**|**datetime**|  
|4,000 文字以下の **String**|**varchar**/**nvarchar**|  
|4,000 文字を超える **String**|**text**/**ntext**|  
|8,000 バイト以下の 1 次元 **Byte()** 配列|**varbinary**|  
|8,000 バイトを超える 1 次元 **Byte()** 配列|**image**|  
  
## <a name="see-also"></a>関連項目
[OLE オートメーション ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/ole-automation-stored-procedures-transact-sql.md)  
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)  
[COLLATE &#40;Transact-SQL&#41;](../statements/collations.md)
  
