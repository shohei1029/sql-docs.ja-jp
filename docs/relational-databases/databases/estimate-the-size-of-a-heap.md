---
title: ヒープ サイズの見積もり | Microsoft Docs
description: SQL Server でヒープにデータを格納するために必要な領域を見積もるには、次の手順を実行します。
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
helpviewer_keywords:
- disk space [SQL Server], indexes
- estimating heap size
- size [SQL Server], heap
- space [SQL Server], indexes
- heaps
ms.assetid: 81fd5ec9-ce0f-4c2c-8ba0-6c483cea6c75
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: aa3910c7ff3b66474835f2d3335e0cdac44430bf
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748052"
---
# <a name="estimate-the-size-of-a-heap"></a>ヒープ サイズの見積もり
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  ヒープにデータを格納するために必要な領域は、次の手順で見積もることができます。  
  
1.  次のように、テーブル内の行数を指定します。  
  
     **_Num_Rows_** = テーブル内の行数  
  
2.  次のように、固定長列と可変長列の数を指定し、それらの列を格納するために必要な領域を計算します。  
  
     固定長列のグループと可変長列のグループがデータ行内で使用する領域を計算します。 列のサイズは、データ型と長さの指定によって異なります。  
  
     **_Num_Cols_** = 列 (固定長および可変長) の総数  
  
     **_Fixed_Data_Size_** = すべての固定長列の合計バイト サイズ  
  
     **_Num_Variable_Cols_** = 可変長列の数  
  
     **_Max_Var_Size_** = すべての可変長列の最大合計バイト サイズ  
  
3.  NULL ビットマップと呼ばれる行の部分は、列の NULL 値の許容を管理するために予約されています。 このサイズは次のように計算します。  
  
     **_Null_Bitmap_**  = 2 + ((**_Num_Cols_** + 7) / 8)  
  
     この式の計算結果は、整数部分だけを使用します。 小数部分は無視してください。  
  
4.  次の式で、可変長のデータ サイズを計算します。  
  
     テーブル内に可変長列が存在する場合、次の式を使用して、行内でそれらの列を格納するために使用する領域を計算します。  
  
     **_Variable_Data_Size_**  = 2 + (**_Num_Variable_Cols_** x 2) + **_Max_Var_Size_**  
  
     **_Max_Var_Size_** に追加されたバイトは、それぞれの可変長列を追跡するためのものです。 この式は、すべての可変長列がいっぱいになることを前提としています。 可変長列の格納領域の使用率が 100% 以下になることが予想される場合、その使用率に基づいて **_Max_Var_Size_** の値を調整し、テーブルの全体サイズをより正確に見積もることができます。  
  
    > [!NOTE]  
    >  定義済みのテーブルの合計サイズが 8,060 バイトを超える **varchar**、 **nvarchar**、 **varbinary**、または **sql_variant** 列の連結が可能です。 この場合も、**varchar**、**nvarchar、varbinary**、または **sql_variant** 列では、8,000 バイトの制限内に各列のサイズを収める必要があります。 ただし、これらの列を連結したサイズは、テーブルの制限である 8,060 バイトを超過してもかまいません。  
  
     可変長列が存在しない場合は、**_Variable_Data_Size_** に 0 を設定します。  
  
5.  次の式で行サイズの合計を計算します。  
  
     **_Row_Size_**  = **_Fixed_Data_Size_** + **_Variable_Data_Size_** + **_Null_Bitmap_** + 4  
  
     上記の式の 4 という値は、データ行の行ヘッダー オーバーヘッドです。  
  
6.  次の式で、1 ページあたりの行数を計算します (1 ページあたりの空きバイト数は 8,096 です)。  
  
     **_Rows_Per_Page_**  = 8096 / (**_Row_Size_** + 2)  
  
     行は複数のページにまたがらないので、計算結果の端数は切り捨ててください。 上記の式の 2 という値は、ページのスロット配列内の行のエントリのためのものです。  
  
7.  次の式で、すべての行を格納するために必要なページ数を計算します。  
  
     **_Num_Pages_**  = **_Num_Rows_** / **_Rows_Per_Page_**  
  
     算出したページ数の端数は切り上げてください。  
  
8.  次の式で、ヒープにデータを格納するために必要な領域を計算します (1 ページあたりの総バイト数は 8,192 です)。  

     ヒープのサイズ (バイト) = 8192 x **_Num_Pages_**  
  
 この計算では、次のことは考慮されていません。  
  
-   パーティション分割  
  
     パーティション分割による領域のオーバーヘッドはわずかですが、計算が複雑になります。 これは、計算に含めるほど重要なことではありません。  
  
-   アロケーション ページ  
  
     ヒープに割り当てられたページの追跡に使用する IAM ページが少なくとも 1 ページありますが、使用される IAM ページ数を正確に計算できるアルゴリズムはなく、領域のオーバーヘッドはわずかです。  
  
-   ラージ オブジェクト (LOB) の値  
  
     LOB データ型の **varchar(max)** 、 **varbinary(max)** 、 **nvarchar(max)** 、 **text**、 **ntextxml**、および **image** の値を格納するのに使用する領域を正確に特定するためのアルゴリズムは複雑です。 LOB データ型の値で使用される領域の計算は、予想される LOB 値の平均サイズを合計し、ヒープの合計サイズに加算するだけで十分です。  
  
-   圧縮  
  
     圧縮されたヒープのサイズを事前に計算することはできません。  
  
-   スパース列  
  
     スパース列の領域要件については、「 [スパース列の使用](../../relational-databases/tables/use-sparse-columns.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [ヒープ &#40;クラスター化インデックスなしのテーブル&#41;](../../relational-databases/indexes/heaps-tables-without-clustered-indexes.md)   
 [クラスター化インデックスと非クラスター化インデックスの概念](../../relational-databases/indexes/clustered-and-nonclustered-indexes-described.md)   
 [クラスター化インデックスの作成](../../relational-databases/indexes/create-clustered-indexes.md)   
 [非クラスター化インデックスの作成](../../relational-databases/indexes/create-nonclustered-indexes.md)   
 [テーブル サイズの見積もり](../../relational-databases/databases/estimate-the-size-of-a-table.md)   
 [クラスター化インデックスのサイズの見積もり](../../relational-databases/databases/estimate-the-size-of-a-clustered-index.md)   
 [非クラスター化インデックスのサイズの算出](../../relational-databases/databases/estimate-the-size-of-a-nonclustered-index.md)   
 [データベース サイズの見積もり](../../relational-databases/databases/estimate-the-size-of-a-database.md)  
  
  
