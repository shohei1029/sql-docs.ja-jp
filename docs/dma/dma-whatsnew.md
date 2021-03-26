---
title: Data Migration Assistant の新機能 (SQL Server) |Microsoft Docs
description: SQL Server と Azure SQL Database の Data Migration Assistant の各リリースの新機能について説明します。
ms.custom: ''
ms.date: 11/05/2019
ms.prod: sql
ms.prod_service: dma
ms.reviewer: ''
ms.technology: dma
ms.topic: conceptual
keywords: ''
helpviewer_keywords:
- Data Migration Assistant, new features
ms.assetid: ''
author: rajeshsetlem
ms.author: rajpo
ms.openlocfilehash: a8f7e74a44b489f22827c42bab2bd5ee60a7658a
ms.sourcegitcommit: 038a3a730cf088a64d55501d9605f2f6b31d573d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2021
ms.locfileid: "105616671"
---
# <a name="whats-new-in-data-migration-assistant"></a>Data Migration Assistant の新機能

この記事では、Data Migration Assistant の各リリースで追加された機能の一覧を示します。

## <a name="data-migration-assistant-v-53"></a>Data Migration Assistant v 5.3

Data Migration Assistant のバージョン5.3 リリースでは、次の機能がサポートされています。
- コマンドラインでの外部アプリケーションのアドホッククエリ評価。 
- Dma.exe.config 構成ファイルのコマンドタイムアウトパラメーターをカスタマイズするユーザー。
- 構成ファイルを使用して DMA コマンドラインを実行する方法を説明する、評価構成サンプルファイルと PowerShell サンプルスクリプト。
- タイムアウト制限のない、オンプレミスの移行でのバックアップと復元の操作。

さらに、このリリースの Data Migration Assistant は .NET 4.8 に更新されており、次の問題のバグ修正が行われています。
- 移行フェーズでは、sysadmin アクセス許可がない評価を実行したり Azure SQL Database に接続したりすることはできません。
- ユーザーは、RDS 評価に対する sysadmin アクセス許可なしに評価を実行することはできません。
- ユーザーが、JSON 評価レポートを Azure Migrate にアップロードするときに問題が発生しました。
- 中国語文字を含むオブジェクトを移行することはできません。

## <a name="data-migration-assistant-v-52"></a>Data Migration Assistant v 5.2

Data Migration Assistant の v1.0 リリースでは、次のことがサポートされています。
- Azure Government および national クラウド (ソブリンオファリング) をサポートする Azure Migrate に評価をアップロードしています。  この機能により、は、Azure SQL に移行する SQL Server のデータ資産の準備状況を評価できるようになります。
- Azure Government および国内のクラウドをサポートする Azure Migrate に評価をアップロードするためのコマンドラインサポート。  これで、Azure migrate project への評価のアップロードを完全に自動化して、統合された Azure SQL 対応レポートを取得できます。 

## <a name="data-migration-assistant-v-50"></a>Data Migration Assistant v 5.0

Data Migration Assistant の v1.0 リリースでは、次の機能がサポートされています。

