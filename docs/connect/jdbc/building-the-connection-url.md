---
title: 接続 URL の構築
description: Microsoft JDBC Driver for SQL Server によって使用される接続文字列の形式について説明します。
ms.custom: ''
ms.date: 01/29/2020
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 44996746-d373-4f59-9863-a8a20bb8024a
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 709855be591f527191028ef3164066f0adb63819
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633262"
---
# <a name="building-the-connection-url"></a>接続 URL の構築

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

接続 URL の一般的な書式は次のとおりです。

`jdbc:sqlserver://[serverName[\instanceName][:portNumber]][;property=value[;property=value]]`

各値の説明:

- **jdbc:sqlserver://** (必須) は、サブプロトコルであり定数です。

- **serverName** (省略可能) は、接続先のサーバーのアドレスです。 このアドレスは DNS または IP アドレスとすることができます。または、ローカル コンピューターを表す localhost あるいは 127.0.0.1 とすることもできます。 接続 URL で指定されていない場合、プロパティのコレクションでサーバー名を指定する必要があります。

- **instanceName** (省略可能) は、serverName 上にある接続先のインスタンスです。 指定されていない場合は、既定のインスタンスに接続されます。

- **portNumber** (省略可能) は、serverName 上にある接続先のポートです。 既定では 1433 です。 既定のポートを使用する場合は、URL でポートおよびその前の ':' を指定する必要はありません。

    > [!NOTE]
    >  最適な接続パフォーマンスのために、名前付きインスタンスに接続するときは portNumber を設定します。 こうすることにより、ポート番号を決定するためのサーバーへのラウンド トリップが回避されます。 portNumber および instanceName の両方が使用されている場合、portNumber が優先され、instanceName は無視されます。

- **property** (省略可能) は、1 つ以上の接続プロパティ オプションです。 詳細については、「[接続プロパティの設定](setting-the-connection-properties.md)」を参照してください。 リストに含まれる任意のプロパティを指定できます。 プロパティを区切るには必ずセミコロン (';') を使用します。またプロパティを重複して指定することはできません。

> [!CAUTION]
> セキュリティ上の理由から、ユーザー入力に基づく接続 URL の作成は避ける必要があります。 URL では、サーバー名とドライバーだけを指定するようにします。 ユーザー名とパスワードの値には、接続プロパティのコレクションを使用します。 JDBC アプリケーションにおけるセキュリティの詳細については、「[JDBC ドライバー アプリケーションのセキュリティ保護](securing-jdbc-driver-applications.md)」を参照してください。

## <a name="connection-examples"></a>接続の例

ユーザー名とパスワードを使用して、ローカル コンピューター上の既定のデータベースに接続します。

`jdbc:sqlserver://localhost;user=MyUserName;password=*****;`

> [!NOTE]
> 前の例では接続文字列でユーザー名とパスワードを使用していますが、より安全な統合セキュリティを使用する必要があります。 詳細については、後の「[Windows 上で統合認証を使用する接続](#Connectingintegrated)」セクションを参照してください。

次の接続文字列は、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] でサポートされている任意のオペレーティング システム上で実行中のアプリケーションから統合認証および Kerberos を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースに接続する方法の例を示しています。

```java
jdbc:sqlserver://;servername=server_name;integratedSecurity=true;authenticationScheme=JavaKerberos
```

統合認証を使用して、ローカル コンピューター上の既定のデータベースに接続します。

`jdbc:sqlserver://localhost;integratedSecurity=true;`

リモート サーバー上の名前付きデータベースに接続します。

`jdbc:sqlserver://localhost;databaseName=AdventureWorks;integratedSecurity=true;`

既定のポートでリモート サーバーに接続します。

`jdbc:sqlserver://localhost:1433;databaseName=AdventureWorks;integratedSecurity=true;`

カスタマイズされたアプリケーション名を指定して接続します。

`jdbc:sqlserver://localhost;databaseName=AdventureWorks;integratedSecurity=true;applicationName=MyApp;`

## <a name="named-and-multiple-sql-server-instances"></a>名前付きおよび複数の SQL Server インスタンス

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、サーバーごとに複数のデータベース インスタンスをインストールできます。 各インスタンスは個別の名前によって識別されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の名前付きインスタンスに接続するには、名前付きインスタンスのポート番号を指定するか (推奨)、またはインスタンス名を JDBC URL プロパティまたは **datasource** プロパティとして指定することができます。 インスタンス名またはポート番号のプロパティを指定しない場合は、既定のインスタンスへの接続が作成されます。 次の例を参照してください。

