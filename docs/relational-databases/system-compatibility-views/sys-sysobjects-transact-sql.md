---
description: sys.sysオブジェクト (Transact-sql)
title: sys.sysオブジェクト (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.sysobjects_TSQL
- sysobjects
- sysobjects_TSQL
- sys.sysobjects
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysobjects compatibility view
- sysobjects system table
ms.assetid: 44fdc387-67b0-4139-8bf5-ed26cf640cd1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 84b5856b68d04f5df2f4bfd8d10a30f7d25b0d73
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755242"
---
# <a name="syssysobjects-transact-sql"></a>sys.sysオブジェクト (Transact-sql)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  制約、デフォルト、ログ、ルール、ストアド プロシージャなど、データベース内で作成されるオブジェクトごとに 1 行のデータを保持します。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|name|**sysname**|オブジェクト名|  
|id|**int**|オブジェクト id 番号|  
|xtype|**char(2)**|オブジェクトの種類です。 次のいずれかのオブジェクトの種類を指定できます。<br /><br /> AF = 集計関数 (CLR)<br /><br /> C = CHECK 制約<br /><br /> D = Default または DEFAULT 制約<br /><br /> F = FOREIGN KEY 制約<br /><br /> L = ログ<br /><br /> FN = スカラー関数<br /><br /> FS = アセンブリ (CLR) スカラー関数<br /><br /> FT = アセンブリ (CLR) テーブル値関数<br /><br /> IF = インラインテーブル関数<br /><br /> 内部テーブル<br /><br /> P = ストアドプロシージャ<br /><br /> PC = アセンブリ (CLR) ストアドプロシージャ<br /><br /> PK = PRIMARY KEY 制約 (種類は K)<br /><br /> RF = レプリケーションフィルターストアドプロシージャ<br /><br /> S = システムテーブル<br /><br /> SN = シノニム<br /><br /> SQ = サービスキュー<br /><br /> TA = アセンブリ (CLR) DML トリガー<br /><br /> TF = テーブル関数<br /><br /> TR = SQL DML トリガー<br /><br /> TT = テーブル型<br /><br /> U = ユーザーテーブル<br /><br /> UQ = UNIQUE 制約 (種類は K)<br /><br /> V = ビュー<br /><br /> X = 拡張ストアド プロシージャ|  
|uid|**smallint**|オブジェクトの所有者のスキーマ ID です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の前のバージョンからアップグレードしたデータベースの場合、スキーマ ID は所有者のユーザー ID と同じです。 ユーザーおよびロールの数が32767を超えた場合、オーバーフローまたは NULL を返します。<br /><br /> 重要次のいずれかの DDL ステートメントを使用する場合は、sys.sysオブジェクトではなく、 **sys \* \* . objects カタログビューを使用する必要があります。 \* \*** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)<br /><br /> CREATE &#124; ALTER &#124; DROP USER<br /><br /> CREATE &#124; ALTER &#124; DROP ROLE<br /><br /> CREATE &#124; ALTER &#124; DROP APPLICATION ROLE<br /><br /> CREATE SCHEMA<br /><br /> オブジェクトに対する ALTER AUTHORIZATION|  
|info|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|status|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|base_schema_ver|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|replinfo|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|parent_obj|**int**|親オブジェクトのオブジェクト id 番号。 たとえば、トリガーや制約の場合はテーブル ID です。|  
|crdate|**datetime**|オブジェクトが作成された日付です。|  
|ftcatid|**smallint**|フルテキスト インデックス作成で登録されたすべてのユーザー テーブルのフルテキスト カタログの識別子です。登録されていないすべてのユーザー テーブルには 0 を指定します。|  
|schema_ver|**int**|テーブルのスキーマが変更されるたびに増加するバージョン番号です。 常に 0 を返します。|  
|stats_schema_ver|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|type|**char(2)**|オブジェクトの種類です。 値は、次のいずれかです。<br /><br /> AF = 集計関数 (CLR)<br /><br /> C = CHECK 制約<br /><br /> D = Default または DEFAULT 制約<br /><br /> F = FOREIGN KEY 制約<br /><br /> FN = スカラー関数<br /><br /> FS = アセンブリ (CLR) スカラー関数<br /><br /> FT = アセンブリ (CLR) テーブル値関数 If = インラインテーブル関数<br /><br /> IT = 内部テーブル<br /><br /> K = PRIMARY KEY 制約または UNIQUE 制約<br /><br /> L = ログ<br /><br /> P = ストアドプロシージャ<br /><br /> PC = アセンブリ (CLR) ストアドプロシージャ<br /><br /> R = ルール<br /><br /> RF = レプリケーションフィルターストアドプロシージャ<br /><br /> S = システムテーブル<br /><br /> SN = シノニム<br /><br /> SQ = サービスキュー<br /><br /> TA = アセンブリ (CLR) DML トリガー<br /><br /> TF = テーブル関数<br /><br /> TR = SQL DML トリガー<br /><br /> TT = テーブル型<br /><br /> U = ユーザーテーブル<br /><br /> V = ビュー<br /><br /> X = 拡張ストアド プロシージャ|  
|userstat|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|sysstat|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|indexdel|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|refdate|**datetime**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|version|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|deltrig|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|instrig|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|updtrig|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|seltrig|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|category|**int**|パブリケーション、制約、および id に使用されます。|  
|cache|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
  
## <a name="see-also"></a>参照  
 [システムビューへのシステムテーブルのマッピング &#40;Transact-sql&#41;](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [互換性ビュー &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
