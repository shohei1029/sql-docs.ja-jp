---
description: Views
title: ビュー |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- views [SQL Server], about views
ms.assetid: ada83c28-e8b7-45d9-b53c-b3d67c8820c8
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1e2e23f4690fc35b8b1daa0b5cb9de936edbfe1b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755212"
---
# <a name="views"></a>Views
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  ビューとは、クエリによって内容が定義される仮想テーブルです。 ビューは、テーブルと同様に、一連の名前の付いた列とデータ行で構成されます。 インデックスが設定されていないと、データベース内に保存された一連のデータ値としてビューが作成されることはありません。 データは、ビューを定義するクエリが参照するテーブルから取り出され、ビューの行と列はビューを参照したときに動的に作成されます。  
  
 ビューは、ビューが参照する基になるテーブルに対するフィルターの役目を果たします。 ビューを定義するクエリでは、1 つ以上のテーブルを参照することも、現在のデータベースや他のデータベースのビューを参照することもできます。 参照先が複数で種類が異なる場合、ビューの定義に分散クエリも使用できます。 分散クエリは、組織のデータを地域ごとのサーバーに保存している場合に、複数のサーバーから同じ構造のデータを結合するときなどに便利です。  
  
 ビューは一般的に、各ユーザーのデータベースに対する認識を特化、簡素化、およびカスタマイズするために使用されます。 ビューは、基になるベース テーブルに直接アクセスする権限をユーザーに与えずに、ユーザーがビューを介してデータにアクセスできるように設定することにより、セキュリティのメカニズムとして使用できます。 ビューを使用すると、以前に存在していたテーブルのスキーマが変更された場合に、このテーブルをエミュレートするための後方互換性インターフェイスを提供できます。 また、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にデータをコピーする場合、および SQL Server からデータをコピーする場合に、パフォーマンスの向上やデータのパーティション分割にビューを使用できます。  
  
## <a name="types-of-views"></a>ビューの種類  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] には、標準的な役割を果たす基本的なユーザー定義のビュー以外に、データベース内で特別な目的で使用される次のようなビューがあります。  
  
 インデックス付きビュー  
 インデックス付きビューは、具体化されたビューです。 つまり、ビュー定義が計算され、結果のデータがテーブルのように格納されています。 ビューに一意クラスター化インデックスを作成することで、ビューにインデックスを設定します。 ある種のクエリでは、インデックス付きビューにより、大幅にパフォーマンスが向上する場合があります。 インデックス付きビューは、多くの行を集計するクエリで最も効果を発揮します。 インデックス付きビューは、基になるデータセットが頻繁に更新される場合は適していません。  
  
 パーティション ビュー  
 パーティション ビューでは、1 台以上のサーバーに分散された一連のメンバー テーブルからの、行方向にパーティション分割されたデータが結合されます。 これにより、データが 1 つのテーブルからのデータのように表示されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の同じインスタンスのメンバー テーブルを結合するビューは、ローカル パーティション ビューです。  
  
 システム ビュー  
 システム ビューは、カタログ メタデータを公開します。 システム ビューを使用すると、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスの情報や、インスタンスで定義されているオブジェクトの情報を返すことができます。 たとえば、sys.databases カタログ ビューに対してクエリを実行し、インスタンスで使用可能なユーザー定義データベースについての情報を返すことができます。 詳細については、「[システム ビュー &#40;Transact-SQL&#41;](../../t-sql/language-reference.md)」を参照してください。  
  
## <a name="common-view-tasks"></a>ビューの一般的なタスク  
 次の表に、ビューの作成や変更に関連する一般的なタスクへのリンクを示します。  
  
|ビューのタスク|トピック|  
|----------------|-----------|  
|ビューを作成する方法について説明します。|[ビューの作成](../../relational-databases/views/create-views.md)|  
|インデックス付きビューを作成する方法について説明します。|[インデックス付きビューの作成](../../relational-databases/views/create-indexed-views.md)|  
|ビュー定義を変更する方法について説明します。|[ビューの変更](../../relational-databases/views/modify-views.md)|  
|ビューでデータを変更する方法について説明します。|[ビューを使用したデータ変更](../../relational-databases/views/modify-data-through-a-view.md)|  
|ビューを削除する方法について説明します。|[ビューの削除](../../relational-databases/views/delete-views.md)|  
|ビュー定義など、ビューに関する情報を返す方法について説明します。|[ビューに関する情報の取得](../../relational-databases/views/get-information-about-a-view.md)|  
|ビューの名前を変更する方法について説明します。|[ビューの名前の変更](../../relational-databases/views/rename-views.md)|  
  
## <a name="see-also"></a>参照  
 [XML 列でのビューの作成](../../relational-databases/xml/create-views-over-xml-columns.md)   
 [CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)  
  
