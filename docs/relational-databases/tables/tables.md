---
description: テーブル
title: テーブル | Microsoft Docs
ms.custom: ''
ms.date: 09/18/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- tables [SQL Server]
- table components [SQL Server]
ms.assetid: 82d7819c-b801-4309-a849-baa63083e83f
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 922bd14b923a7fd82e7e9c779712085a4e1c61ba
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755822"
---
# <a name="tables"></a>テーブル
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

テーブルは、データベースのすべてのデータを格納するデータベース オブジェクトです。 テーブルでは、スプレッドシートのように、データが論理的に行と列の形式にまとめられます。 各行は一意なレコードを表し、各列はレコードのフィールドを表します。 たとえば、会社の従業員のデータを格納するテーブルを、各従業員に相当する行と、従業員の社員番号、姓名、住所、役職名、自宅の電話番号などの情報を格納する列から構成する場合があります。 

- データベース内のテーブルの数は、データベースで許可されているオブジェクトの数 (2,147,483,647) によってのみ制限されます。 標準のユーザー定義テーブルは、最大で 1, 024 の列を持つことができます。 テーブルの行の数は、サーバーの記憶域容量によってのみ制限されます。 

- テーブルやテーブルの各列にプロパティを割り当てて、許可されているデータや他のプロパティを制御することができます。 たとえば、列に NULL 値を許容しない制約を作成したり、値が指定されない場合の既定値を設定したりすることができます。また、テーブルにキー制約を割り当てて一意性を強制したり、テーブル間のリレーションシップを定義したりすることもできます。 

- テーブル内のデータは、行ごとまたはページごとに圧縮できます。 データを圧縮すると、より多くの行をページに格納できます。 詳細については、「 [Data Compression](../../relational-databases/data-compression/data-compression.md)」を参照してください。 

## <a name="types-of-tables"></a>テーブルの種類
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] には、標準的な役割を果たす基本的なユーザー定義のテーブル以外に、データベース内で特別な目的で使用される次のようなテーブルがあります。 

### <a name="partitioned-tables"></a>パーティション テーブル

