---
description: sys.dm_db_uncontained_entities (Transact-SQL)
title: sys.dm_db_uncontained_entities (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_db_uncontained_entities
- dm_db_uncontained_entities_TSQL
- sys.dm_db_uncontained_entities_TSQL
- dm_db_uncontained_entities
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_uncontained_entities dynamic management view
ms.assetid: f417efd4-8c71-4f81-bc9c-af13bb4b88ad
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 6d3fd46894bcfe446b4fcaa741ad08bb2a5fab7c
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054746"
---
# <a name="sysdm_db_uncontained_entities-transact-sql"></a>sys.dm_db_uncontained_entities (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  データベースで使用される非包含オブジェクトを表示します。 非包含オブジェクトとは、包含データベースのデータベース境界を越えるオブジェクトです。 このビューには、包含データベースと非包含データベースの両方からアクセスできます。 sys.dm_db_uncontained_entities が空の場合は、データベースは非包含エンティティを使用していません。  
  
 モジュールがデータベース境界を 2 回以上越えている場合、最初に検出された交差のみが報告されます。  
  
|**列名**|**Type**|**説明**|  
|-|-|-|  
|*class*|**int**|1 = オブジェクトまたは列 (モジュール、XP、ビュー、シノニム、およびテーブルを含む)。<br /><br /> 4 = データベースプリンシパル<br /><br /> 5 = アセンブリ<br /><br /> 6 = 型<br /><br /> 7 = インデックス (フルテキスト インデックス)<br /><br /> 12 = データベース DDL トリガー<br /><br /> 19 = ルート<br /><br /> 30 = 監査の仕様|  
|*class_desc*|**nvarchar(120)**|エンティティのクラスの説明。 クラスに一致する次のいずれかです。<br /><br /> **OBJECT_OR_COLUMN**<br /><br /> **DATABASE_PRINCIPAL**<br /><br /> **ASSEMBLY**<br /><br /> **TYPE**<br /><br /> **INDEX**<br /><br /> **DATABASE_DDL_TRIGGER**<br /><br /> **ROUTE**<br /><br /> **AUDIT_SPECIFICATION**|  
|*major_id*|**int**|エンティティの ID。<br /><br /> *Class* が1の場合は、object_id<br /><br /> *Class* が4の場合は、database_principals. principal_id です。<br /><br /> *Class* = 5 の場合は、sys.assemblies.assembly_id ます。<br /><br /> *Class* が6の場合は、sys.types.user_type_id ます。<br /><br /> *Class* が7の場合は、sys.indexes.index_id ます。<br /><br /> *Class* が12の場合は、sys.triggers.object_id ます。<br /><br /> *Class* が19の場合は、sys.routes.route_id ます。<br /><br /> *Class* が30の場合は、sys です。 database_audit_specifications.database_specification_id を database_audit_specifications します。|  
|*statement_line_number*|**int**|クラスがモジュールの場合は、非包含エンティティの使用が見つかった行番号を返します。  それ以外の場合、値は null になります。|  
|*statement_ offset_begin*|**int**|クラスがモジュールの場合は、非包含エンティティの使用が開始する開始位置 (バイト単位) が 0 で始まることを示します。 それ以外の場合、戻り値は null になります。|  
|*statement_ offset_end*|**int**|クラスがモジュールの場合は、非包含エンティティの使用の終了位置 (バイト単位) が 0 で始まることを示します。 値 -1 はモジュールの最後を表します。 それ以外の場合、戻り値は null になります。|  
|*statement_type*|**nvarchar(512)**|ステートメントの種類。|  
|*feature_ 名*|**nvarchar (256)**|オブジェクトの外部名を返します。|  
|*feature_type_name*|**nvarchar (256)**|機能の種類を返します。|  
  
## <a name="remarks"></a>解説  
 sys.dm_db_uncontained_entities には、データベースの境界を越える可能性のあるエンティティが表示されます。 データベースの外部のオブジェクトを使用する可能性のあるユーザー エンティティが返されます。  
  
 次の機能の種類が報告されます。  
  
-   不明な包含動作 (動的な SQL または名前の遅延解決)  
  
-   DBCC コマンド  
  
-   システム ストアド プロシージャ  
  
-   システム スカラー関数  
  
-   システム テーブル値関数  
  
-   システム組み込み関数  
  
## <a name="security"></a>Security  
  
### <a name="permissions"></a>アクセス許可  
 sys.dm_db_uncontained_entities は、ユーザーが何らかの権限を持っているオブジェクトのみを返します。 データベースの包含を完全に評価するには、 **sysadmin** 固定サーバーロールのメンバーや **db_owner** ロールのメンバーなど、高い特権を持つユーザーがこの関数を使用する必要があります。  
  
## <a name="examples"></a>例  
 次の例では、P1 という名前のプロシージャを作成し、 `sys.dm_db_uncontained_entities`にクエリを実行します。 このクエリは、P1 がデータベースの外部にある **sys.endpoints** を使用していることを報告します。  
  
```sql  
CREATE DATABASE Test;  
GO  
  
USE Test;  
GO  
CREATE PROC P1  
AS   
SELECT * FROM sys.endpoints ;  
GO  
SELECT SO.name, UE.* FROM sys.dm_db_uncontained_entities AS UE  
LEFT JOIN sys.objects AS SO  
    ON UE.major_id = SO.object_id;  
```  
  
## <a name="see-also"></a>参照  
 [包含データベース](../../relational-databases/databases/contained-databases.md)  
  
  
