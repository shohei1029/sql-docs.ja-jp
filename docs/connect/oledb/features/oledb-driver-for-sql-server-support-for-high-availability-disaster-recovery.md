---
title: OLE DB Driver for SQL Server の高可用性、ディザスター リカバリーに関するサポート
description: 高可用性とディザスター リカバリーの機能が構成されているデータベースに接続する場合の、これらの機能に対する OLE DB Driver for SQL Server でのサポートについて説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 819e81cd1d1b09d866901b54cc02341ef3e200cf
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742572"
---
# <a name="ole-db-driver-for-sql-server-support-for-high-availability-disaster-recovery"></a>OLE DB Driver for SQL Server の高可用性、ディザスター リカバリーに関するサポート
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  この記事では、*OLE DB Driver for SQL Server* での [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] のサポートについて説明します。 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] の詳細については、「[可用性グループ リスナー、クライアント接続、およびアプリケーションのフェールオーバー &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)」、「[可用性グループの作成と構成 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/creation-and-configuration-of-availability-groups-sql-server.md)」、「[フェールオーバー クラスタリングと AlwaysOn 可用性グループ &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server.md)」、および「[アクティブなセカンダリ: 読み取り可能なセカンダリ レプリカ &#40;AlwaysOn 可用性グループ&#41;](../../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)」を参照してください。  
  
 接続文字列で、特定の可用性グループの可用性グループ リスナーを指定できます。 フェールオーバーする可用性グループ内のデータベースに OLE DB Driver for SQL Server アプリケーションが接続されている場合、元の接続が切断されるため、フェールオーバー後にアプリケーションが動作を継続するには新しい接続を開く必要があります。  
  
 可用性グループ リスナーに接続しておらず、ホスト名に複数の IP アドレスが関連付けられている場合、OLE DB Driver for SQL Server では DNS エントリに関連付けられているすべての IP アドレスが順次繰り返し処理されます。 DNS サーバーが最初に返した IP アドレスがネットワーク インターフェイス カード (NIC) にバインドされていない場合、この処理に時間がかかる可能性があります。 可用性グループ リスナーに接続している場合、OLE DB Driver for SQL Server では同時にすべての IP アドレスとの間で接続の確立が試行されます。接続試行が成功した場合、ドライバーでは未解決の試行がすべて破棄されます。  
  
> [!NOTE]  
> 接続タイムアウト値を大きくし、接続再試行ロジックを実装することにより、アプリケーションが可用性グループに接続する確立が高まります。 また、可用性グループのフェールオーバーにより接続が失敗する可能性があるため、接続再試行ロジックを実装して、再接続されるまで、失敗した接続の再接続を試行する必要があります。  
  
## <a name="connecting-with-multisubnetfailover"></a>MultiSubnetFailover を使用した接続  
 SQL Server Always On 可用性グループ リスナーまたは [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] フェールオーバー クラスター インスタンスに接続する際には、必ず **MultiSubnetFailover=Yes** を指定してください。 **MultiSubnetFailover** を使用することで、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のすべての Always On 可用性グループおよびフェールオーバー クラスター インスタンスに対して高速フェールオーバーが有効化され、単一サブネットおよびマルチサブネットの AlwaysOn トポロジにおけるフェールオーバー時間が大幅に短縮されます。 マルチサブネット フェールオーバーの際には、クライアントは複数の接続を並列で試行します。 サブネットのフェールオーバー時に、OLE DB Driver for SQL Server によって TCP 接続が再試行されます。  
  
 **MultiSubnetFailover** 接続プロパティを指定すると、アプリケーションが可用性グループまたはフェールオーバー クラスター インスタンスに配置され、OLE DB Driver for SQL Server ではすべての IP アドレスに対して接続を試行することで、プライマリ [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンス上のデータベースに接続が試行されます。 接続に対して **MultiSubnetFailover=Yes** を指定した場合、オペレーティング システムの既定の TCP 再送信間隔より短い間隔で、クライアントにより TCP 接続が再試行されます。 これにより、Always On 可用性グループまたはフェールオーバー クラスター インスタンスのフェールオーバー後、再接続されるまでの時間を短縮することができます。単一サブネットとマルチサブネットの可用性グループ インスタンスおよびフェールオーバー クラスター インスタンスに適用することができます。  
  
 接続文字列キーワードの詳細については、「[OLE DB Driver for SQL Server での接続文字列キーワードの使用](../../oledb/applications/using-connection-string-keywords-with-oledb-driver-for-sql-server.md)」を参照してください。  
  
 可用性グループ リスナーまたはフェールオーバー クラスター インスタンス以外に接続するときに **MultiSubnetFailover=Yes** を指定するとパフォーマンスが低下する可能性があるため、このような指定はサポートされていません。  
  
 可用性グループまたはフェールオーバー クラスター インスタンス内のサーバーに接続する際には、次のガイドラインに従います。  
  
-   単一サブネットまたはマルチサブネットに接続する場合に **MultiSubnetFailover** 接続プロパティを使用すると、パフォーマンスを向上させることができます。  
  
-   可用性グループに接続するには、接続文字列でサーバーとして、可用性グループの可用性グループ リスナーを指定します。  
  
-   64 個を超える数の IP アドレスが構成された [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスに接続すると、接続エラーが発生します。  
  
-   **MultiSubnetFailover** 接続プロパティを使用するアプリケーションの動作は、認証の種類 ([!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証、Kerberos 認証、または Windows 認証) の影響を受けません。  
  
-   **loginTimeout** の値を増やすことで、フェールオーバー時間に対応し、アプリケーションの接続試行回数を減らすことができます。  
  
-   分散トランザクションはサポートされません。  
  
読み取り専用のルーティングが無効である場合、次の状況では可用性グループのセカンダリ レプリカの場所には接続できません。  
  
1.  セカンダリ レプリカの場所が、接続を許可するように構成されていない。  
  
2.  アプリケーションに **ApplicationIntent=ReadWrite** (後述します) が使用されているが、セカンダリ レプリカの場所が読み取り専用アクセスとして構成されている。  
  
プライマリ レプリカが読み取り専用ワークロードを拒否するように構成されているとき、接続文字列に **ApplicationIntent=ReadOnly** が含まれていると、接続は失敗します。  
  
## <a name="upgrading-to-use-multi-subnet-clusters-from-database-mirroring"></a>データベース ミラーリングの使用からマルチサブネット クラスターの使用へのアップグレード  
接続文字列に **MultiSubnetFailover** および **Failover_Partner** の接続キーワードが存在する場合、接続エラーが発生します。 また、**MultiSubnetFailover** が使用されているとき、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] から、データベース ミラーリング ペアに属していることを示すフェールオーバー パートナー応答が返された場合にも、エラーが発生します。  
  
現在、データベース ミラーリングを使用している OLE DB Driver for SQL Server アプリケーションをマルチサブネットのシナリオにアップグレードする場合、**Failover_Partner** 接続プロパティを削除して **MultiSubnetFailover** に置き換え、それを **Yes** に設定し、接続文字列内のサーバー名を可用性グループ リスナーの名前に置き換えます。 接続文字列で **Failover_Partner** および **MultiSubnetFailover=Yes** が使用されている場合、ドライバーでエラーが発生します。 ただし、接続文字列に **Failover_Partner** と **MultiSubnetFailover=No** (または **ApplicationIntent=ReadWrite**) が使用されている場合、アプリケーションはデータベース ミラーリングを使用します。  
  
ドライバーは、可用性グループのプライマリ データベースでデータベース ミラーリングが使用されている場合、および可用性グループ リスナーではなくプライマリ データベースに接続する接続文字列内で **MultiSubnetFailover=Yes** が使用されている場合、エラーを返します。  


[!INCLUDE[specify-application-intent_read-only-routing](~/includes/paragraph-content/specify-application-intent-read-only-routing.md)]


## <a name="ole-db"></a>OLE DB (OLE DB)  
OLE DB Driver for SQL Server では、**ApplicationIntent** と **MultiSubnetFailover** の両方のキーワードがサポートされています。   
  
OLE DB Driver for SQL Server で [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] をサポートするために、次の 2 つの OLE DB 接続文字列キーワードが追加されました。  
  
-   **ApplicationIntent** 
-   **MultiSubnetFailover**  
  
 OLE DB Driver for SQL Server での接続文字列キーワードについて詳しくは、「[OLE DB Driver for SQL Server での接続文字列キーワードの使用](../../oledb/applications/using-connection-string-keywords-with-oledb-driver-for-sql-server.md)」をご覧ください。  

### <a name="application-intent"></a>アプリケーションの目的 

対応する接続プロパティは次のとおりです。  
  
-   **SSPROP_INIT_APPLICATIONINTENT**  
  
-   **DBPROP_INIT_PROVIDERSTRING**  
  
OLE DB Driver for SQL Server アプリケーションでは、次のいずれかのメソッドを使用して、アプリケーション インテントを指定できます。  
  
 -   **IDBInitialize::Initialize**  
 **IDBInitialize::Initialize** は、あらかじめ構成された一連のプロパティを使用して、データ ソースを初期化し、データ ソース オブジェクトを作成します。 アプリケーション インテントは、プロバイダーのプロパティとして指定するか、拡張プロパティ文字列の一部として指定します。  
  
 -   **IDataInitialize::GetDataSource**  
 **IDataInitialize::GetDataSource** は **Application Intent** キーワードを格納できる入力接続文字列を受け取ります。  
  
 -   **IDBProperties::SetProperties**  
 **ApplicationIntent** プロパティ値を設定するには、**IDBProperties::SetProperties** を呼び出して、"**ReadWrite**" または "**ReadOnly**" の値で **SSPROP_INIT_APPLICATIONINTENT** プロパティを渡すか、または "**ApplicationIntent=ReadOnly**" または "**ApplicationIntent=ReadWrite**" を含む値で **DBPROP_INIT_PROVIDERSTRING** プロパティを渡します。  
  
アプリケーション インテントは、 **[データ リンク プロパティ]** ダイアログ ボックスの [すべて] タブの [アプリケーション インテントのプロパティ] フィールドで指定できます。  
  
暗黙的な接続が確立された場合、その接続には、親の接続のアプリケーション インテント設定が使用されます。 同様に、同じデータ ソースから作成されたセッションはいずれも、そのデータ ソースのアプリケーション インテント設定を継承します。  
  
### <a name="multisubnetfailover"></a>MultiSubnetFailover

対応する接続プロパティは次のとおりです。  
  
-   **SSPROP_INIT_MULTISUBNETFAILOVER**  
  
-   **DBPROP_INIT_PROVIDERSTRING**  

OLE DB Driver for SQL Server アプリケーションでは、次のいずれかのメソッドを使用して MultiSubnetFailover オプションを設定できます。  

 -   **IDBInitialize::Initialize**  
 **IDBInitialize::Initialize** は、あらかじめ構成された一連のプロパティを使用して、データ ソースを初期化し、データ ソース オブジェクトを作成します。 アプリケーション インテントは、プロバイダーのプロパティとして指定するか、拡張プロパティ文字列の一部として指定します。  
  
 -   **IDataInitialize::GetDataSource**  
 **IDataInitialize::GetDataSource** は **MultiSubnetFailover** キーワードを格納できる入力接続文字列を受け取ります。  

-   **IDBProperties::SetProperties**  
**MultiSubnetFailover** プロパティ値を設定するには、値が **VARIANT_TRUE** または **VARIANT_FALSE** の **SSPROP_INIT_MULTISUBNETFAILOVER** プロパティを渡すか、"**MultiSubnetFailover=Yes**" または "**MultiSubnetFailover=No**" が値に含まれている **DBPROP_INIT_PROVIDERSTRING** プロパティを渡して、**IDBProperties::SetProperties** を呼び出します。

#### <a name="example"></a>例

```
DBPROP rgPropMultisubnet;

rgPropMultisubnet.dwPropertyID = SSPROP_INIT_MULTISUBNETFAILOVER;
rgPropMultisubnet.dwOptions = DBPROPOPTIONS_REQUIRED;
rgPropMultisubnet.dwStatus = DBPROPSTATUS_OK;
rgPropMultisubnet.colid = DB_NULLID;
V_VT(&(rgPropMultisubnet.vValue)) = VT_BOOL;
V_BOOL(&(rgPropMultisubnet.vValue)) = VARIANT_TRUE;

DBPROPSET PropSet;

PropSet.rgProperties = &rgPropMultisubnet;
PropSet.cProperties = 1;
PropSet.guidPropertySet = DBPROPSET_SQLSERVERDBINIT;
IDBProperties* pIDBProperties = NULL;
hr = pIDBInitialize->QueryInterface(IID_IDBProperties, (void **)&pIDBProperties);
pIDBProperties->SetProperties(1, &PropSet);
```

## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)    
 [OLE DB Driver for SQL Server での接続文字列キーワードの使用](../../oledb/applications/using-connection-string-keywords-with-oledb-driver-for-sql-server.md)  
  
  
