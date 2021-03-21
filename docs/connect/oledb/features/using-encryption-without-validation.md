---
title: 検証を伴わない暗号化の使用 | Microsoft Docs
description: SQL Server 接続の検証を伴わない暗号化について説明します。 OLE DB Driver for SQL Server では、検証を伴わない暗号化がサポートされています。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- data access [OLE DB Driver for SQL Server], encryption
- cryptography [OLE DB Driver for SQL Server]
- MSOLEDBSQL, encryption
- encryption [OLE DB Driver for SQL Server]
- OLE DB Driver for SQL Server, encryption
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 805350b09c624709fa1ea6746c2e97eb2bdbed59
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751122"
---
# <a name="using-encryption-without-validation"></a>検証を伴わない暗号化の使用
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] は、常に、ログインに関連するネットワーク パケットを暗号化します。 サーバーの起動時に証明書がサーバーに提供されないと、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] はログイン パケットの暗号化に使用される自己署名入りの証明書を生成します。  

自己署名証明書では、セキュリティは保証されません。 暗号化されたハンドシェイクは、NT LAN Manager (NTLM) に基づいています。 セキュリティで保護された接続には、SQL Server で検証可能な証明書をプロビジョニングすることを強くお勧めします。 トランスポート層セキュリティ (TLS) は、証明書の検証によってのみセキュリティで保護することができます。

アプリケーションでは、接続文字列キーワードまたは接続プロパティを使用して、すべてのネットワーク トラフィックの暗号化を要求することもできます。 OLE DB で **IDbInitialize::Initialize** にプロバイダー文字列を使用する場合、キーワードは "Encrypt" です。また、ADO と OLE DB で **IDataInitialize** に初期化文字列を使用する場合、キーワードは "Use Encryption for Data" です。 これは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Configuration Manager で **[プロトコルの暗号化を設定する]** オプションを使用して構成することも、暗号化された接続を要求するようにクライアントを構成することによって構成することもできます。 既定では、接続のネットワーク トラフィックをすべて暗号化するには、証明書をサーバーに提供する必要があります。 クライアントがサーバー上の証明書を信頼するように設定すると、中間者攻撃に対して脆弱になる可能性があります。 検証可能な証明書をサーバーに展開する場合は、証明書の信頼に関するクライアント設定を確実に FALSE に変更してください。

接続文字列キーワードの詳細については、「[OLE DB Driver for SQL Server での接続文字列キーワードの使用](../../oledb/applications/using-connection-string-keywords-with-oledb-driver-for-sql-server.md )」を参照してください。  
  
 証明書がサーバーに提供されていないときに暗号化を有効にするには、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 構成マネージャーを使用して **[Force Protocol Encryption]** オプションと **[Trust Server Certificate]** オプションの両方を設定できます。 このように、検証可能なサーバー証明書がプロビジョニングされていない場合、暗号化には検証を伴わない自己署名入りのサーバー証明書が使用されます。  
  
 アプリケーションでは、暗号化が行われることを保証するために "TrustServerCertificate" キーワードまたはそれに関連する接続属性も使用できます。 アプリケーションの設定によって、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] クライアント構成マネージャーで設定されるセキュリティのレベルを緩和することはできません。ただし、厳密にすることはできます。 たとえば、クライアントに **[Force Protocol Encryption]** オプションが設定されていない場合、アプリケーションから暗号化自体を要求することができます。 サーバー証明書が提供されなかった場合でも暗号化を保証するには、アプリケーションから暗号化と "TrustServerCertificate" を要求できます。 ただし、クライアントの構成で "TrustServerCertificate" が有効になっていない場合は、サーバー証明書を提供する必要があります。 次の表ですべてのケースを説明します。  
  
|[プロトコルの暗号化を設定する] クライアント設定|[サーバー証明書を信頼する] クライアント設定|接続文字列/接続属性 Encrypt/Use Encryption for Data|接続文字列/接続属性 Trust Server Certificate|結果|  
|----------------------------------------------|---------------------------------------------|------------------------------------------------------------------------------|----------------------------------------------------------------------|------------|  
|いいえ|N/A|無効 (既定値)|無視|暗号化は行われません。|  
|いいえ|該当なし|はい|無効 (既定値)|暗号化は、検証可能なサーバー証明書が提供されている場合にのみ行われます。それ以外の場合は、接続試行が失敗します。|  
|いいえ|該当なし|はい|はい|暗号化は常に行われますが、自己署名入りのサーバー証明書を使用することがあります。|  
|はい|いいえ|無視|無視|暗号化は、検証可能なサーバー証明書が提供されている場合にのみ行われます。それ以外の場合は、接続試行が失敗します。|  
|はい|はい|無効 (既定値)|無視|暗号化は常に行われますが、自己署名入りのサーバー証明書を使用することがあります。|  
|はい|はい|はい|無効 (既定値)|暗号化は、検証可能なサーバー証明書が提供されている場合にのみ行われます。それ以外の場合は、接続試行が失敗します。|  
|はい|はい|はい|はい|暗号化は常に行われますが、自己署名入りのサーバー証明書を使用することがあります。|  
||||||

> [!CAUTION]
> 上の表では、さまざまな構成でのシステムの動作についてのみ説明しています。 セキュリティで保護された接続を実現するには、クライアントとサーバーの両方で暗号化が必要であることを確実にします。 また、確実にサーバーに検証可能な証明書があり、クライアントの **TrustServerCertificate** 設定が FALSE に設定されているようにします。

## <a name="ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server 
 OLE DB Driver for SQL Server では、DBPROPSET_SQLSERVERDBINIT プロパティ セットに実装される SSPROP_INIT_TRUST_SERVER_CERTIFICATE データ ソース初期化プロパティの追加によって、検証を伴わない暗号化がサポートされます。 また、新しい接続文字列のキーワードとして "TrustServerCertificate" が追加されました。 "TrustServerCertificate" は、yes または no を値として受け取ります。既定値は no です。 サービス コンポーネントを使用しているときは、"TrustServerCertificate" は true または false を値として受け取ります。既定値は false です。  
  
 DBPROPSET_SQLSERVERDBINIT プロパティ セットに行われた機能強化の詳細については、「[初期化プロパティと承認プロパティ](../../oledb/ole-db-data-source-objects/initialization-and-authorization-properties.md)」を参照してください。  

  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)  
  
  
