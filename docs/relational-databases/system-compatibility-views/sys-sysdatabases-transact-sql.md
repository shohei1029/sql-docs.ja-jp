---
description: sys.sysdatabases (Transact-SQL)
title: sys.sysデータベース (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sysdatabases_TSQL
- sys.sysdatabases_TSQL
- sys.sysdatabases
- sysdatabases
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysdatabases compatibility view
- sysdatabases system table
ms.assetid: 60a93880-62f1-4eda-a886-f046706ba90c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 890ef3b430b44a2ea5c8f407d12fa1c0a709fe86
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753442"
---
# <a name="syssysdatabases-transact-sql"></a>sys.sysdatabases (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  のインスタンス内のデータベースごとに1行のデータを格納 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]が最初にインストールされるときに、 **sysdatabases** には **master**、 **model**、 **msdb**、および **tempdb** の各データベースのエントリが格納されます。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|データベース名|  
|**dbid**|**smallint**|データベース ID|  
|**sid**|**varbinary(85)**|データベース作成者のシステム ID|  
|**mode**|**smallint**|作成中にデータベースをロックするために内部的に使用されます。|  
|**status**|**int**|次のように [ALTER database](../../t-sql/statements/alter-database-transact-sql.md) を使用して設定できる状態ビット。<br /><br /> 1 = **autoclose** (ALTER database)<br /><br /> 4 = **select into/bulkcopy** (SET RECOVERY を使用した ALTER database)<br /><br /> 8 = **chkpt. on** (SET RECOVERY を使用した ALTER database)<br /><br /> 16 = **破損ページ検出** (ALTER database)<br /><br /> 32 = **読み込み中**<br /><br /> 64 = **回復前**<br /><br /> 128 =**回復** 中<br /><br /> 256 = **復旧されていません**<br /><br /> 512 = **offline** (ALTER database)<br /><br /> 1024 = **読み取り専用** (ALTER database)<br /><br /> 2048 = **dbo use only** (SET RESTRICTED_USER を使用した ALTER database)<br /><br /> 4096 = **シングルユーザー** (ALTER database)<br /><br /> 32768 = **緊急モード**<br /><br /> 65536 = **CHECKSUM** (ALTER database)<br /><br /> 4194304 = 自動 **圧縮** (ALTER database)<br /><br /> 1073741824 = **クリーンシャットダウン**<br /><br /> 複数のビットが同時にオンであってもかまいません。|  
|**status2**|**int**|16384 = **ANSI null 既定** (ALTER database)<br /><br /> 65536 = **concat null 生成 null** (ALTER DATABASE)<br /><br /> 131072 = **再帰トリガー** (ALTER database)<br /><br /> 1048576 = **ローカルカーソルに対する既定値** (ALTER database)<br /><br /> 8388608 = **引用符で囲ま** れた識別子 (ALTER database)<br /><br /> 33554432 = **カーソルがコミット時に閉じる** (ALTER database)<br /><br /> 67108864 = **ANSI null** (ALTER database)<br /><br /> 268435456 = **ANSI 警告** (ALTER database)<br /><br /> 536870912 = **フルテキストが有効** ( **sp_fulltext_database** を使用して設定)|  
|**crdate**|**datetime**|作成日|  
|**確保**|**datetime**|将来使用するために予約されています。|  
|**category**|**int**|レプリケーションに使用される情報のビットマップが含まれます。<br /><br /> 1 = スナップショットレプリケーションまたはトランザクションレプリケーション用にパブリッシュされます。<br /><br /> 2 = スナップショットパブリケーションまたはトランザクションパブリケーションをサブスクライブしています。<br /><br /> 4 = マージレプリケーション用にパブリッシュされます。<br /><br /> 8 = マージ パブリケーションにサブスクライブしています。<br /><br /> 16 = ディストリビューションデータベース。|  
|**cmptlevel**|**tinyint**|データベースの互換性レベル。 詳細については、「[ALTER DATABASE 互換性レベル &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)」を参照してください。|  
|**ファイル名**|**nvarchar(260)**|データベースのプライマリ ファイルのオペレーティング システム パスと名前です。<br /><br /> **ファイル名** は、 **dbcreator**、 **sysadmin**、データベース所有者、CREATE ANY database 権限、または次の権限のいずれかを持つているユーザーに表示されます: ALTER any DATABASE、CREATE any database、VIEW any DEFINITION。 パスとファイル名を返すには、 [sys.sysファイル](../../relational-databases/system-compatibility-views/sys-sysfiles-transact-sql.md) の互換性ビューまたは [sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md) ビューに対してクエリを実行します。|  
|**version**|**smallint**|このデータベースが作成された [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コードの内部バージョン番号です。 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
  
## <a name="see-also"></a>参照  
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [システムビューへのシステムテーブルのマッピング &#40;Transact-sql&#41;](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [互換性ビュー &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
