---
description: SET ANSI_NULL_DFLT_ON (Transact-SQL)
title: SET ANSI_NULL_DFLT_ON (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/04/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ANSI_NULL_DFLT_ON
- ANSI_NULL_DFLT_ON_TSQL
- SET ANSI_NULL_DFLT_ON
- SET_ANSI_NULL_DFLT_ON_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- SET ANSI_NULL_DFLT_ON statement
- ANSI_NULL_DFLT_ON option
- default nullability
- null values [SQL Server], overriding
- overriding default nullability
ms.assetid: 8c925924-a466-4c8b-aeb2-7e0d341f32db
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 484ce155e200c51f3607157579078c5c7ec1b94a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755352"
---
# <a name="set-ansi_null_dflt_on-transact-sql"></a>SET ANSI_NULL_DFLT_ON (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  セッションの動作を変更して、データベースの **ANSI null default** オプションが **false** である場合の、新しい列の NULL 値に関する既定の設定をオーバーライドします。 **ANSI null default** の値の設定について詳しくは、「[ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)」をご覧ください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for SQL Server and Azure SQL Database

SET ANSI_NULL_DFLT_ON {ON | OFF}
```

```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse

SET ANSI_NULL_DFLT_ON ON
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>解説
 この設定は、新しい列に対する NULL 値の許容にのみ影響を与えます。また、この影響が生じるのは、CREATE TABLE および ALTER TABLE ステートメントで、列に対して NULL 値の許容が設定されていない場合です。 SET ANSI_NULL_DFLT_ON が ON の場合、ALTER TABLE ステートメントと CREATE TABLE ステートメントで作成される新しい列は、列の NULL 値の許容状態を明示的に指定しない場合に NULL 値が許可されます。 NULL または NOT NULL を明示的に指定して作成した列に対して、SET ANSI_NULL_DFLT_ON は影響しません。  
  
 ANSI_NULL_DFLT_ON と ANSI_NULL_DFLT_OFF の両方を同時に ON に設定することはできません。 一方のオプションが ON に設定されている場合、他方のオプションは OFF に設定されます。 つまり、SET ANSI_NULL_DFLT_OFF と ANSI_NULL_DFLT_ON のいずれか一方を ON に設定するか、または両方を OFF に設定することだけが可能です。 いずれかのオプションが ON の場合、その設定 (SET ANSI_NULL_DFLT_OFF または SET ANSI_NULL_DFLT_ON) が有効になります。 両方のオプションを OFF に設定した場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) カタログ ビューにある **is_ansi_null_default_on** 列の値が使用されます。  
  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] スクリプトを、NULL 値の許容に関する設定が異なっているデータベースで使用する場合、このスクリプトによる動作の信頼性を高めるには、CREATE TABLE ステートメントと ALTER TABLE ステートメントで NULL または NOT NULL を指定することをお勧めします。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーおよび [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB Provider for [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、接続時に自動的に ANSI_NULL_DFLT_ON が ON に設定されます。 DB-Library アプリケーションからの接続に対しては、既定では SET ANSI_NULL_DFLT_ON は OFF に設定されています。  
  
 SET ANSI_DEFAULTS が ON の場合には、SET ANSI_NULL_DFLT_ON は有効になります。  
  
 SET ANSI_NULL_DFLT_ON は、解析時ではなく実行時に設定されます。  
  
 SELECT INTO ステートメントを使用してテーブルを作成した場合は、SET ANSI_NULL_DFLT_ON の設定は適用されません。  
  
 この設定の現在の設定を表示するには、次のクエリを実行します。  
  
```sql  
DECLARE @ANSI_NULL_DFLT_ON VARCHAR(3) = 'OFF';  
IF ( (1024 & @@OPTIONS) = 1024 ) SET @ANSI_NULL_DFLT_ON = 'ON';  
SELECT @ANSI_NULL_DFLT_ON AS ANSI_NULL_DFLT_ON;  
  
```  
  
## <a name="permissions"></a>アクセス許可  
 ロール **public** のメンバーシップが必要です。  
  
## <a name="examples"></a>例  
 次の例では、**ANSI null default** データベース オプションに 2 種類の設定を使用した場合の `SET ANSI_NULL_DFLT_ON` の効果を示します。  
  
```sql  
USE AdventureWorks2012;  
GO  
  
-- The code from this point on demonstrates that SET ANSI_NULL_DFLT_ON  
-- has an effect when the 'ANSI null default' for the database is false.  
-- Set the 'ANSI null default' database option to false by executing  
-- ALTER DATABASE.  
ALTER DATABASE AdventureWorks2012 SET ANSI_NULL_DEFAULT OFF;  
GO  
-- Create table t1.  
CREATE TABLE t1 (a TINYINT) ;  
GO   
-- NULL INSERT should fail.  
INSERT INTO t1 (a) VALUES (NULL);  
GO  
  
-- SET ANSI_NULL_DFLT_ON to ON and create table t2.  
SET ANSI_NULL_DFLT_ON ON;  
GO  
CREATE TABLE t2 (a TINYINT);  
GO   
-- NULL insert should succeed.  
INSERT INTO t2 (a) VALUES (NULL);  
GO  
  
-- SET ANSI_NULL_DFLT_ON to OFF and create table t3.  
SET ANSI_NULL_DFLT_ON OFF;  
GO  
CREATE TABLE t3 (a TINYINT);  
GO  
-- NULL insert should fail.  
INSERT INTO t3 (a) VALUES (NULL);  
GO  
  
-- The code from this point on demonstrates that SET ANSI_NULL_DFLT_ON   
-- has no effect when the 'ANSI null default' for the database is true.  
-- Set the 'ANSI null default' database option to true.  
ALTER DATABASE AdventureWorks2012 SET ANSI_NULL_DEFAULT ON  
GO  
  
-- Create table t4.  
CREATE TABLE t4 (a TINYINT);  
GO   
-- NULL INSERT should succeed.  
INSERT INTO t4 (a) VALUES (NULL);  
GO  
  
-- SET ANSI_NULL_DFLT_ON to ON and create table t5.  
SET ANSI_NULL_DFLT_ON ON;  
GO  
CREATE TABLE t5 (a TINYINT);  
GO   
-- NULL INSERT should succeed.  
INSERT INTO t5 (a) VALUES (NULL);  
GO  
  
-- SET ANSI_NULL_DFLT_ON to OFF and create table t6.  
SET ANSI_NULL_DFLT_ON OFF;  
GO  
CREATE TABLE t6 (a TINYINT);  
GO   
-- NULL INSERT should succeed.  
INSERT INTO t6 (a) VALUES (NULL);  
GO  
  
-- Set the 'ANSI null default' database option to false.  
ALTER DATABASE AdventureWorks2012 SET ANSI_NULL_DEFAULT ON;  
GO  
  
-- Drop tables t1 through t6.  
DROP TABLE t1,t2,t3,t4,t5,t6;  
```  
  
## <a name="see-also"></a>参照  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET ANSI_DEFAULTS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-defaults-transact-sql.md)   
 [SET ANSI_NULL_DFLT_OFF &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-null-dflt-off-transact-sql.md)  
  
  