- 評価とアップグレードのターゲットとして、Windows の場合は2019、Linux の場合は SQL Server 2019 SQL Server。
- 以前のバージョンの Data Migration Assistant で作成された評価の保存と読み込みのサポートを含む、評価の保存と読み込み。
- SSISDB およびパッケージストアでホストされている SSIS パッケージでホストされている SQL Server Integration Services (SSIS) プロジェクトを評価します。 データベース Migration Assistant は、ソースパッケージで使用されているサポートされていない、部分的にサポートされている機能や非推奨の機能、互換性に関する問題を検出し、これらの問題に対処するための推奨事項を提供
- 外部アプリケーションからの SQL クエリ (C# ソースコード内の SQL クエリなど) の評価。 ユーザーは、データアクセス移行ツールキットを使用して、C# ソースコードで使用される SQL クエリの完全な JSON レポートを生成し、そのレポートを Data Migration Assistant にアップロードできます。

さらに、このリリースの Data Migration Assistant には追加の機能強化とバグ修正が含まれており、このツールは .Net 4.7.2 に更新されました。

## <a name="data-migration-assistant-v45"></a>Data Migration Assistant version 4.5

Data Migration Assistant の version 4.5 リリースでは、ファイルシステムでホストされている SQL Server Integration Services (SSIS) パッケージを Azure SQL Database または SQL Managed Instance に移行するための評価がサポートされています。

## <a name="data-migration-assistant-v44"></a>Data Migration Assistant v1.0

Data Migration Assistant の version 4.4 リリースでは、Azure Migrate への評価のアップロードがサポートされています。

## <a name="data-migration-assistant-v43"></a>Data Migration Assistant v1.0

Data Migration Assistant の v1.0 リリースでは、次のことがサポートされています。

- ワークロード評価に基づく Azure SQL Managed Instance の SKU に関する推奨事項。
- RDS SQL Server 評価のソースとして使用されます。
- ターゲットとしての Azure SQL Managed Instance のエージェントジョブの評価。
- 特定の評価規則を無視する機能。DMA で構成された ' ignoreErrorCodes ' プロパティに指定されているエラーコードの一覧は、DMA 評価の結果に表示されません。
- ジョブアクティビティステップでの T-sql クエリの評価と適切な推奨事項の提供
- 拡張イベントの評価 (パブリックプレビュー)。

さらに、このリリースの DMA では、データベース内の多数のスキーマオブジェクトを処理するパフォーマンスが向上し、に関連するバグの修正が行われています。

- ネイティブコンパイルを使用してコンパイルされたプロシージャ (場合によっては)。
- 複雑なデータベーススキーマ。

## <a name="data-migration-assistant-v42"></a>Data Migration Assistant v5.0

Data Migration Assistant のバージョン4.2 リリースでは、オンプレミスの SQL Server から SQL Managed Instance に移行するときに、1つまたは複数のサーバーインスタンスのターゲット準備状態評価がコマンドラインでサポートされます。 Data Migration Assistant コマンドラインを使用して、データベーススキーマに関するメタデータを収集し、ブロッカーを検出し、SQL Managed Instance への移行に影響する部分的にサポートされている機能やサポートされていない機能について学習できるようになりました。 その後、提供された Power BI テンプレートを使用して結果を表示できます。

## <a name="data-migration-assistant-v41"></a>Data Migration Assistant v1.0

Data Migration Assistant の version 4.1 リリースでは、SQL Managed Instance に移行するオンプレミスの SQL Server データベースの包括的な評価がサポートされています。

評価ワークフローは、SQL Managed Instance への移行に影響する可能性がある次の問題を検出するのに役立ちます。

- サポート **されていない機能または部分的にサポートされる機能**。 Data Migration Assistant は、ターゲット SQL Managed Instance で部分的にサポートされているかサポートされていない使用中の機能について、ソース SQL Server データベースを評価します。 このツールでは、一連の推奨事項、Azure で利用可能なその他のアプローチ、および移行プロジェクトを計画する際にお客様がこの情報を考慮できるようにするための対策を講じます。

- **互換性の問題**。 Data Migration Assistant は、次の領域に関連する互換性の問題も識別します。

  - 重大な変更: ターゲットデータベースに移行する機能を損なう可能性のある特定のスキーマオブジェクト。  これらのスキーマオブジェクトは、データベースの移行後に修正することをお勧めします。
  - 動作の変更: 報告されたスキーマオブジェクトは引き続き機能しますが、パフォーマンスの低下など、動作が異なる場合があります。
  - 情報に関する問題: これらのオブジェクトは移行に影響を与えることはありませんが、機能 SQL Server のリリースで非推奨とされる可能性があります。

評価が完了したら、 [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS) を使用して、SQL Server データベースの SQL Managed Instance への移行を実行します。  DMS では、SQL Managed Instance への [オフライン](/azure/dms/tutorial-sql-server-to-managed-instance) (1 回限り) および [オンライン](/azure/dms/tutorial-sql-server-managed-instance-online) (最小限のダウンタイム) データベースの移行がサポートされています。

## <a name="data-migration-assistant-v40"></a>Data Migration Assistant v4.0

Data Migration Assistant の v2.0 リリースでは、Azure SQL Database SKU の推奨事項機能が導入されています。これにより、ユーザーは、データベースをホストしているコンピューターから収集されたパフォーマンスカウンターに基づいて、推奨される最小 Azure SQL Database SKU を特定できます。 この機能では、価格レベル、コンピューティングレベル、最大データサイズに関連する推奨事項に加え、月あたりの推定コストも提供します。 また、すべてのデータベースを Azure に一括してプロビジョニングする機能も提供されます。

> [!NOTE]
> 現在、この機能は、コマンドラインインターフェイス (CLI) を介してのみ使用できます。

詳細については、 [オンプレミスデータベースの適切な AZURE SQL DATABASE SKU の特定に](dma-sku-recommend-sql-db.md)関する記事を参照してください。

## <a name="data-migration-assistant-v36"></a>Data Migration Assistant v1.0

Data Migration Assistant の v1.0 リリースでは、最も一般的な移行ブロックの影響を受けるスキーマオブジェクトに対して "自動修正" が導入されています。

このリリースでは、次の移行ブロックと動作の変更に関する問題の autofix が提供されています。

- 非修飾結合構文を使用するスキーマオブジェクト。
- レガシ RAISEERROR ステートメントを使用するスキーマオブジェクト。
- Order By 整数リテラルを使用する SQL ステートメント。

Data Migration Assistant は、一覧にある問題の影響を受けるオブジェクトに対してスキーマの自動変換を実行し、スキーマ変換を続行する前にユーザーに確認を求めるメッセージを表示します。 ユーザーは、提案されたコードの変更を確認し、特定のデータベースオブジェクトのすべての変換を受け入れるか拒否するかを選択できます。

Data Migration Assistant は、Microsoft プログラム合成 (PROSE) テクノロジを使用してコード修正を提案します。 [PROSE](https://microsoft.github.io/prose/)の詳細については、こちらを参照してください。

## <a name="data-migration-assistant-v35"></a>Data Migration Assistant v1.0

Data Migration Assistant の v2.0 リリースには、次の追加情報が含まれています。

- Azure SQL Database への移行におけるパフォーマンスの大幅な向上 (ベンチマークテストでは、以前のバージョンの Data Migration Assistant) よりも4倍高速に処理が行われることを示しています。
- メモリフットプリントは、移行ワークフローの安定性を向上させるためにさらに最適化されています。
- スキーマおよびデータの移行中に評価をスキップする機能 (既に評価を実行済みで、移行前にスキーマオブジェクトの互換性を解消している場合)。
- 古いバージョンの SQL Server オンプレミスからそれ以降のバージョンへのアップグレードを実行する場合、または Azure Vm 上で SQL Server する場合に、バックアップファイルに無効なネットワーク共有パスが指定されているときに、ツールがクラッシュする問題に対処するための修正。

## <a name="data-migration-assistant-v34"></a>Data Migration Assistant 3.4

Data Migration Assistant の v1.1 リリースには、次の追加情報が含まれています。

- Azure SQL Database への移行のソースとして SQL Server 2017 がサポートされます。
- 安定性、パフォーマンス、および評価ルールの正確性に関する機能強化。

## <a name="data-migration-assistant-v33"></a>Data Migration Assistant v1.0

Data Migration Assistant の v2.0 リリースでは、Windows と Linux の両方で、オンプレミスの SQL Server インスタンスを SQL Server 2017 の新しいバージョンに移行できます。 Windows と Linux の移行の全体的なワークフローは同じですが、Linux 用の SQL Server 2017 に移行する場合は、いくつかの追加の考慮事項が必要です。

### <a name="specifying-the-back-up-path"></a>バックアップパスの指定

Linux と Windows では、異なるパス形式を使用します。 結果として、Linux 上の SQL Server 2017 に移行するには、ユーザーがパスの Windows と Linux の両方のバージョンを物理ファイルの場所に提供する必要があります。 物理ファイルの場所に応じて、パスの両方のバージョンをさまざまな方法で指定できます。
物理バックアップファイルが、を実行しているコンピューター上にある場合は、次のようになります。

- Linux では、"samba" 共有を使用して、ネットワーク上の他のコンピューターとファイルを共有します。
- Windows では、' mnt ' コマンドを使用して、Linux を実行しているコンピューターに共有をマウントします。

> [!NOTE]
> ' Samba ' 共有または ' mnt ' コマンドの使用の詳細については、この記事では説明しません。

### <a name="migrating-windows-logins"></a>Windows ログインの移行

Active Directory (AD) ログインの移行は Linux 上の SQL Server 2017 で正式にサポートされていますが、正常に機能するためには追加の構成が必要です。 Linux での SQL Server 2017 での Active Directory ログインの設定の詳細については、 [SQL Server on Linux での認証の Active Directory](../linux/sql-server-linux-active-directory-authentication.md) に関する記事を参照してください。 必要な構成を実行すると、セットアップが完了し、Active Directory ログインを通常どおりに移行できるようになります。 標準の SQL 認証は、追加の設定を行わなくても期待どおりに動作します。

## <a name="data-migration-assistant-v32"></a>Data Migration Assistant 3.2

Data Migration Assistant の v2.0 リリースには、次の追加情報が含まれています。

- スキーマとデータの移行は、新しい移行ワークフローで Azure SQL Database するために、オンプレミスの SQL Server データベースから有効になります。
- Azure SQL Database へのスキーマの移行中に、ソースデータベースオブジェクトに対して DMA スクリプトを実行し、潜在的な互換性の問題を修正してから Azure にスキーマをデプロイする方法に関するガイダンスを提供します。

## <a name="data-migration-assistant-v31"></a>Data Migration Assistant v1.0

Data Migration Assistant のバージョン3.1 リリースには、次の追加機能が含まれています。

- データベースの照合順序、サポートされていないシステムストアドプロシージャの使用、および CLR オブジェクトに関して、Azure SQL Database の評価に関する推奨事項が向上しました。
- Azure SQL Database に移行するときの互換性レベル130、120、110、および100の評価ガイダンス。

## <a name="data-migration-assistant-v30"></a>Data Migration Assistant v1.0

Data Migration Assistant の v2.0 リリースでは Azure SQL Database 評価が拡張され、に関連する問題の解決に役立つ包括的な推奨事項が提供されます。

- 移行のブロックの問題。
- 部分的またはサポートされていない機能と機能。

## <a name="data-migration-assistant-v21"></a>Data Migration Assistant v1.0

Data Migration Assistant のバージョン2.1 リリースには、次の追加機能が含まれています。

- 評価を大規模に実行するのに役立つ無人モードで評価を実行するためのコマンドラインサポート。 詳細については、「 [コマンドラインから Data Migration Assistant を実行](dma-commandline.md)する」を参照してください。
- ユーザーが DMA を起動して閉じるときのパフォーマンスが向上しました。
- SQL 接続のタイムアウトを構成する権限です。詳細については、「 [Data Migration Assistant の構成設定](dma-configurationsettings.md)」を参照してください。

## <a name="data-migration-assistant-v20"></a>Data Migration Assistant v2.0

V2.0 リリースの Data Migration Assistant には、ストレージの節約を最大化する適切な優先順位のテーブルを提供する拡張データベース機能の推奨事項が強化されています。

## <a name="data-migration-assistant-v10"></a>Data Migration Assistant v1.0

Data Migration Assistant の v1.0 リリースは最初のリリースであり、次の機能を備えています。

- オンプレミスバージョンの SQL Server へのアップグレードに影響する可能性がある問題の検出。 すべての結果は互換性の問題として記述されており、次の領域に分類されています。
  - 重大な変更
  - 動作の変更
  - 非推奨の機能
- アップグレード後にデータベースが恩恵を受けることのできるターゲット SQL Server プラットフォームの新機能の検出。 すべての結果は、機能に関する推奨事項として説明されており、次の領域に分類されています。
  - パフォーマンス
  - セキュリティ
  - Storage
- 評価を実行する最新のユーザーエクスペリエンス。

## <a name="see-also"></a>関連項目

[Data Migration Assistant の概要](../dma/dma-overview.md)
