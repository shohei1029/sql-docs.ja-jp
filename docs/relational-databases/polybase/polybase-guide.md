---
title: PolyBase によるデータ仮想化の概要
description: PolyBase を使用すると、Hadoop や Azure Blob Storage など、外部データ ソースからデータを読み取る Transact-SQL クエリを SQL Server インスタンスで処理できるようになります。
ms.date: 03/23/2021
ms.prod: sql
ms.technology: polybase
ms.topic: overview
f1_keywords:
- PolyBase
- PolyBase, guide
helpviewer_keywords:
- PolyBase
- PolyBase, overview
- Hadoop import
- Hadoop export
- Hadoop export, PolyBase overview
- Hadoop import, PolyBase overview
ms.custom: contperf-fy21q2
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: ''
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||>=aps-pdw-2016||=azure-sqldw-latest'
ms.openlocfilehash: debeb0916d8ecb14a5b0f52726bed90f04429fca
ms.sourcegitcommit: 17f05be5c08cf9a503a72b739da5ad8be15baea5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105103299"
---
# <a name="introducing-data-virtualization-with-polybase"></a>PolyBase によるデータ仮想化の概要

[!INCLUDE[appliesto-ss-xxxx-asdw-pdw-md](../../includes/appliesto-ss-xxxx-asdw-pdw-md.md)]

PolyBase は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 用のデータ仮想化機能です。 

## <a name="what-is-polybase"></a>PolyBase とは

PolyBase を使用すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスにより、クライアント接続ソフトウェアを別途インストールしなくても、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、Oracle、Teradata、MongoDB、Hadoop クラスター、Cosmos DB から T-SQL を使用してデータを直接照会できます。 また、汎用 ODBC コネクタを使用して、サードパーティの ODBC ドライバーを使用して追加のプロバイダーに接続することもできます。 PolyBase を使用すると、T-SQL クエリで、外部ソースからのデータを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンス内のリレーショナル テーブルに結合できるようになります。  

PolyBase 機能を使用したデータの仮想化の主な用途は、データを元の場所と形式で維持されるようにすることです。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスを介してデータを仮想化して、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内の他のテーブルと同じようにクエリを行うことができます。 このプロセスにより、データ移動に必要な ETL プロセスを最小化できます。 このデータ仮想化シナリオは、PolyBase コネクタを使用することによって実現します。

> [!NOTE]
> PolyBase 機能の一部の機能は、**Azure SQL マネージド インスタンス** のプライベート プレビューに含まれており、これには Azure Data Lake Storage (ADLS) Gen2 で外部データ (Parquet ファイル) に対してクエリを実行する機能が含まれています。 プライベート プレビューには、一般提供されていないクライアント ライブラリやテスト用のドキュメントへのアクセスが含まれています。 機能をお試しになったり、フィードバックや質問を共有したりしたい場合は、[Azure SQL Managed Instance PolyBase プライベート プレビュー ガイド](https://sqlmipg.blob.core.windows.net/azsqlpolybaseshare/Azure_SQL_Managed_Instance_Polybase_Private_Preview_Onboarding_Guide.pdf)を参照してください。

### <a name="supported-sql-products-and-services"></a>サポートされる SQL 製品とサービス

PolyBase では、次の Microsoft の SQL 製品にこれらと同じ機能を提供します。

- [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降のバージョン (Windows のみ)
- [!INCLUDE[sssql19-md](../../includes/sssql19-md.md)] 以降のバージョン (Linux)
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[pdw](../../includes/sspdw-md.md)] (PDW)、Analytics Platform System (APS) でホストされます 
- [!INCLUDE[ssazuresynapse_md](../../includes/ssazuresynapse_md.md)]

### <a name="polybase-connectors"></a>PolyBase コネクタ

 PolyBase 機能は、次の外部データ ソースへの接続を提供します。

| 外部データ ソース     | PolyBase を使用した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] | APS PDW    | [!INCLUDE[ssazuresynapse_md](../../includes/ssazuresynapse_md.md)] |
|---------------------------|--------------------------|------------|---------------|
| Oracle、MongoDB、Teradata | Read                     | **いいえ**     | **いいえ**        |  
| 汎用 ODBC              | 読み取り (Windows のみ)      | **いいえ**     | **いいえ**        |  
| Azure Storage             | 読み取り/書き込み               | 読み取り/書き込み | 読み取り/書き込み    |
| Hadoop                    | 読み取り/書き込み               | 読み取り/書き込み | **いいえ**        |  
| [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] | Read                     | **いいえ**     | **いいえ**        |  
|                           |                          |            |               |


* [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] では、Hadoop および Azure Blob Storage への接続をサポートする PolyBase が導入されました。
* [!INCLUDE[sssql19-md](../../includes/sssql19-md.md)] では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、Oracle、Teradata、MongoDB などの追加のコネクタが導入されました。

 外部コネクタの例を次に示します。

- [[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]](polybase-configure-sql-server.md)
- [Oracle](polybase-configure-oracle.md)
- [Teradata](polybase-configure-teradata.md)
- [MongoDB](polybase-configure-mongodb.md)
- [Hadoop](polybase-configure-hadoop.md)*