ポート番号を指定するには、次の形式を使用します。

`jdbc:sqlserver://localhost:1433;integratedSecurity=true;<more properties as required>;`

JDBC URL プロパティを使用するには、次の形式を使用します。

`jdbc:sqlserver://localhost;instanceName=instance1;integratedSecurity=true;<more properties as required>;`

## <a name="escaping-values-in-the-connection-url"></a>接続 URL での値のエスケープ

スペース、セミコロン、引用符などの特殊文字が値に含まれている場合は、接続 URL の値の特定部分をエスケープすることが必要になる場合があります。 JDBC ドライバーでは、これらの文字を中かっこで囲むことでエスケープすることがサポートされています。 たとえば、{;} とするとセミコロンがエスケープされます。

エスケープする値には特殊文字 (特に '='、';'、'[]'、およびスペース) を含めることができますが、中かっこはエスケープできません。 エスケープが必要な値に中かっこが含まれる場合は、プロパティのコレクションに加える必要があります。

> [!NOTE]
> 中かっこ内の空白はリテラルでありトリミングされません。

## <a name="connecting-with-integrated-authentication-on-windows"></a><a name="Connectingintegrated"></a> Windows 上で統合認証を使用する接続

JDBC ドライバーでは、`integratedSecurity` 接続文字列プロパティを使用して、Windows オペレーティング システム上でのタイプ 2 の統合認証の使用がサポートされます。 統合認証を使用するには、JDBC ドライバーがインストールされているコンピューターの Windows システム パス上のディレクトリに mssql-jdbc_auth-\<version>-\<arch>.dll ファイルをコピーします。

mssql-jdbc_auth-\<version>-\<arch>.dll ファイルは次の場所にインストールされています。

\<*installation directory*>\sqljdbc_\<*version*>\\<*language*>\auth\

[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] でサポートされているオペレーティング システムについては、「[Kerberos 統合認証による SQL Server への接続](using-kerberos-integrated-authentication-to-connect-to-sql-server.md)」で、統合認証とタイプ 4 の Kerberos 認証を使用したデータベースへの接続をアプリケーションに許可する [!INCLUDE[jdbc_40](../../includes/jdbc_40_md.md)] で追加された機能の説明を参照してください。

> [!NOTE]
> 32 ビットの Java 仮想マシン (JVM) を実行している場合は、オペレーティング システムのバージョンが x64 であっても、x86 フォルダーの mssql-jdbc_auth-\<version>-\<arch>.dll ファイルを使用してください。 64 ビットの JVM を x64 プロセッサ上で実行している場合は、x64 フォルダーの mssql-jdbc_auth-\<version>-\<arch>.dll ファイルを使用してください。

または、java.library.path システム プロパティを設定して、mssql-jdbc_auth-\<version>-\<arch>.dll のディレクトリを指定することもできます。 たとえば、JDBC ドライバーが既定のディレクトリにインストールされている場合、Java アプリケーションの起動時に次の仮想マシン (VM) 引数を使用することで、DLL の場所を指定できます。

`-Djava.library.path=C:\Microsoft JDBC Driver 6.4 for SQL Server\sqljdbc_<version>\enu\auth\x86`

## <a name="connecting-with-ipv6-addresses"></a>IPv6 アドレスによる接続

JDBC ドライバーでは、接続プロパティのコレクション、および serverName 接続文字列プロパティと合わせて IPv6 アドレスを使用できます。 jdbc:*sqlserver*://*serverName* など、serverName の初期値は、接続文字列内の IPv6 アドレスをサポートしていません。 あらゆる接続で、そのままの IPv6 アドレスの代わりに *serverName* の名前を使用できます。 詳細を次の例に示します。

**serverName プロパティを使用するには:**

`jdbc:sqlserver://;serverName=3ffe:8311:eeee:f70f:0:5eae:10.203.31.9\\instance1;integratedSecurity=true;`

**プロパティのコレクションを使用するには:**

`Properties pro = new Properties();`

`pro.setProperty("serverName", "serverName=3ffe:8311:eeee:f70f:0:5eae:10.203.31.9\\instance1");`

`Connection con = DriverManager.getConnection("jdbc:sqlserver://;integratedSecurity=true;", pro);`

## <a name="see-also"></a>関連項目

[JDBC ドライバーによる SQL Server への接続](connecting-to-sql-server-with-the-jdbc-driver.md)
