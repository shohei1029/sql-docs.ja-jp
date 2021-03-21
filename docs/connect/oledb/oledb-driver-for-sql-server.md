---
title: Microsoft OLE DB Driver for SQL Server
description: Microsoft OLE DB Driver for SQL Server を使うと、標準の OLE DB API を使用して SQL Server や Azure SQL Database に接続できます。
ms.custom: ''
ms.date: 05/06/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- MSOLEDBSQL, about OLE DB Driver for SQL Server
- OLE DB Driver for SQL Server, about OLE DB Driver for SQL Server
- data access [OLE DB Driver for SQL Server], about OLE DB Driver for SQL Server
- data access [OLE DB Driver for SQL Server]
- OLE DB Driver for SQL Server
- MSOLEDBSQL
- native data access [OLE DB Driver for SQL Server]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 5ed2642e679cb9822f98285c7dd13f1bdfeda6ef
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748302"
---
# <a name="microsoft-ole-db-driver-for-sql-server"></a>Microsoft OLE DB Driver for SQL Server
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../includes/driver_oledb_download.md)]

OLE DB Driver for SQL Server とは、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で導入された OLE DB で使用されるスタンドアロンのデータ アクセス アプリケーション プログラミング インターフェイス (API) です。 OLE DB Driver for SQL Server では、1 つのダイナミック リンク ライブラリ (DLL) で SQL OLE DB ドライバーが提供されます。 また、Windows Data Access Components (Windows DAC、以前の Microsoft Data Access Components (MDAC)) にはない新しい機能も用意されています。 MARS (複数のアクティブな結果セット)、UDT (ユーザー定義データ型)、クエリ通知、スナップショット分離、XML データ型のサポートなどの [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で導入された機能を必要とする新しいアプリケーションを作成したり、これらの機能で既存のアプリケーションを強化したりするために、OLE DB Driver for SQL Server を使用できます。  
  
> [!NOTE]  
> OLE DB Driver for SQL Server と Windows DAC の相違点の一覧や、Windows DAC アプリケーションを OLE DB Driver for SQL Server に更新する前の考慮事項に関する情報については、「[MDAC から OLE DB Driver for SQL Server へのアプリケーションの更新」](../oledb/applications/updating-an-application-to-oledb-driver-for-sql-server-from-mdac.md)」を参照してください。  

> [!IMPORTANT]
> 以前の [Microsoft OLE DB Provider for SQL Server](../../ado/guide/appendixes/microsoft-ole-db-provider-for-sql-server.md) (SQLOLEDB) と [SQL Server Native Client OLE DB](../../relational-databases/native-client/sql-server-native-client.md) プロバイダー (SQLNCLI) は非推奨のままであり、新しい開発作業にはどちらの使用もお勧めできません。
  
 また、OLE DB Driver for SQL Server は、Windows DAC 付属の OLE DB Core Services と共に使用できますが、必須ではありません。OLE DB Core Services を使用するかどうかは、個々のアプリケーションの要件 (たとえば、接続プールが必要であるかどうかなど) によって異なります。  
  
 ADO (ActiveX Data Object) アプリケーションで OLE DB Driver for SQL Server を使用できますが、ADO を使用するときは **DataTypeCompatibility** 接続文字列キーワード (またはそれに対応する **DataSource** プロパティ) を指定することをお勧めします。 OLE DB Driver for SQL Server を使用すると、接続文字列のキーワード、OLE DB プロパティ、または [!INCLUDE[tsql](../../includes/tsql-md.md)] から OLE DB Driver for SQL Server を経由することにより、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で導入された上記の新機能を ADO アプリケーションで利用できます。 ADO で新機能を使用する方法の詳細については、「[OLE DB Driver for SQL Server での ADO の使用](../oledb/applications/using-ado-with-oledb-driver-for-sql-server.md)」を参照してください。  
  
 OLE DB Driver for SQL Server は、OLE DB を使用して簡単に SQL Server へのネイティブ データ アクセスを実現できるように設計されています。 Microsoft Windows プラットフォームの一部になっている既存の Windows DAC コンポーネントを変更することなく新しいデータ アクセス機能を導入および展開できます。  
  
 OLE DB Driver for SQL Server では、Windows DAC にあるコンポーネントが使用されますが、明確に特定バージョンの Windows DAC に依存しているわけではありません。 OLE DB Driver for SQL Server は、OLE DB Driver for SQL Server でサポートされるオペレーティング システムにインストールされているバージョンの Windows DAC と共に使用できます。  

 ## <a name="different-generations-of-ole-db-drivers"></a>OLE DB Driver のさまざまな世代

SQL Server 向けの Microsoft OLE DB プロバイダーには 3 つの世代があります。

### <a name="1-microsoft-ole-db-provider-for-sql-server-sqloledb"></a>1.Microsoft OLE DB Provider for SQL Server (SQLOLEDB)
[Microsoft OLE DB Provider for SQL Server](../../ado/guide/appendixes/microsoft-ole-db-provider-for-sql-server.md) (SQLOLEDB) は、現在でも [Windows Data Access Components](/previous-versions/windows/desktop/ms692897(v=vs.85)) の一部として付属しています。 これに対する保守は今後行われません。また、新規開発にこのドライバーを使用することはお勧めしません。

### <a name="2-sql-server-native-client-snac"></a>2.SQL Server Native Client (SNAC)
[SQL Server Native Client (SNAC)](../../relational-databases/native-client/sql-server-native-client.md) は [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] から始まり、OLE DB プロバイダー インターフェイス (SQLNCLI) が含まれる、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] から [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] に付属する OLE DB ドライバーです。

