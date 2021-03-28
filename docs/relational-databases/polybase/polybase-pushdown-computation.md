---
description: PolyBase でのプッシュダウン計算
title: PolyBase でのプッシュダウン計算
dexcription: Enable pushdown computation to improve performance of queries on your Hadoop cluster. You can select a subset of rows/columns in an external table for pushdown.
ms.date: 03/09/2021
ms.prod: sql
ms.technology: polybase
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: ''
monikerRange: '>= sql-server-2016||>=sql-server-linux-ver15'
ms.openlocfilehash: 872d23ca6c908bae5e238a50aad76c52ff4bca26
ms.sourcegitcommit: 17f05be5c08cf9a503a72b739da5ad8be15baea5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105103889"
---
# <a name="pushdown-computations-in-polybase"></a>PolyBase でのプッシュダウン計算

[!INCLUDE [sqlserver2016](../../includes/applies-to-version/sqlserver2016.md)]

プッシュダウン計算を使用すると、外部データ ソースに対するクエリのパフォーマンスが向上します。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、プッシュダウン計算は Hadoop の外部データ ソースで使用できました。 [!INCLUDE[sssql19-md](../../includes/sssql19-md.md)] には、他の種類の外部データ ソースのプッシュダウン計算が導入されています。  

## <a name="enable-pushdown-computation"></a> プッシュダウン計算を有効にする

次の記事には、特定の種類の外部データ ソース用のプッシュダウン計算の構成に関する情報が含まれています。

- [Enable pushdown computation in Hadoop (Hadoop でのプッシュダウン計算を有効にする)](polybase-configure-hadoop.md#pushdown)
- [Oracle 上の外部データにアクセスするための PolyBase の構成](polybase-configure-oracle.md)
- [Teradata 上の外部データにアクセスするための PolyBase の構成](polybase-configure-teradata.md)
- [MongoDB 上の外部データにアクセスするための PolyBase の構成](polybase-configure-mongodb.md)
- [ODBC ジェネリック型の外部データにアクセスするための PolyBase の構成](polybase-configure-odbc-generic.md)
- [SQL Server 上の外部データにアクセスするための PolyBase の構成](polybase-configure-sql-server.md)

この表には、さまざまな外部データ ソースに対するプッシュダウン計算のサポートがまとめてあります。

| Data Source      | 結合  | プロジェクション | 集計 | フィルタ   | 統計 |
|------------------|--------|-------------|--------------|-----------|------------|
| **汎用 ODBC** | Yes    | Yes         | Yes          | Yes       | Yes        |  
| **Oracle**       | Yes    | Yes         | Yes          | Yes       | Yes        |
| **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]**   | Yes    | Yes         | Yes          | Yes       | はい        |
| **Teradata**     | はい    | Yes         | Yes          | Yes       | Yes        |  
| **MongoDB**      | **いいえ** | Yes         | Yes          | Yes       | Yes        |
| **Hadoop\** _     | _ *いいえ** | Yes         | \*一部\*     | \*一部\*  | Yes        |  
|                  |

\* PolyBase では現在、Hortonworks Data Platform (HDP) と Cloudera Distributed Hadoop (CDH) の 2 つの Hadoop プロバイダーがサポートされています。 プッシュダウン計算の観点からは、この 2 つの機能に違いはありません。

