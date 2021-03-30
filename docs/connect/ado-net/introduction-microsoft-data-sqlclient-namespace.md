---
title: Microsoft.Data.SqlClient 名前空間の概要
description: Microsoft.Data.SqlClient 名前空間と .NET アプリケーション向け SQL に接続する方法としてそれが推奨される理由について説明します。
ms.date: 11/19/2020
ms.assetid: c18b1fb1-2af1-4de7-80a4-95e56fd976cb
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-jizho2
ms.openlocfilehash: 8fdb8f726fc33ccb5acc6129c693a5233f87e313
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633284"
---
# <a name="introduction-to-microsoftdatasqlclient-namespace"></a>Microsoft.Data.SqlClient 名前空間の概要

[!INCLUDE [Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

## <a name="release-notes-for-microsoftdatasqlclient-21"></a>Microsoft.Data.SqlClient 2.1 のリリース ノート

リリース ノートは、GitHub リポジトリの [2.1 リリース ノート](https://github.com/dotnet/SqlClient/tree/master/release-notes/2.1)でも入手できます。

### <a name="new-features-in-21"></a>2\.1 の新機能

### <a name="cross-platform-support-for-always-encrypted"></a>Always Encrypted でのクロスプラットフォームのサポート

Microsoft.Data.SqlClient v2.1 での拡張により、次のプラットフォームで Always Encrypted がサポートされるようになります。

| Always Encrypted のサポート | セキュリティで保護されたエンクレーブを使用する Always Encrypted のサポート  | [対象とする Framework] | Microsoft.Data.SqlClient のバージョン | オペレーティング システム |
|:--|:--|:--|:--:|:--:|
| はい | はい | .NET Framework 4.6+ | 1.1.0 以降 | Windows |
| はい | はい | .NET Core 2.1 以降 | 2.1.0 以降<sup>1</sup> | Windows、Linux、macOS |
| はい | いいえ<sup>2</sup> | .NET Standard 2.0 | 2.1.0 以降 | Windows、Linux、macOS |
| はい | はい | .NET Standard 2.1 以降 | 2.1.0 以降 | Windows、Linux、macOS |

> [!NOTE]
> <sup>1</sup> v2.1 より前のバージョンの Microsoft.Data.SqlClient では、Always Encrypted は Windows でのみサポートされています。
> <sup>2</sup> セキュリティで保護されたエンクレーブを使用する Always Encrypted は、.NET Standard 2.0 ではサポートされていません。

### <a name="azure-active-directory-device-code-flow-authentication"></a>Azure Active Directory でのデバイス コード フロー認証

Microsoft.Data.SqlClient v2.1 では、MSAL.NET での "デバイス コード フロー" 認証がサポートされています。
リファレンス ドキュメント:[OAuth 2.0 デバイス許可付与フロー](/azure/active-directory/develop/v2-oauth2-device-code)に関するページ

接続文字列の例:

`Server=<server>.database.windows.net; Authentication=Active Directory Device Code Flow; Database=Northwind;`

次の API を使用すると、デバイス コード フローのコールバック メカニズムをカスタマイズできます。

```csharp
public class ActiveDirectoryAuthenticationProvider
{
    // For .NET Framework, .NET Core and .NET Standard targeted applications
    public void SetDeviceCodeFlowCallback(Func<DeviceCodeResult, Task> deviceCodeFlowCallbackMethod)
}
```

### <a name="azure-active-directory-managed-identity-authentication"></a>Azure Active Directory マネージド ID 認証

[マネージド ID](/azure/active-directory/managed-identities-azure-resources/overview) を使用する Azure Active Directory 認証のサポートが、Microsoft.Data.SqlClient v2.1 で導入されています。

次の認証モード キーワードがサポートされています。

- Active Directory Managed Identity
- Active Directory MSI (クロス MS SQL ドライバーの互換性の場合)

接続文字列の例:

```cs
// For System Assigned Managed Identity
"Server={serverURL}; Authentication=Active Directory MSI; Initial Catalog={db};"

// For System Assigned Managed Identity
"Server={serverURL}; Authentication=Active Directory Managed Identity; Initial Catalog={db};"

// For User Assigned Managed Identity
"Server={serverURL}; Authentication=Active Directory MSI; User Id={ObjectIdOfManagedIdentity}; Initial Catalog={db};"

// For User Assigned Managed Identity
"Server={serverURL}; Authentication=Active Directory Managed Identity; User Id={ObjectIdOfManagedIdentity}; Initial Catalog={db};"
```

### <a name="azure-active-directory-interactive-authentication-enhancements"></a>Azure Active Directory 対話型認証の機能強化

Microsoft.Data.SqlClient v2.1 で追加された次の API を使用すると、"Active Directory 対話型" 認証のエクスペリエンスをカスタマイズできます。

```csharp
public class ActiveDirectoryAuthenticationProvider
{
    // For .NET Framework targeted applications only
    public void SetIWin32WindowFunc(Func<IWin32Window> iWin32WindowFunc);

    // For .NET Standard targeted applications only
    public void SetParentActivityOrWindowFunc(Func<object> parentActivityOrWindowFunc);

    // For .NET Framework, .NET Core and .NET Standard targeted applications
    public void SetAcquireAuthorizationCodeAsyncCallback(Func<Uri, Uri, CancellationToken, Task<Uri>> acquireAuthorizationCodeAsyncCallback);

    // For .NET Framework, .NET Core and .NET Standard targeted applications
    public void ClearUserTokenCache();
}
```

### <a name="sqlclientauthenticationproviders-configuration-section"></a>`SqlClientAuthenticationProviders` 構成セクション

Microsoft.Data.SqlClient v2.1 には、新しい構成セクション `SqlClientAuthenticationProviders` が導入されています (既存の `SqlAuthenticationProviders` の複製)。 既存の構成セクション `SqlAuthenticationProviders` は、適切な型が定義されているときの下位互換性のために引き続きサポートされます。

新しいセクションを使用すると、アプリケーション構成ファイルに、System.Data.SqlClient 用の SqlAuthenticationProviders セクションと、Microsoft.Data.SqlClient 用の SqlClientAuthenticationProviders セクションの両方を含めることができます。

### <a name="azure-active-directory-authentication-using-an-application-client-id"></a>アプリケーション クライアント ID を使用する Azure Active Directory 認証

Microsoft.Data.SqlClient v2.1 には、ユーザー定義のアプリケーション クライアント ID を Microsoft 認証ライブラリに渡すためのサポートが導入されています。 アプリケーション クライアント ID は、Azure Active Directory で認証を行うときに使用されます。

次の新しい API が導入されています。

1. 新しいコンストラクターが ActiveDirectoryAuthenticationProvider に導入されました。\
_[すべての .NET プラットフォーム (.NET Framework、.NET Core、.NET Standard) に適用されます]_

    ```csharp
    public ActiveDirectoryAuthenticationProvider(string applicationClientId)
    ```

    用途:

    ```csharp
    string APP_CLIENT_ID = "<GUID>";
    SqlAuthenticationProvider customAuthProvider = new ActiveDirectoryAuthenticationProvider(APP_CLIENT_ID);
    SqlAuthenticationProvider.SetProvider(SqlAuthenticationMethod.ActiveDirectoryInteractive, customAuthProvider);
    
    using (SqlConnection sqlConnection = new SqlConnection("<connection_string>")
    {
        sqlConnection.Open();
    }
    ```

2. `SqlAuthenticationProviderConfigurationSection` と `SqlClientAuthenticationProviderConfigurationSection` に新しい構成プロパティが導入されました。\
_[.NET Framework と .NET Core に適用されます]_

    ```csharp
    internal class SqlAuthenticationProviderConfigurationSection : ConfigurationSection
    {
        ...
        [ConfigurationProperty("applicationClientId", IsRequired = false)]
        public string ApplicationClientId => this["applicationClientId"] as string;
    }
    
    // Inheritance
    internal class SqlClientAuthenticationProviderConfigurationSection : SqlAuthenticationProviderConfigurationSection
    { ... }
    ```

    用途:

    ```xml
    <configuration>
        <configSections>
            <section name="SqlClientAuthenticationProviders"
                             type="Microsoft.Data.SqlClient.SqlClientAuthenticationProviderConfigurationSection, Microsoft.Data.SqlClient" />
        </configSections>
        <SqlClientAuthenticationProviders applicationClientId ="<GUID>" />
    </configuration>
    
    <!--or-->
    
    <configuration>
        <configSections>
            <section name="SqlAuthenticationProviders"
                             type="Microsoft.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, Microsoft.Data.SqlClient" />
        </configSections>
        <SqlAuthenticationProviders applicationClientId ="<GUID>" />
    </configuration>
    ```

### <a name="data-classification-v2-support"></a>データ分類 v2 のサポート

Microsoft.Data.SqlClient v2.1 には、データ分類の "秘密度ランク" 情報のサポートが導入されています。 次の新しい API を使用できるようになりました。

```csharp
public class SensitivityClassification
{
    public SensitivityRank SensitivityRank;
}

public class SensitivityProperty
{
    public SensitivityRank SensitivityRank;
}

public enum SensitivityRank
{
    NOT_DEFINED = -1,
    NONE = 0,
    LOW = 10,
    MEDIUM = 20,
    HIGH = 30,
    CRITICAL = 40
}
```

### <a name="server-process-id-for-an-active-sqlconnection"></a>アクティブな `SqlConnection` のサーバー プロセス ID

Microsoft.Data.SqlClient v2.1 には、アクティブな接続での新しい `SqlConnection` プロパティ `ServerProcessId` が導入されています。

```csharp
public class SqlConnection
{
    // Returns the server process Id (SPID) of the active connection.
    public int ServerProcessId;
}
```

### <a name="trace-logging-support-in-native-sni"></a>ネイティブ SNI でのトレース ログのサポート

Microsoft.Data.SqlClient v2.1 では、SNI.dll でイベントのトレースを使用できるように、既存の `SqlClientEventSource` の実装が拡張されています。 イベントは、Xperf などのツールを使用してキャプチャする必要があります。

トレースを有効にするには、次に示すように `SqlClientEventSource` にコマンドを送信します。

```csharp
// Enables trace events:
EventSource.SendCommand(eventSource, (EventCommand)8192, null);

// Enables flow events:
EventSource.SendCommand(eventSource, (EventCommand)16384, null);

// Enables both trace and flow events:
EventSource.SendCommand(eventSource, (EventCommand)(8192 | 16384), null);
```

### <a name="command-timeout-connection-string-property"></a>"Command Timeout" 接続文字列プロパティ

Microsoft.Data.SqlClient v2.1 で導入された "Command Timeout" 接続文字列プロパティを使用すると、既定の 30 秒をオーバーライドできます。 個々のコマンドのタイムアウトは、SqlCommand の `CommandTimeout` プロパティを使用してオーバーライドできます。

接続文字列の例:

`"Server={serverURL}; Initial Catalog={db}; Integrated Security=true; Command Timeout=60"`

### <a name="removal-of-symbols-from-native-sni"></a>ネイティブ SNI からのシンボルの削除

Microsoft.Data.SqlClient v2.1 では、[v2.0.0](https://www.nuget.org/packages/Microsoft.Data.SqlClient.SNI/2.0.0) で導入されたシンボルが、[Microsoft.Data.SqlClient.SNI.runtime](https://www.nuget.org/packages/Microsoft.Data.SqlClient.SNI.runtime) NuGet の [v2.1.1](https://www.nuget.org/packages/Microsoft.Data.SqlClient.SNI.runtime/2.1.1) 以降から削除されます。 パブリック シンボルにアクセスする必要がある BinSkim などのツールのために、パブリック シンボルが Microsoft シンボル サーバーに発行されるようになります。

### <a name="source-linking-of-microsoftdatasqlclient-symbols"></a>Microsoft.Data.SqlClient のシンボルのソース リンク

Microsoft.Data.SqlClient v2.1 以降、ソース コードをダウンロードする必要のない、強化されたデバッグ エクスペリエンスのため、Microsoft.Data.SqlClient のシンボルが Microsoft シンボル サーバーにソース リンクされて発行されます。

## <a name="release-notes-for-microsoftdatasqlclient-20"></a>Microsoft.Data.SqlClient 2.0 のリリース ノート

リリース ノートは、GitHub リポジトリの [2.0 リリース ノート](https://github.com/dotnet/SqlClient/tree/master/release-notes/2.0).

### <a name="breaking-changes-in-20"></a>2\.0 における重大な変更

- エンクレーブ プロバイダー インターフェイス `SqlColumnEncryptionEnclaveProvider` のアクセス修飾子が `public` から `internal` に変更されました。
- `SqlClientMetaDataCollectionNames` クラスの定数は、SQL Server の変更を反映するように更新されました。
- ターゲット SQL Server で TLS 暗号化が適用されている場合、ドライバーによってサーバー証明書の検証が実行されるようになりました。これは、Azure の接続では既定です。
- `SqlDataReader.GetSchemaTable()` から、`null` ではなく空の `DataTable` が返されるようになりました。
- ドライバーによる小数点以下桁数の丸めの実行が、SQL Server の動作に一致するようになりました。 下位互換性を維持するために、AppContext スイッチを使用して以前の切り捨ての動作を有効にすることができます。
- **Microsoft.Data.SqlClient** を使用する .NET Framework アプリケーションの場合、以前は `bin\x64` および `bin\x86` フォルダーにダウンロードされた SNI.dll ファイルが `Microsoft.Data.SqlClient.SNI.x64.dll` および`Microsoft.Data.SqlClient.SNI.x86.dll` という名前になり、`bin` ディレクトリにダウンロードされます。
- `SqlConnectionStringBuilder` から接続文字列をフェッチするとき、一貫性を保つために、新しい接続文字列プロパティのシノニムによって古いプロパティが置き換えられます。 [詳細](#new-connection-string-property-synonyms)

### <a name="new-features-in-20"></a>2\.0 の新機能

次の新機能は、Microsoft.Data.SqlClient 2.0 で導入されました。

#### <a name="dns-failure-resiliency"></a>DNS エラーの回復性

この機能をサポートする SQL Server エンドポイントへの接続が成功するたびに、ドライバーによって IP アドレスがキャッシュされます。 接続試行中に DNS 解決エラーが発生した場合、ドライバーによって、そのサーバーのキャッシュされた IP アドレス (存在する場合) を使用した接続の確立が試みられます。

#### <a name="eventsource-tracing"></a>EventSource の追跡

このリリースでは、アプリケーションをデバッグするためのイベント トレース ログのキャプチャに対するサポートが導入されています。 これらのイベントをキャプチャするには、クライアント アプリケーションで SqlClient の EventSource 実装からのイベントをリッスンする必要があります。

```csharp
Microsoft.Data.SqlClient.EventSource
```

詳細については、[SqlClient でイベントのトレースを有効にする](enable-eventsource-tracing.md)方法を参照してください。

#### <a name="enabling-managed-networking-on-windows"></a>Windows でのマネージド ネットワークの有効化

新しい AppContext スイッチ **"Switch.Microsoft.Data.SqlClient.UseManagedNetworkingOnWindows"** によって、Windows 上でテストおよびデバッグの目的のためにマネージド SNI 実装を使用できるようになります。 このスイッチを使用すると、Windows 上で .NET Core 2.1 以降のマネージド SNI と .NET Standard 2.0 以降のプロジェクトを使用するようにドライバーの動作が切り替わり、Microsoft.Data.SqlClient ライブラリでネイティブ ライブラリのすべての依存関係が削除されます。

```csharp
AppContext.SetSwitch("Switch.Microsoft.Data.SqlClient.UseManagedNetworkingOnWindows", true);
```

ドライバーで使用できるスイッチの完全な一覧については、「[SqlClient の AppContext スイッチ](appcontext-switches.md)」を参照してください。

#### <a name="enabling-decimal-truncation-behavior"></a>小数点の切り捨て動作の有効化

小数点以下のデータ スケールは、ドライバーによって、既定では SQL Server で行われるように丸められます。 下位互換性を維持するために、AppContext スイッチ **"Switch.Microsoft.Data.SqlClient.TruncateScaledDecimal"** を **true** に設定できます。

```csharp
AppContext.SetSwitch("Switch.Microsoft.Data.SqlClient.TruncateScaledDecimal", true);
```

#### <a name="new-connection-string-property-synonyms"></a>新しい接続文字列プロパティのシノニム

次の既存の接続文字列プロパティに対して新しいシノニムが追加されました。これにより、複数の単語を使用しているプロパティのスペースあけの混乱を回避できます。 古いプロパティ名は下位互換性のために引き続きサポートされますが、[SqlConnectionStringBuilder](/dotnet/api/microsoft.data.sqlclient.sqlconnectionstringbuilder) から接続文字列をフェッチすると、新しい接続文字列プロパティが含まれるようになります。

|既存の接続文字列プロパティ|新しいシノニム|
|-----------------------------------|-----------|
| ApplicationIntent | アプリケーションの目的 |
| ConnectRetryCount | Connect Retry Count |
| ConnectRetryInterval | Connect Retry Interval |
| PoolBlockingPeriod | Pool Blocking Period |
| MultipleActiveResultSets | Multiple Active Result Sets |
| MultiSubnetFailover | Multiple Subnet Failover |
| TransparentNetworkIPResolution | Transparent Network IP Resolution |
| TrustServerCertificate | [Trust Server Certificate] |

#### <a name="sqlbulkcopy-rowscopied-property"></a>SqlBulkCopy RowsCopied プロパティ

RowsCopied プロパティを使用すると、実行中の一括コピー操作で処理された行の数に読み取り専用でアクセスできます。 この値は、必ずしもターゲット テーブルに追加された行の最終的な数と同じになるとは限りません。

#### <a name="connection-open-overrides"></a>接続オープンのオーバーライド

SqlConnection.Open() の既定の動作をオーバーライドして、一時的なエラーによってトリガーされる 10 秒の遅延と自動接続の再試行を無効にすることができます。

```csharp
using SqlConnection sqlConnection = new SqlConnection("Data Source=(local);Integrated Security=true;Initial Catalog=AdventureWorks;");
sqlConnection.Open(SqlConnectionOverrides.OpenWithoutRetry);
```

> [!NOTE]
> このオーバーライドは、SqlConnection.Open() にのみ適用でき、SqlConnection.OpenAsync() には適用できないことにご注意ください。

#### <a name="username-support-for-active-directory-interactive-mode"></a>Active Directory 対話モードでのユーザー名のサポート

.NET Framework と .NET Core の両方に対して Azure Active Directory 対話型認証モードを使用する場合は、接続文字列にユーザー名を指定できます。

**ユーザー ID** または **UID** 接続文字列プロパティを使用してユーザー名を設定します。

```csharp
"Server=<server name>; Database=<db name>; Authentication=Active Directory Interactive; User Id=<username>;"
```

#### <a name="order-hints-for-sqlbulkcopy"></a>SqlBulkCopy の順序のヒント

クラスター化インデックスを持つテーブルに対する一括コピー操作のパフォーマンスを向上させるために、順序のヒントを指定できます。 詳細については、「[の一括コピー操作](sql/bulk-copy-order-hints.md)」セクションを参照してください。

#### <a name="sni-dependency-changes"></a>SNI 依存関係の変更

Windows 上の Microsoft.Data.SqlClient (.NET Core および .NET Standard) は、**Microsoft.Data.SqlClient.SNI.runtime** に依存するようになりました。これにより、**runtime.native.System.Data.SqlClient.SNI** に対する以前の依存関係が置き換えられます。 新しい依存関係によって、Windows で既にサポートされているプラットフォームである ARM64、x64、x86 に加えて、ARM プラットフォームのサポートが追加されます。

## <a name="release-notes-for-microsoftdatasqlclient-110"></a>Microsoft.Data.SqlClient 1.1.0 のリリース ノート

リリース ノートは、GitHub リポジトリの [1.1 リリース ノート](https://github.com/dotnet/SqlClient/tree/master/release-notes/1.1)でも入手できます。

### <a name="new-features-in-11"></a>1\.1 の新機能

#### <a name="always-encrypted-with-secure-enclaves"></a>セキュア エンクレーブを使用する Always Encrypted

Always Encrypted は、Microsoft SQL Server 2016 から使用できます。 セキュリティで保護されたエンクレーブは、Microsoft SQL Server 2019 から使用できます。 エンクレーブ機能を使用するには、必要な構成証明プロトコルと構成証明 URL が接続文字列に含まれている必要があります。 次に例を示します。

```csharp
"Attestation Protocol=HGS;Enclave Attestation Url=<attestation_url_for_HGS>"
```

詳細については、次を参照してください。

- [SqlClient による Always Encrypted のサポート](sql/sqlclient-support-always-encrypted.md)
- [チュートリアル:セキュリティで保護されたエンクレーブが設定された Always Encrypted を使用する .NET アプリケーションの開発](sql/tutorial-always-encrypted-enclaves-develop-net-apps.md)」をご覧ください。

## <a name="release-notes-for-microsoftdatasqlclient-10"></a>Microsoft.Data.SqlClient 1.0 のリリース ノート

Microsoft.Data.SqlClient 名前空間の最初のリリースでは、既存の System.Data.SqlClient 名前空間よりも多くの機能が提供されています。
リリース ノートは、GitHub リポジトリの [1.0 リリース ノート](https://github.com/dotnet/SqlClient/tree/master/release-notes/1.0)でも入手できます。

### <a name="new-features-in-10"></a>1\.0 の新機能

#### <a name="new-features-over-net-framework-472-systemdatasqlclient"></a>.NET Framework 4.7.2 System.Data.SqlClient に対する新機能

- **データ分類** - Azure SQL Database および Microsoft SQL Server 2019 で使用できます。

- **UTF-8 のサポート** - Microsoft SQL Server 2019 で使用できます。

#### <a name="new-features-over-net-core-22-systemdatasqlclient"></a>.NET Core 2.2 System.Data.SqlClient に対する新機能

- **データ分類** - Azure SQL Database および Microsoft SQL Server 2019 で使用できます。

- **UTF-8 のサポート** - Microsoft SQL Server 2019 で使用できます。

- **認証** - Active Directory パスワード認証モード。

### <a name="data-classification"></a>データ分類

データ分類では、基になるソースでこの機能がサポートされ、[データの機密性と分類](../../relational-databases/security/sql-data-discovery-and-classification.md)に関するメタデータが含まれている場合に、SqlDataReader 経由で取得したオブジェクトに関するデータの機密性と分類に関する読み取り専用の情報を公開する新しい一連の API が提供されます。 「[SqlClient でのデータ検出と分類](https://github.com/dotnet/SqlClient/tree/master/release-notes/1.1)」にあるサンプル アプリケーションを参照してください。

```csharp
public class SqlDataReader
{
    public Microsoft.Data.SqlClient.DataClassification.SensitivityClassification SensitivityClassification
}

namespace Microsoft.Data.SqlClient.DataClassification
{
    public class ColumnSensitivity
    {
        public System.Collections.ObjectModel.ReadOnlyCollection<Microsoft.Data.SqlClient.DataClassification.SensitivityProperty> SensitivityProperties
    }
    public class InformationType
    {
        public string Id
        public string Name
    }
    public class Label
    {
        public string Id
        public string Name
    }
    public class SensitivityClassification
    {
        public System.Collections.ObjectModel.ReadOnlyCollection<Microsoft.Data.SqlClient.DataClassification.ColumnSensitivity> ColumnSensitivities
        public System.Collections.ObjectModel.ReadOnlyCollection<Microsoft.Data.SqlClient.DataClassification.InformationType> InformationTypes
        public System.Collections.ObjectModel.ReadOnlyCollection<Microsoft.Data.SqlClient.DataClassification.Label> Labels
    }
    public class SensitivityProperty
    {
        public Microsoft.Data.SqlClient.DataClassification.InformationType InformationType
        public Microsoft.Data.SqlClient.DataClassification.Label Label
    }
}
```

### <a name="utf-8-support"></a>UTF-8 のサポート

UTF-8 のサポートで、アプリケーション コードを変更する必要はありません。 これらの SqlClient の変更によって、サーバーで UTF-8 がサポートされ、基になる列の照合順序が UTF-8 である場合に、クライアントとサーバーの間の通信が最適化されます。 [SQL Server 2019 の新機能](../../sql-server/what-s-new-in-sql-server-ver15.md)の UTF-8 のセクションを参照してください。

### <a name="always-encrypted-with-secure-enclaves"></a>セキュリティで保護されたエンクレーブが設定された Always Encrypted:

一般に、.NET Framework で System.Data.SqlClient **および組み込み列マスター キー ストア プロバイダー** を使用する既存のドキュメントも .NET Core で動作するようになりました。

 [Always Encrypted と .NET Framework Data Provider を使用して開発する](../../relational-databases/security/encryption/develop-using-always-encrypted-with-net-framework-data-provider.md)

 [Always Encrypted:Windows 証明書ストアで機密データを保護し、暗号化キーを格納する](/azure/sql-database/sql-database-always-encrypted)

### <a name="authentication"></a>認証

_[認証]_ 接続文字列オプションを使用して、さまざまな認証モードを指定できます。 詳細については、[SqlAuthenticationMethod のドキュメント](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod?view=netframework-4.7.2&preserve-view=true)を参照してください。

> [!NOTE]
> Azure Key Vault プロバイダーなどのカスタム キー ストア プロバイダーは、Microsoft.Data.SqlClient をサポートするように更新する必要があります。 同様に、エンクレーブ プロバイダーも Microsoft.Data.SqlClient をサポートするように更新する必要があります。
> Always Encrypted は、.NET Framework および .NET Core ターゲットに対してのみサポートされます。 .NET Standard には特定の暗号化依存関係がないため、.NET Standard に対してはサポートされません。
