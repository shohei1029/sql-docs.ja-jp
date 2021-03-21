---
description: SQL_VARIANT_PROPERTY (Transact-SQL)
title: SQL_VARIANT_PROPERTY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/25/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SQL_VARIANT_PROPERTY_TSQL
- SQL_VARIANT_PROPERTY
dev_langs:
- TSQL
helpviewer_keywords:
- SQL_VARIANT_PROPERTY function
- sql_variant data type
ms.assetid: 50e5c1d9-4e95-4ed0-9c92-435c872a399e
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 24134065588787a367b7a975c36b3be5631483cd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750512"
---
# <a name="sql_variant_property-transact-sql"></a>SQL_VARIANT_PROPERTY (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **sql_variant** 値の基本データ型およびその他の情報を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
SQL_VARIANT_PROPERTY ( expression , property )  
```  
  
[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *式 (expression)*  
 **sql_variant** 型の式です。  
  
 *property*  
 情報を提供する **sql_variant** プロパティの名前です。 *property* は **varchar(** 128 **)** であり、次のいずれかの値を指定できます。  
  
|値|説明|返される sql_variant の基本データ型|  
|-----------|-----------------|----------------------------------------|  
|**BaseType**|以下のような [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型です。<br /><br /> **bigint**<br /><br /> **[バイナリ]**<br /><br /> **bit**<br /><br /> **char**<br /><br /> **date**<br /><br /> **datetime**<br /><br /> **datetime2**<br /><br /> **datetimeoffset**<br /><br /> **decimal**<br /><br /> **float**<br /><br /> **int**<br /><br /> **money**<br /><br /> **nchar**<br /><br /> **numeric**<br /><br /> **nvarchar**<br /><br /> **real**<br /><br /> **smalldatetime**<br /><br /> **smallint**<br /><br /> **smallmoney**<br /><br /> **time**<br /><br /> **tinyint**<br /><br /> **uniqueidentifier**<br /><br /> **varbinary**<br /><br /> **varchar**|**sysname**<br /><br /> NULL = 入力は無効です。|  
|**[精度]**|数値基本データ型の桁数です。<br /><br /> **date** = 10<br /><br /> **datetime** = 23<br /><br /> **datetime2** = 27<br /><br /> **datetime2** (s) = 19 when s = 0, else s + 20<br /><br /> **datetimeoffset** = 34<br /><br /> **datetimeoffset** (s) = 26 when s = 0, else s + 27<br /><br /> **smalldatetime** = 16<br /><br /> **time** = 16<br /><br /> **time** (s) = 8 when s = 0, else s + 9<br /><br /> **float** = 53<br /><br /> **real** = 24<br /><br /> **decimal** および **numeric** = 18<br /><br /> **decimal** (p,s) および **numeric** (p,s) = p<br /><br /> **money** = 19<br /><br /> **smallmoney** = 10<br /><br /> **bigint** = 19<br /><br /> **int** = 10<br /><br /> **smallint** = 5<br /><br /> **tinyint** = 3<br /><br /> **bit** = 1<br /><br /> その他のすべてのデータ型 = 0|**int**<br /><br /> NULL = 入力は無効です。|  
|**スケール**|数値基本データ型の小数点の右側の桁数です。<br /><br /> **decimal** および **numeric** = 0<br /><br /> **decimal** (p,s) および **numeric** (p,s) = s<br /><br /> **money** および **smallmoney** = 4<br /><br /> **datetime** = 3<br /><br /> **datetime2** = 7<br /><br /> **datetime2** (s) = s (0 - 7)<br /><br /> **datetimeoffset** = 7<br /><br /> **datetimeoffset** (s) = s (0 - 7)<br /><br /> **time** = 7<br /><br /> **time** (s) = s (0 - 7)<br /><br /> その他のすべての型 = 0|**int**<br /><br /> NULL = 入力は無効です。|  
|**TotalBytes**|メタデータと値のデータの両方を保持するのに必要なバイト数です。 この情報は、**sql_variant** 列内のデータの最大サイズをチェックする上で役に立ちます。 値が 900 を超える場合は、インデックスを作成できません。|**int**<br /><br /> NULL = 入力は無効です。|  
|**Collation**|特定の **sql_variant** 値の照合順序を表します。|**sysname**<br /><br /> NULL = 入力は無効です。|  
|**MaxLength**|データ型の最大データ長 (バイト単位) です。 たとえば、**nvarchar(** 50 **)** の **MaxLength** は 100、**int** の **MaxLength** は 4 です。|**int**<br /><br /> NULL = 入力は無効です。|  
  
## <a name="return-types"></a>戻り値の型  
 **sql_variant**  
  
## <a name="examples"></a>例  
### <a name="a-using-a-sql_variant-in-a-table"></a>A. テーブルで sql_variant を使用する  
 次の例では、`colB` =`1689` の `colA` 値 `46279.1` に関する `SQL_VARIANT_PROPERTY` 情報を取得しています。`tableA` には `sql_variant` 型の `colA` と、`colB` が含まれているものとします。  
  
```sql    
CREATE   TABLE tableA(colA sql_variant, colB int)  
INSERT INTO tableA values ( cast (46279.1 as decimal(8,2)), 1689)  
SELECT   SQL_VARIANT_PROPERTY(colA,'BaseType') AS 'Base Type',  
         SQL_VARIANT_PROPERTY(colA,'Precision') AS 'Precision',  
         SQL_VARIANT_PROPERTY(colA,'Scale') AS 'Scale'  
FROM      tableA  
WHERE      colB = 1689  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)] これら 3 つの値の各ことに注意してください、 **sql_variant** です。  
  
```  
Base Type    Precision    Scale  
---------    ---------    -----  
decimal      8           2  
  
(1 row(s) affected)  
```  
  
### <a name="b-using-a-sql_variant-as-a-variable"></a>B. 変数として sql_variant を使用する   
 次の例では、変数 @v1 に関する `SQL_VARIANT_PROPERTY` の情報を取得します。  
  
```sql    
DECLARE @v1 sql_variant;  
SET @v1 = 'ABC';  
SELECT @v1;  
SELECT SQL_VARIANT_PROPERTY(@v1, 'BaseType');  
SELECT SQL_VARIANT_PROPERTY(@v1, 'MaxLength');  
```  
  
## <a name="see-also"></a>参照  
 [sql_variant &#40;Transact-SQL&#41;](../../t-sql/data-types/sql-variant-transact-sql.md)  
  
  