パーティション テーブルはデータが行方向に分割されているテーブルで、パーティションがデータベースの複数のファイル グループに分散される場合もあります。 大きなテーブルやインデックスをパーティション分割すると、コレクション全体の整合性を維持しながら、データのサブセットに対するアクセスや管理を迅速かつ効率的に行うことができるので、大きなテーブルやインデックスを管理しやすくなります。 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、最大 15,000 個のパーティションを既定でサポートしています。 詳細については、「 [Partitioned Tables and Indexes](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」を参照してください。

### <a name="temporary-tables"></a>一時テーブル

一時テーブルは **tempdb** に格納されます。 一時テーブルには、ローカル一時テーブルとグローバル一時テーブルの 2 種類があります。 この 2 種類の一時テーブルでは、名前、表示設定、および可用性が異なります。 ローカル一時テーブル名の先頭には、番号記号 (#) が 1 つ付いています。このテーブルは、作成したユーザーの現在の接続でのみ表示され、このユーザーが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスから切断すると削除されます。 グローバル一時テーブル名の先頭には、番号記号が 2 つ (##) 付いています。このテーブルは、作成されるとすべてのユーザーに表示され、このテーブルを参照するすべてのユーザーが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスから切断すると削除されます。 


#### <a name="reduced-recompilations-for-workloads-using-temporary-tables-across-multiple-scopes"></a><a name="ctp23"></a> 複数のスコープにまたがる一時テーブルを使用するワークロードの再コンパイルの削減

すべてのデータベース互換レベルにおいて、[!INCLUDE[ss2019](../../includes/sssql19-md.md)] により、複数のスコープにまたがる一時テーブルを使用するワークロードの再コンパイルが減ります。 この機能は、すべてのデプロイ モデルのデータベース互換レベル 150 の Azure SQL Database でも有効になります。  この機能が提供されるまでは、一時テーブルが外側のスコープのバッチによって作成されていた場合、データ操作言語 (DML) ステートメント (`SELECT`、`INSERT`、`UPDATE`、`DELETE`) で一時テーブルを参照すると、実行のたびに DML ステートメントが再コンパイルされました。 この改良により、SQL Server では追加の軽量なチェックが実行されて、不要な再コンパイルが回避されます。

- コンパイル時に一時テーブルの作成に使用される外側のスコープのモジュールが、連続実行に使用されるものと同じかどうかを確認してください。 
- 最初のコンパイルで行われたデータ定義言語 (DDL) のすべての変更を追跡し、連続実行に対する DDL 操作と比較します。

最終的な結果は、余分な再コンパイルと CPU のオーバーヘッドを削減することです。

### <a name="system-tables"></a>システム テーブル

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、サーバーの構成とすべてのテーブルの構成を定義したデータが、システム テーブルという特殊なテーブル セットに格納されます。 ユーザーは、システム テーブルに対して直接クエリや更新を行うことはできません。 システム テーブル内の情報は、システム ビューから入手できます。 詳細については、「[システム ビュー &#40;Transact-SQL&#41;](../../t-sql/language-reference.md)」を参照してください。 
 
### <a name="wide-tables"></a>幅の広いテーブル

幅の広いテーブルでは、 [スパース列](../../relational-databases/tables/use-sparse-columns.md) を使用して、テーブルに持たせることができる列の合計数が 30,000 まで増加します。 スパース列は、NULL 値用にストレージが最適化されている通常の列です。 スパース列によって、NULL 以外の値を取得するためのオーバーヘッドは増大しますが、NULL 値に必要となる領域は削減されます。 幅の広いテーブルでは [列セット](../../relational-databases/tables/use-column-sets.md)が定義されています。これは、型指定されていない XML 表記であり、テーブルのすべてのスパース列を 1 つにまとめて構造化した出力です。 インデックスと統計情報の数も、それぞれ 1,000 と 30,000 に増加します。 幅の広いテーブルの最大行サイズは 8,019 バイトです。 そのため、特定の行のデータの大部分を NULL にする必要があります。 幅の広いテーブルの非スパース列と計算列の最大数は合わせて 1,024 のままです。 

幅の広いテーブルは、パフォーマンスに対して次のような影響を与えます。

- 幅の広いテーブルは、テーブルのインデックスの管理にかかるコストを増加させる場合があります。 幅の広いテーブルのインデックスの数は、ビジネス ロジックで必要なインデックスの数に制限することをお勧めします。 インデックスの数が増加すると、DML のコンパイル時間および必要なメモリ容量も増加します。 非クラスター化インデックスは、データのサブセットに適用されるフィルター選択されたインデックスにする必要があります。 詳細については、「 [Create Filtered Indexes](../../relational-databases/indexes/create-filtered-indexes.md)」を参照してください。 

- アプリケーションでは、幅の広いテーブルから動的に列を追加したり削除したりできます。 列が追加または削除された場合、コンパイルされたクエリ プランも無効になります。 スキーマの変更が最小限になるように、予測されるワークロードに合わせてアプリケーションを設計することをお勧めします。 

- データが幅の広いテーブルから追加および削除される場合、パフォーマンスに影響が生じる可能性があります。 テーブルに対する変更が最小限になるように、予測されるワークロードに合わせてアプリケーションを設計する必要があります。 

- クラスター化キーの複数の行を更新する、幅の広いテーブル上での DML ステートメントの実行を制限します。 これらのステートメントのコンパイルおよび実行には、大量のメモリ リソースが必要になる場合があります。 

- 幅の広いテーブルでのパーティションの切り替え操作は、実行速度が遅く、処理に大量のメモリが必要になる場合があります。 パフォーマンスおよびメモリの要件は、切り替え元のパーティションと切り替え先のパーティションの両方の列の合計数に比例します。 

- 幅の広いテーブルの特定の列を更新する更新カーソルは、FOR UPDATE 句で明示的に列を列挙する必要があります。 これは、カーソルを使用する場合にパフォーマンスを最適化するのに役立ちます。 

## <a name="common-table-tasks"></a>一般的なテーブルのタスク
 次の表に、テーブルの作成や変更に関連する一般的なタスクへのリンクを示します。 

|テーブルのタスク|トピック|
|-----------------|-----------|
|テーブルを作成する方法について説明します。|[テーブルの作成 &#40;データベース エンジン&#41;](../../relational-databases/tables/create-tables-database-engine.md)|
|テーブルを削除する方法について説明します。|[テーブルの削除 &#40;データベース エンジン&#41;](../../relational-databases/tables/delete-tables-database-engine.md)|
|既存のテーブルの一部またはすべての列を含む新しいテーブルを作成する方法について説明します。|[テーブルの複製](../../relational-databases/tables/duplicate-tables.md)|
|テーブル名を変更する方法について説明します。|[テーブル名の変更 &#40;データベース エンジン&#41;](../../relational-databases/tables/rename-tables-database-engine.md)|
|テーブルのプロパティを表示する方法について説明します。|[テーブルの定義の表示](../../relational-databases/tables/view-the-table-definition.md)|
|ビューやストアド プロシージャなどの他のオブジェクトがテーブルに依存しているかどうかを判断する方法について説明します。|[テーブルの依存関係の表示](../../relational-databases/tables/view-the-dependencies-of-a-table.md)|

 次の表に、テーブル内の列の作成や変更に関連する一般的なタスクへのリンクを示します。 

|列のタスク|トピック|
|------------------|-----------|
|既存のテーブルに列を追加する方法について説明します。|[テーブルへの列の追加 &#40;データベース エンジン&#41;](../../relational-databases/tables/add-columns-to-a-table-database-engine.md)|
|テーブルの列を削除する方法について説明します。|[テーブルからの列の削除](../../relational-databases/tables/delete-columns-from-a-table.md)|
|列の名前を変更する方法について説明します。|[列名の変更 &#40;データベース エンジン&#41;](../../relational-databases/tables/rename-columns-database-engine.md)|
|列の定義のみ、または定義とデータの両方をコピーすることで、あるテーブルから別のテーブルに列をコピーする方法について説明します。|[テーブル間での列のコピー &#40;データベース エンジン&#41;](../../relational-databases/tables/copy-columns-from-one-table-to-another-database-engine.md)|
|データ型やその他のプロパティを変更することによって列定義を変更する方法について説明します。|[列の変更 &#40;データベース エンジン&#41;](../../relational-databases/tables/modify-columns-database-engine.md)|
|列の表示順序を変更する方法について説明します。|[テーブル内の列の順序の変更](../../relational-databases/tables/change-column-order-in-a-table.md)|
|テーブルに計算列を作成する方法について説明します。|[テーブルの計算列の指定](../../relational-databases/tables/specify-computed-columns-in-a-table.md)|
|列の既定値を指定する方法を説明します。 この値は、別の値が指定されない場合に使用されます。|[列の既定値の指定](../../relational-databases/tables/specify-default-values-for-columns.md)|

## <a name="see-also"></a>参照
 [主キー制約と外部キー制約](../../relational-databases/tables/primary-and-foreign-key-constraints.md) [UNIQUE 制約と CHECK 制約](../../relational-databases/tables/unique-constraints-and-check-constraints.md)