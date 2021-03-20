---
description: uniqueidentifier (Transact-SQL)
title: uniqueidentifier (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/01/2017
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- uniqueidentifier
- uniqueidentifier_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- uniqueidentifier data type
- globally unique identifiers [SQL Server]
- GUIDs [SQL Server]
ms.assetid: b026035b-f3d2-4d70-989d-3884b4ca0233
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 45113196ca2af518ae01e28b5ebec5559bc21e15
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753172"
---
# <a name="uniqueidentifier-transact-sql"></a>uniqueidentifier (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

16 バイトの GUID です。
  
## <a name="remarks"></a>解説  
列またはのローカル変数 **uniqueidentifier** データ型は、次の方法で値に初期化することができます。
-   [NEWID](../../t-sql/functions/newid-transact-sql.md) または [NEWSEQUENTIALID](../../t-sql/functions/newsequentialid-transact-sql.md) 関数を使用する。    
-   *xxxxxxxx*-*xxxx*-*xxxx*-*xxxx*-*xxxxxxxxxxxx* の形式の文字列定数を変換する。各 *x* は 0 ～ 9 または a ～ f の 16 進数です。 たとえば、6F9619FF-8B86-D011-B42D-00C04FC964FF は有効な **uniqueidentifier** 値です。  
  
比較演算子で使用できる **uniqueidentifier** 値。 しかし、2 つの値のビット パターンを比較することによって、順序付けは実装されません。 **uniqueidentifier** 型の値に対して行うことができる操作は、比較 (=、<>、\<, >、\<=, >=) および NULL であるかどうかのチェック (IS NULL と IS NOT NULL) だけです。 他の算術演算子は使用できません。 **uniqueidentifier** データ型では、IDENTITY 以外のすべての列制約とプロパティを使用できます。
  
更新サブスクリプションでのマージ レプリケーションとトランザクション レプリケーションでは、テーブルの複数のコピーの間で列を確実に一意に識別するため、**uniqueidentifier** 列が使用されます。
  
## <a name="converting-uniqueidentifier-data"></a>uniqueidentifier データを変換する  
**uniqueidentifier** 型は、文字式からの変換のための文字型と見なされるため、文字型に変換する場合は切り捨てルールが適用されます。 つまり、文字式を異なるサイズの文字型に変換する場合、値が新しいデータ型に長すぎる場合は、切り捨てられます。 「使用例」セクションを参照してください。
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項

これらのツールと機能では、`uniqueidentifier` データ型はサポートされません。
- PolyBase
- Parallel Data Warehouse 用の [dwloader 読み込みツール](../../analytics-platform-system/dwloader.md)

## <a name="examples"></a>例  
次の例では、`uniqueidentifier` 型の値を `char` 型の値に変換します。
  
```sql
DECLARE @myid uniqueidentifier = NEWID();  
SELECT CONVERT(CHAR(255), @myid) AS 'char';  
```  
  
次の例は、変換後のデータ型に対して値が長すぎる場合のデータの切り捨てを示します。 **uniqueidentifier** 型は 36 文字に制限されているため、この長さを超える文字は切り捨てられます。
  
```sql
DECLARE @ID NVARCHAR(max) = N'0E984725-C51C-4BF4-9960-E1C80E27ABA0wrong';  
SELECT @ID, CONVERT(uniqueidentifier, @ID) AS TruncatedValue;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
String                                       TruncatedValue  
-------------------------------------------- ------------------------------------  
0E984725-C51C-4BF4-9960-E1C80E27ABA0wrong    0E984725-C51C-4BF4-9960-E1C80E27ABA0  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>関連項目
[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)  
[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)  
[DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)  
[NEWID &#40;Transact-SQL&#41;](../../t-sql/functions/newid-transact-sql.md)  
[NEWSEQUENTIALID &#40;Transact-SQL&#41;](../../t-sql/functions/newsequentialid-transact-sql.md)    
[SET @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-local-variable-transact-sql.md)  
[Updatable Subscriptions for Transactional Replication](../../relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication.md)
  
