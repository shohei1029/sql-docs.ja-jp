---
description: DROP PROCEDURE (Transact-SQL)
title: DROP PROCEDURE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DROP PROCEDURE
- DROP_PROCEDURE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- removing stored procedures
- dropping procedure groups
- deleting stored procedures
- deleting procedure groups
- DROP PROCEDURE statement
- dropping stored procedures
- stored procedures [SQL Server], removing
- removing procedure groups
ms.assetid: 1c2d7235-7b9b-4336-8f17-429e7d82c2c3
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4d35c02396103b030d91c3e8ab0b4152b48f9ae8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750602"
---
# <a name="drop-procedure-transact-sql"></a>DROP PROCEDURE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  1 つ以上のストアド プロシージャまたはプロシージャ グループを [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] の現在のデータベースから削除します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
DROP { PROC | PROCEDURE } [ IF EXISTS ] { [ schema_name. ] procedure } [ ,...n ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
DROP { PROC | PROCEDURE } { [ schema_name. ] procedure_name }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *IF EXISTS*  
 **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から [現在のバージョン](/troubleshoot/sql/general/determine-version-edition-update-level)まで)。  
  
 条件付きでは既に存在する場合にのみ、プロシージャを削除します。  
  
 *schema_name*  
 プロシージャが属するスキーマの名前を指定します。 サーバー名またはデータベース名は指定できません。  
  
 *procedure*  
 削除するストアド プロシージャまたはストアド プロシージャ グループの名前です。 番号の付いたプロシージャ グループ内の個々のプロシージャは削除できません。プロシージャ グループ全体が削除されます。  
  
## <a name="best-practices"></a>ベスト プラクティス  
 ストアド プロシージャを削除する前に、依存オブジェクトを確認し、それらのオブジェクトを適切に修正します。 ストアド プロシージャを削除すると、これらのオブジェクトが更新されていない場合、依存オブジェクトとスクリプトがエラーになることがあります。 詳細については、「[ストアド プロシージャの依存関係の表示](../../relational-databases/stored-procedures/view-the-dependencies-of-a-stored-procedure.md)」を参照してください  
  
## <a name="metadata"></a>Metadata  
 既存のプロシージャの一覧を表示するには、**sys.objects** カタログ ビューに対してクエリを実行します。 プロシージャの定義を表示するには、**sys.sql_modules** カタログ ビューに対してクエリを実行します。  
  
## <a name="security"></a>セキュリティ  
  
### <a name="permissions"></a>アクセス許可  
 プロシージャの **CONTROL** 権限か、プロシージャが属しているスキーマに対する **ALTER** 権限、または **db_ddladmin** 固定サーバー ロールのメンバーシップが必要です。  
  
## <a name="examples"></a>例  
 次の例では、現在のデータベースから `dbo.uspMyProc` ストアド プロシージャを削除します。  
  
```sql  
DROP PROCEDURE dbo.uspMyProc;  
GO  
```  
  
 次の例では、現在のデータベースからいくつかのストアド プロシージャを削除します。  
  
```sql  
DROP PROCEDURE dbo.uspGetSalesbyMonth, dbo.uspUpdateSalesQuotes, dbo.uspGetSalesByYear;  
```  
  
 次の例では、削除、 `dbo.uspMyProc` ストアド プロシージャが存在するが、プロシージャが存在しない場合のエラーは発生しません。 この構文はで新しい [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]です。  
  
```sql  
DROP PROCEDURE IF EXISTS dbo.uspMyProc;  
GO  
```  
  
  
## <a name="see-also"></a>参照  
 [ALTER PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-procedure-transact-sql.md)   
 [CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)   
 [ストアド プロシージャの削除](../../relational-databases/stored-procedures/delete-a-stored-procedure.md)  
