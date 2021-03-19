---
description: 'チュートリアル : 証明書を使用したストアド プロシージャへの署名'
title: 'チュートリアル : 証明書を使用したストアド プロシージャへの署名'
ms.custom: seo-dt-2019
ms.date: 08/23/2018
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: quickstart
helpviewer_keywords:
- signing stored procedures tutorial [SQL Server]
ms.assetid: a4b0f23b-bdc8-425f-b0b9-e0621894f47e
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 91bbdaacd4765639d056fe4ccf54da4c1e0dd371
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610942"
---
# <a name="tutorial-signing-stored-procedures-with-a-certificate"></a>チュートリアル : 証明書を使用したストアド プロシージャへの署名
[!INCLUDE [SQL Server Azure SQL Database SQL Managed Instance](../includes/applies-to-version/sql-asdb-asdbmi.md)]
このチュートリアルでは、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]で生成された証明書を使用してストアド プロシージャに署名する方法について説明します。  
  
> [!NOTE]  
> このチュートリアルのコードを実行するには、混合モードのセキュリティが構成されていることと、AdventureWorks2017 データベースがインストールされていることが条件となります。   
  
証明書を使用したストアド プロシージャへの署名は、ストアド プロシージャに権限を設定する一方、ユーザーにはこのような権限を明示的に与えないでおこうとする場合に便利です。 この動作は EXECUTE AS ステートメントを使用するなどの方法でも実現できますが、証明書を使用すると、トレースによってプロシージャの最初の呼び出し元を見つけることができ、 高いレベルの監査を行うことができます。特に、セキュリティやデータ定義言語 (DDL) の操作に対しては有効です。  
  
master データベースに証明書を作成し、サーバーレベルの権限を付与するか、任意のユーザー データベースに証明書を作成し、データベースレベルの権限を付与することができます。 このシナリオでは、ベース テーブルに対する権限を持たないユーザーが AdventureWorks2017 データベースにアクセスする必要があることを想定し、また、オブジェクト アクセスの記録を監査します。 ここでは所有権を継承する方法を使用するのではなく、サーバーおよびデータベース ユーザー アカウントを 1 つとデータベース ユーザー アカウントを 1 つ作成し、前者のアカウントにはベース オブジェクトに対する権限を与えず、後者にはテーブルとストアド プロシージャに対する権限を与えます。 ストアド プロシージャと 2 番目のデータベース ユーザー アカウントは両方とも、証明書によりセキュリティ保護されます。 2 番目のデータベース アカウントではすべてのオブジェクトにアクセスでき、ストアド プロシージャへのアクセス許可を最初のデータベース ユーザー アカウントに与えることができます。  
  
このシナリオでは、最初にデータベース証明書、ストアド プロシージャ、ユーザーを 1 つずつ作成し、次にプロセスをテストします。手順は次のとおりです。  
  
