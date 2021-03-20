---
title: MDAC から OLE DB Driver for SQL Server へのアプリケーションの更新
description: 以前の OLE DB Provider for SQL Server と新しい OLE DB Driver for SQL Server との変更点、および新しいドライバーに更新するために知っておくべきことについて説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- MDAC [SQL Server]
- MSOLEDBSQL, vs. MDAC
- OLE DB Driver for SQL Server, vs. MDAC
- data access [OLE DB Driver for SQL Server], vs. MDAC
- OLE DB Driver for SQL Server, updating applications
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: a6acf12dd61b4d772139811b9ead823ded0b635c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751212"
---
# <a name="updating-an-application-to-ole-db-driver-for-sql-server-from-mdac"></a>MDAC から OLE DB Driver for SQL Server へのアプリケーションの更新
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server と MDAC (Microsoft Data Access Components) には多くの違いがあります。Windows Vista 以降、データ アクセス コンポーネントは Windows DAC (Windows Data Access Components) と呼ばれています。 どちらを使用しても [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データベースへのネイティブ データ アクセスは可能ですが、OLE DB Driver for SQL Server は、旧バージョンとの互換性を維持しながら、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の新機能を公開するように設計されています。   

 また、MDAC には OLE DB、ODBC、および ActiveX Data Objects (ADO) を使用するためのコンポーネントが含まれていますが、OLE DB Driver for SQL Server には、OLE DB しか実装されていません (ただし、ADO から OLE DB Driver for SQL Server の機能にアクセスすることはできます)。  

 OLE DB Driver for SQL Server と MDAC のその他の違いを次に示します。  

-   ADO を使用して OLE DB Driver for SQL Server にアクセスした場合に使用できるフィルター機能は、同じユーザーが SQL OLE DB プロバイダーにアクセスした場合よりも少ないことがあります。  

-   ADO アプリケーションで OLE DB Driver for SQL Server を使用している場合に計算列の更新を試みると、エラーが報告されます。 MDAC では、更新が受け付けられたうえで、無視されていました。  

-   OLE DB Driver for SQL Server は、1 つの自己完結型ダイナミック リンク ライブラリ (DLL) ファイルです。 配布を容易にすることと、セキュリティの危険にさらされるのを防ぐことの両面から、最小限のインターフェイスしか公開されません。  

-   OLE DB インターフェイスのみがサポートされます。  

-   OLE DB Driver for SQL Server の名前は MDAC で使用される名前と異なります。  

-   MDAC コンポーネントによって提供される、ユーザーがアクセスできる機能は、OLE DB Driver for SQL Server の使用時に使用できます。 この機能には、接続プーリング、ADO サポート、クライアント カーソルのサポートなどがあります。 このような機能を使用するとき、OLE DB Driver for SQL Server ではデータベース接続のみが提供されます。 MDAC は、トレース、管理制御、およびパフォーマンス カウンターなどの機能を提供します。  

-   アプリケーションでは、OLE DB Driver for SQL Server と共に OLE DB Core Services を使用できます。ただし、OLE DB カーソル エンジンを使用する場合、このカーソル エンジンでは新しい [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] のデータ型が認識されないため、発生する可能性のある問題を回避するために、アプリケーションでデータ型互換性オプションを使用する必要があります。  

-   OLE DB Driver for SQL Server は、以前の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データベースへのアクセスをサポートします。  

-   OLE DB Driver for SQL Server には、XML 統合が含まれていません。 OLE DB Driver for SQL Server では SELECT ...FOR XML クエリはサポートされますが、その他の XML 機能はサポートされません。 ただし、OLE DB Driver for SQL Server では、[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] で導入された **xml** データ型がサポートされています。  

-   OLE DB Driver for SQL Server では、接続文字列の属性のみを使用する、クライアント側のネットワーク ライブラリの構成がサポートされます。 ネットワーク ライブラリをさらに詳細に構成する場合は、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 構成マネージャーを使用する必要があります。  

-   MDAC 接続文字列では **Trusted_Connection** キーワードのブール値 (**true**) を指定できます。 OLE DB Driver for SQL Server では **yes** または **no** を使用する必要があります。  

-   警告とエラーが一部変更されています。 サーバーから返される警告とエラーの重大度は、OLE DB Driver for SQL Server に渡されるときも保持されるようになりました。 特定の警告やエラーのトラッピングに依存しているアプリケーションは、十分にテストする必要があります。  

-   OLE DB Driver for SQL Server では、MDAC よりも厳密なエラー チェックが行われます。そのため、アプリケーションが OLE DB の仕様に厳密に準拠していないときには動作が異なることがあります。 たとえば、SQLOLEDB プロバイダーでは、結果のパラメーターの名前を '\@' で始める必要があるという規則を適用していなかったのに対して、OLE DB Driver for SQL Server では適用します。  

-   OLE DB Driver for SQL Server の失敗した接続に関する動作は MDAC とは異なります。 たとえば、MDAC では接続が失敗した場合はキャッシュされたプロパティ値を返しますが、OLE DB Driver for SQL Server では呼び出し元のアプリケーションにエラーを報告します。  

-   OLE DB Driver for SQL Server では、Visual Studio Analyzer イベントは生成されませんが、代わりに Windows トレース イベントが生成されます。  

-   OLE DB Driver for SQL Server とパフォーマンス モニターは併用できません。 パフォーマンス モニターは、Windows に付属している MDAC SQLODBC ドライバーを使用する DSN のみと併用できる Windows ツールです。  

-   OLE DB Driver for SQL Server を [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] 以降のバージョンに接続すると、サーバー エラー 16947 が SQL_ERROR として返されます。 このエラーは、位置指定更新または位置指定削除による行の更新や削除が失敗したときに発生します。 任意のバージョンの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に接続している MDAC では、サーバー エラー 16947 は警告 (SQL_SUCCESS_WITH_INFO) として返されます。  

-   OLE DB Driver for SQL Server には、以前は実装されていなかった、省略可能な OLE DB インターフェイスの **IDBDataSourceAdmin** インターフェイスが実装されています。ただし、この省略可能なインターフェイスに実装されているのは **CreateDataSource** メソッドのみです。 [!INCLUDE[ssNoteDepFutureAvoid](../../../includes/ssnotedepfutureavoid-md.md)]  

-   OLE DB Driver for SQL Server では、TABLE_TYPE を SYNONYM に設定すると、TABLES スキーマ行セットと TABLE_INFO スキーマ行セットのシノニムを返します。  

-   **varchar(max)** データ型、**nvarchar(max)** データ型、**varbinary(max)** データ型、**xml** データ型、**udt** データ型、またはその他のラージ オブジェクト型の戻り値を [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] より前のバージョンのクライアントに返すことはできません。 これらの型を戻り値として使用する場合は、OLE DB Driver for SQL Server を使用する必要があります。  

-   手動および暗黙のトランザクション開始時において、以下のステートメントの実行が MDAC では許可されていましたが、OLE DB Driver for SQL Server では許可されません。 これらは、自動コミット モードで実行する必要があります。  

    -   すべてのフルテキスト操作 (インデックスおよびカタログ DDL)  

    -   すべてのデータベース操作 (データベースの作成、変更、および削除)  

    -   再構成  

    -   Shutdown  

    -   強制終了  

    -   バックアップ  

-   MDAC アプリケーションから [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に接続すると、[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] で導入されたデータ型は、次の表に示すような、[!INCLUDE[ssVersion2000](../../../includes/ssversion2000-md.md)] と互換性を持つデータ型として扱われます。  

    |SQL Server 2005 の型|SQL Server 2000 の型|  
    |--------------------------|--------------------------|  
    |**varchar(max)**|**text**|  
    |**nvarchar(max)**|**ntext**|  
    |**varbinary(max)**|**image**|  
    |**udt**|**varbinary**|  
    |**xml**|**ntext**|  

     この型マッピングは、列のメタデータに返される値に影響を与えます。 たとえば、**text** 列の最大サイズは 2,147,483,647 ですが、OLE DB Driver for SQL Server では、**varchar(max)** 列の最大サイズをプラットフォームに応じて 2,147,483,647 または -1 として報告します。  

-   OLE DB Driver for SQL Server では、旧バージョンとの互換性を維持するために接続文字列のあいまい性が許可されます (たとえば、キーワードを複数回指定したり、位置や優先順位に基づいた解決方法を使用して、競合するキーワードを指定したりすることができます)。 OLE DB Driver for SQL Server の今後のリリースでは、あいまいな接続文字列を使用できなくなる可能性があります。 OLE DB Driver for SQL Server を使用するアプリケーションでは、あいまいな接続文字列を利用しないように変更することをお勧めします。  

-   OLE DB 呼び出しを使用してトランザクションを開始した場合、OLE DB Driver for SQL Server と MDAC とでは動作が異なります。OLE DB Driver for SQL Server ではトランザクションがすぐに開始されますが、MDAC では最初のデータベース アクセスの後にトランザクションが開始されます。 これはストアド プロシージャとバッチの動作に影響を与える可能性があります。[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、バッチまたはストアド プロシージャの実行開始時と実行終了後で @@TRANCOUNT の値が同じである必要があるためです。  

-   OLE DB Driver for SQL Server では、ITransactionLocal::BeginTransaction によってトランザクションがすぐに開始されます。 MDAC では、暗黙のトランザクション モードでのトランザクションを必要とするステートメントがアプリケーションで実行されるまで、トランザクションの開始が遅延されました。 詳細については、「[SET IMPLICIT_TRANSACTIONS &#40;Transact-SQL&#41;](../../../t-sql/statements/set-implicit-transactions-transact-sql.md)」を参照してください。  


 行のバージョン管理機能を使用した Read Committed トランザクション分離は OLE DB Driver for SQL Server と MDAC の両方でサポートされていますが、スナップショット トランザクション分離は OLE DB Driver for SQL Server のみでサポートされています。 (プログラミング用語では、「行のバージョン管理機能を使用した Read Committed トランザクション分離」は「Read Committed トランザクション」と同義語です)。  

## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server を使用したアプリケーションの構築](../../oledb/applications/building-applications-with-oledb-driver-for-sql-server.md)  
