---
description: sys. アセンブリ (Transact-sql)
title: sys. assemblies (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.assemblies
- assemblies_TSQL
- sys.assemblies_TSQL
- assemblies
dev_langs:
- TSQL
helpviewer_keywords:
- sys.assemblies catalog view
ms.assetid: e321753f-293f-42ab-b225-d118713df40b
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: db4e6e8348dec7f19bbd53b5b413528c0cffee5d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754412"
---
# <a name="sysassemblies-transact-sql"></a>sys. アセンブリ (Transact-sql)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  アセンブリごとに1行の値を返します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|アセンブリの名前。 データベース内で一意です。|  
|**principal_id**|**int**|このアセンブリを所有するプリンシパルの ID。|  
|**assembly_id**|**int**|アセンブリ識別番号。 データベース内で一意です。|  
|**clr_name**|**nvarchar (4000)**|アセンブリの簡単な名前、バージョン番号、カルチャ、公開キー、およびアーキテクチャをエンコードする正規文字列。 この値は、共通言語ランタイム (CLR) 側でアセンブリを一意に識別する値です。|  
|**permission_set**|**tinyint**|アセンブリの権限セット/セキュリティレベル。<br /><br /> 1 = 安全なアクセス<br /><br /> 2 = 外部アクセス<br /><br /> 3 = 安全でないアクセス|  
|**permission_set_desc**|**nvarchar(60)**|アセンブリに対する権限セットまたはセキュリティ レベルの説明。<br /><br /> SAFE_ACCESS<br /><br /> EXTERNAL_ACCESS<br /><br /> UNSAFE_ACCESS|  
|**is_visible**|**bit**|1 = アセンブリは [!INCLUDE[tsql](../../includes/tsql-md.md)] エントリ ポイントを登録するため表示されます。<br /><br /> 0 = アセンブリは管理されている呼び出し元のみを対象にしています。 つまり、アセンブリによって、データベース内の他のアセンブリの内部実装が提供されます。|  
|**create_date**|**datetime**|アセンブリが作成または登録された日付。|  
|**modify_date**|**datetime**|アセンブリが変更された日付。|  
|**is_user_defined**|**bit**|アセンブリのソースを示します。<br /><br /> 0 = システム定義のアセンブリ ( **hierarchyid** データ型の場合は、Microsoft. SqlServer. Types など)<br /><br /> 1 = ユーザー定義のアセンブリ|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [CLR アセンブリカタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/clr-assembly-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [ASSEMBLYPROPERTY &#40;Transact-sql&#41;](../../t-sql/functions/assemblyproperty-transact-sql.md)  
  
  
