---
title: 動的なデータ マスキング | Microsoft Docs
description: 特権のないユーザーに対して機密データをマスクすることでその公開を制限する動的データ マスキングについて説明します。 これにより、SQL Server におけるセキュリティが大幅に簡素化できます。
ms.date: 05/02/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
ms.assetid: a62f4ff9-2953-42ca-b7d8-1f8f527c4d66
author: VanMSFT
ms.author: vanto
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 03ef32905fd9fcd79c296279095e089691625a86
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751262"
---
# <a name="dynamic-data-masking"></a>動的なデータ マスキング
[!INCLUDE [SQL Server 2016 ASDB, ASDBMI, ASDW ](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

![動的データ マスク](../../relational-databases/security/media/dynamic-data-masking.png)

動的データ マスク (DDM) では、機密データをマスクすることにより、特権のないユーザーへの機密データの公開を制限します。 DDM を使用すると、アプリケーションのセキュリティの設計とコーディングを大幅に簡略化することができます。  

動的データ マスクでは、公開する機密データの量を指定することで、そのようなデータに対する未承認のアクセスを防ぎ、アプリケーション レイヤーへの影響が最小限に抑えられます。 DDM は、指定されたデータベース フィールドで、クエリの結果セットに含まれる機密データを隠蔽するように構成できます。 DDM によって、データベース内のデータが変更されることはありません。 動的データ マスクは、クエリの結果にマスク ルールが適用されるため、既存のアプリケーションで簡単に使用できます。 多くのアプリケーションは、既存のクエリを変更せずに、デリケートなデータをマスクすることができます。

* 中央のデータ マスク ポリシーは、データベースの機密フィールドに対して直接動作します。
* 機密データに対するアクセス権を持つ特権のあるユーザーまたはロールを指定します。
* DDM には、フル マスク関数と部分マスク関数、および数値データ用のランダム マスクがあります。
* 単純な [!INCLUDE[tsql_md](../../includes/tsql-md.md)] コマンドで、マスクを定義し、管理します。

動的データ マスクの目的は、アクセスすべきではないユーザーがデータを閲覧することを防ぎ、デリケートなデータの公開を制限することにあります。 動的データ マスクは、ユーザーが直接データベースに接続し、徹底的なクエリを実行して、デリケートなデータの漏えいを防ぐことを目的としてはいません。 動的データ マスクは、その他の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セキュリティ機能 (監査、暗号化、行レベルのセキュリティなど) を補完します。データベース内のデリケートなデータの保護をより強化するために、セキュリティ機能と連携して動的データ マスクを使用することをお勧めします。  
  
動的データ マスクは [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] と [!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)]で使用できます。 [!INCLUDE[tsql](../../includes/tsql-md.md)] をコマンドを使用して構成します。 Azure portal で動的データ マスクを構成する方法の詳細については、[SQL Database 動的データ マスクの使用 (Azure ポータル)](/azure/azure-sql/database/dynamic-data-masking-overview)に関するページを参照してください。  
  
## <a name="defining-a-dynamic-data-mask"></a>動的データ マスクを定義する
 マスク ルールは、列のデータを難読化するために、テーブル内の列で定義することがあります。 4 種類のマスクを利用できます。  
  
|機能|説明|例|  
|--------------|-----------------|--------------|  
|Default|指定のフィールドのデータ型に応じたフル マスク。<br /><br /> 文字列データ型 (**char**、 **nchar**、  **varchar**、 **nvarchar**、 **text**、 **ntext**) のフィールドのサイズが 4 文字未満の場合は、XXXX またはそれ未満の数の X を使用します。  <br /><br /> 数値データ型 (**bigint**、 **bit**、 **decimal**、 **int**、 **money**、 **numeric**、 **smallint**、 **smallmoney**、 **tinyint**、 **float**、 **real**) の場合は値 0 を使用します。<br /><br /> 日付/時刻のデータ型 (**date**、 **datetime2**、 **datetime**、 **datetimeoffset**、 **smalldatetime**、 **time**) の場合は、01.01.1900 00:00:00.0000000 を使用します。<br /><br />バイナリ データ型 (**binary**、 **varbinary**、 **image**) の場合は、ASCII 値 0 のシングル バイトを使用します。|列定義の構文例: `Phone# varchar(12) MASKED WITH (FUNCTION = 'default()') NULL`<br /><br /> ALTER 構文例: `ALTER COLUMN Gender ADD MASKED WITH (FUNCTION = 'default()')`|  
|Email|メール アドレスの最初の 1 文字と定数サフィックスの ".com" をメール アドレスのフォームで公開するマスク方法。 `aXXX@XXXX.com`.|定義の構文例: `Email varchar(100) MASKED WITH (FUNCTION = 'email()') NULL`<br /><br /> ALTER 構文例: `ALTER COLUMN Email ADD MASKED WITH (FUNCTION = 'email()')`|  
|ランダム|ランダム マスク関数は任意の数字型に使用でき、指定した範囲内で生成したランダムな値でオリジナルの値をマスクします。|定義の構文例: `Account_Number bigint MASKED WITH (FUNCTION = 'random([start range], [end range])')`<br /><br /> ALTER 構文例: `ALTER COLUMN [Month] ADD MASKED WITH (FUNCTION = 'random(1, 12)')`|  
|カスタム文字列|間にカスタム埋め込み文字列を追加し、最初と最後の文字を公開するマスク方法。 `prefix,[padding],suffix`<br /><br /> 注:元の文字列が全体をマスクするには短すぎる場合、プレフィックスまたはサフィックスの一部は公開されません。|定義の構文例: `FirstName varchar(100) MASKED WITH (FUNCTION = 'partial(prefix,[padding],suffix)') NULL`<br /><br /> ALTER 構文例: `ALTER COLUMN [Phone Number] ADD MASKED WITH (FUNCTION = 'partial(1,"XXXXXXX",0)')`<br /><br /> その他の例:<br /><br /> `ALTER COLUMN [Phone Number] ADD MASKED WITH (FUNCTION = 'partial(5,"XXXXXXX",0)')`|  
  
## <a name="permissions"></a>アクセス許可  
 動的データ マスクでテーブルを作成するのに特別なアクセス許可は要りません。スキーマ アクセス許可に対する **CREATE TABLE** と **ALTER** のみ必要です。  
  
 列のマスクを追加、置換、削除するには、 **ALTER ANY MASK** アクセス許可と、テーブルに対する **ALTER** アクセス許可が必要です。 セキュリティの責任者には、 **ALTER ANY MASK** の付与が適切です。  
  
 テーブルに対して **SELECT** アクセス許可を持つユーザーは、テーブル データを閲覧できます。 マスク済みとして定義されている列には、マスクされたデータが表示されます。 **UNMASK** アクセス許可を付与されたユーザーは、マスクが定義された列から、マスクを解除したデータを取得できます。  
  
 データベースに対する **CONTROL** アクセス許可には、 **ALTER ANY MASK** と **UNMASK** の両方のアクセス許可が含まれます。  
  
## <a name="best-practices-and-common-use-cases"></a>ベスト プラクティスと一般的なユース ケース  
  
-   列にマスクを作成しても、その列に対する更新を妨げることはありません。 マスクされた列にクエリを実行すると、ユーザーはマスクされたデータを受け取りますが、書き込みアクセス許可があるユーザーは、データを更新できます。 更新する許可を制限するために、適切なアクセス制御ポリシーを使用する必要があります。  
  
-   `SELECT INTO` または `INSERT INTO` を使用して、マスクされた列を別のテーブルにコピーすると、対象のテーブルにマスクされたデータが表示されます。  
  
-   動的データ マスクは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インポートとエクスポートを実行すると適用されます。 マスクされた列を含むデータベースは、マスクされたデータを含むエクスポートされたデータ ファイルを生成し (**UNMASK** 特権がないユーザーがエクスポートしたことを前提とします)、インポートされたデータベースは、マスクされたデータを静的に格納します。  
  
## <a name="querying-for-masked-columns"></a>マスクされた列に対してクエリを実行する  
 **sys.masked_columns** ビューを使用して、マスク関数が適用されたテーブルの列に対してクエリを実行します。 このビューが継承、 **sys.columns** ビューです。 **sys.columns** ビューのすべての列と、 **is_masked** 列および **masking_function** 列を返して、マスクされた列かどうかを示し、マスクされた列の場合は、どのようなマスキング関数が定義されているかを示します。 これは、列があるマスキング関数が適用されるは表示のみを表示します。  
  
```sql 
SELECT c.name, tbl.name as table_name, c.is_masked, c.masking_function  
FROM sys.masked_columns AS c  
JOIN sys.tables AS tbl   
    ON c.[object_id] = tbl.[object_id]  
WHERE is_masked = 1;  
```  
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項  
 次の列の型には、マスク ルールを定義することはできません。  
  
-   暗号化された列 (常に暗号化されます)  
  
-   FILESTREAM  
  
-   COLUMN_SET、または列セットの一部であるスパース列  
  
-   マスクは計算列に構築できません。ただし、計算列が MASK を所有する列に依存する場合は、計算列がマスクされたデータを返します。  
  
-   データ マスクを持つ列を FULLTEXT インデックスのキーにすることはできません。  
  
 **UNMASK** アクセス許可のないユーザーの場合、非推奨とされている **READTEXT**、 **UPDATETEXT**、および **WRITETEXT** ステートメントは、動的データ マスク用に構成された列で適切に動作しません。 
 
 動的データ マスクの追加は基になっているテーブルでのスキーマ変更として実装されるため、依存関係を持つ列では実行できません。 この制限を回避するには、最初に依存関係を削除してから、動的データ マスクを追加した後、依存関係を再作成します。 たとえば、依存関係がその列に依存するインデックスによるものである場合は、インデックスを削除し、マスクを追加してから、依存するインデックスを再作成します。
 

## <a name="security-note-bypassing-masking-using-inference-or-brute-force-techniques"></a>セキュリティに関する注意:推論またはブルートフォース手法を使用してマスクをバイパスする

動的データ マスクは、アプリケーションに使用される事前定義されたクエリ セットのデータ公開を制限することで、アプリケーション開発を単純化するために設計されています。 動的データ マスクは、実稼働データベースに直接アクセスするときに機密データが誤って公開されることを防ぐためにも有効ですが、アドホック クエリ アクセス許可を持つ特権のないユーザーが、実際のデータに対するアクセス権を得る手法を適用する可能性にも注意する必要があります。 このようなアドホック アクセス権を付与する必要がある場合、すべてのデータベース操作を監視し、このシナリオを軽減するために監査を使用することをお勧めします。
 
たとえば、データベースに対してアドホック クエリを実行する十分な特権を持つデータベース プリンシパルがいて、基になるデータを ’推測’ し、最終的には実際の値を推測しようと試みているとします。 管理者は `[Employee].[Salary]` 列に対してマスクを定義しましたが、このユーザーはデータベースに直接接続し、値の推測を開始し、最終的には従業員セットの `[Salary]` 値を推測します。
 

```sql
SELECT ID, Name, Salary FROM Employees
WHERE Salary > 99999 and Salary < 100001;
```

>    |  Id | 名前| Salary |   
>    | ----- | ---------- | ------ | 
>    |  62543 | Jane Doe | 0 | 
>    |  91245 | John Smith | 0 |  

この例は、データベースに対してアドホック クエリを実行するユーザーから、機密データをセキュリティで完全に保護する唯一の手段として動的データ マスクを使用すべきではないことを示しています。 動的データ マスクは偶発的な機密データの公開を防ぐ場合に適していますが、基になるデータを推測する悪意のある攻撃を防ぐためのものではありません。
 
データベースのアクセス許可を適切に管理し、必要最小限のアクセス許可の原則に常に従うことが重要です。 また、データベースに対して実行されるすべてのアクティビティを追跡するために、必ず監査を有効にしてください。

  
## <a name="examples"></a>例  
  
### <a name="creating-a-dynamic-data-mask"></a>動的データ マスクを作成する  
 次の例では、3 種類の動的データ マスクでテーブルを作成します。 この例ではテーブルを作成し、結果を表示するように選びます。  
  
```sql

-- schema to contain user tables
CREATE SCHEMA Data
GO

-- table with masked columns
CREATE TABLE Data.Membership(
    MemberID        int IDENTITY(1,1) NOT NULL PRIMARY KEY CLUSTERED,
    FirstName       varchar(100) MASKED WITH (FUNCTION = 'partial(1, "xxxxx", 1)') NULL,
    LastName        varchar(100) NOT NULL,
    Phone           varchar(12) MASKED WITH (FUNCTION = 'default()') NULL,
    Email           varchar(100) MASKED WITH (FUNCTION = 'email()') NOT NULL,
    DiscountCode    smallint MASKED WITH (FUNCTION = 'random(1, 100)') NULL
    )

-- inserting sample data
INSERT INTO Data.Membership (FirstName, LastName, Phone, Email, DiscountCode)
VALUES   
('Roberto', 'Tamburello', '555.123.4567', 'RTamburello@contoso.com', 10),  
('Janice', 'Galvin', '555.123.4568', 'JGalvin@contoso.com.co', 5),  
('Shakti', 'Menon', '555.123.4570', 'SMenon@contoso.net', 50),  
('Zheng', 'Mu', '555.123.4569', 'ZMu@contoso.net', 40);  

```  
  
 新しいユーザーが作成され、テーブルが属しているスキーマに対する **SELECT** アクセス許可が付与されます。 `MaskingTestUser` ビューのマスクされたデータとして、クエリが実行されます。  
  
```sql 
CREATE USER MaskingTestUser WITHOUT LOGIN;  

GRANT SELECT ON SCHEMA::Data TO MaskingTestUser;  
  
  -- impersonate for testing:
EXECUTE AS USER = 'MaskingTestUser';  

SELECT * FROM Data.Membership;  

REVERT;  
```  
  
 次のようにデータが変更された結果によって、マスクが示されます。  
  
 `1    Roberto     Tamburello    555.123.4567    RTamburello@contoso.com    10`  
  
 変更前:  
  
 `1    Rxxxxxo    Tamburello    xxxx            RXXX@XXXX.com            91`
 
 DiscountCode の番号は、すべてのクエリ結果に対してランダムになります
  
### <a name="adding-or-editing-a-mask-on-an-existing-column"></a>既存の列のマスクを追加または編集する  
 **ALTER TABLE** ステートメントを使用して、テーブル内の既存の列にマスクを追加したり、その列のマスクを編集したりします。  
次の例では、`LastName` 列にマスク関数を追加します。  
  
```sql  
ALTER TABLE Data.Membership  
ALTER COLUMN LastName ADD MASKED WITH (FUNCTION = 'partial(2,"xxxx",0)');  
```  
  
 次の例では、 `LastName` 列のマスク関数を変更します。  

```sql  
ALTER TABLE Data.Membership  
ALTER COLUMN LastName varchar(100) MASKED WITH (FUNCTION = 'default()');  
```  
  
### <a name="granting-permissions-to-view-unmasked-data"></a>アクセス許可を付与して、マスクが解除されたデータを表示する  
 **UNMASK** アクセス許可が付与されると、 `MaskingTestUser` は、マスクが解除されたデータを閲覧できるようになります。  
  
```sql
GRANT UNMASK TO MaskingTestUser;  

EXECUTE AS USER = 'MaskingTestUser';  

SELECT * FROM Data.Membership;  

REVERT;    
  
-- Removing the UNMASK permission  
REVOKE UNMASK TO MaskingTestUser;  
```  
  
### <a name="dropping-a-dynamic-data-mask"></a>動的データ マスクをドロップする  
 次のステートメントは、先ほどの例で作成された `LastName` 列のマスクをドロップします。  
  
```sql  
ALTER TABLE Data.Membership   
ALTER COLUMN LastName DROP MASKED;  
```  
  
## <a name="see-also"></a>参照  
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [column_definition (Transact-SQL)](../../t-sql/statements/alter-table-column-definition-transact-sql.md)   
 [sys.masked_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-masked-columns-transact-sql.md)   
 [SQL Database 動的データ マスクの使用 (Azure portal)](/azure/azure-sql/database/dynamic-data-masking-overview)
