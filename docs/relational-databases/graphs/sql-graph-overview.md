---
description: SQL Server と Azure SQL Database でのグラフ処理
title: グラフの処理
titleSuffix: SQL Server and Azure SQL Database
ms.date: 06/26/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: ''
ms.topic: language-reference
helpviewer_keywords:
- SQL graph
- SQL graph, overview
ms.assetid: ''
author: shkale-msft
ms.author: shkale
ms.custom: seo-dt-2019
monikerRange: =azuresqldb-current||>=sql-server-2017||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 8c7dfeaa67e0ede78b0a891409be3211e1fa8d2e
ms.sourcegitcommit: 8dc7e0ececf15f3438c05ef2c9daccaac1bbff78
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2021
ms.locfileid: "100351419"
---
# <a name="graph-processing-with-sql-server-and-azure-sql-database"></a>SQL Server と Azure SQL Database でのグラフ処理
[!INCLUDE[sqlserver2017-asdb](../../includes/applies-to-version/sqlserver2017-asdb-asdbmi.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] には、多対多リレーションシップをモデル化するためのグラフデータベース機能が用意されています。 グラフのリレーションシップはに統合されて [!INCLUDE[tsql-md](../../includes/tsql-md.md)] おり、を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 基盤とするデータベース管理システムとしてを使用する利点があります。


## <a name="what-is-a-graph-database"></a>グラフ データベースとは  
グラフ データベースは、ノード (または頂点) とエッジ (またはリレーションシップ) のコレクションです。 ノードはエンティティ (たとえば、個人や組織) を表し、エッジは接続されている 2 つのノード間のリレーションシップ (たとえば、お気に入りや友人) を表します。 ノードとエッジの両方に、プロパティが関連付けられている場合があります。 グラフ データベースを特徴付けるいくつかの特性を次に示します。  
-    エッジ (リレーションシップ) は、グラフ データベース内のファースト クラスのエンティティであり、属性またはプロパティを関連付けることができます。 
-    1 つのエッジは、グラフ データベース内の複数のノードを柔軟に接続できます。
-    パターン マッチングとマルチホップ ナビゲーション クエリを簡単に表現できます。
-    推移閉包およびポリモーフィック クエリを簡単に表現できます。

## <a name="when-to-use-a-graph-database"></a>グラフデータベースを使用する場合

リレーショナルデータベースでは、グラフデータベースが可能なことをすべて達成できます。 ただし、グラフデータベースを使用すると、特定の種類のクエリを簡単に表現できるようになります。 また、特定の最適化を使用すると、特定のクエリのパフォーマンスが向上する場合があります。 リレーショナルデータベースとグラフデータベースのどちらを選択するかは、次の要因に基づいて決定します。  
-    アプリケーションに階層データがある。 HierarchyID データ型は階層の実装に使用できますが、いくつかの制限があります。 たとえば、1つのノードに複数の親を格納することはできません。
-    アプリケーションに複雑な多対多リレーションシップがある。アプリケーションの進化に伴って、新しいリレーションシップが追加されます。
-    相互接続されたデータとリレーションシップを分析する必要があります。

## <a name="graph-features-introduced-in-sssql17"></a>で導入されたグラフ機能 [!INCLUDE[sssql17](../../includes/sssql17-md.md)] 
グラフのデータの格納とクエリを容易にするために、SQL Server にグラフ拡張機能を追加し始めます。 次の機能は、最初のリリースで導入されました。 


### <a name="create-graph-objects"></a>グラフオブジェクトの作成
[!INCLUDE[tsql-md](../../includes/tsql-md.md)] 拡張機能を使用すると、ユーザーはノードテーブルまたはエッジテーブルを作成できます。 ノードとエッジの両方にプロパティを関連付けることができます。 ノードとエッジはテーブルとして格納されるため、リレーショナルテーブルでサポートされているすべての操作は、ノードまたはエッジテーブルでサポートされています。 たとえば次のようになります。  

```   
CREATE TABLE Person (ID INTEGER PRIMARY KEY, Name VARCHAR(100), Age INT) AS NODE;
CREATE TABLE friends (StartDate date) AS EDGE;
```   

![ノードとエッジを示す図はテーブルとして格納されます。](../../relational-databases/graphs/media/person-friends-tables.png "Person ノードと友人のエッジテーブル")  
ノードとエッジはテーブルとして格納されます。  

### <a name="query-language-extensions"></a>クエリ言語の拡張機能  
新しい `MATCH` 句は、グラフを使用したパターンマッチングとマルチホップナビゲーションをサポートするために導入されました。 関数は、 `MATCH` パターンマッチングに ASCII アートスタイル構文を使用します。 次に例を示します。  

```   
-- Find friends of John
SELECT Person2.Name 
FROM Person Person1, Friends, Person Person2
WHERE MATCH(Person1-(Friends)->Person2)
AND Person1.Name = 'John';
```   
 
### [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エンジンに完全に統合 
グラフ拡張機能は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エンジンに完全に統合されています。 同じストレージエンジン、メタデータ、クエリプロセッサなどを使用して、グラフデータを格納し、クエリを実行します。 1つのクエリで、グラフとリレーショナルデータにまたがるクエリを実行します。 グラフ機能と、列ストア、HA、R サービスなどの他の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テクノロジとの組み合わせ可能です。SQL グラフデータベースでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で使用できるすべてのセキュリティ機能とコンプライアンス機能もサポートされています。
 
### <a name="tooling-and-ecosystem"></a>ツールとエコシステム

を提供する既存のツールとエコシステムを活用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] できます。 バックアップと復元、インポート、エクスポートなどのツールは、すぐに使用できます。 SSIS、SSRS、Power BI などの他のツールやサービスは、リレーショナルテーブルを操作するのと同じように、グラフテーブルで使用できます。

