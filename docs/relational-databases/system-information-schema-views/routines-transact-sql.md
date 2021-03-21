---
description: ROUTINES (Transact-SQL)
title: ルーチン (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- ROUTINES_TSQL
- ROUTINES
dev_langs:
- TSQL
helpviewer_keywords:
- ROUTINES view
- INFORMATION_SCHEMA.ROUTINES view
ms.assetid: c75561b2-c9a1-48a1-9afa-a5896b6454cf
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 231973e8fa89e1e507502bf2f2a62d730fb9f517
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104736092"
---
# <a name="routines-transact-sql"></a>ROUTINES (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベースの現在のユーザーがアクセスできるストアド プロシージャと関数ごとに、1 行のデータを返します。 戻り値を記述する列は、関数にのみ適用されます。 ストアドプロシージャの場合、これらの列は NULL になります。  
  
 これらのビューから情報を取得するには、INFORMATION_SCHEMA の完全修飾名を指定します。*view_name*。  
  
> [!NOTE]  
>  ROUTINE_DEFINITION 列には、関数またはストアドプロシージャを作成したソースステートメントが含まれています。 これらのソースステートメントには、埋め込みのキャリッジリターンが含まれている可能性があります。 結果をテキスト形式で表示するアプリケーションにこの列を返す場合、ROUTINE_DEFINITION の結果に埋め込まれた復帰は、全体的な結果セットの書式設定に影響を与える可能性があります。 ROUTINE_DEFINITION 列を選択する場合は、結果セットをグリッドで返すか、ROUTINE_DEFINITION を専用のテキスト ボックスに返すなどして、埋め込み型のキャリッジ リターンを調整する必要があります。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|SPECIFIC_CATALOG|**nvarchar (** 128 **)**|カタログ固有の名前。 この名前は ROUTINE_CATALOG と同じです。|  
|SPECIFIC_SCHEMA|**nvarchar (** 128 **)**|スキーマ固有の名前。<br /><br /> 重要オブジェクトのスキーマを決定するために INFORMATION_SCHEMA ビューを使用しないでください。 **\* \* \* \*** INFORMATION_SCHEMA ビューは、オブジェクトのメタデータのサブセットのみを表します。 オブジェクトのスキーマを調べる唯一の信頼性のある方法は、sys.objects カタログ ビューに対するクエリを実行する方法です。|  
|SPECIFIC_NAME|**nvarchar (** 128 **)**|カタログ固有の名前。 この名前は ROUTINE_NAME と同じです。|  
|ROUTINE_CATALOG|**nvarchar (** 128 **)**|関数のカタログの名前。|  
|ROUTINE_SCHEMA|**nvarchar (** 128 **)**|関数を含むスキーマの名前。<br /><br /> 重要オブジェクトのスキーマを決定するために INFORMATION_SCHEMA ビューを使用しないでください。 **\* \* \* \*** INFORMATION_SCHEMA ビューは、オブジェクトのメタデータのサブセットのみを表します。 オブジェクトのスキーマを調べる唯一の信頼性のある方法は、sys.objects カタログ ビューに対するクエリを実行する方法です。|  
|ROUTINE_NAME|**nvarchar (** 128 **)**|関数の名前です。|  
|ROUTINE_TYPE|**nvarchar (** 20 **)**|ストアドプロシージャのプロシージャと関数の関数を返します。|  
|MODULE_CATALOG|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|MODULE_SCHEMA|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|MODULE_NAME|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|UDT_CATALOG|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|UDT_SCHEMA|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|UDT_NAME|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|DATA_TYPE|**nvarchar (** 128 **)**|関数の戻り値のデータ型。 テーブル値関数の場合、 **テーブル** を返します。|  
|CHARACTER_MAXIMUM_LENGTH|**int**|戻り値のデータ型が文字型の場合の最大文字数。<br /><br /> **xml** と大きな値の型のデータの場合は-1。|  
|CHARACTER_OCTET_LENGTH|**int**|戻り値の型が文字型の場合は、バイト単位の最大長。<br /><br /> **xml** と大きな値の型のデータの場合は-1。|  
|COLLATION_CATALOG|**nvarchar (** 128 **)**|常に NULL が返されます。|  
|COLLATION_SCHEMA|**nvarchar (** 128 **)**|常に NULL が返されます。|  
|COLLATION_NAME|**nvarchar (** 128 **)**|戻り値の照合順序名。 非文字型の場合、は NULL を返します。|  
|CHARACTER_SET_CATALOG|**nvarchar (** 128 **)**|常に NULL が返されます。|  
|CHARACTER_SET_SCHEMA|**nvarchar (** 128 **)**|常に NULL が返されます。|  
|CHARACTER_SET_NAME|**nvarchar (** 128 **)**|戻り値の文字セットの名前。 非文字型の場合、は NULL を返します。|  
|NUMERIC_PRECISION|**smallint**|戻り値の数値の有効桁数。 数値以外の型の場合、は NULL を返します。|  
|NUMERIC_PRECISION_RADIX|**smallint**|戻り値の数値の有効桁数の基数。 数値型でない場合、は NULL を返します。|  
|NUMERIC_SCALE|**smallint**|戻り値の小数点以下桁数。 数値型でない場合、は NULL を返します。|  
|DATETIME_PRECISION|**smallint**|戻り値が **datetime** 型の場合の秒の小数部の有効桁数。 その他の場合は NULL が返されます。|  
|INTERVAL_TYPE|**nvarchar (** 30 **)**|NULL。 将来使用するために予約されています。|  
|INTERVAL_PRECISION|**smallint**|NULL。 将来使用するために予約されています。|  
|TYPE_UDT_CATALOG|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|TYPE_UDT_SCHEMA|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|TYPE_UDT_NAME|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|SCOPE_CATALOG|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|SCOPE_SCHEMA|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|SCOPE_NAME|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|MAXIMUM_CARDINALITY|**bigint**|NULL。 将来使用するために予約されています。|  
|DTD_IDENTIFIER|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|ROUTINE_BODY|**nvarchar (** 30 **)**|外部で記述された関数の場合は、関数の場合は SQL を返し [!INCLUDE[tsql](../../includes/tsql-md.md)] ます。<br /><br /> 関数は常に SQL です。|  
|ROUTINE_DEFINITION|**nvarchar (** 4000 **)**|関数またはストアドプロシージャが暗号化されていない場合、関数またはストアドプロシージャの定義テキストの最初の4000文字を返します。 その他の場合は NULL が返されます。<br /><br /> 完全な定義を確実に取得するには、 [sys.sql_modules](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)カタログビューの[OBJECT_DEFINITION](../../t-sql/functions/object-definition-transact-sql.md)関数または定義列に対してクエリを実行します。|  
|EXTERNAL_NAME|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|EXTERNAL_LANGUAGE|**nvarchar (** 30 **)**|NULL。 将来使用するために予約されています。|  
|PARAMETER_STYLE|**nvarchar (** 30 **)**|NULL。 将来使用するために予約されています。|  
|IS_DETERMINISTIC|**nvarchar (** 10 **)**|ルーチンが決定的な場合は YES を返します。<br /><br /> ルーチンが非決定的である場合、は NO を返します。<br /><br /> ストアド プロシージャの場合は常に NO が返されます。|  
|SQL_DATA_ACCESS|**nvarchar (** 30 **)**|次の値のいずれか。<br /><br /> NONE = 関数に SQL が含まれません。<br /><br /> CONTAINS = 関数に SQL が含まれる可能性があります。<br /><br /> READS = 関数で SQL データが読み取られる可能性があります。<br /><br /> 変更 = 関数は SQL データを変更する可能性があります。<br /><br /> すべての関数に READS、すべてのストアド プロシージャに MODIFIES が返されます。|  
|IS_NULL_CALL|**nvarchar (** 10 **)**|いずれかの引数が NULL の場合、ルーチンを呼び出すことができるかどうかを示します。|  
|SQL_PATH|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|SCHEMA_LEVEL_ROUTINE|**nvarchar (** 10 **)**|スキーマレベルの関数の場合は YES、スキーマレベルの関数でない場合は NO を返します。<br /><br /> 常に YES を返します。|  
|MAX_DYNAMIC_RESULT_SETS|**smallint**|ルーチンによって返される動的結果セットの最大数。<br /><br /> 関数の場合は 0 が返されます。|  
|IS_USER_DEFINED_CAST|**nvarchar (** 10 **)**|ユーザー定義のキャスト関数の場合は YES、ユーザー定義の cast 関数でない場合は NO を返します。<br /><br /> 常に NO が返されます。|  
|IS_IMPLICITLY_INVOCABLE|**nvarchar (** 10 **)**|ルーチンを暗黙的に呼び出すことができる場合は YES、関数を暗黙的に呼び出すことができない場合は NO を返します。<br /><br /> 常に NO が返されます。|  
|CREATED|**datetime**|ルーチンが作成された時刻。|  
|LAST_ALTERED|**datetime**|関数が最後に変更された時刻。|  
  
## <a name="see-also"></a>参照  
 [システムビュー &#40;Transact-sql&#41;](../../t-sql/language-reference.md)   
 [情報スキーマビュー &#40;Transact-sql&#41;](~/relational-databases/system-information-schema-views/system-information-schema-views-transact-sql.md)   
 [sys.columns (Transact-SQL)](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [sys.procedures &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-procedures-transact-sql.md)   
 [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)  
  