以下で、この例の各コード ブロックについて説明します。 完全なサンプル コードをコピーするには、このチュートリアルの最後の「 [完全なサンプル コード](#CompleteExample) 」を参照してください。  

## <a name="prerequisites"></a>前提条件
このチュートリアルを実行するには、SQL Server Management Studio、SQL Server を実行しているサーバーへのアクセス、および AdventureWorks データベースが必要です。

- [SQL Server Management Studio](../ssms/download-sql-server-management-studio-ssms.md) をインストールします。
- [SQL Server 2017 Developer Edition](https://www.microsoft.com/sql-server/sql-server-downloads) をインストールする。
- [AdventureWorks2017 サンプル データベース](../samples/adventureworks-install-configure.md)をダウンロードする。

SQL Server Management Studio でデータベースを復元する手順については、[データベースの復元](./backup-restore/restore-a-database-backup-using-ssms.md)に関するページを参照してください。   
  
## <a name="1-configure-the-environment"></a>1.環境を構成する  
この例の初期コンテキストを設定するには、[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] で新しいクエリを開き、次のコードを実行して Adventureworks2017 データベースを開きます。 このコードにより、データベース コンテキストが `AdventureWorks2017` に変更され、新しいサーバー ログインとデータベース ユーザー アカウント (`TestCreditRatingUser`) が作成されます。ログインにはパスワードが設定されます。  
  
```sql  
USE AdventureWorks2017;  
GO  
-- Set up a login for the test user  
CREATE LOGIN TestCreditRatingUser  
   WITH PASSWORD = 'ASDECd2439587y'  
GO  
CREATE USER TestCreditRatingUser  
FOR LOGIN TestCreditRatingUser;  
GO  
```  
  
CREATE USER ステートメントの詳細については、[「CREATE USER (Transact-SQL)」](../t-sql/statements/create-user-transact-sql.md) を参照してください。 CREATE LOGIN ステートメントの詳細については、[「CREATE LOGIN (Transact-SQL)」](../t-sql/statements/create-login-transact-sql.md) を参照してください。  
  
## <a name="2-create-a-certificate"></a>2.証明書を作成する  
コンテキストの master データベース、またはユーザー データベース、あるいはその両方を使用して、サーバー内に証明書を作成できます。 証明書をセキュリティ保護するには複数のオプションがあります。 証明書の詳細については、[「CREATE CERTIFICATE (Transact-SQL)」](../t-sql/statements/create-certificate-transact-sql.md) を参照してください。  
  
次のコードを実行し、データベース証明書をパスワードの保護付きで作成します。  
  
```sql  
CREATE CERTIFICATE TestCreditRatingCer  
   ENCRYPTION BY PASSWORD = 'pGFD4bb925DGvbd2439587y'  
      WITH SUBJECT = 'Credit Rating Records Access',   
      EXPIRY_DATE = '12/31/2021';  -- Error 3701 will occur if this date is not in the future
GO  
```  
  
## <a name="3-create-and-sign-a-stored-procedure-using-the-certificate"></a>3.ストアド プロシージャを作成し、証明書を使用して署名する  
次のコードを実行し、 `Vendor` データベース スキーマ内の `Purchasing` テーブルからデータを選択するストアド プロシージャを作成して、信用格付け 1 の会社にアクセスを限定します。 ストアド プロシージャの最初のセクションに、ストアド プロシージャを実行するユーザー アカウントのコンテキストが表示されていますが、これは概念を説明するためだけのものです。 要件を満たすために必要なものではありません。  
  
```sql  
CREATE PROCEDURE TestCreditRatingSP  
AS  
BEGIN  
   -- Show who is running the stored procedure  
   SELECT SYSTEM_USER 'system Login'  
   , USER AS 'Database Login'  
   , NAME AS 'Context'  
   , TYPE  
   , USAGE   
   FROM sys.user_token     
  
   -- Now get the data  
   SELECT AccountNumber, Name, CreditRating   
   FROM Purchasing.Vendor  
   WHERE CreditRating = 1  
END  
GO  
```  
  
次のコードを実行し、データベース証明書を使用してストアド プロシージャに署名します。このときパスワードも使用します。  
  
```sql  
ADD SIGNATURE TO TestCreditRatingSP   
   BY CERTIFICATE TestCreditRatingCer  
    WITH PASSWORD = 'pGFD4bb925DGvbd2439587y';  
GO  
```  
  
ストアド プロシージャの詳細については、[「ストアド プロシージャ (データベース エンジン)」](../relational-databases/stored-procedures/stored-procedures-database-engine.md) を参照してください。  
  
ストアド プロシージャへの署名の詳細については、[「ADD SIGNATURE (Transact-SQL)」](../t-sql/statements/add-signature-transact-sql.md) を参照してください。  
  
## <a name="4-create-a-certificate-account-using-the-certificate"></a>4.証明書を使用して証明書アカウントを作成する  
次のコードを実行し、証明書からデータベース ユーザー (`TestCreditRatingcertificateAccount`) を作成します。 このアカウントにサーバー ログインは与えません。最終的には、このアカウントによって基になるテーブルに対するアクセスが制御されます。  
  
```sql  
USE AdventureWorks2017;  
GO  
CREATE USER TestCreditRatingcertificateAccount  
   FROM CERTIFICATE TestCreditRatingCer;  
GO  
```  
  
## <a name="5-grant-the-certificate-account-database-rights"></a>5.証明書アカウントにデータベース権限を与える  
次のコードを実行し、ベース テーブルとストアド プロシージャに `TestCreditRatingcertificateAccount` 権限を与えます。  
  
```sql  
GRANT SELECT   
   ON Purchasing.Vendor   
   TO TestCreditRatingcertificateAccount;  
GO  
  
GRANT EXECUTE   
   ON TestCreditRatingSP   
   TO TestCreditRatingcertificateAccount;  
GO  
```  
  
オブジェクトに対する権限付与の詳細については、[「GRANT (Transact-SQL)」](../t-sql/statements/grant-transact-sql.md) を参照してください。  
  
## <a name="6-display-the-access-context"></a>6.アクセス コンテキストを表示する  
ストアド プロシージャへのアクセスに関連する権限を表示するには、次のコードを実行して、ストアド プロシージャの実行権限を `TestCreditRatingUser` ユーザーに与えます。  
  
```sql  
GRANT EXECUTE   
   ON TestCreditRatingSP   
   TO TestCreditRatingUser;  
GO  
```  
  
その後、次のコードを実行し、サーバーに使用した dbo ログインでストアド プロシージャを実行して、 ユーザー コンテキスト情報の出力を確認します。 この出力では、dbo アカウントはグループ メンバーシップを介してではなく、自身の権限を持つコンテキストとして表示されます。  
  
```sql  
EXECUTE TestCreditRatingSP;  
GO  
```  
  
次のコードを実行し、 `EXECUTE AS` ステートメントにより `TestCreditRatingUser` アカウントとなってストアド プロシージャを実行します。 今度は、ユーザー コンテキストが USER MAPPED TO CERTIFICATE コンテキストに設定されていることを確認できます。 包含データベース、Azure SQL Database、Azure Synapse Analytics ではこのオプションはサポートされていないことにご注意ください。
  
```sql  
EXECUTE AS LOGIN = 'TestCreditRatingUser';  
GO  
EXECUTE TestCreditRatingSP;  
GO  
```  
  
これは、ストアド プロシージャへの署名により監査が可能になったことを示しています。  
  
> [!NOTE]  
> データベース内でコンテキストを切り替えるには、EXECUTE AS を使用します。  
  
## <a name="7-reset-the-environment"></a>7.環境をリセットする  
次のコードを実行して、`REVERT` ステートメントにより現在のアカウントのコンテキストを dbo に戻し、環境をリセットします。  
  
```sql  
REVERT;  
GO  
DROP PROCEDURE TestCreditRatingSP;  
GO  
DROP USER TestCreditRatingcertificateAccount;  
GO  
DROP USER TestCreditRatingUser;  
GO  
DROP LOGIN TestCreditRatingUser;  
GO  
DROP CERTIFICATE TestCreditRatingCer;  
GO  
```  
  
REVERT ステートメントの詳細については、「[REVERT (Transact-SQL)](../t-sql/statements/revert-transact-sql.md)」 を参照してください。  
  
## <a name="complete-example"></a><a name="CompleteExample"></a>完全なサンプル コード  
次に、完全なサンプル コードを示します。  
  
```sql  
/* Step 1 - Open the AdventureWorks2017 database */  
USE AdventureWorks2017;  
GO  
-- Set up a login for the test user  
CREATE LOGIN TestCreditRatingUser  
   WITH PASSWORD = 'ASDECd2439587y'  
GO  
CREATE USER TestCreditRatingUser  
FOR LOGIN TestCreditRatingUser;  
GO  
  
/* Step 2 - Create a certificate in the AdventureWorks2017 database */  
CREATE CERTIFICATE TestCreditRatingCer  
   ENCRYPTION BY PASSWORD = 'pGFD4bb925DGvbd2439587y'  
      WITH SUBJECT = 'Credit Rating Records Access',   
      EXPIRY_DATE = '12/31/2021';   -- Error 3701 will occur if this date is not in the future
GO  
  
/* Step 3 - Create a stored procedure and  
sign it using the certificate */  
CREATE PROCEDURE TestCreditRatingSP  
AS  
BEGIN  
   -- Shows who is running the stored procedure  
   SELECT SYSTEM_USER 'system Login'  
   , USER AS 'Database Login'  
   , NAME AS 'Context'  
   , TYPE  
   , USAGE   
   FROM sys.user_token;     
  
   -- Now get the data  
   SELECT AccountNumber, Name, CreditRating   
   FROM Purchasing.Vendor  
   WHERE CreditRating = 1;  
END  
GO  
  
ADD SIGNATURE TO TestCreditRatingSP   
   BY CERTIFICATE TestCreditRatingCer  
    WITH PASSWORD = 'pGFD4bb925DGvbd2439587y';  
GO  
  
/* Step 4 - Create a database user for the certificate.   
This user has the ownership chain associated with it. */  
USE AdventureWorks2017;  
GO  
CREATE USER TestCreditRatingcertificateAccount  
   FROM CERTIFICATE TestCreditRatingCer;  
GO  
  
/* Step 5 - Grant the user database rights */  
GRANT SELECT   
   ON Purchasing.Vendor   
   TO TestCreditRatingcertificateAccount;  
GO  
  
GRANT EXECUTE  
   ON TestCreditRatingSP   
   TO TestCreditRatingcertificateAccount;  
GO  
  
/* Step 6 - Test, using the EXECUTE AS statement */  
GRANT EXECUTE   
   ON TestCreditRatingSP   
   TO TestCreditRatingUser;  
GO  
  
-- Run the procedure as the dbo user, notice the output for the type  
EXEC TestCreditRatingSP;  
GO  
  
EXECUTE AS LOGIN = 'TestCreditRatingUser';  
GO  
EXEC TestCreditRatingSP;  
GO  
  
/* Step 7 - Clean up the example */  
REVERT;  
GO  
DROP PROCEDURE TestCreditRatingSP;  
GO  
DROP USER TestCreditRatingcertificateAccount;  
GO  
DROP USER TestCreditRatingUser;  
GO  
DROP LOGIN TestCreditRatingUser;  
GO  
DROP CERTIFICATE TestCreditRatingCer;  
GO  
```  
  
## <a name="see-also"></a>参照  
[SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター](../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)  
  
  
