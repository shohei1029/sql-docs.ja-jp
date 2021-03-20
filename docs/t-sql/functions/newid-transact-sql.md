---
description: NEWID (Transact-SQL)
title: NEWID (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: synapse-analytics, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- NEWID
- NEWID_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- uniqueidentifier data type
- NEWID function
ms.assetid: f7014e60-96d5-457e-afc3-72b60ba20c0f
author: julieMSFT
ms.author: jrasnick
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: a79dada161166bbc7975176fe3a727f17455a2b0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104734946"
---
# <a name="newid-transact-sql"></a>NEWID (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

**uniqueidentifier** 型の一意の値を作成します。  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql 
NEWID ( )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型
 **uniqueidentifier**  
  
## <a name="remarks"></a>注釈  
 `NEWID()` は RFC4122 に準拠しています。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-the-newid-function-with-a-variable"></a>A. NEWID 関数を変数と共に使用する  
 次の例では、`NEWID()` を使用して、**uniqueidentifier** データ型として宣言された変数に値を割り当てます。 値、 **uniqueidentifier** 値をテストする前に、データ型の変数が出力されます。  
  
```sql
-- Creating a local variable with DECLARE/SET syntax.  
DECLARE @myid uniqueidentifier  
SET @myid = NEWID()  
PRINT 'Value of @myid is: '+ CONVERT(varchar(255), @myid)  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Value of @myid is: 6F9619FF-8B86-D011-B42D-00C04FC964FF  
```  
  
> [!NOTE]  
>  NEWID によって返される値は、コンピューターごとに異なります。 この数値は、説明のためだけに示しています。  
  
### <a name="b-using-newid-in-a-create-table-statement"></a>B. CREATE TABLE ステートメント内で NEWID を使用する  
  
**適用対象**:  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]
  
 次の例では、**uniqueidentifier** データ型を使用して `cust` テーブルを作成し、NEWID を使用してテーブルに既定値を入力します。 `NEWID()` の既定値が代入されると、新しい行と既存の行の `CustomerID` 列にそれぞれ一意な値が設定されます。  
  
```sql
-- Creating a table using NEWID for uniqueidentifier data type.  
CREATE TABLE cust  
(  
 CustomerID uniqueidentifier NOT NULL  
   DEFAULT newid(),  
 Company VARCHAR(30) NOT NULL,  
 ContactName VARCHAR(60) NOT NULL,   
 Address VARCHAR(30) NOT NULL,   
 City VARCHAR(30) NOT NULL,  
 StateProvince VARCHAR(10) NULL,  
 PostalCode VARCHAR(10) NOT NULL,   
 CountryRegion VARCHAR(20) NOT NULL,   
 Telephone VARCHAR(15) NOT NULL,  
 Fax VARCHAR(15) NULL  
);  
GO  
-- Inserting 5 rows into cust table.  
INSERT cust  
(Company, ContactName, Address, City, StateProvince,   
 PostalCode, CountryRegion, Telephone, Fax)  
VALUES  
 ('Wartian Herkku', 'Pirkko Koskitalo', 'Torikatu 38', 'Oulu', NULL,  
 '90110', 'Finland', '981-443655', '981-443655')  
,('Wellington Importadora', 'Paula Parente', 'Rua do Mercado, 12', 'Resende', 'SP',  
 '08737-363', 'Brasil', '(14) 555-8122', '')  
,('Cactus Comidas para Ilevar', 'Patricio Simpson', 'Cerrito 333', 'Buenos Aires', NULL,   
 '1010', 'Argentina', '(1) 135-5555', '(1) 135-4892')  
,('Ernst Handel', 'Roland Mendel', 'Kirchgasse 6', 'Graz', NULL,  
 '8010', 'Austria', '7675-3425', '7675-3426')  
,('Maison Dewey', 'Catherine Dewey', 'Rue Joseph-Bens 532', 'Bruxelles', NULL,  
 'B-1180', 'Belgium', '(02) 201 24 67', '(02) 201 24 68');  
GO
```  
  
### <a name="c-using-uniqueidentifier-and-variable-assignment"></a>C. uniqueidentifier と変数代入を使用する  
 次の例では、`@myid` という名前の変数を **uniqueidentifier** データ型の変数として宣言します。 この変数に、`SET` ステートメントを使用して値を代入します。  
  
```sql  
DECLARE @myid uniqueidentifier ;  
SET @myid = 'A972C577-DFB0-064E-1189-0154C99310DAAC12';  
SELECT @myid;  
GO  
```  
  
## <a name="see-also"></a>関連項目  
 [NEWSEQUENTIALID &#40;Transact-SQL&#41;](../../t-sql/functions/newsequentialid-transact-sql.md)   
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)   
 [一意識別子 &#40;Transact-SQL&#41;](../../t-sql/data-types/uniqueidentifier-transact-sql.md)   
 [シーケンス番号](../../relational-databases/sequence-numbers/sequence-numbers.md)  
  
  