[2011 年に非推奨として発表済み](/archive/blogs/sqlnativeclient/microsoft-is-aligning-with-odbc-for-native-relational-data-access)であり、新規開発にこのドライバーを使用することはお勧めしません。 SNAC ライフサイクルと使用可能なダウンロードの詳細については、[「SNAC lifecycle explained」 (SNAC ライフサイクルの説明)](/archive/blogs/sqlreleaseservices/snac-lifecycle-explained) を参照してください。

### <a name="3-microsoft-ole-db-driver-for-sql-server-msoledbsql"></a>3.Microsoft OLE DB Driver for SQL Server (MSOLEDBSQL)
2018 年に OLE DB の[非推奨が取り消しとなり](/archive/blogs/sqlnativeclient/announcing-the-new-release-of-ole-db-driver-for-sql-server)、リリースされました。

新しい OLE DB プロバイダーは、Microsoft OLE DB Driver for SQL Server (MSOLEDBSQL) と呼ばれます。 この新しいプロバイダーは今後最新のサーバー機能で更新されます。

> [!NOTE]
> 新しい Microsoft OLE DB Driver for SQL Server を既存のアプリケーションで使用するには、使用している接続文字列を SQLOLEDB または SQLNCLI から MSOLEDBSQL に変換することを計画する必要があります。
  
## <a name="in-this-section"></a>このセクションの内容  
[OLE DB Driver for SQL Server をいつ使用するか](../oledb/when-to-use-oledb-driver-for-sql-server.md)  
 マイクロソフトのデータ アクセス テクノロジにおける OLE DB Driver for SQL Server の位置付け、Windows DAC および ADO.NET との比較、使用するデータ アクセス テクノロジの決定方法について説明します。  
  
 [OLE DB Driver for SQL Server の機能](../oledb/features/oledb-driver-for-sql-server-features.md )  
 OLE DB Driver for SQL Server でサポートされている機能について説明します。  
  
 [OLE DB Driver for SQL Server を使用したアプリケーションの構築](../oledb/applications/building-applications-with-oledb-driver-for-sql-server.md)  
 OLE DB Driver for SQL Server の開発における、Windows DAC との違い、使用されるコンポーネント、ADO と併用する方法などの概要を示します。  
  
 このセクションではまた、OLE DB Driver for SQL Server のインストールと展開について、OLE DB Driver for SQL Server ライブラリの再配布方法も含めて説明します。  
  
 [OLE DB Driver for SQL Server のシステム要件](../oledb/system-requirements-for-oledb-driver-for-sql-server.md)  
 OLE DB Driver for SQL Server を使用するために必要なシステム リソースについて説明します。  
  
 [OLE DB Driver for SQL Server のプログラミング](../oledb/ole-db/oledb-driver-for-sql-server-programming.md)  
 OLE DB Driver for SQL Server の使用に関する情報を提供します。  
  
 [OLE DB Driver for SQL Server の詳細情報の検索](../oledb/finding-more-oledb-driver-for-sql-server-information.md)  
 外部リソースへのリンク、詳しい補助資料など、OLE DB Driver for SQL Server についての関連情報を提供します。  
  
  
## <a name="see-also"></a>関連項目  
 [SQL Server 2005 Native Client からのアプリケーションの更新](../oledb/applications/updating-an-application-from-sql-server-2005-native-client.md)    
 [OLE DB の使用法に関するトピック](../oledb/ole-db-how-to/ole-db-how-to-topics.md)  
  
  
