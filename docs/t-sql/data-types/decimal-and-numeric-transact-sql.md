---
title: decimal 型と numeric 型 (Transact-SQL) | Microsoft Docs
description: 10 進数データ型と数値データ型の Transact-SQL リファレンス。 10 進数と数値は、精度やスケールが固定されている数値データ型にとって類義語となります。
ms.date: 09/10/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- decimal
- decimal_TSQL
- numeric
- numeric_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- decimal data type
- decimal data type, about decimal data type
- numeric data type
- numeric data type, about numeric data type
ms.assetid: 9d862a90-e6b7-4692-8605-92358dccccdf
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a0ff8f34468e4186700d6942cbbfcbf5d6f2b09b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747722"
---
# <a name="decimal-and-numeric-transact-sql"></a>decimal 型と numeric 型 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

固定長の有効桁数と小数点以下保持桁数を持つ数値データ型です。 decimal と numeric は同義であり、どちらを使ってもかまいません。
  
## <a name="arguments"></a>引数
**decimal**[ **(** _p_[ **,** _s_] **)** ] and **numeric**[ **(** _p_[ **,** _s_] **)** ]  
固定長の有効桁数と小数点以下保持桁数です。 最大有効桁数を使用した場合、有効値は - 10^38 +1 から 10^38 - 1 です。 **decimal** の ISO のシノニムは、**dec** および **dec(** _p_, _s_ **)** です。 **numeric** は機能的には **decimal** と同じです。
  
p (precision)  
格納される 10 進数の桁数の最大合計数。 この数には、小数点の左側と右側の両方が含まれます。 有効桁数の値は、1 - 38 (最大有効桁数) にする必要があります。 既定の有効桁数は 18 です。
  
> [!NOTE]  
>  Informatica では、有効桁数と小数点以下桁数の指定に関係なく、16 の有効桁数のみサポートされます。  
  
*s* (scale)  
小数点の右側の保存される桁数です。 この数値が *p* から差し引かれ、小数点の左側の最大桁数が判別されます。 小数点以下桁数は、0 から *p* の範囲の値である必要があり、有効桁数が指定されている場合にのみ指定できます。 既定の小数点以下桁数は 0 です。したがって、0 <= *s* \<= *p* です。 ストレージの最大サイズは有効桁数によって異なります。
  
|Precision|ストレージのバイト数|  
|---|---|
|1 - 9|5|  
|10 から 19|9|  
|20 から 28|13|  
|29 から 38|17|  
  
> [!NOTE]  
>  Informatica (SQL Server PDW Informatica コネクタをによって接続されます) は、有効桁数と小数点以下桁数の指定に関係なく、16 桁の有効桁数のみをサポートします。  
  
## <a name="converting-decimal-and-numeric-data"></a>decimal 型データと numeric 型データの変換
**decimal** データ型と **numeric** データ型の場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、有効桁数と小数点以下桁数の組み合わせが異なる場合は、異なるデータ型と見なされます。 たとえば、**decimal(5,5)** と **decimal(5,0)** は異なるデータ型と見なされます。
  
[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントでは、小数点の付いた定数は、必要最小限の有効桁数と小数点以下桁数で自動的に **numeric** 型の値に変換されます。 たとえば、定数 12.345 は有効桁数が 5、小数点以下桁数が 3 の **numeric** 型に変換されます。
  
**decimal** または **numeric** から **float** または **real** への変換では、ある程度の有効桁数の損失が発生することがあります。 **int**、**smallint**、**tinyint**、**float**、**real**、**money**、または **smallmoney** から **decimal** または **numeric** への変換では、オーバーフローが発生することがあります。
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、既定で、数値を **decimal** 型または **numeric** 型の値に変換する場合、有効桁数と小数点以下桁数が少なくなって丸められます。 反対に、SET ARITHABORT オプションが ON に設定されている場合は、オーバーフローが起こると [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ではエラーが生成されます。 有効桁数と小数点以下桁数が失われただけではエラーは生成されません。
  
[!INCLUDE[ssSQL16](../../includes/sssql16-md.md)] より前では、**float** 値から **decimal** または **numeric** への変換は、有効桁数 17 桁までの値に制限されます。 5E-18 未満の **float** 値はすべて (5E-18 の科学的記数法または 0.0000000000000000050000000000000005 の小数点表記のいずれかを使用して設定されている場合) 0 に丸められます。 これは [!INCLUDE[ssSQL16](../../includes/sssql16-md.md)] の時点で制限がなくなりました。
  
## <a name="examples"></a>例  
次の例では、**decimal** および **numeric** データ型を使用してテーブルを作成します。  各列に値が挿入されます。 結果は、SELECT ステートメントを使用して返されます。
  
```sql
CREATE TABLE dbo.MyTable  
(  
  MyDecimalColumn DECIMAL(5,2)  
,MyNumericColumn NUMERIC(10,5)
  
);  
  
GO  
INSERT INTO dbo.MyTable VALUES (123, 12345.12);  
GO  
SELECT MyDecimalColumn, MyNumericColumn  
FROM dbo.MyTable;  
  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```sql
MyDecimalColumn                         MyNumericColumn  
--------------------------------------- ---------------------------------------  
123.00                                  12345.12000  
  
(1 row(s) affected)  
  
```  
  
## <a name="see-also"></a>関連項目
[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)  
[DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)  
[SET @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-local-variable-transact-sql.md)  
[sys.types &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-types-transact-sql.md)
  
