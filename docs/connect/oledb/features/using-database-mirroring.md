---
title: データベース ミラーリングの使用 | Microsoft Docs
description: OLE DB Driver for SQL Server では、データベース ミラーリングがサポートされています。 データベースのミラーリングが構成されていれば、開発者がそれ以外の操作を行う必要はありません。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- database mirroring [SQL Server], interoperability
- OLE DB Driver for SQL Server, database mirroring
- data access [OLE DB Driver for SQL Server], database mirroring
- database mirroring [SQL Server], connecting clients to
- MSOLEDBSQL, database mirroring
- OLE DB Driver for SQL Server, database mirroring
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: f46815556d8387da8f99689716158d6cb62a0ff9
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751172"
---
# <a name="using-database-mirroring"></a>データベース ミラーリングの使用
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

    
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../../includes/ssnotedepfutureavoid-md.md)] 代わりに [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] を使用します。  
  
 [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] で導入されたデータベース ミラーリングは、データベースの可用性とデータの冗長性を高めるためのソリューションです。 OLE DB Driver for SQL Server では、データベース用に構成することにより、開発者がコードを記述したりそれ以外の操作を行わなくてもデータベース ミラーリングが暗黙にサポートされます。  
  
 データベース ミラーリングは、データベースごとに実装され、スタンバイ サーバー上に [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 運用データベースのコピーを保持します。 このサーバーは、データベース ミラーリング セッションの構成および状態に応じて、ホット スタンバイ サーバーかウォーム スタンバイ サーバーのいずれかになります。 ホット スタンバイ サーバーはコミット済みトランザクションが失われない高速フェールオーバーをサポートし、ウォーム スタンバイ サーバーはサービスの強制 (データ損失の可能性あり) をサポートします。  
  
 運用データベースは *プリンシパル データベース* と呼ばれ、スタンバイ コピーは *ミラー データベース* と呼ばれます。 プリンシパル データベースおよびミラー データベースは、別個の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンス (サーバー インスタンス) に配置する必要があります。また、可能な場合は別個のコンピューターに配置してください。  
  
 *プリンシパル サーバー* と呼ばれる実稼働サーバー インスタンスは、*ミラー サーバー* と呼ばれるスタンバイ サーバーと通信します。 プリンシパル サーバーとミラー サーバーは、データベース ミラーリング *セッション* の中でパートナーとして機能します。 プリンシパル サーバーで障害が発生した場合、ミラー サーバーは *フェールオーバー* と呼ばれる処理を通じて、ミラー サーバーのデータベースをプリンシパル データベースにできます。 たとえば、Partner_A と Partner_B がパートナー サーバーで、初期時点ではプリンシパル データベースがプリンシパル サーバーである Partner_A にあり、ミラー データベースがミラー サーバーである Partner_B にあるとします。 Partner_A がオフラインになった場合、Partner_B がフェールオーバーして現在のプリンシパル データベースになることができます。 Partner_A がミラー化セッションに再び参加すると、このサーバーがミラー サーバーになり、このサーバーのデータベースがミラー データベースになります。  
  
 代替データベース ミラーリング構成は、さまざまなレベルのパフォーマンスとデータの安全性を提供し、さまざまな形態のフェールオーバーをサポートします。 詳細については、「[データベース ミラーリング &#40;SQL Server&#41;](../../../database-engine/database-mirroring/database-mirroring-sql-server.md)」を参照してください。  
  
 ミラー データベース名を指定するときには別名を使用できます。  
  
> [!NOTE]  
>  ミラー化されたデータベースへの最初の接続試行と再接続試行の詳細については、「[データベース ミラーリング セッションへのクライアントの接続 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server.md)」を参照してください。  
  
## <a name="programming-considerations"></a>プログラミングの考慮事項  
 プリンシパル データベース サーバーに障害が発生した場合、クライアント アプリケーションの API 呼び出しの応答がエラーになり、データベースへの接続が失われたことが伝えられます。 このとき、データベースへのコミットされていない変更は反映されず、現在のトランザクションはロールバックされます。 その場合、アプリケーションは接続を閉じ (またはデータ ソース オブジェクトを解放し)、再度接続を開く必要があります。 再接続の結果、プリンパル サーバーの機能を引き継いだミラー データベースに自動的にリダイレクトされます。  
  
 接続を確立するときに、プリンシパル サーバーからクライアントに対し、フェールオーバーが行われるときに使用されるフェールオーバー パートナーの ID が送信されます。 プリンシパル サーバーで障害が発生した後で接続を確立すると、クライアント側でフェールオーバー パートナーの ID を把握できません。 このシナリオでクライアントが適切な処理を行うため、初期化プロパティおよび関連する接続文字列キーワードを使用して、クライアントが独自にフェールオーバー パートナーの ID を指定することができます。 クライアント属性が使用されるのは、このシナリオのみです。プリンシパル サーバーが利用できる場合、クライアント属性は使用されません。 クライアントが指定したフェールオーバー パートナー サーバーが、フェールオーバー パートナーとして機能しているサーバーを参照していない場合は、サーバーへの接続が拒否されます。 アプリケーションが構成の変更に対応できるようにするため、接続を確立した後で属性を調査することにより、実際のフェールオーバー パートナーの ID を特定できます。 パートナー情報をキャッシュして接続文字列を更新することを検討するか、最初の接続に失敗した場合の再接続の方法を検討することをお勧めします。  
  
> [!NOTE]  
>  この機能を DSN、接続文字列、または接続プロパティや接続属性で使用する場合は、接続で使用するデータベースを明示的に指定する必要があります。 指定しないと、OLE DB Driver for SQL Server でパートナー データベースへのフェールオーバーが実行されません。  
>   
>  ミラー化はデータベースの機能です。 複数のデータベースを併用するアプリケーションでは、この機能を利用できない場合があります。  
>   
>  また、サーバー名は大文字小文字が区別されませんが、データベース名は区別されます。 したがって、大文字小文字の使い方を DSN と接続文字列で統一してください。  
  
## <a name="ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server  
 OLE DB Driver for SQL Server は、接続属性および接続文字列の属性を使用してデータベース ミラーリングをサポートしています。 DBPROPSET_SQLSERVERDBINIT プロパティ セットには、SSPROP_INIT_FAILOVERPARTNER プロパティが追加されています。**FailoverPartner** キーワードは、DBPROP_INIT_PROVIDERSTRING の新しい接続文字列属性です。 詳細については、「[OLE DB Driver for SQL Server での接続文字列キーワードの使用](../../oledb/applications/using-connection-string-keywords-with-oledb-driver-for-sql-server.md)」を参照してください。  
  
 プロバイダーを読み込んでいる間 (**CoUninitialize** が呼び出されるまで)、または OLE DB Driver for SQL Server の管理下にある、データ ソース オブジェクトなどのオブジェクトをアプリケーションから参照している間は、フェールオーバー キャッシュが保持されます。  
  
 OLE DB Driver for SQL Server でのデータベース ミラーリングのサポートの詳細については、「[初期化プロパティと承認プロパティ](../../oledb/ole-db-data-source-objects/initialization-and-authorization-properties.md)」を参照してください。  
 
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)   
 [データベース ミラーリング セッションへのクライアントの接続 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server.md)   
 [データベース ミラーリング &#40;SQL Server&#41;](../../../database-engine/database-mirroring/database-mirroring-sql-server.md)  
  
  
