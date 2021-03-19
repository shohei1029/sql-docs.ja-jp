---
title: 互換性証明書 | Microsoft Docs
description: 互換性証明書により、アプリケーションの互換性のリスクがなくなり、オンプレミスとクラウドで SQL Server データベースをアップグレードできるようになります。
ms.custom: ''
ms.date: 08/26/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- compatibility [SQL Server], databases
- compatibility levels [SQL Server], after upgrade
- Database Engine [SQL Server], upgrading
- Databases [SQL Server], upgrading
- Database Engine [SQL Server], upgrading
- compatibility [SQL Server], certification
- compatibility level [SQL Server], upgrades
ms.assetid: 3c036813-36cf-4415-a0c9-248d0a433856
author: pmasl
ms.author: pelopes
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 8921ce4bc3ba2317e2d8ed5324d26d1dfba52b3e
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611125"
---
# <a name="compatibility-certification"></a>互換性証明書

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

互換性証明書を利用すると、企業はオンプレミス、クラウド、エッジに置いている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースをアップグレードし、最新化し、アプリケーションの互換性リスクをなくすことができます。 

同じ [!INCLUDE[ssde_md](../../includes/ssde_md.md)] が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] の両方を支援します (Managed Instance を含む)。 このように [!INCLUDE[ssde_md](../../includes/ssde_md.md)] が共有されることで、ユーザー データベースをオンプレミスの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] の間でシームレスに移動できて、かつ、[!INCLUDE[tsql](../../includes/tsql-md.md)] としてデータベースで実行されるアプリケーション コードはそのソース システムで実行される場合と同様に機能します。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が新しくリリースされるたびに、既定の互換性レベルが [!INCLUDE[ssDE](../../includes/ssde-md.md)] のバージョンに設定されます。 ただし、以前のバージョンの互換性レベルが維持され、既存のアプリケーションの互換性が残ります。 この互換性マトリックスは[こちら](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#supported-dbcompats)で確認できます。
そのため、特定の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バージョンで動作することが認定されているアプリケーションは、**実際にはそのバージョンの既定の互換性レベルで動作することが認定されていました**。

たとえば、データベース互換性レベル 130 は [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] の既定でした。 互換性レベルは [!INCLUDE[tsql](../../includes/tsql-md.md)] の特定の機能およびクエリ最適化動作を強制するものであるため、 **[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で動作することが認定されたデータベースはデータベース互換性レベル 130 で暗黙的に認定されていました**。 このデータベースは、データベース互換性レベルが 130 で維持される限り、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE [sssql19-md](../../includes/sssql19-md.md)] など) と [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] の最近のバージョンで現状のまま動作できます。

これは [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 継続的インテグレーション運用モデルの基本原則です。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] は Azure で継続的に改善され、アップグレードされますが、既存のデータベースで現行の互換性レベルが維持されるため、基礎となる [!INCLUDE[ssde_md](../../includes/ssde_md.md)] にアップグレードされた後でも、引き続き設計どおりに動作します。 

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] および [!INCLUDE[ssSDSMIfull](../../includes/sssdsmifull-md.md)] での SharePoint Server 2016 と SharePoint Server 2019 の認定も、この方法で行われます。これにより、これらのバージョンの SharePoint Server に対して、サポートされているデータベース互換性レベルを使用できる [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] を配置できるようになります。 詳細については、「[SharePoint Server 2016 のハードウェア要件およびソフトウェア要件](/sharepoint/install/hardware-and-software-requirements#minimum-requirements-for-a-database-server-in-a-farm)」と、「[SharePoint Server 2019 のハードウェア要件およびソフトウェア要件](/sharepoint/install/hardware-and-software-requirements-2019#minimum-requirements-for-a-database-server-in-a-farm)」を参照してください。

## <a name="managing-upgrade-risk-with-compatibility-certification"></a>互換性証明書によるアップグレード リスクの管理
互換性証明書の使用は、データベースの最新化に役立つアプローチです。 互換性レベルに基づいて認定することで、開発者は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] でサポートするアプリケーションの技術的要件を設定しつつ、アプリケーションのライフサイクルをデータベース プラットフォームのライフサイクルから切り離します。 そうすることで、企業はライフサイクル ポリシーの必要に応じて [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] を継続的にアップグレードしながら、コードに依存しない新しい拡張性やパフォーマンス拡張機能を活用できます。また、アップグレードを通してアプリケーションは **その機能的ステータスを維持します**。

