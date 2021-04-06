---
title: sp_rxPredict
description: sp_rxPredict は、SQL Server データベースにバイナリ形式で格納されている機械学習モデルで構成される、特定の入力に対して予測値を生成します。
ms.date: 04/05/2021
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: machine-learning-services
ms.topic: reference
f1_keywords:
- sp_rxPredict
- sp_rxPredict_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_rxPredict procedure
author: dphansen
ms.author: davidph
ms.custom: ''
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: d4933bc344e1fb3ffade12e4ad9dd594ac8f90d6
ms.sourcegitcommit: ab0c654d924eeb5647e47444abb59d934345b205
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450184"
---
# <a name="sp_rxpredict"></a>sp_rxPredict  
[!INCLUDE [SQL Server 2016 Windows only](../../includes/applies-to-version/sqlserver2016-windows-only.md)]

SQL Server データベースにバイナリ形式で格納されている機械学習モデルで構成される、指定された入力の予測値を生成します。

R と Python の機械学習モデルのスコア付けをほぼリアルタイムで提供します。 `sp_rxPredict` は、のラッパーとして提供されるストアドプロシージャです。 
- `rxPredict`[RevoScaleR](/r-server/r-reference/revoscaler/revoscaler)および[microsoft ml](/r-server/r-reference/microsoftml/microsoftml-package)の R 関数と、 [revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)と[microsoft ml](/machine-learning-server/python-reference/microsoftml/microsoftml-package)の[rx_predict](/machine-learning-server/python-reference/revoscalepy/rx-predict) Python 関数。 これは C++ で記述され、スコアリング操作専用に最適化されています。

モデルは R または Python を使用して作成する必要がありますが、シリアル化され、ターゲットデータベースエンジンインスタンスでバイナリ形式で格納されると、R または Python の統合がインストールされていない場合でも、そのデータベースエンジンのインスタンスから使用できます。 詳細については、「 [sp_rxPredict を使用したリアルタイムのスコアリング](../../machine-learning/predictions/real-time-scoring.md)」を参照してください。

## <a name="syntax"></a>構文

```
sp_rxPredict  ( @model, @input )
```

### <a name="arguments"></a>引数

**model**

サポートされている形式の事前トレーニング済みモデル。 

**input**

有効な SQL クエリ

### <a name="return-values"></a>戻り値

スコア列、および入力データソースのすべてのパススルー列が返されます。
アルゴリズムがそのような値の生成をサポートしている場合は、信頼区間などの追加のスコア列を返すことができます。

## <a name="remarks"></a>解説

ストアドプロシージャを使用できるようにするには、インスタンスで SQLCLR が有効になっている必要があります。

> [!NOTE]
> このオプションを有効にすると、セキュリティに影響があります。 サーバーで SQLCLR が有効になっていない場合は、 [TRANSACT-SQL PREDICT](../../t-sql/queries/predict-transact-sql.md?view=sql-server-2017&preserve-view=true) 関数などの代替の実装を使用します。

ユーザーは、 `EXECUTE` データベースに対する権限が必要です。

### <a name="supported-algorithms"></a>サポートされているアルゴリズム

モデルを作成してトレーニングするには、R または Python に対してサポートされているアルゴリズムのいずれかを使用します。 [SQL Server Machine Learning Services (r または python)](../../machine-learning/sql-server-machine-learning-services.md)、 [SQL Server 2016 R Services](../../machine-learning/r/sql-server-r-services.md)、 [SQL Server Machine Learning Server (スタンドアロン)](../../machine-learning/r/r-server-standalone.md)、または [SQL Server 2016 R Server (スタンドアロン)](../../machine-learning/r/r-server-standalone.md?view=sql-server-2016&preserve-view=true)によって提供されます。

#### <a name="r-revoscaler-models"></a>R: RevoScaleR モデル

  + [rxLinMod](/machine-learning-server/r-reference/revoscaler/rxlinmod) \*
  + [rxLogit](/machine-learning-server/r-reference/revoscaler/rxlogit) \*
  + [rxBTrees](/machine-learning-server/r-reference/revoscaler/rxbtrees) \*
  + [rxDtree](/machine-learning-server/r-reference/revoscaler/rxdtree) \*
  + [rxdForest](/machine-learning-server/r-reference/revoscaler/rxdforest) \*

でマークされたモデルは \* 、関数を使用したネイティブスコアリングもサポート `PREDICT` します。

