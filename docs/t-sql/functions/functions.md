---
description: Microsoft SQL データベース関数とは
title: Microsoft SQL データベース関数とは | Microsoft Docs
ms.custom: ''
ms.date: 06/28/2017
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- built-in functions [SQL Server]
- function [SQL Server] See functions [SQL Server]
- functions [Transact-SQL]
- functions [SQL Server], about functions
- scalar functions
- functions [SQL Server]
ms.assetid: 17186213-5ab5-40b0-b470-b660af1ec44c
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 68ea2973865ed43d357c99e7ad285c021f68f9bc
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747382"
---
# <a name="what-are-the-sql-database-functions"></a>Microsoft SQL データベース関数とは
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

SQL データベースで使うことができる組み込み関数のカテゴリについて説明します。 組み込み関数を使うか、独自のユーザー定義関数を作成することができます。
  
## <a name="aggregate-functions"></a>集計関数

集計関数は、値の集まりに対して計算を実行し、1 つの値を返します。 SELECT ステートメントの選択リストまたは HAVING 句で使うことができます。 集計を GROUP BY 句と組み合わせて使って、行のカテゴリに対する集計を計算できます。 特定の値範囲の集計を計算するには、OVER 句を使います。 GROUPING または GROUPING_ID の集計の後に OVER 句を使うことはできません。

すべての集計関数は決定論的であり、同じ入力値に対して実行すると常に同じ値を返します。 詳しくは、「[決定的関数と非決定的関数](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)」をご覧ください。

## <a name="analytic-functions"></a>分析関数
分析関数によって、行のグループに基づいた集計値が計算されます。 ただし集計関数とは異なり、分析関数は各グループについて複数の行を返すことがあります。 分析関数を使うと、グループ内の移動平均、集計途中経過、パーセンテージまたは上位 N 位の結果を計算できます。

## <a name="ranking-functions"></a>順位付け関数
順位付け関数により、パーティションの各行の順位値が返されます。 使用する関数によっては、いくつかの行で、他の行と同じ値を受け取る場合があります。 順位付け関数は非決定的です。

## <a name="rowset-functions"></a>行セット関数
行セット関数は、SQL ステートメントの中でテーブル参照のように使用できるオブジェクトを返します。

## <a name="scalar-functions"></a>スカラー関数
1 つの値に対して操作を行い、1 つの値を返します。 スカラー関数は、式を使用できるすべての場所で使用できます。

### <a name="categories-of-scalar-functions"></a>スカラー関数のカテゴリ
  
|関数のカテゴリ|説明|  
|-----------------------|-----------------|  
|[構成関数](configuration-functions-transact-sql.md)|現在の構成についての情報を返します。|  
|[変換関数](conversion-functions-transact-sql.md)|データ型のキャストと変換をサポートします。|  
|[カーソル関数](cursor-functions-transact-sql.md)|カーソルについての情報を返します。|  
|[日付と時刻のデータ型および関数](date-and-time-data-types-and-functions-transact-sql.md)|日付時刻型の入力値に対して操作を実行し、文字列値、数値、または日付時刻値を返します。|  
|[JSON 関数](json-functions-transact-sql.md)|JSON データを検証、クエリ、または変更します。|  
|[論理関数](logical-functions-choose-transact-sql.md)|論理演算を実行します。|  
|[数学関数](mathematical-functions-transact-sql.md)|パラメーターとして関数に渡された入力値に基づいて計算を実行し、数値を返します。|  
|[メタデータ関数](metadata-functions-transact-sql.md)|データベースおよびデータベース オブジェクトについての情報を返します。|  
|[セキュリティ関数](security-functions-transact-sql.md)|ユーザーとロールについての情報を返します。|  
|[文字列関数](string-functions-transact-sql.md)|文字列型 (**char** または **varchar**) の入力値に対して操作を実行し、文字列値または数値を返します。|  
|[システム関数](../../relational-databases/system-functions/system-functions-category-transact-sql.md)|値、オブジェクト、および [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンス内の設定値に対して操作を実行し、それについての情報を返します。|  
|[システム統計関数](system-statistical-functions-transact-sql.md)|システムについての統計情報を返します。|  
|[テキストとイメージ関数](./text-and-image-functions-textptr-transact-sql.md)|テキスト入力値、イメージ入力値、または列に対して操作を実行し、値についての情報を返します。|  
  
## <a name="function-determinism"></a>関数の決定性  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の組み込み関数は、決定的または非決定的のいずれかです。 特定の一連の入力値を使用して呼び出されたときに必ず同じ結果を返す場合、その関数は決定的です。 同じ特定の一連の入力値を使用しても呼び出すたびに異なる結果を返す場合、その関数は非決定的です。 詳しくは、「[決定的関数と非決定的関数](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)」をご覧ください。  
  
## <a name="function-collation"></a>関数の照合順序  
 入力に文字列を使用し、出力に文字列を返す関数の場合、入力文字列の照合順序が出力に適用されます。  
  
 入力に文字列以外を使用し、文字列を返す関数の場合は、現在のデータベースの既定の照合順序が出力に適用されます。  
  
 入力に複数の文字列を使用し、単一の文字列を返す関数の場合は、照合順序の優先順位ルールによって出力文字列の照合順序が設定されます。 詳細については、「[照合順序の優先順位 &#40;Transact-SQL&#41;](../../t-sql/statements/collation-precedence-transact-sql.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)   
 [決定的関数と非決定的関数](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)   
 [ストアド プロシージャの使用 &#40;MDX&#41;](../../mdx/using-stored-procedures-mdx.md)  
  
