---
description: DATABASEPROPERTYEX (Transact-SQL)
title: DATABASEPROPERTYEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/23/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATABASEPROPERTYEX
- DATABASEPROPERTYEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- DATABASEPROPERTYEX function
- displaying database properties
- database properties [SQL Server]
ms.assetid: 8a9e0ffb-28b5-4640-95b2-a54e3e5ad941
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 72585ca22444ab5e21c6744e303225224dc0a9d3
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754622"
---
# <a name="databasepropertyex-transact-sql"></a>DATABASEPROPERTYEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の指定データベースについては、指定のデータベース オプションまたはプロパティの現在の設定がこの関数によって返されます。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DATABASEPROPERTYEX ( database , property )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*database*  
データベースの名前を指定する式。このデータベースに対して、`DATABASEPROPERTYEX` は指定されたプロパティ情報を返します。 *database* には、データ型 **nvarchar(128)** が与えられます。  

[!INCLUDE[ssSDS](../../includes/sssds-md.md)] については、`DATABASEPROPERTYEX` は現在のデータベースの名前を必要とします。 別のデータベース名が指定されている場合、すべてのプロパティに対して NULL を返します。
  
*property*  
返されるデータベース プロパティの名前を指定する式。 *property* にはデータ型 **varchar(128)** が与えられ、この表のいずれかの値が入ります。
  
> [!NOTE]  
>  データベースがまだ開始されていない場合、`DATABASEPROPERTYEX` を呼び出したとき、`DATABASEPROPERTYEX` がメタデータから検索せず、データベースに直接アクセスすることで値を引き出した場合、NULL が返されます。 AUTO_CLOSE が ON に設定されているか、それ以外の方法でオフラインになっているデータベースは '開始されていない' と見なされます。  
  