#### <a name="r-microsoftml-models"></a>R: Microsoft Ml モデル

  + [rxFastTrees](/machine-learning-server/r-reference/microsoftml/rxfasttrees)
  + [rxFastForest](/machine-learning-server/r-reference/microsoftml/rxfastforest)
  + [rxLogisticRegression](/machine-learning-server/r-reference/microsoftml/rxlogisticregression)
  + [rxOneClassSvm](/machine-learning-server/r-reference/microsoftml/rxoneclasssvm)
  + [rxNeuralNet](/machine-learning-server/r-reference/microsoftml/rxneuralnet)
  + [rxFastLinear](/machine-learning-server/r-reference/microsoftml/rxfastlinear)

#### <a name="r-transformations-supplied-by-microsoftml"></a>R: Microsoft Ml によって提供される変換

  + [featurizeText](/machine-learning-server/r-reference/microsoftml/rxfasttrees)
  + [concat](/machine-learning-server/r-reference/microsoftml/concat)
  + [categorical](/machine-learning-server/r-reference/microsoftml/categorical)
  + [categoricalHash](/machine-learning-server/r-reference/microsoftml/categoricalHash)
  + [selectFeatures](/machine-learning-server/r-reference/microsoftml/selectFeatures)

#### <a name="python-revoscalepy-models"></a>Python: revoscalepy モデル

  + [rx_lin_mod](/machine-learning-server/python-reference/revoscalepy/rx-lin-mod) \*
  + [rx_logit](/machine-learning-server/python-reference/revoscalepy/rx-logit) \*
  + [rx_btrees](/machine-learning-server/python-reference/revoscalepy/rx-btrees) \*
  + [rx_dtree](/machine-learning-server/python-reference/revoscalepy/rx-dtree) \*
  + [rx_dforest](/machine-learning-server/python-reference/revoscalepy/rx-dforest) \*

でマークされたモデルは \* 、関数を使用したネイティブスコアリングもサポート `PREDICT` します。

#### <a name="python-microsoftml-models"></a>Python: microsoft ml モデル

  + [rx_fast_trees](/machine-learning-server/python-reference/microsoftml/rx-fast-trees)
  + [rx_fast_forest](/machine-learning-server/python-reference/microsoftml/rx-fast-forest)
  + [rx_logistic_regression](/machine-learning-server/python-reference/microsoftml/rx-logistic-regression)
  + [rx_oneclass_svm](/machine-learning-server/python-reference/microsoftml/rx-oneclass-svm)
  + [rx_neural_network](/machine-learning-server/python-reference/microsoftml/rx-neural-network)
  + [rx_fast_linear](/machine-learning-server/python-reference/microsoftml/rx-fast-linear)

#### <a name="python-transformations-supplied-by-microsoftml"></a>Python: microsoft ml によって提供される変換

  + [featurize_text](/machine-learning-server/python-reference/microsoftml/rx-fast-trees)
  + [concat](/machine-learning-server/python-reference/microsoftml/concat)
  + [categorical](/machine-learning-server/python-reference/microsoftml/categorical)
  + [categorical_hash](/machine-learning-server/python-reference/microsoftml/categorical-hash)
  
### <a name="unsupported-model-types"></a>サポートされていないモデルの種類

次の種類のモデルはサポートされていません。

+ `rxGlm`RevoScaleR のアルゴリズムまたはアルゴリズムを使用するモデル `rxNaiveBayes` 。
+ R の PMML モデル。
+ 他のサードパーティライブラリを使用して作成されたモデル。
+ 変換関数または変換を含む数式を使用するモデル (`A ~ log(B` など) は、リアルタイム スコアリングではサポートされていません。 この種類のモデルを使用するには、データをリアルタイム スコアリングに渡す前に、入力データに対して変換を実行することをお勧めします。

リアルタイムスコアリングではインタープリターが使用されないため、インタープリターを必要とする可能性がある機能は、スコアリング手順ではサポートされていません。

## <a name="examples"></a>例

```sql
DECLARE @model = SELECT @model 
FROM model_table 
WHERE model_name = 'rxLogit trained';

EXEC sp_rxPredict @model = @model,
@inputData = N'SELECT * FROM data';
```

*\@ Inputdata* の入力データには、有効な SQL クエリに加えて、格納されているモデルの列と互換性のある列が含まれている必要があります。

`sp_rxPredict` でサポートされている .NET 列の型は、double、float、short、ushort、long、ulong、および string のみです。 リアルタイムスコアリングに使用する前に、入力データでサポートされていない型を除外することが必要になる場合があります。

対応する SQL 型の詳細については、「[SQL と CLR 型のマッピング](/dotnet/framework/data/adonet/sql/linq/sql-clr-type-mapping)」または「[CLR パラメーター データのマッピング](../clr-integration-database-objects-types-net-framework/mapping-clr-parameter-data.md)」を参照してください。
