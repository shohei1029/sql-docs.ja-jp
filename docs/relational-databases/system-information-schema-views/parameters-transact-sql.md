---
description: パラメーター (Transact-sql)
title: PARAMETERS (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- PARAMETERS_TSQL
- PARAMETERS
dev_langs:
- TSQL
helpviewer_keywords:
- PARAMETERS view
- INFORMATION_SCHEMA.PARAMETERS view
ms.assetid: 06ded0ca-7d21-4400-864a-b801e855b257
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e0b6bc62151f8628e1fb93ea08a619da9f291e8b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755712"
---
# <a name="parameters-transact-sql"></a>パラメーター (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベース内の、現在のユーザーがアクセスできるユーザー定義の関数またはストアド プロシージャのパラメーターごとに 1 行のデータを返します。 関数の場合は、戻り値情報の行も返します。  
  
 これらのビューから情報を取得するには、INFORMATION_SCHEMA の完全修飾名を指定し **ます。**_view_name_。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**SPECIFIC_CATALOG**|**nvarchar (** 128 **)**|パラメーターであるルーチンのカタログ名。|  
|**SPECIFIC_SCHEMA**|**nvarchar (** 128 **)**|パラメーターの基になるルーチンのスキーマ名。<br /><br /> 重要オブジェクトのスキーマを決定するために INFORMATION_SCHEMA ビューを使用しないでください。 <strong> \* \* \* \* </strong> INFORMATION_SCHEMA ビューは、オブジェクトのメタデータのサブセットのみを表します。 オブジェクトのスキーマを調べる唯一の信頼性のある方法は、sys.objects カタログ ビューに対するクエリを実行する方法です。|  
|**SPECIFIC_NAME**|**nvarchar (** 128 **)**|パラメーターの基になるルーチンの名前。|  
|**ORDINAL_POSITION**|**int**|パラメーターの位置を示す 1 から始まる序数。 関数の戻り値の場合、これは0です。|  
|**PARAMETER_MODE**|**nvarchar (** 10 **)**|入力パラメーターでは IN が返され、出力パラメーターでは OUT が返され、I/O パラメーターでは INOUT が返されます。|  
|**IS_RESULT**|**nvarchar (** 10 **)**|関数であるルーチンの結果がである場合は YES を返します。 その他の場合は NO が返されます。|  
|**AS_LOCATOR**|**nvarchar (** 10 **)**|ロケーターとして宣言された場合は YES が返されます。 その他の場合は NO が返されます。|  
|**PARAMETER_NAME**|**nvarchar (** 128 **)**|パラメーターの名前。 関数の戻り値に相当する場合は NULL になります。|  
|**DATA_TYPE**|**nvarchar (** 128 **)**|システムにより提供されるデータ型。|  
|**CHARACTER_MAXIMUM_LENGTH**|**int**|バイナリまたは文字データ型の文字列の最大長。<br /><br /> **xml** と大きな値の型のデータの場合は-1。 その他の場合は NULL が返されます。|  
|**CHARACTER_OCTET_LENGTH**|**int**|バイナリまたは文字データ型の最大バイト数。<br /><br /> **xml** と大きな値の型のデータの場合は-1。 その他の場合は NULL が返されます。|  
|**COLLATION_CATALOG**|**nvarchar (** 128 **)**|常に NULL が返されます。|  
|**COLLATION_SCHEMA**|**nvarchar (** 128 **)**|常に NULL が返されます。|  
|**COLLATION_NAME**|**nvarchar (** 128 **)**|パラメーター照合の名前。 文字型の 1 つでない場合は、NULL が返されます。|  
|**CHARACTER_SET_CATALOG**|**nvarchar (** 128 **)**|パラメーターの文字セットのカタログ名。 文字型の 1 つでない場合は、NULL が返されます。|  
|**CHARACTER_SET_SCHEMA**|**nvarchar (** 128 **)**|常に NULL が返されます。|  
|**CHARACTER_SET_NAME**|**nvarchar (** 128 **)**|パラメーターの文字セット名。 文字型の 1 つでない場合は、NULL が返されます。|  
|**NUMERIC_PRECISION**|**tinyint**|数値データの概数、正確な数値データ、整数データ、または通貨データの有効桁数。 その他の場合は NULL が返されます。|  
|**NUMERIC_PRECISION_RADIX**|**smallint**|数値データの概数、正確な数値データ、整数データ、または通貨データの有効桁数の基数。 その他の場合は NULL が返されます。|  
|**NUMERIC_SCALE**|**tinyint**|数値データの概数、正確な数値データ、整数データ、または通貨データの桁数。 その他の場合は NULL が返されます。|  
|**DATETIME_PRECISION**|**smallint**|パラメーターの型が **datetime** または **smalldatetime** の場合の秒の小数部の有効桁数。 その他の場合は NULL が返されます。|  
|**INTERVAL_TYPE**|**nvarchar (** 30 **)**|NULL。 将来使用するために予約されています。|  
|**INTERVAL_PRECISION**|**smallint**|NULL。 将来使用するために予約されています。|  
|**USER_DEFINED_TYPE_CATALOG**|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|**USER_DEFINED_TYPE_SCHEMA**|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|**USER_DEFINED_TYPE_NAME**|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|**SCOPE_CATALOG**|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|**SCOPE_SCHEMA**|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
|**SCOPE_NAME**|**nvarchar (** 128 **)**|NULL。 将来使用するために予約されています。|  
  
## <a name="see-also"></a>参照  
 [システムビュー &#40;Transact-sql&#41;](../../t-sql/language-reference.md)   
 [情報スキーマビュー &#40;Transact-sql&#41;](~/relational-databases/system-information-schema-views/system-information-schema-views-transact-sql.md)   
 [sys.columns (Transact-SQL)](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [sys.parameters &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-parameters-transact-sql.md)  
  
