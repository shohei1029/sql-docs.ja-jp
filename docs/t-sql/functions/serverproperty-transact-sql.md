---
description: SERVERPROPERTY (Transact-SQL)
title: SERVERPROPERTY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/28/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SERVERPROPERTY_TSQL
- SERVERPROPERTY
- sql13.swb.serverpropeties.connections.f1
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- SERVERPROPERTY function
- server instance property information [SQL Server]
- IsHadrEnabled server property
- instances of SQL Server, property information
- server properties [SQL Server]
ms.assetid: 11e166fa-3dd2-42d8-ac4b-04f18c612c4a
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c5572f1d3e85ed5e9efc2006dabbddddfc0c3b20
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747942"
---
# <a name="serverproperty-transact-sql"></a>SERVERPROPERTY (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

サーバー インスタンスについてのプロパティ情報を返します。  

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
SERVERPROPERTY ( 'propertyname' )  
```  

> [!IMPORTANT]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] の [!INCLUDE[ssde_md](../../includes/ssde_md.md)] バージョン番号は類似のものではありません。別個の製品に与えられる内部製造番号を表しています。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] の [!INCLUDE[ssde_md](../../includes/ssde_md.md)] は、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] と同じコード ベースに基づいています。 最も重要なのは、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] の [!INCLUDE[ssde_md](../../includes/ssde_md.md)] には常に最新の SQL [!INCLUDE[ssde_md](../../includes/ssde_md.md)] ビットがあることです。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] のバージョン 12 は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のバージョン 15 よりも新しいものです。

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

*propertyname*  
サーバーについて、返されるプロパティ情報を含む式を指定します。 *propertyname* 値は次のいずれかを指定することができます。  
  
|プロパティ|返される値|  
|--------------|---------------------|  
|BuildClrVersion|[!INCLUDE[msCoName](../../includes/msconame-md.md)] インスタンスの構築時に使用された [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 共通言語ランタイム (CLR) のバージョン。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **nvarchar(128)**|  
|照合順序|サーバーの既定の照合順序の名前。<br /><br /> NULL = 無効な入力またはエラー。<br /><br /> 基本データ型: **nvarchar(128)**|  
|CollationID|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 照合順序の ID。<br /><br /> 基本データ型: **int**|  
|ComparisonStyle|照合順序の Windows 比較形式。<br /><br /> 基本データ型: **int**|  
|ComputerNamePhysicalNetBIOS|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスが現在実行されているローカル コンピューターの NetBIOS 名。<br /><br /> フェールオーバー クラスターの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クラスター化インスタンスでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスがフェールオーバー クラスターの他のノードにフェールオーバーすると、この値が変わります。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] スタンドアロン インスタンスではこの値は変わらず、MachineName プロパティと同じ値が返されます。<br /><br /> **注:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスがフェールオーバー クラスター内にある場合に、フェールオーバー クラスター インスタンスの名前を取得するには、MachineName プロパティを使用します。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **nvarchar(128)**|  
|Edition|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのインストールされている製品のエディション。 機能や制限 ([SQL Server のエディション別の計算容量制限](../../sql-server/compute-capacity-limits-by-edition-of-sql-server.md)など) は、このプロパティの値を使用して調べます。 このバージョン (64 ビット) には、64 ビット バージョンの[!INCLUDE[ssDE](../../includes/ssde-md.md)]が追加されています。<br /><br /> 戻り値:<br /><br /> 'Enterprise Edition'<br /><br /> 'Enterprise Edition:Core-based Licensing'<br /><br /> 'Enterprise Evaluation Edition'<br /><br /> 'Business Intelligence Edition'<br /><br /> 'Developer Edition'<br /><br /> 'Express Edition'<br /><br /> 'Express Edition with Advanced Services'<br /><br /> 'Standard Edition'<br /><br /> 'Web Edition'<br /><br /> 'SQL Azure' は、[!INCLUDE[ssSDS](../../includes/sssds-md.md)] または [!INCLUDE[ssDW](../../includes/ssdw-md.md)] を示します<br /><br /> 'Azure SQL Edge Developer' は、Azure SQL Edge の開発専用エディションを示します<br /><br /> 'Azure SQL Edge' は、Azure SQL Edge の有料エディションを示します<br /><br /> 基本データ型: **nvarchar(128)**|  
|EditionID|EditionID は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスについて、インストールされている製品のエディションを表します。 機能や制限 ([SQL Server のエディション別の計算容量制限](../../sql-server/compute-capacity-limits-by-edition-of-sql-server.md)など) は、このプロパティの値を使用して調べます。<br /><br /> 1804890536 = Enterprise<br /><br /> 1872460670 = Enterprise Edition:Core-based Licensing<br /><br /> 610778273= Enterprise Evaluation<br /><br /> 284895786 = Business Intelligence<br /><br /> -2117995310 = Developer<br /><br /> -1592396055 = Express<br /><br /> -133711905 = Express with Advanced Services<br /><br /> -1534726760 = Standard<br /><br /> 1293598313 = Web<br /><br /> 1674378470 = [!INCLUDE[ssSDS](../../includes/sssds-md.md)] または [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]<br /><br /> -1461570097 = Azure SQL Edge Developer <br /><br /> 1994083197 = Azure SQL Edge <br /><br />基本データ型: **bigint** 型|  
|EngineEdition|サーバーにインストールされている [!INCLUDE[ssDE](../../includes/ssde-md.md)] インスタンスの[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のエディション。<br /><br /> 1 = Personal または Desktop Engine ([!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降のバージョンでは使用できません)<br /><br /> 2 = Standard (Standard、Web、および Business Intelligence の場合に返されます)<br /><br /> 3 = Enterprise (Evaluation、Developer、Enterprise エディションの場合にこの値が返されます)<br /><br /> 4 = Express (Express、Express with Tools、Express with Advanced Services の場合に返されます)<br /><br /> 5 = [!INCLUDE[ssSDS](../../includes/sssds-md.md)]<br /><br /> 6 = [!INCLUDE[ssDW](../../includes/ssdw-md.md)]<br /><br /> 8 = [!INCLUDE[ssSDSMIfull](../../includes/sssdsmifull-md.md)]<br /><br /> 9 = Azure SQL Edge (Azure SQL Edge のすべてのエディションで返されます)<br /><br /> 11 = Azure Synapse サーバーレス SQL プール<br /><br /> 基本データ型: **int**|  
|FilestreamConfiguredLevel|FILESTREAM アクセスの構成されているレベル。 詳細については、「[filestream のアクセス レベル](../../database-engine/configure-windows/filestream-access-level-server-configuration-option.md)」を参照してください。<br /><br /> 基本データ型: **int**|  
|FilestreamEffectiveLevel|FILESTREAM アクセスの有効なレベル。 レベルが変更されており、インスタンスの再起動またはコンピューターの再起動のいずれかが保留されている場合、この値は FilestreamConfiguredLevel と異なることがあります。 詳細については、「[filestream のアクセス レベル](../../database-engine/configure-windows/filestream-access-level-server-configuration-option.md)」を参照してください。<br /><br /> 基本データ型: **int**|  
|FilestreamShareName|FILESTREAM で使用される共有の名前。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **nvarchar(128)**| 
|HadrManagerStatus|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] マネージャーが開始されたかどうかを示します。<br /><br /> 0 = 未開始状態、通信保留中<br /><br /> 1 = 開始されて実行中<br /><br /> 2 = 未開始状態、失敗<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**|  
|InstanceDefaultBackupPath|**適用対象**: [!INCLUDE[ssSQL2019](../../includes/sssql19-md.md)] 以降。<br /><br /> インスタンス バックアップ ファイルの既定のパスの名前。|  
|InstanceDefaultDataPath|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] から 2015 年後半以降の更新プログラムの現在のバージョン。<br /><br /> インスタンス データ ファイルの既定のパスの名前。<br /><br /> 基本データ型: **nvarchar(128)**|  
|InstanceDefaultLogPath|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] から 2015 年後半以降の更新プログラムの現在のバージョン。<br /><br /> インスタンス ログ ファイルの既定のパスの名前。<br /><br /> 基本データ型: **nvarchar(128)**|  
|InstanceName|ユーザーの接続先のインスタンス名。<br /><br /> インスタンス名が既定インスタンスの場合や、無効な入力またはエラーの場合は、NULL が返されます。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **nvarchar(128)**|  
|IsAdvancedAnalyticsInstalled|セットアップ時に Advanced Analytics 機能がインストールされた場合は 1 を返します。Advanced Analytics がインストールされていない場合は 0 を返します。<br /><br /> 基本データ型: **int**|  
|IsBigDataCluster| CU4 から始まった [!INCLUDE[ssSQL2019](../../includes/sssql19-md.md)] で導入されました。<br /><br />インスタンスが SQL Server ビッグ データ クラスターの場合は 1 を返し、ビッグ データ クラスターではない場合は 0 を返します。<br /><br /> 基本データ型: **int**|  
|IsClustered|サーバー インスタンスがフェールオーバー クラスターで構成されます。<br /><br /> 1 = クラスター化<br /><br /> 0 = 非クラスター化<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**|  
|IsFullTextInstalled|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の現在のインスタンスに、フルテキスト インデックス作成およびセマンティック インデックス作成のコンポーネントがインストールされています。<br /><br /> 1 = フルテキスト インデックス作成およびセマンティック インデックス作成のコンポーネントがインストールされています。<br /><br /> 0 = フルテキスト インデックス作成およびセマンティック インデックス作成のコンポーネントがインストールされていません。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**|  
|IsHadrEnabled|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]は、このサーバー インスタンス上で有効です。<br /><br /> 0 = [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]機能は無効です。<br /><br /> 1 = [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]機能は有効です。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスで可用性レプリカを作成して実行するには、サーバー インスタンス上で [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]が有効になっている必要があります。 詳細については、「[AlwaysOn 可用性グループの有効化と無効化 (SQL Server)](../../database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server.md)」を参照してください。<br /><br /> **注:** IsHadrEnabled プロパティは、[!INCLUDE[ssHADR](../../includes/sshadr-md.md)] のみに関連します。 データベース ミラーリングやログ配布などの、その他の高可用性機能およびディザスター リカバリー機能は、このサーバー プロパティの影響を受けません。|  
|IsIntegratedSecurityOnly|サーバーは統合セキュリティ モードです。<br /><br /> 1 = 統合セキュリティ (Windows 認証)<br /><br /> 0 = 非統合セキュリティ (Windows 認証と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証の両方)。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**|  
|IsLocalDB|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> サーバーは [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] LocalDB のインスタンスです。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**|  
|IsPolyBaseInstalled|**適用対象**: [!INCLUDE[ssSQL2016](../../includes/sssql16-md.md)]<br /><br /> サーバー インスタンスに PolyBase 機能がインストールされているかどうかを返します。<br /><br /> 0 = Polybase はインストールされていません。<br /><br /> 1 = Polybase はインストールされています。<br /><br /> 基本データ型: **int**|  
|IsSingleUser|サーバーはシングル ユーザー モードです。<br /><br /> 1 = シングル ユーザー<br /><br /> 0 = 非シングル ユーザー<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**|  
|IsTempDbMetadataMemoryOptimized|**適用対象**: [!INCLUDE[ssSQL2019](../../includes/sssql19-md.md)] 以降。<br /><br />メタデータに対してメモリ最適化テーブルを使用するように tempdb が有効になっている場合は、1 を返します。メタデータに対して通常のディスク ベースのテーブルが tempdb によって使用されている場合は 0 になります。 詳細については、「 [tempdb Database](../../relational-databases/databases/tempdb-database.md#memory-optimized-tempdb-metadata)」をご覧ください。<br /><br /> 基本データ型: **int**|  
|IsXTPSupported|**適用対象**:SQL Server ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降)、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。<br /><br /> サーバーは、インメモリ OLTP をサポートします。<br /><br /> 1 = サーバーはインメモリ OLTP をサポートします。<br /><br /> 0 = サーバーはインメモリ OLTP をサポートしません。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**|  
|LCID|照合順序の Windows ロケール識別子 (LCID)。<br /><br /> 基本データ型: **int**|  
|LicenseType|未使用。 ライセンス情報は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 製品によって保存または維持されていません。 常に DISABLED が返されます。<br /><br /> 基本データ型: **nvarchar(128)**|  
|MachineName|サーバー インスタンスが稼働している Windows コンピューターの名前。<br /><br /> クラスター化インスタンスの場合、つまり、Microsoft Cluster Service 上の仮想サーバーで稼働している [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスの場合は、仮想サーバーの名前が返されます。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **nvarchar(128)**|  
|NumLicenses|未使用。 ライセンス情報は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 製品によって保存または維持されていません。 常に NULL が返されます。<br /><br /> 基本データ型: **int**|  
|ProcessID|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスのプロセス ID。 このインスタンスに属する Sqlservr.exe を識別するときに、ProcessID は効果的です。<br /><br /> NULL = 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**|  
|ProductBuild|**適用対象**: 2015 年 10 月以降の [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]。<br /><br /> ビルド番号です。<br /><br /> 基本データ型: **nvarchar(128)**|  
|ProductBuildType|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] から 2015 年後半以降の更新プログラムの現在のバージョン。<br /><br /> 現在のビルドのビルドの種類です。<br /><br /> 次のいずれかを返します。<br /><br /> OD = 特定の顧客のオンデマンド リリース。<br /><br /> GDR = Windows Update を介してリリースされた一般配布リリース。<br /><br /> NULL = 適用なし。<br /><br /> 基本データ型: **nvarchar(128)**|  
|ProductLevel|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのバージョンのレベルです。<br /><br /> 次のいずれかを返します。<br /><br /> 'RTM' = オリジナル リリース バージョン<br /><br /> 'SP *n*' = サービス パックのバージョン<br /><br /> 'CTP *n*', = Community Technology Preview のバージョン<br /><br /> 基本データ型: **nvarchar(128)**|  
|ProductMajorVersion|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] から 2015 年後半以降の更新プログラムの現在のバージョン。<br /><br /> メジャー バージョン。<br /><br /> 基本データ型: **nvarchar(128)**|  
|ProductMinorVersion|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] から 2015 年後半以降の更新プログラムの現在のバージョン。<br /><br /> マイナー バージョン。<br /><br /> 基本データ型: **nvarchar(128)**|  
|ProductUpdateLevel|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] から 2015 年後半以降の更新プログラムの現在のバージョン。<br /><br /> 現在のビルドのレベルを更新します。 CU は累積更新を示します。<br /><br /> 次のいずれかを返します。<br /><br /> CU *n* = 累積更新プログラム<br /><br /> NULL = 適用なし。<br /><br /> 基本データ型: **nvarchar(128)**|  
|ProductUpdateReference|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] から 2015 年後半以降の更新プログラムの現在のバージョン。<br /><br /> そのリリースのサポート技術情報の記事です。<br /><br /> 基本データ型: **nvarchar(128)**|  
|ProductVersion|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのバージョンです ('*major.minor.build.revision*' 形式)。<br /><br /> 基本データ型: **nvarchar(128)**|  
|ResourceLastUpdateDateTime|リソース データベースが最後に更新された日時を返します。<br /><br /> 基本データ型: **datetime**|  
|ResourceVersion|リソース データベースのバージョンを返します。<br /><br /> 基本データ型: **nvarchar(128)**|  
|ServerName|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の特定のインスタンスに関連付けられている Windows サーバーとインスタンスの情報。<br /><br /> NULL = 無効な入力またはエラー。<br /><br /> 基本データ型: **nvarchar(128)**|  
|SqlCharSet|照合順序 ID の SQL 文字セット ID。<br /><br /> 基本データ型: **tinyint**|  
|SqlCharSetName|照合順序の SQL 文字セットの名前。<br /><br /> 基本データ型: **nvarchar(128)**|  
|SqlSortOrder|照合順序の SQL 並べ替え順序 ID。<br /><br /> 基本データ型: **tinyint**|  
|SqlSortOrderName|照合順序の SQL 並べ替え順序の名前。<br /><br /> 基本データ型: **nvarchar(128)**|  
  
## <a name="return-types"></a>戻り値の型  

**sql_variant**
  
## <a name="remarks"></a>解説  
  
### <a name="servername-property"></a>ServerName プロパティ

`SERVERPROPERTY` 関数の `ServerName` プロパティと [@@SERVERNAME](../../t-sql/functions/servername-transact-sql.md) は、同様の情報を返します。 `ServerName` プロパティが返す Windows サーバーとインスタンス名を合わせることで、一意なサーバー インスタンスが形成されます。 [@@SERVERNAME](../../t-sql/functions/servername-transact-sql.md) は、現在構成されているローカル サーバー名を返します。  
  
`ServerName` プロパティと [@@SERVERNAME](../../t-sql/functions/servername-transact-sql.md) は、インストール時の既定のサーバー名が変更されていない場合は、同じ情報を返します。 ローカル サーバー名は次のストアド プロシージャを実行することによって構成できます。  
  
```sql
EXEC sp_dropserver 'current_server_name';  
GO  
EXEC sp_addserver 'new_server_name', 'local';  
GO  
```  
  
ローカル サーバー名がインストール時の既定のサーバーから変更されている場合、[@@SERVERNAME](../../t-sql/functions/servername-transact-sql.md) は新しい名前を返します。  
  
### <a name="version-properties"></a>バージョンのプロパティ

`SERVERPROPERTY` 関数は、バージョン情報に関連するプロパティを個別に返します。一方、[@@VERSION](../../t-sql/functions/version-transact-sql-configuration-functions.md) 関数は、出力を 1 つの文字列に結合します。 アプリケーションで個々のプロパティ文字列が必要な場合は、[@@VERSION](../../t-sql/functions/version-transact-sql-configuration-functions.md) の結果を解析する代わりに `SERVERPROPERTY` 関数を使用して個々のプロパティ文字列を取得できます。  

## <a name="permissions"></a>アクセス許可

すべてのユーザーは、サーバーのプロパティをクエリできます。
  
## <a name="examples"></a>例

次の例では、`SELECT` ステートメント内で `SERVERPROPERTY` 関数を使用することによって、[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] の現在のインスタンスに関する情報を返します。
  
```sql
SELECT  
  SERVERPROPERTY('MachineName') AS ComputerName,
  SERVERPROPERTY('ServerName') AS InstanceName,  
  SERVERPROPERTY('Edition') AS Edition,
  SERVERPROPERTY('ProductVersion') AS ProductVersion,  
  SERVERPROPERTY('ProductLevel') AS ProductLevel;  
GO  
```  
  
## <a name="see-also"></a>参照

[SQL Server 2016 のエディションとコンポーネント](../../sql-server/editions-and-components-of-sql-server-2016.md)  