## <a name="edge-constraints"></a>エッジ制約
エッジ制約は、グラフエッジテーブルで定義され、特定のエッジタイプが接続できるノードテーブルのペアです。 これにより、ユーザーはグラフスキーマをより適切に制御できるようになります。 エッジ制約を使用すると、特定のエッジが接続を許可されているノードの種類を制限できます。 

エッジの制約を作成して使用する方法の詳細については、「[エッジの制約](../../relational-databases/tables/graph-edge-constraints.md)」を参照してください。

## <a name="merge-dml"></a>DML のマージ 
[MERGE](../../t-sql/statements/merge-transact-sql.md)ステートメントは、ソーステーブルとの結合の結果に基づいて、対象テーブルに対して挿入、更新、または削除の操作を実行します。 たとえば、ターゲットテーブルとソーステーブルの違いに基づいて、対象テーブルの行を挿入、更新、または削除することによって、2つのテーブルを同期できます。 MERGE ステートメントでの MATCH 述語の使用は、Azure SQL Database と SQL Server vNext でサポートされるようになりました。 つまり、INSERT/UPDATE/DELETE ステートメントを個別に指定する代わりに、MATCH 述語を使用して、現在のグラフデータ (ノードテーブルまたはエッジテーブル) を新しいデータとマージできるようになりました。

Merge DML で match を使用する方法の詳細については、 [Merge ステートメント](../../t-sql/statements/merge-transact-sql.md)に関するページを参照してください。

## <a name="shortest-path"></a>最短パス
[SHORTEST_PATH](./sql-graph-shortest-path.md)関数は、グラフ内の2つのノード間、または特定のノードからグラフ内の他のすべてのノードまでの最短パスを検索します。 最短パスを使用して、グラフ内で推移的なクロージャまたは任意の長さのトラバーサルを見つけることもできます。 

 ## <a name="next-steps"></a>次のステップ  
[SQL グラフデータベース-アーキテクチャ](./sql-graph-architecture.md)の読み取り
   

