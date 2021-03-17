---
description: ALTER VIEW (Transact-SQL)
title: ALTER VIEW (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/25/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ALTER_VIEW_TSQL
- ALTER VIEW
dev_langs:
- TSQL
helpviewer_keywords:
- indexed views [SQL Server], modifying
- views [SQL Server], modifying
- modifying views
- ALTER VIEW statement
ms.assetid: 03eba220-13e2-49e3-bd9d-ea9df84dc28c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ece59fa96d7fc3f81df663456f2ba276f96708d0
ms.sourcegitcommit: ecf074e374426c708073c7da88313d4915279fb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103575345"
---
# <a name="alter-view-transact-sql"></a>ALTER VIEW (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  以前に作成されたビューを変更します。 これにはインデックス付きビューが含まれます。 ALTER VIEW は、従属するストアド プロシージャやトリガーに影響を与えず、権限を変更することもありません。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
ALTER VIEW [ schema_name . ] view_name [ ( column [ ,...n ] ) ]   
[ WITH <view_attribute> [ ,...n ] ]   
AS select_statement   
[ WITH CHECK OPTION ] [ ; ]  
  
<view_attribute> ::=   
{   
    [ ENCRYPTION ]  
    [ SCHEMABINDING ]  
    [ VIEW_METADATA ]       
}   
```

```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
ALTER VIEW [ schema_name . ] view_name [  ( column_name [ ,...n ] ) ]   
AS <select_statement>   
[;]  
``` 
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *schema_name*  
 ビューが所属するスキーマの名前を指定します。  
  
 *view_name*  
 変更するビューを指定します。  
  
 *column*  
 指定したビューに含まれている列の名前を 1 つ以上指定します。列を複数指定するときは、コンマで区切ります。  
  
> [!IMPORTANT]  
>  列の権限は、ALTER VIEW の実行の前後で列の名前が変わらないときだけ維持されます。  
  
> [!NOTE]  
>  ビューの列では、基底となるデータのソースにかかわらず、列名に対する権限は、CREATE VIEW ステートメントまたは ALTER VIEW ステートメントを超えて適用されます。 たとえば、CREATE VIEW ステートメントで **SalesOrderID** 列に対して権限が与えられる場合、ALTER VIEW ステートメントは **SalesOrderID** 列の名前を **OrderRef** などに変更でき、その後も **SalesOrderID** を使用してビューに関連付けられた権限を保持します。  
  
 ENCRYPTION  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 ALTER VIEW ステートメントのテキストを含んでいる [sys.syscomments](../../relational-databases/system-compatibility-views/sys-syscomments-transact-sql.md) のエントリを暗号化します。 WITH ENCRYPTION を指定すると、そのビューを SQL Server レプリケーションの一部としてパブリッシュできなくなります。  
  
 SCHEMABINDING  
 基になるテーブルのスキーマにビューをバインドします。 SCHEMABINDING を指定すると、ビュー定義に影響する変更をベース テーブルに加えられなくなります。 まずビュー定義を変更または削除して、変更するテーブルとの依存関係を解消する必要があります。 SCHEMABINDING を使用する場合は、_select\_statement_ に、参照されるテーブル、ビュー、またはユーザー定義関数の名前として、2 つの部分から構成される名前 (_schema_ **.** _object_) を指定する必要があります。 参照されるオブジェクトは、すべて同じデータベース内にあることが必要です。  
  
 SCHEMABINDING 句を指定して作成されたビューが削除または変更されて、スキーマ バインドがなくならない限り、そのビューに参加しているビューまたはテーブルは削除できません。 スキーマ バインドが残っている場合は、[!INCLUDE[ssDE](../../includes/ssde-md.md)]からエラーが返されます。 また、ビュー定義に影響を与える ALTER TABLE ステートメントを、スキーマ バインドを持つビューに参加しているテーブルに対して実行すると、ステートメントは失敗します。  
  
 VIEW_METADATA  
 ビューを参照するクエリ用にブラウズ モード メタデータが要求されている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスは、DB-Library、ODBC、および OLE DB API に対して、ベース テーブルではなくビューに関するメタデータ情報を返します。 ブラウズ モード メタデータとは、[!INCLUDE[ssDE](../../includes/ssde-md.md)]のインスタンスがクライアント側の DB-Library、ODBC、および OLE DB API に返す追加のメタデータです。 クライアント側 API ではこのメタデータによって、更新可能なクライアント側カーソルを実装できます。 ブラウズ モード メタデータには、結果セット内の列が属するベース テーブルの情報が含まれています。  
  
 VIEW_METADATA で作成したビューの場合、ブラウズ モード メタデータでは結果セット内のビューの列の説明で、ベース テーブル名ではなくビュー名が返されます。  
  
 WITH VIEW_METADATA を指定して作成されたビューに INSERT または UPDATE での INSTEAD OF トリガーが含まれている場合、そのビューでは、**timestamp** 列を除くすべての列を更新できます。 詳しくは、「[CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)」の「解説」セクションをご覧ください。  
  
 AS  
 ビューが行うアクションです。  
  
 *select_statement*  
 ビューを定義している SELECT ステートメントを指定します。  
  
 WITH CHECK OPTION  
 ビューに対して実行されるすべてのデータ変更ステートメントについて、*select_statement* 内で設定される条件に従うよう強制します。  
  
## <a name="remarks"></a>解説  
 ALTER VIEW について詳しくは、「[CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)」の「解説」をご覧ください。  
  
> [!NOTE]  
>  以前のビュー定義が WITH ENCRYPTION または CHECK OPTION を使用して作成されている場合、これらのオプションは、ALTER VIEW で指定される場合にのみ有効となります。  
  
 現在使用されているビューを ALTER VIEW を使用して変更する場合、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] はビューに対して排他スキーマ ロックを設定します。 ロックが許可され、ビューのアクティブ ユーザーがいなくなると、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] はプロシージャ キャッシュからビューのすべてのコピーを削除します。 ビューを参照している既存のプランはキャッシュに残りますが、呼び出されると再コンパイルされます。  
  
 ALTER VIEW は、インデックス付きビューに適用できますが、そのビューのすべてのインデックスを無条件で削除します。  
  
## <a name="permissions"></a>アクセス許可  
 ALTER VIEW を実行するには、少なくとも OBJECT に対する ALTER 権限が必要です。  
  
## <a name="examples"></a>例  
 次の例では、すべての従業員とその雇用日を含んだ `EmployeeHireDate` というビューを作成します。 ビューには権限が与えられますが、必要条件が変更されて、雇用日が特定の日付よりも古い従業員を選択することになりました。 そこで、`ALTER VIEW` を使用してビューを変更します。  
  
```sql 
USE AdventureWorks2012 ;  
GO  
CREATE VIEW HumanResources.EmployeeHireDate  
AS  
SELECT p.FirstName, p.LastName, e.HireDate  
FROM HumanResources.Employee AS e JOIN Person.Person AS  p  
ON e.BusinessEntityID = p.BusinessEntityID ;  
GO  
```  
  
 `2002` 年より前に雇用された従業員のみを含むようにビューを変更する必要があります。 ALTER VIEW を使用せずに、ビューを削除して再作成する場合は、以前に使用されていた GRANT ステートメント、およびこのビューに関係する権限を処理するその他すべてのステートメントを再入力する必要があります。  
  
```sql  
ALTER VIEW HumanResources.EmployeeHireDate  
AS  
SELECT p.FirstName, p.LastName, e.HireDate  
FROM HumanResources.Employee AS e JOIN Person.Person AS p  
ON e.BusinessEntityID = p.BusinessEntityID  
WHERE HireDate < CONVERT(DATETIME,'20020101',101) ;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)   
 [DROP VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/drop-view-transact-sql.md)   
 [ストアド プロシージャの作成](../../relational-databases/stored-procedures/create-a-stored-procedure.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [パブリケーション データベースでのスキーマの変更](../../relational-databases/replication/publish/make-schema-changes-on-publication-databases.md)  
  
  