機能性とパフォーマンスに悪影響を与える可能性は、いかなるアップグレードにおいても主要なリスク要因です。 互換性証明書は、そのようなアップグレード リスクの管理に安心を与えます。

-  [!INCLUDE[tsql](../../includes/tsql-md.md)] の動作に関連するものにおいて、あらゆる変更は、あるアプリケーションが正しいことを再認定する必要があることを意味します。 しかしながら、[データベース互換性レベル](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)設定は、サーバー全体ではなく、指定のデータベースに対してのみ、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] との下位互換性を与えます。 データベース互換性レベルを現状のまま維持することで、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] アップグレードの前後で、既存のアプリケーション クエリは引き続き同じ動作を示します。 [!INCLUDE[tsql](../../includes/tsql-md.md)] の動作と互換性レベルについては、「[旧バージョンとの互換性を維持するための互換性レベルの使用](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#backwardCompat)」を参照してください。

-  パフォーマンスに関連するものにおいて、クエリ オプティマイザーの機能拡張がすべてのバージョンで導入されるため、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] のバージョンが異なれば、クエリ プランが異なることが予想される可能性があります。 一部の変更が特定のクエリまたはワークロードにとって害になる可能性がある場合、1 つのアップグレードの範囲におけるクエリ プランの違いは通常、リスクとなります。 裏を返せば、このリスクは再認定の動機であり、アップグレードを遅らせ、ライフサイクルやサポートに問題を与えることがあります。 
   アップグレード リスクの軽減は、クエリ オプティマイザーの機能拡張が新しいリリースの既定互換性レベルに制限される理由です (言い換えると、あらゆる新しいバージョンで最も高い互換性レベルを利用できます)。 互換性証明書には、**クエリ プラン シェイプ保護** が含まれます。[!INCLUDE[ssde_md](../../includes/ssde_md.md)] アップグレードの直後、データベース互換性レベルを現状のまま維持するという考えは、新しいバージョンでアップグレード前と同じクエリ最適化モデルを使用するということであり、クエリ プラン シェイプは変更されません。 
   詳細については、この記事の「[クエリ プラン シェイプを使用する理由](#queryplan_shape)」セクションを参照してください。
   
互換性レベルの詳細については、「[旧バージョンとの互換性を維持するための互換性レベルの使用](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#backwardCompat)」を参照してください。
   
> [!IMPORTANT]
> 特定の互換性レベルに対して既に認定されている既存のアプリケーションについては、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] をアップグレードし、以前のデータベース互換性レベルを **維持します**。 このシナリオでは、アプリケーションを再認定する必要はありません。 詳細については、この記事の後半に出てくる「[互換性レベルとデータベース エンジンのアップグレード](#compatibility-levels-and-database-engine-upgrades)」を参照してください。
>
> 新しい開発作業の場合、あるいは[インテリジェント クエリ処理](../../relational-databases/performance/intelligent-query-processing.md)のような新しい機能と一部の新しい [!INCLUDE[tsql](../../includes/tsql-md.md)] を既存のアプリケーションで使用する必要があるとき、データベース互換性レベルを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で利用できる最新のレベルにアップグレードすることを計画し、その互換性レベルでアプリケーションが動作することを再認定します。 データベース互換性レベルのアップグレードに関する詳細については、「[データベース互換性レベルのアップグレードのベスト プラクティス](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#best-practices-for-upgrading-database-compatibility-level)」を参照してください。
   
### <a name="why-query-plan-shape"></a><a name="queryplan_shape"></a> クエリ プラン シェイプを使用する理由      
クエリ プラン シェイプは、クエリ プランを構成するさまざまな演算子の視覚的表現を指します。 これには、シーク、スキャン、結合、並べ替えなどの演算子に加えて、データの流れを示す演算子間のつながりと、意図した結果セットを生成するために実行する必要がある演算の順序が含まれます。 クエリ プラン シェイプは、クエリ オプティマイザーによって決定されます。

アップグレード中のクエリ パフォーマンスを常に予測できるように、基本的な目標の 1 つは、同じクエリ プラン シェイプを使用することになります。 これは、基礎となる [!INCLUDE[ssde_md](../../includes/ssde_md.md)] のバージョンが異なる場合でも、アップグレードの直後にデータベース互換性レベルを変更しないことで達成できます。 他には、クエリ実行エコシステムで変更がない場合 (利用できるリソースの大幅な変更など)、または基礎データのデータ配布で変更がない場合、クエリのパフォーマンスを変えないでください。 

ただし、クエリ プラン シェイプを維持することは、アップグレード後にパフォーマンス上の影響を出す唯一の要因ではありません。 データベースを新しい [!INCLUDE[ssde_md](../../includes/ssde_md.md)] に移動し、環境も変える場合、クエリ プランでバージョンに関係なく同じシェイプが維持されるとしても、クエリのパフォーマンスに直後に影響を与える要因が持ち込まれる可能性があります。 そのような環境の変更には、新しい [!INCLUDE[ssde_md](../../includes/ssde_md.md)] で利用できるメモリと CPU を増やす/減らす、サーバーまたはデータベースの構成オプションを変更する、クエリ プランの作成方法に影響を与えるデータ配布を変更するなどがあります。 このような理由から、データベース互換性レベルを維持するとクエリ プラン **シェイプ** の変更の影響を受けずに済むが、クエリのパフォーマンスに影響を与える他の環境的変更 (ユーザーが変更を始めることもある) からは守られないということを理解しておくことが重要です。

詳細については、「[クエリ処理アーキテクチャ ガイド](../../relational-databases/query-processing-architecture-guide.md#optimizing-select-statements)」をご覧ください。
   
## <a name="compatibility-certification-benefits"></a>互換性証明書の利点
データベースの認定には、名前付きバージョン手法ではなく互換性基準手法として直接の利点があります。

-  **アプリケーション認定をプラットフォームから分離します**。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] を共有することで、[!INCLUDE[tsql](../../includes/tsql-md.md)] クエリを実行する必要があるだけのアプリケーションの場合、Azure とオンプレミスで別々の認定プロセスを維持する必要はありません。
-  **アップグレード リスクを減らします**。データベース プラットフォームを最新化するとき、アプリケーションとプラットフォーム レイヤーのアップグレード サイクルを分離できるため、中断が少なくなり、変更管理が改善されます。
-  **コードを変更せずにアップグレードします**。 互換性レベルをソース システムと同じに維持することで、コードを変更せずに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] の新しいバージョンにアップグレードできます。上位のデータベース互換性でのみ利用できる拡張機能をアプリケーションで活用する必要がない限り、すぐに再認定する必要はありません。
- **管理性とスケーラビリティを向上させます**。データベース互換性レベルで制限されない拡張機能を使用することで、アプリケーションの変更が必要ありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の場合、次のような例があります。 
  - 新しい[システム動的管理ビュー](../../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)、[拡張イベント](../../relational-databases/extended-events/extended-events.md)、[自動チューニング](../../relational-databases/automatic-tuning/automatic-tuning.md)で監視機能と問題解決機能が充実し、改善されました。 
  - スケーラビリティが向上しました。たとえば、[自動ソフト NUMA](../../database-engine/configure-windows/soft-numa-sql-server.md#automatic-soft-numa)、[高速データベース復旧](../../relational-databases/accelerated-database-recovery-concepts.md)、[メモリ最適化 tempdb メタデータ](../../relational-databases/in-memory-database.md#memory-optimized-tempdb-metadata)などです。

新しいデータベースは [!INCLUDE[ssde_md](../../includes/ssde_md.md)] バージョンの既定の互換性レベルに引き続き設定されます。 ただし、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の以前のバージョンから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] の新しいバージョンにデータベースを移すと、データベースはその既存の互換性レベルを維持します。 

> [!IMPORTANT]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] の新しいバージョンにデータベースを移す前に、データベース互換性レベルが引き続きサポートされることを確認します。 データベース互換性レベル サポート マトリックスは[こちら](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#arguments)で確認できます。 
>
> 許可されるレベル (たとえば、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] の既定値であった 90) より低い互換性レベルのデータベースをアップグレードすると、許可される最も下の互換性レベル (100) にデータベースが設定されます。
>
> 現在の互換性レベルを特定するには、**sys.databases** の [compatibility_level](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 列に対してクエリを実行します。

## <a name="compatibility-levels-and-database-engine-upgrades"></a>互換性レベルとデータベース エンジンのアップグレード
アップグレード前に存在したデータベース互換性レベルとそのサポート可能なステータスを維持しながら、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] を最新バージョンにアップグレードするには、**データベース** (ストアド プロシージャ、関数、トリガーなどのプログラミング オブジェクト) および **アプリケーション** (アプリケーションによって送信される動的コードをキャプチャするワークロード トレースを使用) において、アプリケーション コードの **攻撃可能な領域に静的な機能検証を実行** することをお勧めします。

これは、[Microsoft Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) ツール (DMA) を使用して簡単に行うことができます。 DMA ツールの出力でエラーが見つからなければ、あるいは機能性に不足や非互換性がなければ、新しいターゲット バージョンでアプリケーションの機能が退化することはありません。 データベースが新しいバージョンで動作することを保証するために変更が必要な場合は、DMA を使用して、変更が必要な箇所と使用できる回避策を特定できます。 詳細については、「[Data Migration Assistant の概要](../../dma/dma-overview.md)」を参照してください。   

> [!TIP]
> この機能検証は、レガシ バージョン ([!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] や [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] など) から新しいバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] にデータベースを移動する場合に特に重要です。データベース互換性レベルで保護されていない、廃止された [!INCLUDE[tsql](../../includes/tsql-md.md)] がアプリケーション コードで使用される可能性があるためです。 ただし、より新しいバージョン ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] など) から [!INCLUDE [sssql19-md](../../includes/sssql19-md.md)] または [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] に移行する場合は、廃止された [!INCLUDE[tsql](../../includes/tsql-md.md)] について心配する必要はありません。 廃止された [!INCLUDE[tsql](../../includes/tsql-md.md)] の詳細については、「[旧バージョンとの互換性を維持するための互換性レベルの使用](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#backwardCompat)」を参照してください。

> [!NOTE]
> DMA は、レベル 100 以降のデータベース互換性レベルに対応しています。 ソース バージョンとしての [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] は除外されます。   

> [!IMPORTANT]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)] では、最小限のテストをいくつか行い、アップグレードが成功し、さらに前のデータベース互換性レベルが維持されていることを確認することをお勧めしています。 自分のアプリケーションとシナリオでは何が「最小限のテスト」に相当するのかは、ご自身で判断してください。   

> [!IMPORTANT]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)] は、次の場合にクエリ プラン シェイプ保護を提供します。
>
> - 前の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バージョン (ソース) が実行されていたハードウェアに相当するハードウェアで新しい [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バージョン (ターゲット) が実行されるとき。
> - ターゲット [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] とソース [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の両方で同じ[サポートされているデータベース互換性レベル](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#supported-dbcompats)が使用されるとき。
> - ターゲット [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] とソース [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の両方で、**同じ** データベースとワークロードが使用されます。 
>
> 上の条件で発生する (ソース [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と比較したときの) クエリ プラン シェイプ退化は対処されます。 このような場合は、Microsoft カスタマー サポートにお問い合わせください。
  
## <a name="see-also"></a>参照 
[ALTER DATABASE 互換性レベル](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)       
[データベースの互換性レベルの表示または変更](../../relational-databases/databases/view-or-change-the-compatibility-level-of-a-database.md)       
[データベース互換性レベルのアップグレードのベスト プラクティス](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#best-practices-for-upgrading-database-compatibility-level)