---
description: JDBC ドライバーが Java EE と JBDC API をサポートする方法と Java アプリケーションがその機能を利用するために使用する必要があるパッケージについて説明します。
title: Java EE のサポートについて
ms.custom: ''
ms.date: 02/26/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: a9448b80-b7a3-49cf-8bb4-322c73676005
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 5b6ec6bb0be6b03c8e72106e231c23be09fa2c9d
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104793015"
---
# <a name="understanding-java-ee-support"></a>Java EE のサポートについて

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

以下のセクションでは、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] が Java Platform, Enterprise Edition (Java EE) および JDBC 3.0 のオプションの API 機能をサポートするしくみについて説明します。 このヘルプ システムで提供されるソース コード例は、これらの機能を使用するための優れた参考資料となります。

まず、使用している Java 環境 (JDK、JRE) に javax.sql パッケージが含まれていることを確認してください。 このパッケージは、オプションの API を使用するすべての JDBC アプリケーションに必須です。 JDK 1.5 以降のバージョンには、このパッケージが既に含まれているので、別途インストールする必要はありません。

## <a name="driver-name"></a>ドライバー名

このドライバーのクラス名は、**com.microsoft.sqlserver.jdbc.SQLServerDriver** です。 JDBC Driver 4.1、4.2、6.0 の場合、このドライバーは **sqljdbc.jar**、**sqljdbc4.jar**、**sqljdbc41.jar**、または **sqljdbc42.jar** ファイルに含まれています。

JDBC Driver 6.2 では、ドライバーは **mssql-jdbc-6.2.2.jre7.jar** または **mssql-jdbc-6.2.2.jre8.jar** に含まれています。

JDBC Driver 6.4 では、ドライバーは **mssql-jdbc-6.4.0.jre7.jar**、**mssql-jdbc-6.4.0.jre8.jar**、または **mssql-jdbc-6.4.0.jre9.jar** に含まれています。

JDBC Driver 7.0 では、ドライバーは **mssql-jdbc-7.0.0.jre8.jar** または **mssql-jdbc-7.0.0.jre10.jar** に含まれています。

JDBC Driver 7.2 では、ドライバーは **mssql-jdbc-7.2.2.jre8.jar** または **mssql-jdbc-7.2.2.jre11.jar** に含まれています。

JDBC Driver 7.4 では、ドライバーは **mssql-jdbc-7.4.1.jre8.jar**、**mssql-jdbc-7.4.1.jre11.jar**、または **mssql-jdbc-7.4.1.jre12.jar** に含まれています。

JDBC Driver 8.2 では、ドライバーは **mssql-jdbc-8.2.2.jre8.jar**、**mssql-jdbc-8.2.2.jre11.jar**、または **mssql-jdbc-8.2.2.jre13.jar** に含まれています。

JDBC Driver 8.4 では、ドライバーは **mssql-jdbc-8.4.1.jre8.jar**、**mssql-jdbc-8.4.1.jre11.jar**、または **mssql-jdbc-8.4.1.jre14.jar** に含まれています。

JDBC Driver 9.2 では、ドライバーは **mssql-jdbc-9.2.1.jre8.jar**、**mssql-jdbc-9.2.1.jre11.jar**、または **mssql-jdbc-9.2.1.jre15.jar** に含まれています。

JDBC DriverManager クラスを使用してドライバーを読み込む場合、および任意のドライバー構成でドライバーのクラス名を指定する場合は常に、このクラス名が使用されます。 たとえば、Java EE アプリケーション サーバー内でデータ ソースを構成するには、ドライバーのクラス名を入力する必要が生じる場合があります。

## <a name="data-sources"></a>データ ソース

この JDBC ドライバーは、Java EE または JDBC 3.0 データ ソースをサポートします。 JDBC ドライバー [SQLServerXADataSource](reference/sqlserverxadatasource-class.md) クラスは `com.microsoft.sqlserver.jdbc.SQLServerXADataSource` によって実装されます。

### <a name="datasource-names"></a>データソース名

データ ソースを使用して、データベース接続を確立できます。 次の表は、JDBC ドライバーで使用できるデータ ソースを示しています。

|データ ソースの種類|クラスの名前と説明|
|---------------|--------------------------|
|DataSource|`com.microsoft.sqlserver.jdbc.SQLServerDataSource` <br/> <br/> 非プーリング データ ソース。|
|ConnectionPoolDataSource|`com.microsoft.sqlserver.jdbc.SQLServerConnectionPoolDataSource` <br/> <br/> JAVA EE アプリケーション サーバー接続プールを構成するデータ ソース。 通常は、アプリケーションが JAVA EE アプリケーション サーバー内で実行される場合に使用されます。|
|XADataSource|`com.microsoft.sqlserver.jdbc.SQLServerXADataSource` <br/> <br/> JAVA EE XA データ ソースを構成するデータ ソース。 通常は、アプリケーションが JAVA EE アプリケーション サーバーおよび XA トランザクション マネージャー内で実行される場合に使用されます。|

### <a name="data-source-properties"></a>[データ ソースのプロパティ]

すべてのデータ ソースは、基になるドライバーのプロパティ セットに関連付けられているプロパティを設定または取得する機能をサポートします。

次に例を示します。

`setServerName("localhost");`  
`setDatabaseName("AdventureWorks");`  

以下は、アプリケーションがデータ ソースを使用して接続するしくみを示しています。

```java
//initialize JNDI ..
Context ctx = new InitialContext(System.getProperties());
...
DataSource ds = (DataSource) ctx.lookup("MyDataSource");
Connection c = ds.getConnection("user", "pwd");
```

データ ソースのプロパティの詳細については、「[データ ソースのプロパティの設定](setting-the-data-source-properties.md)」を参照してください。

## <a name="see-also"></a>関連項目

[JDBC ドライバーの概要](overview-of-the-jdbc-driver.md)  
