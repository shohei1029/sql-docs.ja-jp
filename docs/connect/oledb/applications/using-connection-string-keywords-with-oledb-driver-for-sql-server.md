---
title: OLE DB Driver での接続文字列キーワードの使用
description: OLE DB Driver for SQL Server API の一部では、接続文字列が使用されます。これは、特定の接続属性を識別するキーワードと値の一覧です。
ms.custom: ''
ms.date: 09/30/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: vanto
ms.technology: connectivity
ms.topic: conceptual
f1_keywords:
- sql13.swb.connecttoserver.options.registeredservers.f1
helpviewer_keywords:
- data access [OLE DB Driver for SQL Server], connection string keywords
- MSOLEDBSQL, connection string keywords
- connection strings [OLE DB Driver for SQL Server]
- OLE DB Driver for SQL Server, connection string keywords
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: c5ddba7e6b2ac0faef93ff544dbb5f23f3fb5d15
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751202"
---
# <a name="using-connection-string-keywords-with-ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server での接続文字列キーワードの使用
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  一部の OLE DB Driver for SQL Server API では、接続文字列を使用して接続属性を指定します。 接続文字列はキーワードとそれに関連する値のリストです。各キーワードで特定の接続属性を識別します。  
  
> [!NOTE]
> OLE DB Driver for SQL Server では、旧バージョンとの互換性を維持するために接続文字列のあいまいさが許可されます (たとえば、キーワードを複数回指定したり、位置や優先順位に基づいた解決方法を使用して、競合するキーワードを指定したりすることができます)。 OLE DB Driver for SQL Server の今後のリリースでは、接続文字列のあいまいさが許容されなくなる可能性があります。 OLE DB Driver for SQL Server を使用するアプリケーションでは、あいまいな接続文字列を利用しないように変更することをお勧めします。  
  
 次のセクションでは、OLE DB Driver for SQL Server をデータ プロバイダーとして使用するときに、OLE DB Driver for SQL Server および ADO (ActiveX Data Objects) と共に使用できるキーワードについて説明します。  

## <a name="ole-db-driver-connection-string-keywords"></a>OLE DB Driver の接続文字列キーワード  

 OLE DB アプリケーションでデータ ソース オブジェクトを初期化する方法は 2 つあります。  
  
-   **IDBInitialize::Initialize**  
  
-   **IDataInitialize::GetDataSource**  
  
 最初の方法では、DBPROPSET_DBINIT プロパティ セットのプロパティ DBPROP_INIT_PROVIDERSTRING を設定することにより、プロバイダー文字列で接続プロパティを初期化できます。 2 番目の方法では、**IDataInitialize::GetDataSource** メソッドに初期化文字列を渡して、接続プロパティを初期化できます。 いずれの方法でも同一の OLE DB 接続プロパティが初期化されますが、使用されるキーワードのセットは異なります。 **IDataInitialize::GetDataSource** で使用されるキーワードのセットには、少なくとも初期化プロパティ グループ内のプロパティの説明が含まれます。  
  
 対応する OLE DB プロパティを持つプロバイダー文字列の設定は一部の既定値に設定されるか、値に明示的に設定され、OLE DB プロパティ値はプロバイダー文字列の設定をオーバーライドします。  
  
 DBPROP_INIT_PROVIDERSTRING 値からプロバイダー文字列内に設定するブール型のプロパティは、値 `yes` と `no` を使用して設定します。 **IDataInitialize::GetDataSource** を使用して初期化文字列内に設定するブール型のプロパティは、値 `true` と `false` を使用して設定します。  
  
 **IDataInitialize::GetDataSource** を使用するアプリケーションでは、既定値を持たないプロパティに対してのみ、**IDBInitialize::Initialize** で用いられるキーワードも利用できます。 **IDataInitialize::GetDataSource** キーワードと **IDBInitialize::Initialize** キーワードの両方を初期化文字列で使用すると、**IDataInitialize::GetDataSource** キーワードの設定が利用されます。 この動作は今後のリリースで維持されない可能性があるため、**IDataInitialize:GetDataSource** 接続文字列に **IDBInitialize::Initialize** キーワードを使用しないことをお勧めします。  
  
> [!NOTE]  
>  **IDataInitialize::GetDataSource** を介して渡される接続文字列は、プロパティに変換され、**IDBProperties::SetProperties** で適用されます。 コンポーネント サービスによって **IDBProperties::GetPropertyInfo** でプロパティ説明が検出された場合、このプロパティはスタンドアロンのプロパティとして適用されます。 それ以外の場合は、DBPROP_PROVIDERSTRING プロパティを介して適用されます。 たとえば、接続文字列 **Data Source=server1;Server=server2** を指定する場合、**Data Source** はプロパティとして設定されますが、**Server** はプロバイダー文字列になります。  
  
 同じプロバイダー固有のプロパティのインスタンスを複数指定した場合、最初のプロパティの最初の値が使用されます。  

## <a name="using-idbinitializeinitialize"></a>Using IDBInitialize::Initialize

 **IDBInitialize::Initialize** と共に DBPROP_INIT_PROVIDERSTRING を利用する OLE DB アプリケーションで使用される接続文字列の構文は次のとおりです。  
  
 - `connection-string ::= empty-string[;] | attribute[;] | attribute; connection-string`  
  
 - `empty-string ::=`  
  
 - `attribute ::= attribute-keyword=[{]attribute-value[}]`  
  
 - `attribute-value ::= character-string`  
  
 - `attribute-keyword ::= identifier`  
  
 属性値は必要に応じて中かっこで囲むことができます。常に中かっこを使用すると、 属性値に英数字以外の文字が含まれる場合の問題を回避できます。 最初の右中かっこが値の終わりと見なされるため、値に右中かっこを含めることはできません。  
  
 接続文字列キーワードの `=` 記号の後のスペース文字は、値を引用符で囲んだ場合でも、リテラルとして解釈されます。  
  
 次の表に、DBPROP_INIT_PROVIDERSTRING と共に使用できるキーワードを示します。  
  
