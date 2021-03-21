---
description: sys.syslanguages (Transact-SQL)
title: sys.sys言語 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.syslanguages
- sys.syslanguages_TSQL
- syslanguages
- syslanguages_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- syslanguages system table
- sys.syslanguages compatibility view
ms.assetid: f216d1cd-997c-42f0-a737-abbdfcd88383
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3fb34e09ceb553c4b1fc592d37e9871195aff77e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753252"
---
# <a name="syssyslanguages-transact-sql"></a>sys.syslanguages (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスに存在する言語ごとに、1 行のデータを格納します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|langid|**smallint**|一意の言語 ID。|  
|dateformat|**nchar(3)**|日付の順序 (例、DMY)。|  
|datefirst|**tinyint**|週の最初の曜日: 1 の場合は1、火曜日の場合は2、日曜日の場合は7です。|  
|upgrade|**int**|システムで使用するために予約されています。|  
|name|**sysname**|公式言語名 (たとえば、フランス語)。|  
|alias|**sysname**|言語の別名 (例 : French)。|  
|months|**nvarchar(372)**|1月から12月までの完全な長さの月名のコンマ区切りのリスト。各名前は最大20文字です。|  
|shortmonths|**nvarchar(132)**|1 月から 12 月の順に、コンマで区切った月の名前。月の名前は省略形で、それぞれ 9 文字までです。|  
|days|**nvarchar(217)**|曜日の名前のコンマ区切りリスト。各名前は最大30文字で指定します。|  
|lcid|**int**|この言語を使用する [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows のロケール ID。|  
|msglangid|**smallint**|[!INCLUDE[ssDE](../../includes/ssde-md.md)] メッセージグループ ID。|  
  
 には、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] インストールされている次の言語が含まれています。  
  
|英語での名前|Windows LCID|[!INCLUDE[ssDE](../../includes/ssde-md.md)] メッセージ グループ ID|  
|---------------------|------------------|-----------------------------------------|  
|英語|1033|1033|  
|ドイツ語|1031|1031|  
|フランス語|1036|1036|  
|日本語|1041|1041|  
|デンマーク語|1030|1030|  
|スペイン語|3082|3082|  
|イタリア語|1040|1040|  
|オランダ語|1043|1043|  
|ノルウェー語|2068|2068|  
|ポルトガル語|2070|2070|  
|フィンランド語|1035|1035|  
|スウェーデン語|1053|1053|  
|チェコ語|1029|1029|  
|ハンガリー語|1038|1038|  
|ポーランド語|1045|1045|  
|ルーマニア語|1048|1048|  
|クロアチア語|1050|1050|  
|スロバキア語|1051|1051|  
|スロヴェニア語|1060|1060|  
|ギリシャ語|1032|1032|  
|ブルガリア語|1026|1026|  
|ロシア語|1049|1049|  
|トルコ語|1055|1055|  
|英語 (U.K.)|2057|1033|  
|エストニア語|1061|1061|  
|ラトビア語|1062|1062|  
|リトアニア語|1063|1063|  
|ポルトガル語 (ブラジル)|1046|1046|  
|Traditional Chinese|1028|1028|  
|韓国語|1042|1042|  
|簡体中国語|2052|2052|  
|アラビア語|1025|1025|  
|タイ語|1054|1054|  
  
## <a name="see-also"></a>参照  
 [互換性ビュー &#40;Transact-sql&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)   
 [システムビューへのシステムテーブルのマッピング &#40;Transact-sql&#41;](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)  
  
  
