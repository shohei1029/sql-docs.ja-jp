---
title: SqlPackage スクリプト
description: SqlPackage.exe スクリプトを使用してデータベース開発タスクを自動化する方法について説明します。 例と使用可能なパラメーター、プロパティ、および SQLCMD 変数を表示します。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
ms.assetid: 198198e2-7cf4-4a21-bda4-51b36cb4284b
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan; sstein
ms.date: 12/4/2020
ms.openlocfilehash: 8e1518b1ebd264a28b94933c332315042df49aae
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833628"
---
# <a name="sqlpackage-script-parameters-and-properties"></a>SqlPackage スクリプト パラメーターとプロパティ
SqlPackage.exe のスクリプト操作では、ソース データベースのスキーマに合わせてターゲット データベースのスキーマを更新する、Transact-SQL の増分更新スクリプトを作成します。 

## <a name="command-line-syntax"></a>コマンドライン構文

**SqlPackage.exe** は、コマンド ラインで指定されたパラメーター、プロパティ、および SQLCMD 変数を使用して指定された操作を開始します。  
  
```bash
SqlPackage {parameters}{properties}{SQLCMD Variables}  
```

## <a name="parameters-for-the-script-action"></a>スクリプト操作のパラメーター

|パラメーター|短い形式|値|説明|
|---|---|---|---|
|**/Action:**|**/a**|スクリプト|実行する操作を指定します。 |
|**/AccessToken:**|**/at**|{string}| ターゲット データベースに接続するときに使用するトークン ベースの認証アクセス トークンを指定します。 |
|**/DeployScriptPath:**|**/dsp**|{string}|配置スクリプトを出力する省略可能なファイル パスを指定します。 Azure の配置では、master データベースを作成または変更するための TSQL コマンドがある場合、スクリプトは同じパスに書き込まれますが、出力ファイル名として "Filename_Master.sql" が使用されます。 |
|**/DeployReportPath:**|**/drp**|{string}|配置レポートの xml ファイルを出力する省略可能なファイル パスを指定します。 |
|**/Diagnostics:**|**/d**|{True&#124;False}|診断ログがコンソールへの出力かどうかを指定します。 既定値は False です。 |
|**/DiagnosticsFile:**|**/df**|{string}|診断ログを保存するファイルを指定します。 |
|**/MaxParallelism:**|**/mp**|{int}| 1 つのデータベースに対して実行される同時実行操作の並列処理の次数を指定します。 既定値は 8 です。 |
|**/OutputPath:**|**/op**|{string}|出力ファイルが生成されるファイル パスを指定します。 |
|**/OverwriteFiles:**|**/of**|{True&#124;False}|sqlpackage.exe が既存のファイルを上書きするかどうかを指定します。 False を指定すると、既存のファイルが検出された場合に sqlpackage.exe の操作が中止します。 既定値は True です。 |
|**/Profile:**|**/pr**|{string}|DAC 公開プロファイルのファイル パスを指定します。 出力の生成時に使用するプロパティと変数のコレクションをプロファイルで定義します。|
|**/Properties:**|**/p**|{PropertyName}={Value}|操作固有のプロパティの名前と値のペア ({PropertyName}={Value}) を指定します。 操作のプロパティ名については、特定の操作のヘルプを参照してください。 例: sqlpackage.exe /Action:Script /?。|
|**/Quiet:**|**/q**|{True&#124;False}|詳細なフィードバックを非表示にするかどうかを指定します。 既定値は False です。|
|**/SourceConnectionString:**|**/scs**|{string}|ソース データベースの有効な SQL Server または SQL Azure 接続文字列を指定します。 このパラメーターを指定する場合、他のどのソース パラメーターとも同時には使用できません。 |
|**/SourceDatabaseName:**|**/sdn**|{string}|ソース データベースの名前を定義します。 |
|**/SourceEncryptConnection:**|**/sec**|{True&#124;False}|ソース データベース接続に SQL 暗号化を使用するかどうかを指定します。 |
|**/SourceFile:**|**/sf**|{string}|ソース ファイルを操作のソースとして使用するように指定します。 このパラメーターを使用した場合、他のソース パラメーターは無効になります。 |
|**/SourcePassword:**|**/sp**|{string}|SQL Server 認証シナリオでは、ソース データベースへのアクセスに使用するパスワードを定義します。 |
|**/SourceServerName:**|**/ssn**|{string}|ソース データベースをホストしているサーバーの名前を定義します。 |
|**/SourceTimeout:**|**/st**|{int}|ソース データベースへの接続を確立する際のタイムアウトを秒単位で指定します。 |
|**/SourceTrustServerCertificate:**|**/stsc**|{True&#124;False}|TLS を使用してソース データベースへの接続を暗号化し、証明書チェーンを検証せずに信頼を確認するかどうかを指定します。 |
|**/SourceUser:**|**/su**|{string}|SQL Server 認証シナリオでは、ソース データベースへのアクセスに使用する SQL Server ユーザーを定義します。 |
|**/TargetConnectionString:**|**/tcs**|{string}|ターゲット データベースの有効な SQL Server または Azure 接続文字列を指定します。 このパラメーターを指定する場合、他のどのターゲット パラメーターとも同時には使用できません。 |
|**/TargetDatabaseName:**|**/tdn**|{string}|sqlpackage.exe の動作のターゲットとなるデータベースの名前のオーバーライドを指定します。 |
|**/TargetEncryptConnection:**|**/tec**|{True&#124;False}|ターゲット データベース接続に SQL 暗号化を使用するかどうかを指定します。 |
|**/TargetFile:**|**/tf**|{string}| データベースではなく、ターゲット ファイル (.dacpac ファイル) をアクションのターゲットとして使用するように指定します。 このパラメーターを使用した場合、他のターゲット パラメーターは無効になります。 このパラメーターは、データベース ターゲットのみをサポートするアクションに対しては無効です。|
|**/TargetPassword:**|**/tp**|{string}|SQL Server 認証シナリオでは、ターゲット データベースへのアクセスに使用するパスワードを定義します。 |
|**/TargetServerName:**|**/tsn**|{string}|ターゲット データベースをホストしているサーバーの名前を定義します。 |
|**/TargetTimeout:**|**/tt**|{int}|ターゲット データベースへの接続を確立するためのタイムアウトを秒単位で指定します。 Azure AD の場合、この値は 30 秒以上にすることをお勧めします。|
|**/TargetTrustServerCertificate:**|**/ttsc**|{True&#124;False}|TLS を使用してターゲット データベースへの接続を暗号化し、証明書チェーンを検証せずに信頼を確認するかどうかを指定します。 |
|**/TargetUser:**|**/tu**|{string}|SQL Server 認証シナリオでは、ターゲット データベースへのアクセスに使用する SQL Server ユーザーを定義します。 |
|**/TenantId:**|**/tid**|{string}|Azure AD のテナント ID またはドメイン名を表します。 このオプションは、ゲスト、またはインポートされた Azure AD ユーザーと Microsoft アカウント (outlook.com、hotmail.com、live.com など) をサポートする必要があります。 このパラメーターを省略すると、Azure AD の既定のテナント ID が、認証済みユーザーがこの AD のネイティブ ユーザーであることを前提として使用されます。 ただし、この場合、ゲスト、またはこの Azure AD でホストされているインポートされたユーザーと Microsoft アカウントはサポートされず、操作は失敗します。 <br/> Active Directory ユニバーサル認証の詳細については、[SQL Database と Azure Synapse Analytics を使用したユニバーサル認証 (SSMS での MFA のサポート)](/azure/sql-database/sql-database-ssms-mfa-authentication) に関するページをご覧ください。|
|**/UniversalAuthentication:**|**/ua**|{True&#124;False}|ユニバーサル認証を使用するかどうかを指定します。 True に設定すると、対話型認証プロトコルがアクティブになり、MFA がサポートされます。 このオプションは、ユーザーによるユーザー名とパスワードの入力を必要とする対話型プロトコルまたは統合認証 (Windows 資格情報) を使用した、MFA なしの Azure AD 認証に使用することもできます。 /UniversalAuthentication が True に設定されているときは、SourceConnectionString (scs) で Azure AD 認証を指定することはできません。 /UniversalAuthentication が False に設定されているときは、SourceConnectionString (scs) で Azure AD 認証を指定する必要があります。 <br/> Active Directory ユニバーサル認証の詳細については、[SQL Database と Azure Synapse Analytics を使用したユニバーサル認証 (SSMS での MFA のサポート)](/azure/sql-database/sql-database-ssms-mfa-authentication) に関するページをご覧ください。|
|**/Variables:**|**/v**|{PropertyName}={Value}|操作固有の変数の名前と値のペア ({VariableName}={Value}) を指定します。 DACPAC ファイルには、有効な SQLCMD 変数の一覧が含まれます。 すべての変数に値を指定しないと、エラーが発生します。 |

## <a name="properties-specific-to-the-script-action"></a>スクリプト アクションに固有のプロパティ

|プロパティ|値|説明|
|---|---|---|
|**/p:**|AdditionalDeploymentContributorArguments=(STRING)|配置コントリビューターに追加の配置コントリビューター引数を指定します。 複数の値を指定する場合は、セミコロンで区切ります。
|**/p:**|AdditionalDeploymentContributors=(STRING)|dacpac を配置するときに実行する必要がある、追加の配置コントリビューターを指定します。 このとき、セミコロン区切りで、完全修飾ビルド コントリビューター名または ID を指定する必要があります。
|**/p:**|AdditionalDeploymentContributorPaths=(STRING)| 追加の配置コントリビューターを読み込むためのパスを指定します。 複数の値を指定する場合は、セミコロンで区切ります。 | 
|**/p:**|AllowDropBlockingAssemblies=(BOOLEAN)|このプロパティは、ブロックしているアセンブリを配置計画から削除する際に SqlClr の配置によって使用されます。 既定では、参照しているアセンブリを削除する必要がある場合、ブロックまたは参照しているアセンブリによって、アセンブリの更新がブロックされます。
|**/p:**|AllowIncompatiblePlatform=(BOOLEAN)|互換性がない SQL Server プラットフォームであっても操作を試行するかどうかを指定します。
|**/p:**|AllowUnsafeRowLevelSecurityDataMovement=(BOOLEAN)|このプロパティが true に設定されている場合は、行レベル セキュリティを使用するテーブルに対するデータ モーションをブロックしません。 既定値は false です。
|**/p:**|BackupDatabaseBeforeChanges=(BOOLEAN)|変更を配置する前にデータベースをバックアップします。
|**/p:**|BlockOnPossibleDataLoss=(BOOLEAN 'True')| データの精度の低下やキャスト操作を必要とするデータ型の変更などによるスキーマの変更によって、データが失われるおそれがある場合は、スキーマ検証ステップ中に操作を終了することを指定します。 既定値 (`True`) を指定すると、ターゲット データベースにデータが含まれているかどうかに関係なく、操作が終了します。  ターゲットに新しい列の型に変換できないデータが存在する場合、BlockOnPossibleDataLoss に `False` 値を指定して実行すると、配置計画の実行中に失敗するおそれがあります。 |
|**/p:**|BlockWhenDriftDetected=(BOOLEAN 'True')|スキーマがその登録と一致しないか、スキーマが登録されていないデータベースの更新をブロックするかどうかを指定します。
|**/p:**|CommandTimeout=(INT32 '60')|SQL Server に対してクエリを実行するときのコマンドのタイムアウト (秒) を指定します。
|**/p:**|CommentOutSetVarDeclarations=(BOOLEAN)|生成する公開スクリプトで SETVAR 変数の宣言をコメント アウトするかどうかを指定します。 このようなコメント アウトが必要になるのは、SQLCMD.EXE などのツールを使用して、公開時にコマンド ラインで値を指定する予定がある場合などです。
|**/p:**|CompareUsingTargetCollation=(BOOLEAN)|この設定では、配置の際にデータベースの照合順序をどのように処理するかを指定します。既定では、ソースで指定されている照合順序と異なる場合にターゲット データベースの照合順序が更新されます。 このオプションを設定した場合、ターゲット データベース (またはサーバー) の照合順序が使用されます。|
|**/p:**|CreateNewDatabase=(BOOLEAN)|データベースへの公開時に、ターゲット データベースを更新するか、削除して再作成するかを指定します。
|**/p:**|DatabaseEdition=({Basic&#124;Standard&#124;Premium&#124;DataWarehouse&#124;GeneralPurpose&#124;BusinessCritical&#124;Hyperscale&#124;Default} 'Default')|Azure SQL Database のエディションを定義します。|
|**/p:**|DatabaseLockTimeout=(INT32 '60')| SQLServer に対してクエリを実行するときのデータベース ロックのタイムアウトを秒単位で指定します。 無期限に待機するには、-1 を使用します。|
|**/p:**|DatabaseMaximumSize=(INT32)|Azure SQL Database の最大サイズを GB 単位で定義します。
|**/p:**|DatabaseServiceObjective=(STRING)|Azure SQL Database のパフォーマンス レベル ("P0" や "S1" など) を定義します。
|**/p:**|DeployDatabaseInSingleUserMode=(BOOLEAN)|true の場合、データベースは配置前にシングル ユーザー モードに設定されます。
|**/p:**|DisableAndReenableDdlTriggers=(BOOLEAN 'True')| 公開プロセスの開始時にデータ定義言語 (DDL) トリガーを無効にして、公開操作の最後に再度有効にするかどうかを指定します。|
|**/p:**|DoNotAlterChangeDataCaptureObjects=(BOOLEAN 'True')|true の場合、Change Data Capture オブジェクトは変更されません。
|**/p:**|DoNotAlterReplicatedObjects=(BOOLEAN 'True')|レプリケートされたオブジェクトが、検証時に識別されるかどうかを指定します。
|**/p:**|DoNotDropObjectType=(STRING)|DropObjectsNotInSource が true の場合、削除されないオブジェクトの種類。 有効なオブジェクトの種類名は、Aggregates、ApplicationRoles、Assemblies、AsymmetricKeys、BrokerPriorities、Certificates、ColumnEncryptionKeys、ColumnMasterKeys、Contracts、DatabaseRoles、DatabaseTriggers、Defaults、ExtendedProperties、ExternalDataSources、ExternalFileFormats、ExternalTables、Filegroups、FileTables、FullTextCatalogs、FullTextStoplists、MessageTypes、PartitionFunctions、PartitionSchemes、Permissions、Queues、RemoteServiceBindings、RoleMembership、Rules、ScalarValuedFunctions、SearchPropertyLists、SecurityPolicies、Sequences、Services、Signatures、StoredProcedures、SymmetricKeys、Synonyms、Tables、TableValuedFunctions、UserDefinedDataTypes、UserDefinedTableTypes、ClrUserDefinedTypes、Users、Views、XmlSchemaCollections、Audits、Credentials、CryptographicProviders、DatabaseAuditSpecifications、DatabaseScopedCredentials、Endpoints、ErrorMessages、EventNotifications、EventSessions、LinkedServerLogins、LinkedServers、Logins、Routes、ServerAuditSpecifications、ServerRoleMembership、ServerRoles、ServerTriggers です。
|**/p:**|DoNotDropObjectTypes=(STRING)|DropObjectsNotInSource が true の場合に削除されない、オブジェクトの種類のセミコロン区切りリスト。 有効なオブジェクトの種類名は、Aggregates、ApplicationRoles、Assemblies、AsymmetricKeys、BrokerPriorities、Certificates、ColumnEncryptionKeys、ColumnMasterKeys、Contracts、DatabaseRoles、DatabaseTriggers、Defaults、ExtendedProperties、ExternalDataSources、ExternalFileFormats、ExternalTables、Filegroups、FileTables、FullTextCatalogs、FullTextStoplists、MessageTypes、PartitionFunctions、PartitionSchemes、Permissions、Queues、RemoteServiceBindings、RoleMembership、Rules、ScalarValuedFunctions、SearchPropertyLists、SecurityPolicies、Sequences、Services、Signatures、StoredProcedures、SymmetricKeys、Synonyms、Tables、TableValuedFunctions、UserDefinedDataTypes、UserDefinedTableTypes、ClrUserDefinedTypes、Users、Views、XmlSchemaCollections、Audits、Credentials、CryptographicProviders、DatabaseAuditSpecifications、DatabaseScopedCredentials、Endpoints、ErrorMessages、EventNotifications、EventSessions、LinkedServerLogins、LinkedServers、Logins、Routes、ServerAuditSpecifications、ServerRoleMembership、ServerRoles、ServerTriggers です。
|**/p:**|DropConstraintsNotInSource=(BOOLEAN 'True')|データベースへの公開時に、ターゲット データベースのデータベース スナップショット (.dacpac) ファイルに存在しない制約を削除するかどうかを指定します。|
|**/p:**|DropDmlTriggersNotInSource=(BOOLEAN 'True')|データベースへの公開時に、ターゲット データベースのデータベース スナップショット (.dacpac) ファイルに存在しない DML トリガーを削除するかどうかを指定します。|
|**/p:**|DropExtendedPropertiesNotInSource=(BOOLEAN 'True')|データベースに公開するとき、データベース スナップショット (.dacpac) ファイルに存在しない拡張プロパティをターゲット データベースから削除するかどうかを指定します。|
|**/p:**|DropIndexesNotInSource=(BOOLEAN 'True')|データベースへの公開時に、ターゲット データベースのデータベース スナップショット (.dacpac) ファイルに存在しないインデックスを削除するかどうかを指定します。|
|**/p:**|DropObjectsNotInSource=(BOOLEAN)|データベースへの公開時に、データベース スナップショット (.dacpac) ファイルに存在しないオブジェクトをターゲット データベースから削除するかどうかを指定します。 この値は DropExtendedProperties よりも優先されます。|
|**/p:**|DropPermissionsNotInSource=(BOOLEAN)|データベースへの更新の公開時に、ターゲット データベースのデータベース スナップショット (.dacpac) ファイルに存在しないアクセス許可を削除するかどうかを指定します。|
|**/p:**|DropRoleMembersNotInSource=(BOOLEAN)|データベースへの更新の公開時に、ターゲット データベースのデータベース スナップショット (.dacpac) ファイルで定義されていないロール メンバーを削除するかどうかを指定します。|
|**/p:**|DropStatisticsNotInSource=(BOOLEAN 'True')|データベースに公開するとき、データベース スナップショット (.dacpac) ファイルに存在しない統計をターゲット データベースから削除するかどうかを指定します。|
|**/p:**|ExcludeObjectType=(STRING)|配置時に無視するオブジェクトの種類。 有効なオブジェクトの種類名は、Aggregates、ApplicationRoles、Assemblies、AsymmetricKeys、BrokerPriorities、Certificates、ColumnEncryptionKeys、ColumnMasterKeys、Contracts、DatabaseRoles、DatabaseTriggers、Defaults、ExtendedProperties、ExternalDataSources、ExternalFileFormats、ExternalTables、Filegroups、FileTables、FullTextCatalogs、FullTextStoplists、MessageTypes、PartitionFunctions、PartitionSchemes、Permissions、Queues、RemoteServiceBindings、RoleMembership、Rules、ScalarValuedFunctions、SearchPropertyLists、SecurityPolicies、Sequences、Services、Signatures、StoredProcedures、SymmetricKeys、Synonyms、Tables、TableValuedFunctions、UserDefinedDataTypes、UserDefinedTableTypes、ClrUserDefinedTypes、Users、Views、XmlSchemaCollections、Audits、Credentials、CryptographicProviders、DatabaseAuditSpecifications、DatabaseScopedCredentials、Endpoints、ErrorMessages、EventNotifications、EventSessions、LinkedServerLogins、LinkedServers、Logins、Routes、ServerAuditSpecifications、ServerRoleMembership、ServerRoles、ServerTriggers です。
|**/p:**|ExcludeObjectTypes=(STRING)|配置時に無視するオブジェクトの種類のセミコロン区切りリスト。 有効なオブジェクトの種類名は、Aggregates、ApplicationRoles、Assemblies、AsymmetricKeys、BrokerPriorities、Certificates、ColumnEncryptionKeys、ColumnMasterKeys、Contracts、DatabaseRoles、DatabaseTriggers、Defaults、ExtendedProperties、ExternalDataSources、ExternalFileFormats、ExternalTables、Filegroups、FileTables、FullTextCatalogs、FullTextStoplists、MessageTypes、PartitionFunctions、PartitionSchemes、Permissions、Queues、RemoteServiceBindings、RoleMembership、Rules、ScalarValuedFunctions、SearchPropertyLists、SecurityPolicies、Sequences、Services、Signatures、StoredProcedures、SymmetricKeys、Synonyms、Tables、TableValuedFunctions、UserDefinedDataTypes、UserDefinedTableTypes、ClrUserDefinedTypes、Users、Views、XmlSchemaCollections、Audits、Credentials、CryptographicProviders、DatabaseAuditSpecifications、DatabaseScopedCredentials、Endpoints、ErrorMessages、EventNotifications、EventSessions、LinkedServerLogins、LinkedServers、Logins、Routes、ServerAuditSpecifications、ServerRoleMembership、ServerRoles、ServerTriggers です。
|**/p:**|GenerateSmartDefaults=(BOOLEAN)|null 値が許可されない列を含むデータが格納されているテーブルを更新する際に、自動的に既定値が設定されます。
|**/p:**|IgnoreAnsiNulls=(BOOLEAN 'True')|データベースに公開するとき、ANSI NULLS 設定の相違を無視するか、更新するかを指定します。
|**/p:**|IgnoreAuthorizer=(BOOLEAN)|データベースに公開するとき、Authorizer の相違を無視するか、更新するかを指定します。
|**/p:**|IgnoreColumnCollation=(BOOLEAN)|データベースに公開するとき、列の照合順序の相違を無視するか、更新するかを指定します。
|**/p:**|IgnoreColumnOrder=(BOOLEAN)|データベースに公開するときに、テーブル列の順序の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreComments=(BOOLEAN)|データベースに公開するとき、コメントの相違を無視するか、更新するかを指定します。
|**/p:**|IgnoreCryptographicProviderFilePath=(BOOLEAN 'True')|データベースに公開するとき、暗号化プロバイダーのファイル パスの相違を無視するか、更新するかを指定します。
|**/p:**|IgnoreDdlTriggerOrder=(BOOLEAN)|データベースまたはサーバーに公開するとき、Data Definition Language (DDL) triggers の順序の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreDdlTriggerState=(BOOLEAN)|データベースに公開するとき、Data Definition Language (DDL) triggers の有効または無効にされた状態の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreDefaultSchema=(BOOLEAN)|データベースに公開するとき、既定のスキーマの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreDmlTriggerOrder=(BOOLEAN)|データベースに公開するとき、Data Manipulation Language (DML) triggers の順序の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreDmlTriggerState=(BOOLEAN)|データベースに公開するとき、DML triggers の有効または無効にされた状態の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreExtendedProperties=(BOOLEAN)|データベースに公開するとき、拡張プロパティの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreFileAndLogFilePath=(BOOLEAN 'True')|データベースに公開するとき、ファイルおよびログ ファイルのパスの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreFilegroupPlacement=(BOOLEAN 'True')|データベースに公開するとき、FILEGROUP 内のオブジェクトの位置の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreFileSize=(BOOLEAN 'True')|データベースに公開するとき、ファイル サイズの相違を無視するか、または警告を発するかを指定します。|
|**/p:**|IgnoreFillFactor=(BOOLEAN 'True')|公開時に、インデックス格納の FILL FACTOR の相違を無視するか、または警告を発するかを指定します。|
|**/p:**|IgnoreFullTextCatalogFilePath=(BOOLEAN 'True')|データベースに公開するとき、フルテキスト カタログのファイル パスの相違を無視するか、または警告を発するかを指定します。|
|**/p:**|IgnoreIdentitySeed=(BOOLEAN)|データベースに公開するとき、Identity 列のシードの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreIncrement=(BOOLEAN)|データベースに公開するとき、Identity 列のインクリメントの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreIndexOptions=(BOOLEAN)|データベースに公開するとき、インデックス オプションの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreIndexPadding=(BOOLEAN 'True')|データベースに公開するとき、インデックス パディングの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreKeywordCasing=(BOOLEAN 'True')|データベースに公開するとき、キーワードの大文字と小文字の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreLockHintsOnIndexes=(BOOLEAN)|データベースに公開するとき、インデックスのロック ヒントの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreLoginSids=(BOOLEAN 'True')| データベースに公開するとき、セキュリティ ID 番号 (SID) の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreNotForReplication=(BOOLEAN)|データベースに公開するとき、レプリケーションでは使わない設定を無視するか、更新するかを指定します。|
|**/p:**|IgnoreObjectPlacementOnPartitionScheme=(BOOLEAN 'True')|データベースに公開するとき、パーティション構成でのオブジェクトの位置を無視するか、更新するかを指定します。|
|**/p:**|IgnorePartitionSchemes=(BOOLEAN)|データベースに公開するとき、パーティション構成と関数の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnorePermissions=(BOOLEAN)|データベースに公開するとき、権限の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreQuotedIdentifiers=(BOOLEAN 'True')|データベースに公開するとき、引用符で囲まれた識別子の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreRoleMembership=(BOOLEAN)|データベースへの公開時に、ログインのロール メンバーシップの相違を無視するか更新するかを指定します。|
|**/p:**|IgnoreRouteLifetime=(BOOLEAN 'True')|データベースへの公開時に、SQL Server がルーティング テーブルにルートを保持する時間の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreSemicolonBetweenStatements=(BOOLEAN 'True')|データベースに公開するとき、T-SQL ステートメント間のセミコロンの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreTableOptions=(BOOLEAN)|データベースに公開するとき、テーブル オプションの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreTablePartitionOptions=(BOOLEAN)|データベースに公開するとき、テーブル パーティション オプションの相違を無視するか、更新するかを指定します。  このオプションは、Azure Synapse Analytics データ ウェアハウス データベースにのみ適用されます。|
|**/p:**|IgnoreUserSettingsObjects=(BOOLEAN)|データベースに公開するとき、ユーザー設定オブジェクトの相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreWhitespace=(BOOLEAN 'True')|データベースに公開するとき、空白の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreWithNocheckOnCheckConstraints=(BOOLEAN)|公開時に、CHECK 制約の WITH NOCHECK 句の値の相違を無視するか、更新するかを指定します。|
|**/p:**|IgnoreWithNocheckOnForeignKeys=(BOOLEAN)|データベースに公開するとき、外部キーの WITH NOCHECK 句の値の相違を無視するか、更新するかを指定します。|
|**/p:**|IncludeCompositeObjects=(BOOLEAN)|単一の公開操作の一部としてすべての複合要素を含めます。|
|**/p:**|IncludeTransactionalScripts=(BOOLEAN)|データベースに公開するとき、可能であればトランザクション ステートメントを使用するかどうかを指定します。|
|**/p:**|LongRunningCommandTimeout=(INT32)| SQL Server に対してクエリを実行するときの実行時間の長いコマンドのタイムアウトを秒単位で指定します。 無期限に待機するには、0 を使用します。|
|**/p:**|NoAlterStatementsToChangeClrTypes=(BOOLEAN)|公開で、相違がある場合に ALTER ASSEMBLY ステートメントを発行するのではなく、常にアセンブリを削除して再作成することを指定します。|
|**/p:**|PopulateFilesOnFileGroups=(BOOLEAN 'True')|ターゲット データベースで新しい FileGroup が作成されたときに新しいファイルも作成するかどうかを指定します。|
|**/p:**|RegisterDataTierApplication=(BOOLEAN)|スキーマがデータベース サーバーに登録されるかどうかを指定します。|
|**/p:**|RunDeploymentPlanExecutors=(BOOLEAN)|他の操作が実行されるときに DeploymentPlanExecutor コントリビューターを実行する必要があるかどうかを指定します。|
|**/p:**|ScriptDatabaseCollation=(BOOLEAN)|データベースに公開するとき、データベース照合順序の相違を無視するか、更新するかを指定します。|
|**/p:**|ScriptDatabaseCompatibility=(BOOLEAN)|データベースに公開するとき、データベース互換性の相違を無視するか、更新するかを指定します。|
|**/p:**|ScriptDatabaseOptions=(BOOLEAN 'True')|ターゲット データベースのプロパティを公開操作の一部として設定するか、更新するかを指定します。|
|**/p:**|ScriptDeployStateChecks=(BOOLEAN)|データベース名とサーバー名がデータベース プロジェクトで指定された名前と一致していることを確認するステートメントを公開スクリプトで生成するかどうかを指定します。|
|**/p:**|ScriptFileSize=(BOOLEAN)|ファイル グループにファイルを追加するときに、サイズを指定するかどうかを制御します。|
|**/p:**|ScriptNewConstraintValidation=(BOOLEAN 'True')|公開の最後にすべての制約が 1 つのセットとして検証されるため、公開の途中でチェックまたは外部キー制約によってデータ エラーが発生することを回避できます。 False に設定すると、対応するデータを確認せずに制約が公開されます。|
|**/p:**|ScriptRefreshModule=(BOOLEAN 'True')|公開スクリプトの末尾に更新ステートメントを追加します。|
|**/p:**|Storage=({File&#124;Memory})|データベース モデルの構築時に要素をどのように格納するかを指定します。 パフォーマンス上の理由から、既定値は InMemory です。 大規模なデータベースの場合は、File バックアップ ストレージが必要です。|
|**/p:**|TreatVerificationErrorsAsWarnings=(BOOLEAN)|公開の検証中に発生したエラーを警告として扱うかどうかを指定します。 配置計画をターゲット データベースに対して実行する前に、生成された配置計画がチェックされます。 計画の検証では、変更を加えるためには取り除く必要のある、ターゲットのみのオブジェクト (インデックスなど) の損失などの問題が検出されます。 また、複合プロジェクトへの参照のためテーブルやビューなどに依存関係が存在するのに、その関係がターゲット データベースに存在しない状況も検出されます。 すべての問題の完全な一覧を取得するには、最初のエラー発生時に公開アクションを停止するのではなく、この方法を使用することをお勧めします。|
|**/p:**|UnmodifiableObjectWarnings=(BOOLEAN 'True')|修正できない相違がオブジェクトで見つかった場合 (たとえば、同じファイルのファイル サイズまたはファイル パスが異なる場合) に警告を生成するかどうかを指定します。|
|**/p:**|VerifyCollationCompatibility=(BOOLEAN 'True')|照合順序の互換性を検証するかどうかを指定します。
|**/p:**|VerifyDeployment=(BOOLEAN 'True')|公開前にチェックを実行して、正常な公開をブロックする可能性のある問題が存在する場合は公開操作を停止するかどうかを指定します。 たとえば、ターゲット データベースの外部キーがデータベース プロジェクトに存在せず、公開時にエラーが発生する場合は、公開操作が停止することがあります。|

## <a name="next-steps"></a>次の手順

- [sqlpackage](sqlpackage.md) について詳しく学習する