|Keyword|初期化プロパティ|説明|  
|-------------|-----------------------------|-----------------|  
|**Addr**|SSPROP_INIT_NETWORKADDRESS|**Address** のシノニム。|  
|**アドレス**|SSPROP_INIT_NETWORKADDRESS|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスを実行しているサーバーのネットワーク アドレス。 **Address** は、通常、サーバーのネットワーク名ですが、パイプ、IP アドレス、または TCP/IP ポートとソケット アドレスなど、他の名前を指定してもかまいません。<br /><br /> IP アドレスを指定する場合は、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 構成マネージャーで TCP/IP または名前付きパイプ プロトコルが有効になっていることを確認します。<br /><br /> OLE DB Driver for SQL Server を使用する場合、**Address** の値が、接続文字列で **Server** に渡される値より優先されます。 `Address=;` の場合は、**Server** キーワードで指定されているサーバーに接続されますが、`Address= ;, Address=.;`、`Address=localhost;`、および `Address=(local);` の場合はすべて、ローカル サーバーに接続されることにも注意してください。<br /><br /> **Address** キーワードの完全な構文は次のとおりです。<br /><br /> [_protocol_ **:** ]_Address_[ **,** _port &#124;\pipe\pipename_]<br /><br /> _protocol_ には、 **tcp** (TCP/IP)、 **lpc** (共有メモリ)、または **np** (名前付きパイプ) を指定できます。 プロトコルの詳細については、「[クライアント プロトコルの構成](../../../database-engine/configure-windows/configure-client-protocols.md)」を参照してください。<br /><br /> _protocol_ も **Network** キーワードも指定されない場合、OLE DB Driver for SQL Server では [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 構成マネージャーで指定されたプロトコルの順序が使用されます。<br /><br /> *port* は、指定したサーバー上の接続先のポートです。 既定では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] はポート 1433 を使用します。|   
|**APP**|SSPROP_INIT_APPNAME|アプリケーションを識別する文字列。|  
|**ApplicationIntent**|SSPROP_INIT_APPLICATIONINTENT|アプリケーションがサーバーに接続するときのワークロードのタイプを宣言します。 設定可能な値は `ReadOnly` および `ReadWrite` です。<br /><br /> 既定では、 `ReadWrite`です。 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] の OLE DB Driver for SQL Server によるサポートの詳細については、「[OLE DB Driver for SQL Server の高可用性、ディザスター リカバリーに関するサポート](../features/oledb-driver-for-sql-server-support-for-high-availability-disaster-recovery.md)」を参照してください。|  
|**AttachDBFileName**|SSPROP_INIT_FILENAME|アタッチできるデータベースのプライマリ ファイルの名前 (完全なパス名を含む)。 **AttachDBFileName** を使用するには、プロバイダー文字列の Database キーワードでデータベース名を指定する必要もあります。 データベースが以前にアタッチされていた場合、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] により再アタッチされることはありません (アタッチされたデータベースがその接続の既定のデータベースとして使用されます)。|  
|**Authentication**<a href="#table1_1"><sup id="table1_authmode">**1**</sup></a>|SSPROP_AUTH_MODE|使用される SQL または Active Directory 認証を指定します。 有効な値は次のとおりです。<br/><ul><li>`(not set)`:他のキーワードによって決定される認証モード。</li><li>`ActiveDirectoryPassword:` Azure Active Directory の ID によるユーザー ID とパスワードの認証。</li><li>`ActiveDirectoryIntegrated:` Azure Active Directory の ID による統合認証。</li><br/>**注:** `ActiveDirectoryIntegrated` キーワードは、SQL Server に対する Windows 認証でも使用できます。 これは、`Integrated Security` (または `Trusted_Connection`) 認証キーワードに代わるものです。 `Integrated Security` (または `Trusted_Connection`) キーワードまたはそれに対応するプロパティを使用するアプリケーションで、`Authentication` キーワード (またはそれに対応するプロパティ) の値を `ActiveDirectoryIntegrated` に設定して、新しい暗号化および証明書の検証の動作を有効にすることを **お勧め** します。<br/><br/><li>`ActiveDirectoryInteractive:` Azure Active Directory の ID による対話型認証。 この方法では、Azure Multi-Factor Authentication (MFA) がサポートされます。 </li><li>`ActiveDirectoryMSI:` [マネージド ID (MSI)](/azure/active-directory/managed-identities-azure-resources/overview) の認証。 ユーザー割り当て ID の場合、ユーザー ID はユーザー ID のオブジェクト ID に設定する必要があります。</li><li>`ActiveDirectoryServicePrincipal:` Azure Active Directory サービス プリンシパルを使用した認証。 ユーザー ID はアプリケーション (クライアント) ID に設定する必要があります。 パスワードはアプリケーション (クライアント) シークレットに設定する必要があります。</li><li>`SqlPassword:` ユーザー ID とパスワードを使用した認証。</li><br/>**注:** `SQL Server` 認証を使用するアプリケーションで、`Authentication` キーワード (またはそれに対応するプロパティ) の値を `SqlPassword` に設定して、[新しい暗号化および証明書の検証の動作](../features/using-azure-active-directory.md#encryption-and-certificate-validation)を有効にすることを **お勧め** します。</ul>|
|**自動翻訳**|SSPROP_INIT_AUTOTRANSLATE|**AutoTranslate** のシノニム。|  
|**AutoTranslate**|SSPROP_INIT_AUTOTRANSLATE|OEM/ANSI 文字の変換を構成します。 認識される値は `yes` と `no` です。|  
|**[データベース]**|DBPROP_INIT_CATALOG|データベース名です。|  
|**DataTypeCompatibility**|SSPROP_INIT_DATATYPECOMPATIBILITY|使用するデータ型処理モードを指定します。 認識される値は、プロバイダー データ型を示す `0` と SQL Server 2000 データ型を示す `80` です。|  
|**Encrypt**<a href="#table1_1"><sup>**1**</sup></a>|SSPROP_INIT_ENCRYPT|データをネットワークに送信する前に暗号化するかどうかを指定します。 設定可能な値は `yes` および `no` です。 既定値は `no` です。|  
|**FailoverPartner**|SSPROP_INIT_FAILOVERPARTNER|データベース ミラーリングに使用するフェールオーバー サーバーの名前。|  
|**FailoverPartnerSPN**|SSPROP_INIT_FAILOVERPARTNERSPN|フェールオーバー パートナーの SPN。 既定値は空の文字列です。 空の文字列を使用すると、OLE DB Driver for SQL Server ではプロバイダーが生成した SPN が既定値として使用されます。|  
|**Language**|SSPROPT_INIT_CURRENTLANGUAGE|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 言語。|  
|**MarsConn**|SSPROP_INIT_MARSCONNECTION|その接続で MARS (複数のアクティブな結果セット) を有効または無効にします ([!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] 以降のサーバーの場合)。 設定可能な値は `yes` および `no` です。 既定値は `no` です。|  
|**MultiSubnetFailover**|SSPROP_INIT_MULTISUBNETFAILOVER|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 可用性グループまたは [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] フェールオーバー クラスター インスタンスの可用性グループ リスナーに接続する際には、必ず **MultiSubnetFailover=Yes** を指定してください。 **MultiSubnetFailover=Yes** の場合、(現在) アクティブなサーバーをより迅速に検出し、接続するように OLE DB Driver for SQL Server が構成されます。 設定可能な値は `Yes` および `No` です。 既定では、 `No`です。 次に例を示します。<br /><br /> `MultiSubnetFailover=Yes`<br /><br /> [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] の OLE DB Driver for SQL Server によるサポートの詳細については、「[OLE DB Driver for SQL Server の高可用性、ディザスター リカバリーに関するサポート](../../oledb/features/oledb-driver-for-sql-server-support-for-high-availability-disaster-recovery.md)」を参照してください。|  
|**Net**|SSPROP_INIT_NETWORKLIBRARY|**Network** のシノニム。|  
|**Network**|SSPROP_INIT_NETWORKLIBRARY|組織内の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスへの接続を確立するために使用するネットワーク ライブラリ。|  
|**Network Library**|SSPROP_INIT_NETWORKLIBRARY|**Network** のシノニム。|  
|**PacketSize**|SSPROP_INIT_PACKETSIZE|表形式データ ストリーム (TDS) パケット サイズ。 既定値は 0 です (実際の値はサーバーによって決定されます)。|  
|**PersistSensitive**|DBPROP_AUTH_PERSIST_SENSITIVE_AUTHINFO|文字列 `yes` と `no` を値として受け取ります。 `no` が使用される場合、データ ソース オブジェクトには機密の認証情報を保持できません|  
|**PWD**|DBPROP_AUTH_PASSWORD|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のログイン パスワード。|  
|**[サーバー]**|DBPROP_INIT_DATASOURCE|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスの名前。 ネットワーク上のサーバーの名前、IP アドレス、または [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 構成マネージャーの別名を指定する必要があります。<br /><br /> 指定されなかった場合は、ローカル コンピューター上にある既定のインスタンスに接続します。<br /><br /> **Address** キーワードは、**Server** キーワードをオーバーライドします。<br /><br /> 次のいずれかを指定することで、ローカル サーバー上の既定のインスタンスに接続できます。<br /><br /> **Server=;**<br /><br /> **Server=.;**<br /><br /> **Server=(local);**<br /><br /> **Server=(local);**<br /><br /> **Server=(localhost);**<br /><br /> **Server=(localdb)\\** *instancename* **;**<br /><br /> LocalDB のサポートの詳細については、「[LocalDB 用 OLE DB Driver for SQL Server のサポート](../../oledb/features/oledb-driver-for-sql-server-support-for-localdb.md)」を参照してください。<br /><br /> 名前付きインスタンスを指定する[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]、追加 **\\** _InstanceName_ します。<br /><br /> サーバーを指定しなかった場合は、ローカル コンピューター上にある既定のインスタンスに接続されます。<br /><br /> IP アドレスを指定する場合は、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 構成マネージャーで TCP/IP または名前付きパイプ プロトコルが有効になっていることを確認します。<br /><br /> **Server** キーワードの完全な構文は次のとおりです。<br /><br /> **Server=** ["_プロトコル_" **:** ]"*サーバー*"[ **,** "_ポート_"]<br /><br /> _protocol_ には、 **tcp** (TCP/IP)、 **lpc** (共有メモリ)、または **np** (名前付きパイプ) を指定できます。<br /><br /> 名前付きパイプを指定する例を次に示します。<br /><br /> `np:\\.\pipe\MSSQL$MYINST01\sql\query`<br /><br /> 上の行では、名前付きパイプのプロトコル (`np`)、ローカル コンピューター上の名前付きパイプ (`\\.\pipe`)、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスの名前 (`MSSQL$MYINST01`)、および名前付きパイプの既定の名前 (`sql/query`) を指定しています。<br /><br /> *protocol* も **Network** キーワードも指定されない場合、OLE DB Driver for SQL Server では [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 構成マネージャーで指定されたプロトコルの順序が使用されます。<br /><br /> *port* は、指定したサーバー上の接続先のポートです。 既定では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] はポート 1433 を使用します。<br /><br /> OLE DB Driver for SQL Server を使用する場合、接続文字列の **Server** に渡される値の先頭のスペースは無視されます。|   
|**ServerSPN**|SSPROP_INIT_SERVERSPN|サーバーの SPN。 既定値は空の文字列です。 空の文字列を使用すると、OLE DB Driver for SQL Server ではプロバイダーが生成した SPN が既定値として使用されます。|  
|**タイムアウト**|DBPROP_INIT_TIMEOUT|データ ソースの初期化が完了するのを待機する秒数。|  
|**TransparentNetworkIPResolution**|SSPROP_INIT_TNIR|ホスト名の解決された最初の IP が応答せず、ホスト名に複数の IP が関連付けられている場合は、接続シーケンスに影響があります。 TNIR では、MultiSubnetFailover と連動して、さまざまな接続シーケンスが提供されます。 設定可能な値は `Yes` および `No` です。 既定値は `Yes` です。 詳しくは、「[透過的なネットワーク IP の解決の使用](../../oledb/features/using-transparent-network-ip-resolution.md)」をご覧ください。|  
|**Trusted_Connection**|DBPROP_AUTH_INTEGRATED|`yes` の場合、ログインの検証に Windows 認証を使用するように OLE DB Driver for SQL Server に指示します。 それ以外の場合は、OLE DB Driver for SQL Server により、ログインの検証に [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のユーザー名とパスワードが使用されるため、UID キーワードと PWD キーワードを指定する必要があります。|  
|**TrustServerCertificate**<a href="#table1_1"><sup>**1**</sup></a>|SSPROP_INIT_TRUST_SERVER_CERTIFICATE|文字列 `yes` と `no` を値として受け取ります。 既定値は `no` です。これはサーバー証明書が検証されることを示します。|  
|**UID**|DBPROP_AUTH_USERID|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のログイン名。|  
|**UseFMTONLY**|SSPROP_INIT_USEFMTONLY|[!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] およびそれ以降に接続する場合のメタデータの取得方法を制御します。 設定可能な値は `yes` および `no` です。 既定値は `no` です。<br /><br />既定では、OLE DB Driver for SQL Server は [sp_describe_first_result_set](../../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md) および [sp_describe_undeclared_parameters](../../../relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql.md) ストアド プロシージャを使用して、メタデータを取得します。 これらのストアド プロシージャにはいくつかの制限があります (たとえば、一時テーブルで操作するとエラーが発生します)。 **UseFMTONLY** を `yes` に設定すると、[SET FMTONLY](../../../t-sql/statements/set-fmtonly-transact-sql.md) を使用して代わりにメタデータを取得するようにドライバーに指示されます。|  
|**UseProcForPrepare**|SSPROP_INIT_USEPROCFORPREP|OLE DB Driver for SQL Server では、このキーワードは非推奨とされ、その設定は無視されます。|  
|**WSID**|SSPROP_INIT_WSID|ワークステーション ID。|  
  
<b id="table1_1">[1]:</b>セキュリティを強化するために、認証またはアクセス トークンの初期化プロパティまたはそれに対応する接続文字列キーワードを使用する場合の、暗号化と証明書の検証の動作が変更されます。 詳細については、「[Encryption and certificate validation](../features/using-azure-active-directory.md#encryption-and-certificate-validation)」 (暗号化と証明書の検証) を参照してください。

## <a name="using-idatainitializegetdatasource"></a>Using IDataInitialize::GetDataSource

 **IDataInitialize::GetDataSource** を利用する OLE DB アプリケーションで使用される接続文字列の構文は次のとおりです。  
  
 - `connection-string ::= empty-string[;] | attribute[;] | attribute; connection-string`  
  
 - `empty-string ::=`  
  
 - `attribute ::= attribute-keyword=[quote]attribute-value[quote]`  
  
 - `attribute-value ::= character-string`  
  
 - `attribute-keyword ::= identifier`  
  
 - `quote ::= " | '`  
  
 プロパティの使用方法は、プロパティのスコープで許可される構文に従っている必要があります。 たとえば、**WSID** では中かっこ ( **{}** ) の引用符を使用し、**Application Name** では一重 ( **'** ) または二重 ( **"** ) 引用符を使用します。 引用符で囲むことができるのは、文字列のプロパティのみです。 整数または列挙のプロパティを引用符で囲むと、`Connection String does not conform to OLE DB specification` エラーになります。  
  
 属性値は必要に応じて一重引用符または二重引用符で囲むことができます。常にこれらの引用符を使用すると、 値に英数字以外の文字が含まれる場合の問題を回避できます。 文字を引用符で囲む場合、二重引用符を使用すれば、値の中に含めることもできます。  
  
 接続文字列キーワードの等号 (=) の後のスペース文字は、値を引用符で囲んだ場合でも、リテラルとして解釈されます。  
  
 接続文字列に次の表のプロパティが複数含まれている場合は、最後のプロパティの値が使用されます。  
  
 次の表では、**IDataInitialize::GetDataSource** と共に使用できるキーワードについて説明します。  
  
|Keyword|初期化プロパティ|説明|  
|-------------|-----------------------------|-----------------|  
|**Access Token**<a href="#table2_1"><sup id="table2_accesstoken">**1**</sup></a>|SSPROP_AUTH_ACCESS_TOKEN|Azure Active Directory に対する認証に使用するアクセス トークン。 <br/><br/>**注:** このキーワードを指定し、`UID`、`PWD`、`Trusted_Connection`、または `Authentication` 接続文字列キーワードまたはそれに対応するプロパティ/キーワードも指定すると、エラーになります。|
|**アプリケーション名**|SSPROP_INIT_APPNAME|アプリケーションを識別する文字列。|  
|**Application Intent**|SSPROP_INIT_APPLICATIONINTENT|アプリケーションがサーバーに接続するときのワークロードのタイプを宣言します。 設定可能な値は `ReadOnly` および `ReadWrite` です。<br /><br /> 既定では、 `ReadWrite`です。 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] の OLE DB Driver for SQL Server によるサポートの詳細については、「[OLE DB Driver for SQL Server の高可用性、ディザスター リカバリーに関するサポート](../features/oledb-driver-for-sql-server-support-for-high-availability-disaster-recovery.md)」を参照してください。|  
|**Authentication**<a href="#table2_1"><sup>**1**</sup></a>|SSPROP_AUTH_MODE|使用される SQL または Active Directory 認証を指定します。 有効な値は次のとおりです。<br/><ul><li>`(not set)`:他のキーワードによって決定される認証モード。</li><li>`ActiveDirectoryPassword:` Azure Active Directory の ID によるユーザー ID とパスワードの認証。</li><li>`ActiveDirectoryIntegrated:` Azure Active Directory の ID による統合認証。</li><br/>**注:** `ActiveDirectoryIntegrated` キーワードは、SQL Server に対する Windows 認証でも使用できます。 これは、`Integrated Security` (または `Trusted_Connection`) 認証キーワードに代わるものです。 `Integrated Security` (または `Trusted_Connection`) キーワードまたはそれに対応するプロパティを使用するアプリケーションで、`Authentication` キーワード (またはそれに対応するプロパティ) の値を `ActiveDirectoryIntegrated` に設定して、新しい暗号化および証明書の検証の動作を有効にすることを **お勧め** します。<br/><br/><li>`ActiveDirectoryInteractive:` Azure Active Directory の ID による対話型認証。 この方法では、Azure Multi-Factor Authentication (MFA) がサポートされます。 </li><li>`ActiveDirectoryMSI:` [マネージド ID (MSI)](/azure/active-directory/managed-identities-azure-resources/overview) の認証。 ユーザー割り当て ID の場合、ユーザー ID はユーザー ID のオブジェクト ID に設定する必要があります。</li><li>`ActiveDirectoryServicePrincipal:` Azure Active Directory サービス プリンシパルを使用した認証。 ユーザー ID はアプリケーション (クライアント) ID に設定する必要があります。 パスワードはアプリケーション (クライアント) シークレットに設定する必要があります。</li><li>`SqlPassword:` ユーザー ID とパスワードを使用した認証。</li><br/>**注:** `SQL Server` 認証を使用するアプリケーションで、`Authentication` キーワード (またはそれに対応するプロパティ) の値を `SqlPassword` に設定して、[新しい暗号化および証明書の検証の動作](../features/using-azure-active-directory.md#encryption-and-certificate-validation)を有効にすることを **お勧め** します。</ul>|
|**自動翻訳**|SSPROP_INIT_AUTOTRANSLATE|**AutoTranslate** のシノニム。|  
|**AutoTranslate**|SSPROP_INIT_AUTOTRANSLATE|OEM/ANSI 文字の変換を構成します。 認識される値は `true` と `false` です。|  
|**Connect Timeout**|DBPROP_INIT_TIMEOUT|データ ソースの初期化が完了するのを待機する秒数。|  
|**Current Language**|SSPROPT_INIT_CURRENTLANGUAGE|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 言語の名前。|  
|**データ ソース**|DBPROP_INIT_DATASOURCE|組織内の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスの名前。<br /><br /> 指定されなかった場合は、ローカル コンピューター上にある既定のインスタンスに接続します。<br /><br /> 有効なアドレス構文の詳細については、このトピックの **Server** キーワードに関する説明を参照してください。|  
|**DataTypeCompatibility**|SSPROP_INIT_DATATYPECOMPATIBILITY|使用するデータ型処理モードを指定します。 認識される値は、プロバイダー データ型を示す `0` と [!INCLUDE[ssVersion2000](../../../includes/ssversion2000-md.md)] データ型を示す `80` です。|  
|**Failover Partner**|SSPROP_INIT_FAILOVERPARTNER|データベース ミラーリングに使用するフェールオーバー サーバーの名前。|  
|**Failover Partner SPN**|SSPROP_INIT_FAILOVERPARTNERSPN|フェールオーバー パートナーの SPN。 既定値は空の文字列です。 空の文字列を使用すると、OLE DB Driver for SQL Server ではプロバイダーが生成した SPN が既定値として使用されます。|  
|**初期カタログ**|DBPROP_INIT_CATALOG|データベース名です。|  
|**初期ファイル名**|SSPROP_INIT_FILENAME|アタッチできるデータベースのプライマリ ファイルの名前 (完全なパス名を含む)。 **AttachDBFileName** を使用するには、プロバイダー文字列の DATABASE キーワードでデータベース名を指定する必要もあります。 データベースが以前にアタッチされていた場合、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] により再アタッチされることはありません (アタッチされたデータベースがその接続の既定のデータベースとして使用されます)。|  
|**統合セキュリティ**|DBPROP_AUTH_INTEGRATED|Windows 認証の値 `SSPI` を受け取ります。|  
|**MARS Connection**|SSPROP_INIT_MARSCONNECTION|その接続で MARS (複数のアクティブな結果セット) を有効または無効にします。 認識される値は `true` と `false` です。 既定では、 `false`です。|  
|**MultiSubnetFailover**|SSPROP_INIT_MULTISUBNETFAILOVER|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 可用性グループまたは [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] フェールオーバー クラスター インスタンスの可用性グループ リスナーに接続する際には、必ず **MultiSubnetFailover=True** を指定してください。 **MultiSubnetFailover=True** の場合、(現在) アクティブなサーバーをより迅速に検出し、接続するように OLE DB Driver for SQL Server が構成されます。 設定可能な値は `True` および `False` です。 既定では、 `False`です。 次に例を示します。<br /><br /> `MultiSubnetFailover=True`<br /><br /> [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] の OLE DB Driver for SQL Server によるサポートの詳細については、「[OLE DB Driver for SQL Server の高可用性、ディザスター リカバリーに関するサポート](../features/oledb-driver-for-sql-server-support-for-high-availability-disaster-recovery.md)」を参照してください。|  
|**Network Address**|SSPROP_INIT_NETWORKADDRESS|組織内の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスのネットワーク アドレス。<br /><br /> 有効なアドレス構文の詳細については、このトピックの **Address** キーワードに関する説明を参照してください。|  
|**Network Library**|SSPROP_INIT_NETWORKLIBRARY|組織内の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスへの接続を確立するために使用するネットワーク ライブラリ。|  
|**Packet Size**|SSPROP_INIT_PACKETSIZE|表形式データ ストリーム (TDS) パケット サイズ。 既定値は 0 です (実際の値はサーバーによって決定されます)。|  
|**パスワード**|DBPROP_AUTH_PASSWORD|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のログイン パスワード。|  
|**Persist Security Info**|DBPROP_AUTH_PERSIST_SENSITIVE_AUTHINFO|文字列 `true` と `false` を値として受け取ります。 `false` の場合、データ ソース オブジェクトには機密の認証情報を保持できません|  
|**プロバイダー**||OLE DB Driver for SQL Server では、これを "MSOLEDBSQL" にする必要があります。|  
|**Server SPN**|SSPROP_INIT_SERVERSPN|サーバーの SPN。 既定値は空の文字列です。 空の文字列を使用すると、OLE DB Driver for SQL Server ではプロバイダーが生成した SPN が既定値として使用されます。|  
|**TransparentNetworkIPResolution**|SSPROP_INIT_TNIR|ホスト名の解決された最初の IP が応答せず、ホスト名に複数の IP が関連付けられている場合は、接続シーケンスに影響があります。 TNIR では、MultiSubnetFailover と連動して、さまざまな接続シーケンスが提供されます。 設定可能な値は `True` および `False` です。 既定値は `True` です。 詳しくは、「[透過的なネットワーク IP の解決の使用](../../oledb/features/using-transparent-network-ip-resolution.md)」をご覧ください。|  
|**Trust Server Certificate**<a href="#table2_1"><sup>**1**</sup></a>|SSPROP_INIT_TRUST_SERVER_CERTIFICATE|文字列 `true` と `false` を値として受け取ります。 既定値は `false` です。これはサーバー証明書が検証されることを示します。|  
|**Use Encryption for Data**<a href="#table2_1"><sup>**1**</sup></a>|SSPROP_INIT_ENCRYPT|データをネットワークに送信する前に暗号化するかどうかを指定します。 設定可能な値は `true` および `false` です。 既定値は `false` です。|  
|**Use FMTONLY**|SSPROP_INIT_USEFMTONLY|[!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] およびそれ以降に接続する場合のメタデータの取得方法を制御します。 設定可能な値は `true` および `false` です。 既定値は `false` です。<br /><br />既定では、OLE DB Driver for SQL Server は [sp_describe_first_result_set](../../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md) および [sp_describe_undeclared_parameters](../../../relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql.md) ストアド プロシージャを使用して、メタデータを取得します。 これらのストアド プロシージャにはいくつかの制限があります (たとえば、一時テーブルで操作するとエラーが発生します)。 **Use FMTONLY** を `true` に設定すると、[SET FMTONLY](../../../t-sql/statements/set-fmtonly-transact-sql.md) を使用して代わりにメタデータを取得するようにドライバーに指示されます。|  
|**[ユーザー ID]**|DBPROP_AUTH_USERID|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のログイン名。|  
|**Workstation ID**|SSPROP_INIT_WSID|ワークステーション ID。|  
  
<b id="table2_1">[1]:</b>セキュリティを強化するために、認証/アクセス トークンの初期化プロパティまたはそれに対応する接続文字列キーワードを使用する場合の、暗号化と証明書の検証の動作が変更されています。 詳細については、「[暗号化と証明書の検証](../features/using-azure-active-directory.md#encryption-and-certificate-validation)」を参照してください。

 > [!NOTE]
 > 接続文字列には、`Old Password` プロパティにより SSPROP_AUTH_OLD_PASSWORD が設定されます。これは現在の (期限切れの可能性がある) パスワードで、プロバイダー文字列のプロパティから使用することはできません。  
  
## <a name="activex-data-objects-ado-connection-string-keywords"></a>ADO (ActiveX Data Objects) の接続文字列のキーワード  

 ADO アプリケーションでは、**ADODBConnection** オブジェクトの **ConnectionString** プロパティが設定されるか、**ADODBConnection** オブジェクトの **Open** メソッドにパラメーターとして接続文字列が指定されます。  
  
 また、ADO アプリケーションでは、既定値がないプロパティにのみ、OLE DB **IDBInitialize::Initialize** メソッドで使用されるキーワードを利用できます。 アプリケーションにより、初期化文字列で ADO キーワードと **IDBInitialize::Initialize** キーワードが両方とも使われている場合、ADO キーワードの設定が使用されます。 アプリケーションでは ADO 接続文字列キーワードのみを使用することをお勧めします。  
  
 ADO で使用される接続文字列の構文を次に示します。  
  
 - `connection-string ::= empty-string[;] | attribute[;] | attribute; connection-string`  
  
 - `empty-string ::=`  
  
 - `attribute ::= attribute-keyword=["]attribute-value["]`  
  
 - `attribute-value ::= character-string`  
  
 - `attribute-keyword ::= identifier`  
  
 属性値は必要に応じて二重引用符で囲むことができます。常に二重引用符を使用すると、 値に英数字以外の文字が含まれる場合の問題を回避できます。 属性値に二重引用符を含めることはできません。  
  
 次の表に、ADO 接続文字列と共に使用できるキーワードを示します。  
  
|Keyword|初期化プロパティ|説明|  
|-------------|-----------------------------|-----------------|  
|**Access Token**<a href="#table3_1"><sup id="table3_accesstoken">**1**</sup></a>|SSPROP_AUTH_ACCESS_TOKEN|Azure Active Directory に対する認証に使用するアクセス トークン。<br/><br/>**注:** このキーワードを指定し、`UID`、`PWD`、`Trusted_Connection`、または `Authentication` 接続文字列キーワードまたはそれに対応するプロパティ/キーワードも指定すると、エラーになります。|
|**Application Intent**|SSPROP_INIT_APPLICATIONINTENT|アプリケーションがサーバーに接続するときのワークロードのタイプを宣言します。 設定可能な値は `ReadOnly` および `ReadWrite` です。<br /><br /> 既定では、 `ReadWrite`です。 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] の OLE DB Driver for SQL Server によるサポートの詳細については、「[OLE DB Driver for SQL Server の高可用性、ディザスター リカバリーに関するサポート](../features/oledb-driver-for-sql-server-support-for-high-availability-disaster-recovery.md)」を参照してください。|  
|**アプリケーション名**|SSPROP_INIT_APPNAME|アプリケーションを識別する文字列。|  
|**Authentication**<a href="#table3_1"><sup>**1**</sup></a>|SSPROP_AUTH_MODE|使用される SQL または Active Directory 認証を指定します。 有効な値は次のとおりです。<br/><ul><li>`(not set)`:他のキーワードによって決定される認証モード。</li><li>`ActiveDirectoryPassword:` Azure Active Directory の ID によるユーザー ID とパスワードの認証。</li><li>`ActiveDirectoryIntegrated:` Azure Active Directory の ID による統合認証。</li><br/>**注:** `ActiveDirectoryIntegrated` キーワードは、SQL Server に対する Windows 認証でも使用できます。 これは、`Integrated Security` (または `Trusted_Connection`) 認証キーワードに代わるものです。 `Integrated Security` (または `Trusted_Connection`) キーワードまたはそれに対応するプロパティを使用するアプリケーションで、`Authentication` キーワード (またはそれに対応するプロパティ) の値を `ActiveDirectoryIntegrated` に設定して、新しい暗号化および証明書の検証の動作を有効にすることを **お勧め** します。<br/><br/><li>`ActiveDirectoryInteractive:` Azure Active Directory の ID による対話型認証。 この方法では、Azure Multi-Factor Authentication (MFA) がサポートされます。 </li><li>`ActiveDirectoryMSI:` [マネージド ID (MSI)](/azure/active-directory/managed-identities-azure-resources/overview) の認証。 ユーザー割り当て ID の場合、ユーザー ID はユーザー ID のオブジェクト ID に設定する必要があります。</li><li>`ActiveDirectoryServicePrincipal:` Azure Active Directory サービス プリンシパルを使用した認証。 ユーザー ID はアプリケーション (クライアント) ID に設定する必要があります。 パスワードはアプリケーション (クライアント) シークレットに設定する必要があります。</li><li>`SqlPassword:` ユーザー ID とパスワードを使用した認証。</li><br/>**注:** `SQL Server` 認証を使用するアプリケーションで、`Authentication` キーワード (またはそれに対応するプロパティ) の値を `SqlPassword` に設定して、[新しい暗号化および証明書の検証の動作](../features/using-azure-active-directory.md#encryption-and-certificate-validation)を有効にすることを **お勧め** します。</ul>|
|**自動翻訳**|SSPROP_INIT_AUTOTRANSLATE|**AutoTranslate** のシノニム。|  
|**AutoTranslate**|SSPROP_INIT_AUTOTRANSLATE|OEM/ANSI 文字の変換を構成します。 認識される値は `true` と `false` です。|  
|**Connect Timeout**|DBPROP_INIT_TIMEOUT|データ ソースの初期化が完了するのを待機する秒数。|  
|**Current Language**|SSPROPT_INIT_CURRENTLANGUAGE|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 言語の名前。|  
|**データ ソース**|DBPROP_INIT_DATASOURCE|組織内の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスの名前。<br /><br /> 指定されなかった場合は、ローカル コンピューター上にある既定のインスタンスに接続します。<br /><br /> 有効なアドレス構文の詳細については、このトピックの **Server** キーワードに関する説明を参照してください。|  
|**DataTypeCompatibility**|SSPROP_INIT_DATATYPECOMPATIBILITY|使用するデータ型処理モードを指定します。 認識される値は、プロバイダー データ型を示す `0` と SQL Server 2000 データ型を示す `80` です。|  
|**Failover Partner**|SSPROP_INIT_FAILOVERPARTNER|データベース ミラーリングに使用するフェールオーバー サーバーの名前。|  
|**Failover Partner SPN**|SSPROP_INIT_FAILOVERPARTNERSPN|フェールオーバー パートナーの SPN。 既定値は空の文字列です。 空の文字列を使用すると、OLE DB Driver for SQL Server ではプロバイダーが生成した SPN が既定値として使用されます。|  
|**初期カタログ**|DBPROP_INIT_CATALOG|データベース名です。|  
|**初期ファイル名**|SSPROP_INIT_FILENAME|アタッチできるデータベースのプライマリ ファイルの名前 (完全なパス名を含む)。 **AttachDBFileName** を使用するには、プロバイダー文字列の **DATABASE** キーワードでデータベース名を指定する必要もあります。 データベースが以前にアタッチされていた場合、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] により再アタッチされることはありません (アタッチされているベースがその接続の既定のデータベースとして使用されます)。|  
|**統合セキュリティ**|DBPROP_AUTH_INTEGRATED|Windows 認証の値 `SSPI` を受け取ります。|  
|**MARS Connection**|SSPROP_INIT_MARSCONNECTION|その接続で MARS (複数のアクティブな結果セット) を有効または無効にします ([!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] 以降のサーバーの場合)。 認識される値は `true` と `false` です。既定値は `false` です。|  
|**MultiSubnetFailover**|SSPROP_INIT_MULTISUBNETFAILOVER|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 可用性グループまたは [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] フェールオーバー クラスター インスタンスの可用性グループ リスナーに接続する際には、必ず **MultiSubnetFailover=True** を指定してください。 **MultiSubnetFailover=True** の場合、(現在) アクティブなサーバーをより迅速に検出し、接続するように OLE DB Driver for SQL Server が構成されます。 設定可能な値は `True` および `False` です。 既定では、 `False`です。 次に例を示します。<br /><br /> `MultiSubnetFailover=True`<br /><br /> [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] の OLE DB Driver for SQL Server によるサポートの詳細については、「[OLE DB Driver for SQL Server の高可用性、ディザスター リカバリーに関するサポート](../features/oledb-driver-for-sql-server-support-for-high-availability-disaster-recovery.md)」を参照してください。|  
|**Network Address**|SSPROP_INIT_NETWORKADDRESS|組織内の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスのネットワーク アドレス。<br /><br /> 有効なアドレス構文の詳細については、このトピックの **Address** キーワードに関する説明を参照してください。|  
|**Network Library**|SSPROP_INIT_NETWORKLIBRARY|組織内の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスへの接続を確立するために使用するネットワーク ライブラリ。|  
|**Packet Size**|SSPROP_INIT_PACKETSIZE|表形式データ ストリーム (TDS) パケット サイズ。 既定値は 0 です (実際の値はサーバーによって決定されます)。|  
|**パスワード**|DBPROP_AUTH_PASSWORD|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のログイン パスワード。|  
|**Persist Security Info**|DBPROP_AUTH_PERSIST_SENSITIVE_AUTHINFO|文字列 `true` と `false` を値として受け取ります。 `false` の場合、データ ソース オブジェクトには機密の認証情報を保持できません。|  
|**プロバイダー**||OLE DB Driver for SQL Server の場合、値は `MSOLEDBSQL` です。|  
|**Server SPN**|SSPROP_INIT_SERVERSPN|サーバーの SPN。 既定値は空の文字列です。 空の文字列を使用すると、OLE DB Driver for SQL Server ではプロバイダーが生成した SPN が既定値として使用されます。|  
|**TransparentNetworkIPResolution**|SSPROP_INIT_TNIR|ホスト名の解決された最初の IP が応答せず、ホスト名に複数の IP が関連付けられている場合は、接続シーケンスに影響があります。 TNIR では、MultiSubnetFailover と連動して、さまざまな接続シーケンスが提供されます。 設定可能な値は `True` および `False` です。 既定値は `True` です。 詳しくは、「[透過的なネットワーク IP の解決の使用](../../oledb/features/using-transparent-network-ip-resolution.md)」をご覧ください。|  
|**Trust Server Certificate**<a href="#table3_1"><sup>**1**</sup></a>|SSPROP_INIT_TRUST_SERVER_CERTIFICATE|文字列 `true` と `false` を値として受け取ります。 既定値は `false` です。これはサーバー証明書が検証されることを示します。|  
|**Use Encryption for Data**<a href="#table3_1"><sup>**1**</sup></a>|SSPROP_INIT_ENCRYPT|データをネットワークに送信する前に暗号化するかどうかを指定します。 設定可能な値は `true` および `false` です。 既定値は `false` です。|  
|**Use FMTONLY**|SSPROP_INIT_USEFMTONLY|[!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] およびそれ以降に接続する場合のメタデータの取得方法を制御します。 設定可能な値は `true` および `false` です。 既定値は `false` です。<br /><br />既定では、OLE DB Driver for SQL Server は [sp_describe_first_result_set](../../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md) および [sp_describe_undeclared_parameters](../../../relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql.md) ストアド プロシージャを使用して、メタデータを取得します。 これらのストアド プロシージャにはいくつかの制限があります (たとえば、一時テーブルで操作するとエラーが発生します)。 **Use FMTONLY** を `true` に設定すると、[SET FMTONLY](../../../t-sql/statements/set-fmtonly-transact-sql.md) を使用して代わりにメタデータを取得するようにドライバーに指示されます。|  
|**[ユーザー ID]**|DBPROP_AUTH_USERID|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のログイン名。|  
|**Workstation ID**|SSPROP_INIT_WSID|ワークステーション ID。|  
  
<b id="table3_1">[1]:</b>セキュリティを強化するために、認証/アクセス トークンの初期化プロパティまたはそれに対応する接続文字列キーワードを使用する場合の、暗号化と証明書の検証の動作が変更されています。 詳細については、「[暗号化と証明書の検証](../features/using-azure-active-directory.md#encryption-and-certificate-validation)」を参照してください。

 > [!NOTE] 
 > 接続文字列の "Old Password" プロパティは SSPROP_AUTH_OLD_PASSWORD に設定され、現在の (または期限切れの) パスワードが設定されます。このパスワードをプロバイダー文字列のプロパティ経由で使用することはできません。  
  
## <a name="see-also"></a>関連項目  

 [OLE DB Driver for SQL Server を使用したアプリケーションの構築](building-applications-with-oledb-driver-for-sql-server.md)