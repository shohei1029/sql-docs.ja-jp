---
description: 算術演算子 (Transact-SQL)
title: 算術演算子 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- operators [Transact-SQL], arithmetic
- arithmetic operators
- math operations [Transact-SQL]
ms.assetid: a41b92a5-1061-4e4d-bb3b-a180b73c88fa
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0bde4e3f5c12dcce82df668aeb01ba0653cebcc4
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751732"
---
# <a name="arithmetic-operators-transact-sql"></a>算術演算子 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

算術演算子は、1 つまたは複数のデータ型の 2 つの式に対して算術演算を実行します。 それらは、数値データ型のカテゴリから実行されます。 データ型の分類の詳細については、「[Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)」を参照してください。  
  
|演算子|説明|  
|--------------|-------------|  
|[+ (加算)](../../t-sql/language-elements/add-transact-sql.md)|加算|  
|[- (減算)](../../t-sql/language-elements/subtract-transact-sql.md)|減算|  
|[* (乗算)](../../t-sql/language-elements/multiply-transact-sql.md)|乗算|  
|[/ (除算)](../../t-sql/language-elements/divide-transact-sql.md)|事業部|  
|[% (剰余)](../../t-sql/language-elements/modulo-transact-sql.md)|除算による整数の剰余を返します。 たとえば、12 % 5 の場合、12 を 5 で割ると余りは 2 なので、12 % 5 = 2 となります。|  
  
加算 (+) 演算子と減算 (-) 演算子を使用して、**datetime** 型と **smalldatetime** 型の値に対して算術演算を実行することもできます。  
  
算術演算の結果の有効桁数と小数点以下桁数の詳細については、「[有効桁数、小数点以下桁数、および長さ &#40;Transact-SQL&#41;](../../t-sql/data-types/precision-scale-and-length-transact-sql.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
[数学関数 &#40;Transact-SQL&#41;](../../t-sql/functions/mathematical-functions-transact-sql.md)   
[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
[式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)  
  
  