\*\* Hadoop のプッシュダウン機能のサポートの詳細については、「[T-SQL 演算子でサポートされるプッシュダウン計算](polybase-versioned-feature-summary.md#pushdown-computation-supported-by-t-sql-operators)」を参照してください。

> [!NOTE]
> T-SQL 構文によってプッシュダウン計算はブロックできます。 詳細については、「[プッシュダウンを防ぐ構文](polybase-versioned-feature-summary.md#syntax-that-prevents-pushdown)」を参照してください。

## <a name="key-beneficial-scenarios-of-pushdown-computation"></a>プッシュダウン計算の主な有益シナリオ

PolyBase プッシュダウン計算を使用すれば、計算タスクを外部データ ソースに委任できます。 これにより、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンス上のワークロードが軽減されので、パフォーマンスが大幅に向上する可能性があります。 

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、リモート コンピューティングを利用してネットワーク経由で送信されるデータを制限するために、結合、プロジェクション、集計、およびフィルターを外部データ ソースにプッシュすることができます。 

### <a name="pushdown-of-joins"></a>結合のプッシュダウン

多くの場合、PolyBase を使用すると結合演算子のプッシュダウンが容易になり、パフォーマンスが大幅に向上します。  

外部データ ソースで結合を行うことができる場合、これによりデータ移動の量が減少し、クエリのパフォーマンスが向上します。 結合プッシュダウンを使用しない場合は、結合対象のテーブルからのデータを tempdb にローカルに配置してから、結合を行う必要があります。

### <a name="select-a-subset-of-rows"></a>行のサブセットを選択する

外部テーブルから行のサブセットを選択するクエリのパフォーマンスを改善するには、述語のプッシュダウンを使用します。

この例では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって map-reduce ジョブを開始し、Hadoop で述語 `customer.account_balance < 200000` である行を取得します。 このクエリは、テーブルのすべての行をスキャンせずに完了できるため、述語の条件に合う行のみが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にコピーされます。 この方法で時間が大幅に短縮され、残高が 200000 未満の顧客数が、口座残高が 200000 以上の顧客数と比較して少ない場合に、一時的な記憶域が少なくなります。

```sql
SELECT * FROM customer WHERE customer.account_balance < 200000;
SELECT * FROM SensorData WHERE Speed > 65;  
```

### <a name="select-a-subset-of-columns"></a>列のサブセットを選択する

外部テーブルから列のサブセットを選択するクエリのパフォーマンスを改善するには、述語のプッシュダウンを使用します。

このクエリでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で map-reduce ジョブを開始し、Hadoop の区切られたテキスト ファイルを前処理して、customer.name および customer.zip_code という 2 列のデータのみが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にコピーされるようにします。

```sql
SELECT customer.name, customer.zip_code
FROM customer
WHERE customer.account_balance < 200000;
```

### <a name="pushdown-for-basic-expressions-and-operators"></a>基本的な式と演算子のプッシュダウン

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、述語のプッシュダウンに次の基本的な式と演算子を使用できます。

- 数値、日付値、時間値の 2 項比較演算子 (`<`、`>`、`=`、`!=`、`<>`、`>=`、`<=`)。
- 算術演算子 (`+`、`-`、`*`、`/`、`%`)。
- 論理演算子 (`AND`、`OR`)。
- 単項演算子 (`NOT`、`IS NULL`、`IS NOT NULL`)。

`BETWEEN`、`NOT`、`IN`、および `LIKE` の演算子がプッシュダウンされる場合があります。 実際の動作は、クエリ オプティマイザーが演算子式をどのように基本的な関係演算子を使用する一連のステートメントとして書き換えるかに依存します。

この例のクエリには、Hadoop にプッシュダウンできる述語が複数あります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を使用すると、map-reduce ジョブを Hadoop にプッシュして、述語 `customer.account_balance <= 200000` を実行できます。 `BETWEEN 92656 AND 92677` の式もまた、Hadoop にプッシュできる 2 項演算子と論理演算子とで構成されます。 `customer.account_balance AND customer.zipcode` 内の論理 **積** が最後の式です。

この述語の組み合わせで、map-reduce ジョブですべての WHERE 句を実行できます。 `SELECT` 条件を満たすデータのみが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にコピーされて戻されます。

```sql
SELECT * FROM customer 
WHERE customer.account_balance <= 200000 
    AND customer.zipcode BETWEEN 92656 AND 92677;
```

## <a name="examples"></a>例

### <a name="force-pushdown"></a>プッシュダウンを強制する

```sql
SELECT * FROM [dbo].[SensorData]
WHERE Speed > 65
OPTION (FORCE EXTERNALPUSHDOWN);
```

### <a name="disable-pushdown"></a>プッシュダウンを無効にする

```sql
SELECT * FROM [dbo].[SensorData]
WHERE Speed > 65
OPTION (DISABLE EXTERNALPUSHDOWN);
```

## <a name="next-steps"></a>次のステップ

PolyBase の詳細については、「[PolyBase とは](polybase-guide.md)」をご覧ください。