|プロパティ|説明|返される値|  
|---|---|---|
|照合順序|データベースの既定の照合順序名です。|照合順序名<br /><br /> NULL: データベースは開始していません<br /><br /> 基本データ型: **nvarchar(128)**|  
|ComparisonStyle|照合順序の Windows 比較形式です。 次のスタイルの値を使用し、完全な ComparisonStyle 値のビットマップを構築します。<br /><br /> 大文字と小文字を区別しない: 1<br /><br /> アクセントを無視する: 2<br /><br /> ひらがなとカタカナを区別しない: 65536<br /><br /> 全角と半角を区別しない: 131072<br /><br /> <br /><br /> たとえば、既定値 196609 は、大文字と小文字を区別しない、ひらがなとカタカナを区別しない、全角と半角を区別しないという 3 つのオプションを足した値を表しています。|比較スタイルを返します。<br /><br /> バイナリ照合順序ではすべて 0 が返されます。<br /><br /> 基本データ型: **int**|  
|Edition|データベースのエディションまたはサービス レベルです。|**適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)]。<br /><br /> <br /><br /> General Purpose<br /><br /> Business Critical<br /><br /> Basic<br /><br /> Standard<br /><br /> Premium<br /><br /> System (マスター データベース向け)<br /><br /> NULL: データベースは開始していません<br /><br /> 基本データ型: **nvarchar**(64)|  
|IsAnsiNullDefault|データベースは、ISO のルールに従い NULL 値を許可します。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsAnsiNullsEnabled|NULL 値との比較はすべて、不明として評価されます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsAnsiPaddingEnabled|比較または挿入を行う前に、対象の文字列が同じ長さになるようにパディングを行います。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsAnsiWarningsEnabled|一般的なエラー状態が発生したとき、SQL Server からエラー メッセージまたは警告メッセージが出力されます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsArithmeticAbortEnabled|クエリ実行中にオーバーフローまたは 0 除算エラーが発生したとき、クエリは終了します。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsAutoClose|最後のユーザーが終了すると、データベースは即座にシャットダウンし、リソースを解放します。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsAutoCreateStatistics|クエリのパフォーマンスを向上させるために、クエリ オプティマイザーが必要に応じて 1 列ずつの統計を作成します。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsAutoCreateStatisticsIncremental|可能な場合、自動作成された 1 列ずつの統計は増分統計になります。|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> 1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsAutoShrink|データベース ファイルを、自動的に行われる定期的な圧縮の対象とします。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsAutoUpdateStatistics|古くなっている可能性がある既存の統計がクエリで使用されるとき、クエリ オプティマイザーによってその統計が更新されます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 入力は無効です<br /><br /> 基本データ型: **int**|
|IsClone|データベースは、DBCC CLONEDATABASE で作成されたユーザー データベースをスキーマと統計のみで複製したものです。 詳細については、[Microsoft サポート技術情報](https://support.microsoft.com/help/3177838)をご覧ください。|**適用対象**:[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 以降。<br /><br /> 1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**| 
|IsCloseCursorsOnCommitEnabled|トランザクションがコミットされると、開いているすべてのカーソルが閉じられます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsFulltextEnabled|データベースではフルテキストおよびセマンティック インデックス作成を有効にします。|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> <br /><br /> 1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 入力は無効です<br /><br /> 基本データ型: **int**<br /><br /> **注:** このプロパティの値は無効になりました。 ユーザー データベースでは、常にフルテキスト検索が有効になっています。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の将来のリリースでこのプロパティはなくなります。 新しい開発作業では、このプロパティを使用しないでください。このプロパティを現在使用しているアプリケーションについては、できるだけ早く修正してください。|  
|IsInStandBy|データベースは読み取り専用のオンライン状態で、復元ログが許可されています。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsLocalCursorsDefault|カーソル宣言の既定値は LOCAL です。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsMemoryOptimizedElevateToSnapshotEnabled|セッション設定 TRANSACTION ISOLATION LEVEL が READ COMMITTED、READ UNCOMMITTED、あるいはそれより下位の分離レベルに設定されているとき、SNAPSHOT 分離を使用してメモリ最適化テーブルにアクセスします。|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> <br /><br /> 1:TRUE<br /><br /> 0:FALSE<br /><br /> 基本データ型: **int**|  
|IsMergePublished|レプリケーションがインストールされている場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、マージ レプリケーションのためにデータベース テーブルをパブリッシュできます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsNullConcat|NULL を連結したオペランドは、NULL になります。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsNumericRoundAbortEnabled|式の有効桁数が失われた場合にエラーが生成されます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsParameterizationForced|データベース SET オプション PARAMETERIZATION が FORCED に設定されています。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力|  
|IsQuotedIdentifiersEnabled|識別子に二重引用符を使用できます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsPublished|レプリケーションがインストールされている場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、データベースのテーブルをスナップショット レプリケーションまたはトランザクション レプリケーション用にパブリッシュできます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsRecursiveTriggersEnabled|トリガーの再帰的な発生が許可されています。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsSubscribed|データベースはパブリケーションにサブスクライブされます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsSyncWithBackup|データベースは、パブリッシュされたデータベースかディストリビューション データベースです。また、トランザクション レプリケーションを中断せずに復元できます。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**|  
|IsTornPageDetectionEnabled|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]は、電源障害やその他のシステムの停止によって発生した不完全な I/O 操作を検出します。|1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**| 
|IsVerifiedClone|データベースは、DBCC CLONEDATABASE の WITH VERIFY_CLONEDB オプションで作成されたユーザー データベースをスキーマと統計のみで複製したものです。 詳細については、[Microsoft サポート技術情報](https://support.microsoft.com/help/3177838)をご覧ください。|**適用対象**:[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP2 で始まる。<br /><br /> <br /><br /> 1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **int**| 
|IsXTPSupported|インメモリ OLTP、つまり、メモリ最適化テーブルとネイティブ コンパイル モジュールをデータベースで作成し、使用できるかどうかを示します。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に固有:<br /><br /> IsXTPSupported は、インメモリ OLTP オブジェクトを作成するために必要な MEMORY_OPTIMIZED_DATA ファイル グループの存在に依存しません。|**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降)、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。<br /><br /> 1:TRUE<br /><br /> 0:FALSE<br /><br /> NULL: 無効な入力、エラー、または該当なし<br /><br /> 基本データ型: **int**|  
|LastGoodCheckDbTime|指定されたデータベース上で実行され、最後に成功した DBCC CHECKDB の日時。<sup>1</sup> DBCC CHECKDB がデータベース上で実行されていない場合は、1900-01-01 00:00:00.000 が返されます。|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] (SP2 以降)。</br>CU9 から始まる [!INCLUDE[sssql17](../../includes/sssql17-md.md)]。</br>[!INCLUDE[sssql19](../../includes/sssql19-md.md)] 以降。</br>Azure SQL Database。<br/><br/>datetime 値<br /><br /> NULL: 無効な入力<br /><br /> 基本データ型: **datetime**| 
|LCID|照合順序の Windows ロケール識別子 (LCID)。|LCID 値 (10 進数形式)。<br /><br /> 基本データ型: **int**|  
|MaxSizeInBytes|最大データベース サイズ (バイト単位)。|**適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)]。<br /><br />[Azure SQL Database と Azure Synapse Analytics](/azure/sql-database/sql-database-single-database-scale#dtu-based-purchasing-model) – 追加のストレージを購入しない限り、値は SLO に基づきます。<br /><br />[vCore](/azure/sql-database/sql-database-single-database-scale#vcore-based-purchasing-model) – 値は最大サイズまで 1GB 単位で増分されます。<br /><br />NULL: データベースは開始していません<br /><br /> 基本データ型: **bigint** 型|  
|Recovery|データベース復旧モデル|FULL: 完全復旧モデル<br /><br /> BULK_LOGGED: 一括ログ モデル<br /><br /> SIMPLE: 単純復旧モデル<br /><br /> 基本データ型: **nvarchar(128)**|  
|ServiceObjective|[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] または [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] のデータベースのパフォーマンス レベルについて説明します。|次のいずれか:<br /><br /> Null: データベースが開始されていません<br /><br /> Shared (Web/Business エディション向け)<br /><br /> Basic<br /><br /> S0<br /><br /> S1<br /><br /> S2<br /><br /> S3<br /><br /> P1<br /><br /> P2<br /><br /> P3<br /><br /> ElasticPool<br /><br /> System (マスター DB 向け)<br /><br /> 基本データ型: **nvarchar(32)**|  
|ServiceObjectiveId|[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] のサービス目標の ID です。|サービス目標を識別する **uniqueidentifier**|  
|SQLSortOrder|以前のバージョンの SQL Server でサポートされている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 並べ替え順 ID です。|0:データベースが Windows 照合順序を使用します<br /><br /> >0: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 並べ替え順 ID<br /><br /> NULL: 無効な入力、またはデータベースが開始していません<br /><br /> 基本データ型: **tinyint**|  
|Status|データベースの状態です。|ONLINE: データベースをクエリに使用できます。<br /><br /> **注:** この関数は、データベースが ONLINE で、まだ復旧されていない間は、オンラインの状態を返すことがあります。 ONLINE のデータベースが接続を受け入れることができるかどうかを特定するには、**DATABASEPROPERTYEX** の Collation プロパティをクエリします。 ONLINE のデータベースは、データベースの照合順序から NULL 以外の値が返されたときに接続を受け入れることができます。 AlwaysOn データベースの場合、`sys.dm_hadr_database_replica_states` の database_state または database_state_desc 列にクエリを実行します。<br /><br /> OFFLINE: データベースが明示的にオフラインになりました。<br /><br /> RESTORING: データベース復旧が開始しています。<br /><br /> RECOVERING: データベース復旧が開始したところで、データベースはまだクエリに対応していません。<br /><br /> SUSPECT: データベースは復旧されませんでした。<br /><br /> EMERGENCY: データベースは読み取り専用の緊急モードです。 sysadmin メンバーのみにアクセスが制限されます。<br /><br /> 基本データ型: **nvarchar(128)**|  
|Updateability|データを変更できるかどうかを示します。|READ_ONLY: データベースでは、データを読み取れますが、修正できません。<br /><br /> READ_WRITE: データベースでは、データを読み取れ、修正できます。<br /><br /> 基本データ型: **nvarchar(128)**|  
|UserAccess|データベースにアクセスできるユーザーを示します。|SINGLE_USER: db_owner、dbcreator、sysadmin ユーザーが一度に 1 人だけとなります。<br /><br /> RESTRICTED_USER: db_owner、dbcreator、または sysadmin ロールのメンバーのみ。<br /><br /> MULTI_USER: すべてのユーザー<br /><br /> 基本データ型: **nvarchar(128)**|  
|Version|このデータベースが作成された [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コードの内部バージョン番号です。 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|バージョン番号: データベースが開いています。<br /><br /> NULL: データベースが開始していません。<br /><br /> 基本データ型: **int**| 

<br/>   

> [!NOTE]  
> <sup>1</sup> 可用性グループの一部であるデータベースの場合、コマンドを実行するレプリカに関係なく、`LastGoodCheckDbTime` は、プライマリ レプリカ上で実行され、最後に成功した DBCC CHECKDB の日時を返します。 

## <a name="return-types"></a>戻り値の型
**sql_variant**
  
## <a name="exceptions"></a>例外  
エラーが発生した場合、または呼び出し元にオブジェクトの表示権限がない場合は、NULL が返されます。
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、そのユーザーが所有している、または権限を与えられている、セキュリティ保護可能なアイテムのメタデータのみを表示できます。 つまり、オブジェクトに対する権限がユーザーに与えられていない場合、`OBJECT_ID` のような、メタデータを生成する組み込み関数によって NULL が返されることがあります。 詳細については、「[メタデータ表示の構成](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。
  
## <a name="remarks"></a>解説  
`DATABASEPROPERTYEX` は、プロパティ設定を一度に 1 つだけ返します。 表示するには複数のプロパティの設定を使用して、 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) カタログ ビューです。
  
## <a name="examples"></a>例  
  
### <a name="a-retrieving-the-status-of-the-auto_shrink-database-option"></a>A. AUTO_SHRINK データベース オプションの設定値を取得する  
この例では、`AdventureWorks` データベースの AUTO_SHRINK データベース オプションのステータスを返します。
  
```sql
SELECT DATABASEPROPERTYEX('AdventureWorks2014', 'IsAutoShrink');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]この結果セットは、AUTO_SHRINK がオフであることを示しています。
  
```
------------------  
0  
```  
  
### <a name="b-retrieving-the-default-collation-for-a-database"></a>B. データベースの既定の照合順序を取得する  
この例は、`AdventureWorks` データベースのいくつかの属性を返します。
  
```sql
SELECT   
    DATABASEPROPERTYEX('AdventureWorks2014', 'Collation') AS Collation,  
    DATABASEPROPERTYEX('AdventureWorks2014', 'Edition') AS Edition,  
    DATABASEPROPERTYEX('AdventureWorks2014', 'ServiceObjective') AS ServiceObjective,  
    DATABASEPROPERTYEX('AdventureWorks2014', 'MaxSizeInBytes') AS MaxSizeInBytes  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Collation                     Edition        ServiceObjective  MaxSizeInBytes  
----------------------------  -------------  ----------------  --------------  
SQL_Latin1_General_CP1_CI_AS  DataWarehouse  DW1000            5368709120  
```  
  
## <a name="see-also"></a>関連項目
[ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)  
[データベースの状態](../../relational-databases/databases/database-states.md)  
[sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)  
[sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)  
[SERVERPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/serverproperty-transact-sql.md)
  
  
