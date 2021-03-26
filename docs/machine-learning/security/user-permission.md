---
title: Python スクリプトと R スクリプトを実行する権限を許可する
description: SQL Server Machine Learning Services で外部の Python スクリプトおよび R スクリプトを実行する権限をユーザーに許可する方法とデータベースに対する読み取り、書き込み、またはデータ定義言語 (DDL) の権限を許可する方法について説明します。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 03/19/2021
ms.topic: how-to
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019, contperf-fy20q4
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: bbfc55b4c0460d948d78d72e628033d195a1f027
ms.sourcegitcommit: 17f05be5c08cf9a503a72b739da5ad8be15baea5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105103719"
---
# <a name="grant-database-users-permission-to-execute-python-and-r-scripts-with-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services で Python スクリプトと R スクリプトを実行する権限をデータベース ユーザーに付与する
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

[SQL Server Machine Learning Services](../../relational-databases/security/authentication-access/create-a-database-user.md) で外部の Python スクリプトおよび R スクリプトを実行する権限を[データベース ユーザー](../sql-server-machine-learning-services.md)に付与する方法とデータベースに対する読み取り、書き込み、またはデータ定義言語 (DDL) の権限を付与する方法について説明します。

詳細については、「[機能拡張フレームワークのセキュリティ概要](../../machine-learning/concepts/security.md#permissions)」の「権限」セクションを参照してください。

<a name="permissions-external-script"></a>

## <a name="permission-to-run-scripts"></a>スクリプトを実行する権限

SQL Server Machine Learning Services で Python スクリプトまたは R スクリプトを実行する、管理者ではない各ユーザーに対して、その言語が使用されている各データベースで外部スクリプトを実行する権限を許可する必要があります。

外部スクリプトを実行する権限を[データベース ユーザー](../../relational-databases/security/authentication-access/create-a-database-user.md)に付与するには、次のスクリプトを実行します。

```sql
USE <database_name>
GO
GRANT EXECUTE ANY EXTERNAL SCRIPT TO [UserName]
```

> [!NOTE]
> サポートされているスクリプト言語には、権限が固有ではありません。 言い換えると、R スクリプトと Python スクリプトには個別の権限レベルがありません。

<a name="permissions-db"></a>

## <a name="grant-database-permissions"></a>データベース アクセス許可を付与する

データベース ユーザーはスクリプトの実行中に他のデータベースからデータを読み取らなければならない場合があります。 また、データベース ユーザーは、結果を格納するための新しいテーブルを作成したり、テーブルにデータを書き込んだりすることが必要になる場合もあります。

R スクリプトまたは Python スクリプトを実行しているデータベース ユーザー アカウントまたは SQL ログインがそれぞれ、特定のデータベースに対して適切なアクセス許可を持っていることを確認します。 

+ データを読み取るための `db_datareader`。
+ オブジェクトをデータベースに保存するための `db_datawriter`。
+ トレーニングおよびシリアル化されたデータを含むストアド プロシージャまたはテーブルなどのオブジェクトを作成する `db_ddladmin`。

たとえば、次の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントでは、SQL ログインである *MySQLLogin* に、*ML_Samples* データベースで T-SQL クエリを実行する権限を与えています。 このステートメントを実行するには、SQL ログインがサーバーのセキュリティ コンテキストに既に存在している必要があります。 詳細については、「[sp_addrolemember (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-addrolemember-transact-sql.md)」をご覧ください。

```sql
USE ML_Samples
GO
EXEC sp_addrolemember 'db_datareader', 'MySQLLogin'
```

## <a name="next-steps"></a>次のステップ

各ロールに含まれる権限の詳細については、「[データベース レベルのロール](../../relational-databases/security/authentication-access/database-level-roles.md)」をご覧ください。
