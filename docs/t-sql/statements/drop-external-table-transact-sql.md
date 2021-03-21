---
description: DROP EXTERNAL TABLE (Transact-SQL)
title: DROP EXTERNAL TABLE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
ms.assetid: 02a6a236-0756-4570-abfa-6f677a7df042
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 26b9a14f07b3e3ebaf7d08484c0dba21e8e2eae4
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752872"
---
# <a name="drop-external-table-transact-sql"></a>DROP EXTERNAL TABLE (Transact-SQL)
[!INCLUDE [sqlserver2016-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdbmi-asa-pdw.md)]

  PolyBase の外部テーブルをデータベースから削除しますが、外部データは削除しません。  
  
 ![記事リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "記事リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DROP EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
[;]  
```  
  

## <a name="arguments"></a>引数  
 `[ database_name . [schema_name] . | schema_name . ] table_name`  
 削除する外部テーブルの 1 つか 3 部構成の名前。 テーブル名には、オプションで、スキーマまたはデータベースとスキーマを含めることができます。  
  
## <a name="permissions"></a>権限  
  
-   テーブルが属するスキーマに対する **ALTER** 権限が必要です。  
  
## <a name="general-remarks"></a>全般的な解説  
 外部テーブルを削除すると、テーブルに関連するすべてのメタデータが削除されます。 外部データは削除されません。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-basic-syntax"></a>A. 基本的な構文を使用します  
  
```sql  
DROP EXTERNAL TABLE SalesPerson;  
DROP EXTERNAL TABLE dbo.SalesPerson;  
DROP EXTERNAL TABLE EasternDivision.dbo.SalesPerson;  
```  
  
### <a name="b-dropping-an-external-table-from-the-current-database"></a>B. 現在のデータベースから、外部テーブルを削除します  
 次の例では、現在のデータベースから、`ProductVendor1` テーブル、そのデータ、インデックス、およびすべての依存ビューを削除します。  
  
```sql  
DROP EXTERNAL TABLE ProductVendor1;  
```  
  
### <a name="c-dropping-a-table-from-another-database"></a>C. 別のデータベースからテーブルを削除します  
 次の例では、`EasternDivision` データベースにある `SalesPerson` テーブルを削除します。  
  
```sql  
DROP EXTERNAL TABLE EasternDivision.dbo.SalesPerson;  
```  
  
## <a name="see-also"></a>参照  
 [CREATE EXTERNAL TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-transact-sql.md)  
  