\* PolyBase は、Hortonworks Data Platform (HDP) と Cloudera Distributed Hadoop (CDH) の 2 つの Hadoop プロバイダーをサポートしています。

 PolyBase を使用するには [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスで次のようにします。

1. [Windows に PolyBase をインストール](polybase-installation.md)するか、[Linux に PolyBase をインストール](polybase-linux-setup.md)します。
1. [!INCLUDE[sssql19-md](../../includes/sssql19-md.md)] 以降では、必要に応じて [sp_configure で PolyBase を有効にします](polybase-installation.md#enable)。 
1. [外部データ ソース](../../t-sql/statements/create-external-data-source-transact-sql.md)を作成します。
1. [外部テーブル](../../t-sql/statements/create-external-table-transact-sql.md)を作成します。



### <a name="azure-integration"></a>Azure との統合

下層 の PolyBase のサポートにより、T-SQL クエリでは Azure Blob Storage のデータをインポートおよびエクスポートすることもできます。 さらに、PolyBase によって、[!INCLUDE[ssazuresynapse_md](../../includes/ssazuresynapse_md.md)] で Azure Data Lake Store および Azure Blob Storage のデータをインポートおよびエクスポートできるようになります。

## <a name="why-use-polybase"></a>PolyBase を使用する理由

PolyBase を使用すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのデータを外部データと結合できます。 PolyBase によってデータが外部データ ソースに結合される前に、次のいずれかを実行できます。

- すべてのデータが 1 つの場所に配置されるように、データの半分を転送します。
- 両方のデータのソースに対してクエリを実行した後、クライアント レベルでデータを結合および統合するためにカスタムのクエリ ロジックを記述する。

PolyBase を使用すると、Transact-SQL を使用してデータを結合するだけで済みます。

PolyBase を使用するうえで Hadoop 環境に追加のソフトウェアをインストールする必要はありません。 外部データを照会するには、データベース テーブルの照会に使用したのと同じ T-SQL 構文を使用します。 PolyBase が実装する補助的なアクションは、すべて透過的に実行されます。 クエリの作成者には、外部ソースに関する知識が必要ありません。

### <a name="polybase-uses"></a>PolyBase の使用

PolyBase を使用すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で次のシナリオに対応できます。

- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスまたは PDW から Hadoop に格納されているデータのクエリを実行する。** ユーザーは、たとえば Hadoop など、コスト効果の高いスケーラブルな分散システムにデータを格納しています。 PolyBase を使用すると、T-SQL で簡単にデータを照会できます。

- **Azure Blob Storage に格納されたデータのクエリを実行する。** Azure BLOB ストレージは、Azure サービスによって使用されるデータを格納するのに便利な場所です。 PolyBase を使用すると、T-SQL で簡単にデータにアクセスできます。

- **Hadoop、Azure Blob Storage、Azure Data Lake Store からデータをインポートする。** Hadoop、Azure Blob Storage、または Azure Data Lake Store からリレーショナル テーブルにデータをインポートすることで、Microsoft SQL の高速な列ストア テクノロジおよび分析機能を活用できます。 ETL やインポート ツールを個別に用意する必要はありません。

- **Hadoop、Azure Blob Storage、または Azure Data Lake Store にデータをエクスポートする。** Hadoop、Azure Blob Storage、または Azure Data Lake Store にデータをアーカイブすることで、コスト効果の高いストレージを実現し、アクセスしやすいようにそのストレージをオンライン状態にしておくことができます。

- **BI ツールと統合する。** PolyBase を Microsoft のビジネス インテリジェンスや分析スタックと一緒に使用したり、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と互換性のあるサードパーティ ツールを使用したりすることができます。

## <a name="performance"></a>パフォーマンス

- **Hadoop に計算をプッシュする。** PolyBase により、クエリ全体を最適化するために計算の一部が外部ソースにプッシュされます。 クエリ オプティマイザーでは、クエリのパフォーマンスが向上する場合は Hadoop への計算のプッシュを行うためのコスト ベースの決定が行われます。  クエリ オプティマイザーでは、コスト ベースの決定に外部テーブルの統計が使用されます。 計算のプッシュでは、MapReduce ジョブが作成され、Hadoop の分散コンピューティング リソースが活用されます。 詳細については、「[PolyBase でのプッシュダウン計算](polybase-pushdown-computation.md)」を参照してください。 

- **コンピューティング リソースをスケーリングする。** クエリのパフォーマンスを向上させるために、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [PolyBase スケールアウト グループ](../../relational-databases/polybase/polybase-scale-out-groups.md)を使用できます。 これにより、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスと Hadoop ノードの間の並列データ転送が可能になります。また、外部データに対する操作のためのコンピューティング リソースが追加されます。

## <a name="next-steps"></a>次のステップ

PolyBase を使用する前に、[Windows に PolyBase をインストール](polybase-installation.md)するか、[Linux に PolyBase をインストール](polybase-linux-setup.md)し、必要に応じて [sp_configure で PolyBase を有効](polybase-installation.md#enable)にする必要があります。 その後、使用するデータ ソースに応じて、次の構成ガイドを参照してください。

- [Hadoop](polybase-configure-hadoop.md)
- [Azure Blob Storage](polybase-configure-azure-blob-storage.md)
- [SQL Server](polybase-configure-sql-server.md)
- [Oracle](polybase-configure-oracle.md)
- [Teradata](polybase-configure-teradata.md)
- [MongoDB](polybase-configure-mongodb.md)
- [ODBC ジェネリック型](polybase-configure-odbc-generic.md)
