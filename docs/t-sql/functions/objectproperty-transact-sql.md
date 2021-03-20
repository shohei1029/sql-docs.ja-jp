---
description: OBJECTPROPERTY (Transact-SQL)
title: OBJECTPROPERTY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- OBJECTPROPERTY
- OBJECTPROPERTY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- displaying schema-scoped object information
- viewing schema-scoped object information
- OBJECTPROPERTY function
- schema-scoped objects [SQL Server]
- objects [SQL Server], schema-scoped
ms.assetid: 27569888-f8b5-4cec-a79f-6ea6d692b4ae
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9d8fe44f54474bdb891383b5a1a9c0fc9e8f404f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755652"
---
# <a name="objectproperty-transact-sql"></a>OBJECTPROPERTY (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベース内のスキーマ スコープ オブジェクトに関する情報を返します。 スキーマ スコープ オブジェクトの一覧については、「[sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)」をご覧ください。 データ定義言語 (DDL) トリガーやイベント通知など、スキーマ スコープ オブジェクト以外のオブジェクトについては、この関数を使用できません。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
OBJECTPROPERTY ( id , property )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *id*  
 現在のデータベース内のオブジェクトの ID を表す式を指定します。 *id* のデータ型は **int** で、現在のデータベース コンテキストでのスキーマ スコープ オブジェクトであることが前提となっています。  
  
 *property*  
 *id* で指定されるオブジェクトに対して返される情報を表す式です。*property* は次のいずれかを指定することができます。  
  
> [!NOTE]  
>  *property* が有効なプロパティ名でない場合、*id* が有効なオブジェクト ID でない場合、*id* が指定した *property* でサポートされていないオブジェクトの種類であった場合、または呼び出し側にオブジェクトのメタデータを表示する権限がない場合は、特に指定のない限り、NULL が返されます。  
  
|プロパティ名|オブジェクトの種類|説明と戻り値|  
|-------------------|-----------------|-------------------------------------|  
|CnstIsClustKey|制約|クラスター化インデックスを指定した PRIMARY KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsColumn|制約|単一列に対する CHECK、DEFAULT、または FOREIGN KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsDeleteCascade|制約|ON DELETE CASCADE オプションを指定した FOREIGN KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsDisabled|制約|制約の無効化。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsNonclustKey|制約|非クラスター化インデックスを持つ PRIMARY KEY 制約または UNIQUE 制約。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsNotRepl|制約|制約を NOT FOR REPLICATION キーワードを使って定義。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsNotTrusted|制約|既存の行を確認せずに制約が有効化されているので、制約がすべての行に対応しているとは限りません。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsUpdateCascade|制約|ON UPDATE CASCADE オプションを指定した FOREIGN KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsAfterTrigger|トリガー|AFTER トリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsAnsiNullsOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー|作成時における ANSI_NULLS の設定。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsDeleteTrigger|トリガー|DELETE トリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsFirstDeleteTrigger|トリガー|テーブルに対して DELETE を実行するときに最初に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsFirstInsertTrigger|トリガー|テーブルに対して INSERT を実行するときに最初に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsFirstUpdateTrigger|トリガー|テーブルに対して UPDATE を実行するときに最初に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsInsertTrigger|トリガー|INSERT トリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsInsteadOfTrigger|トリガー|INSTEAD OF トリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsLastDeleteTrigger|トリガー|テーブルに対して DELETE を実行するときに最後に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsLastInsertTrigger|トリガー|テーブルに対して INSERT を実行するときに最後に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsLastUpdateTrigger|トリガー|テーブルに対して UPDATE を実行するときに最後に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsQuotedIdentOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー|作成時における QUOTED_IDENTIFIER の設定。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsStartup|手順|スタートアップ プロシージャ。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsTriggerDisabled|トリガー|トリガーの無効化。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsTriggerNotForRepl|トリガー|NOT FOR REPLICATION として定義されているトリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsUpdateTrigger|トリガー|UPDATE トリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsWithNativeCompilation|[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> プロシージャはネイティブでコンパイルされます。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|HasAfterTrigger|テーブル、ビュー|テーブルまたはビューに AFTER トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|HasDeleteTrigger|テーブル、ビュー|テーブルまたはビューに DELETE トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|HasInsertTrigger|テーブル、ビュー|テーブルまたはビューに INSERT トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|HasInsteadOfTrigger|テーブル、ビュー|テーブルまたはビューに INSTEAD OF トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|HasUpdateTrigger|テーブル、ビュー|テーブルまたはビューに UPDATE トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsAnsiNullsOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、テーブル、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー|テーブルの ANSI NULLS オプションが ON であることをことを指定します。 つまり、NULL 値との比較結果はすべて UNKNOWN になります。 この設定は、テーブルが存在する限り、計算列や制約をはじめとするテーブル定義内のすべての式に適用されます。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsCheckCnst|任意のスキーマ スコープ オブジェクト|CHECK 制約。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsConstraint|任意のスキーマ スコープ オブジェクト|列またはテーブルに対する単一列の CHECK、DEFAULT、または FOREIGN KEY 制約です。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsDefault|任意のスキーマ スコープ オブジェクト|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> 既定のバインド。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsDefaultCnst|任意のスキーマ スコープ オブジェクト|DEFAULT 制約。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsDeterministic|関数、ビュー|関数またはビューの決定性を示すプロパティ。<br /><br /> 1 = 決定的<br /><br /> 0 = 非決定的|  
|IsEncrypted|[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、テーブル、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー|モジュール ステートメントの元のテキストが、暗号化した形式に変換されたことを示します。 暗号化した形式の出力は、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 内のどのカタログ ビューでも直接見ることはできません。 システム テーブルまたはデータベース ファイルへのアクセス権を持たないユーザーは、暗号化した形式のテキストを取得できません。 ただし、[DAC ポート](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)経由でシステム テーブルにアクセスする権限、または直接データベース ファイルにアクセスする権限を持っているユーザーは、このテキストを使用できます。 また、サーバー プロセスにデバッガーをアタッチできるユーザーは、実行時、元のプロシージャをメモリから取得できます。<br /><br /> 1 = 暗号化<br /><br /> 0 = 暗号化なし<br /><br /> 基本データ型: **int**|  
|IsExecuted|任意のスキーマ スコープ オブジェクト|オブジェクトが実行可能かどうかを示します (ビュー、プロシージャ、関数、またはトリガー)。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsExtendedProc|任意のスキーマ スコープ オブジェクト|拡張プロシージャ。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsForeignKey|任意のスキーマ スコープ オブジェクト|FOREIGN KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsIndexed|テーブル、ビュー|インデックス付きのテーブルまたはビュー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsIndexable|テーブル、ビュー|インデックスを作成できるテーブルまたはビュー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsInlineFunction|機能|インライン関数。<br /><br /> 1 = インライン関数<br /><br /> 0 = インライン関数ではない|  
|IsMSShipped|任意のスキーマ スコープ オブジェクト|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール時に作成されたオブジェクト。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsPrimaryKey|任意のスキーマ スコープ オブジェクト|PRIMARY KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> NULL = 関数ではありません。またはオブジェクト ID が無効です。|  
|IsProcedure|任意のスキーマ スコープ オブジェクト|プロシージャ。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsQuotedIdentOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、テーブル、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー、CHECK 制約、DEFAULT 定義|オブジェクトの引用符で囲まれた識別子の設定が ON であることを指定します。 オブジェクト定義に含まれるすべての式の中で、識別子は二重引用符で区切られます。<br /><br /> 1 = ON<br /><br /> 0 = OFF|  
|IsQueue|任意のスキーマ スコープ オブジェクト|Service Broker キュー<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsReplProc|任意のスキーマ スコープ オブジェクト|レプリケーション プロシージャ。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsRule|任意のスキーマ スコープ オブジェクト|ルールのバインド。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsScalarFunction|機能|スカラー値関数。<br /><br /> 1 = スカラー値関数<br /><br /> 0 = スカラー値関数ではない|  
|IsSchemaBound|関数、ビュー|SCHEMABINDING を使用して作成されたスキーマ バインド関数またはビュー。<br /><br /> 1 = スキーマ バインド<br /><br /> 0 = 非スキーマ バインド|  
|IsSystemTable|テーブル|システム テーブル。<br /><br /> 1 = True<br /><br /> 0 = False| 
|IsSystemVerified|Object|SQL Server では、オブジェクトの決定性のプロパティと有効桁数のプロパティを確認できます。<br /><br /> 1 = True<br /><br /> 0 = False| 
|IsTable|テーブル|テーブル。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsTableFunction|機能|テーブル値関数。<br /><br /> 1 = テーブル値関数<br /><br /> 0 = テーブル値関数ではない|  
|IsTrigger|任意のスキーマ スコープ オブジェクト|トリガー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsUniqueCnst|任意のスキーマ スコープ オブジェクト|UNIQUE 制約。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsUserTable|テーブル|ユーザー定義テーブル。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsView|View|ビュー。<br /><br /> 1 = True<br /><br /> 0 = False|  
|OwnerId|任意のスキーマ スコープ オブジェクト|オブジェクトの所有者。<br /><br /> **注:** スキーマ所有者はオブジェクト所有者である必要はありません。 たとえば、子オブジェクト (*parent_object_id* が NULL でないオブジェクト) では、常に親オブジェクトと同じ所有者 ID が返されます。<br /><br /> NULL 以外 = オブジェクト所有者のデータベース ユーザー ID です。|  
|SchemaId|任意のスキーマ スコープ オブジェクト| オブジェクトが所属するスキーマのスキーマ ID。| 
|TableDeleteTrigger|テーブル|テーブルに DELETE トリガーがあります。<br /><br /> >1 = 指定された種類の最初のトリガーの ID。|  
|TableDeleteTriggerCount|テーブル|テーブルには指定された数の DELETE トリガーがあります。<br /><br /> >0 = DELETE トリガーの数。|  
|TableFullTextMergeStatus|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> 現在マージ中のフルテキスト インデックスがテーブルにあるかどうかを示します。<br /><br /> 0 = テーブルにフルテキスト インデックスがないか、フルテキスト インデックスがマージ中ではない<br /><br /> 1 = フルテキスト インデックスがマージ中。|  
|TableFullTextBackgroundUpdateIndexOn|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> テーブルではフルテキスト インデックスのバックグラウンド更新 (変更の自動追跡) が有効です。<br /><br /> 1 = TRUE<br /><br /> 0 = FALSE|  
|TableFulltextCatalogId|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> テーブルのフルテキスト インデックス データが存在する、フルテキスト カタログの ID。<br /><br /> 0 以外 = フルテキスト インデックス テーブル内の行を識別する一意なインデックスに関連付けられた、フルテキスト カタログ ID。<br /><br /> 0 = テーブルにフルテキスト インデックスはない。|  
|TableFulltextChangeTrackingOn|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> テーブルでは、フルテキストの変更の追跡が有効になっています。<br /><br /> 1 = TRUE<br /><br /> 0 = FALSE|  
|TableFulltextDocsProcessed|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> フルテキスト インデックス作成の開始以降に処理された行の数。 フルテキスト検索用にインデックスが作成されるテーブルでは、1 行のすべての列が、インデックスが作成される 1 つのドキュメントの一部と見なされます。<br /><br /> 0 = アクティブ クロールまたはフルテキスト インデックス作成は完了していない。<br /><br /> > 0 = 次のいずれか (A または B): A) 完全、増分、または手動による変更追跡の作成開始以降、挿入操作や更新操作で処理されたドキュメントの数です。 B) バックグラウンド更新インデックス作成の有効化、フルテキスト インデックス スキーマの変更、フルテキスト カタログの再構築、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスの再起動などの変更の追跡以降、挿入操作や更新操作で処理された行数。<br /><br /> NULL = テーブルにフルテキスト インデックスはない。<br /><br /> このプロパティによって、削除された行の監視またはカウントは行われません。|  
|TableFulltextFailCount|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> フルテキスト検索によるインデックスが設定されなかった行数。<br /><br /> 0 = 作成完了<br /><br /> > 0 = 次のいずれか (A または B): A) 完全、増分、または手動更新による変更追跡の作成開始以降に、インデックスが作成されなかったドキュメントの数。 B) インデックスのバックグラウンド更新による変更追跡の場合、作成の開始または再開以降にインデックスが作成されなかった行数。 これは、スキーマの変更、カタログの再構築、サーバーの再起動などにより発生する場合があります。<br /><br /> NULL = テーブルにフルテキスト インデックスはない。|  
|TableFulltextItemCount|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> フルテキスト インデックスが正常に設定された行数。|  
|TableFulltextKeyColumn|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> フルテキスト インデックス定義に関係している、一意な単一列インデックスに関連付けられた列の ID。<br /><br /> 0 = テーブルにフルテキスト インデックスはない。|  
|TableFulltextPendingChanges|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> 変更の追跡が処理されていないエントリ数。<br /><br /> 0 = 変更の追跡は有効でない<br /><br /> NULL = テーブルにフルテキスト インデックスはない。|  
|TableFulltextPopulateStatus|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> 0 = アイドル状態<br /><br /> 1 = 全体を作成中<br /><br /> 2 = 増分作成中<br /><br /> 3 = 追跡した変更を伝達中<br /><br /> 4 = 自動変更の追跡など、インデックスのバックグラウンド更新が進行中<br /><br /> 5 = フルテキスト インデックス作成が絞り込みまたは停止された|  
|TableHasActiveFulltextIndex|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> テーブルにアクティブなフルテキスト インデックスがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasCheckCnst|テーブル|テーブルに CHECK 制約があります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasClustIndex|テーブル|テーブルにクラスター化インデックスがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasDefaultCnst|テーブル|テーブルに DEFAULT 制約があります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasDeleteTrigger|テーブル|テーブルに DELETE トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasForeignKey|テーブル|テーブルに FOREIGN KEY 制約があります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasForeignRef|テーブル|テーブルは FOREIGN KEY 制約により参照されています。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasIdentity|テーブル|テーブルに ID 列があります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasIndex|テーブル|テーブルに任意の種類のインデックスがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasInsertTrigger|テーブル|オブジェクトに INSERT トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasNonclustIndex|テーブル|テーブルには非クラスター化インデックスがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasPrimaryKey|テーブル|テーブルに主キーがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasRowGuidCol|テーブル|テーブルには、**uniqueidentifier** 列用の ROWGUIDCOL があります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasTextImage|テーブル|テーブルには、**text**、**ntext**、または **image** 列があります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasTimestamp|テーブル|テーブルには、**timestamp** 列があります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasUniqueCnst|テーブル|テーブルに UNIQUE 制約があります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasUpdateTrigger|テーブル|オブジェクトには UPDATE トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasVarDecimalStorageFormat|テーブル|テーブルで **vardecimal** ストレージ形式が有効になっています。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableInsertTrigger|テーブル|テーブルに INSERT トリガーがあります。<br /><br /> >1 = 指定された種類の最初のトリガーの ID。|  
|TableInsertTriggerCount|テーブル|テーブルには指定された数の INSERT トリガーがあります。<br /><br /> >0 = INSERT トリガーの数。|  
|TableIsFake|テーブル|テーブルは実在せず、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]によって、要求時に実際の領域が内部で確保されます。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableIsLockedOnBulkLoad|テーブル|**bcp** または BULK INSERT ジョブによってテーブルがロックされています。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableIsMemoryOptimized|テーブル|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> テーブルはメモリ最適化されています<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**<br /><br /> 詳細については、「[インメモリ OLTP &#40;インメモリ最適化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)」を参照してください。|  
|TableIsPinned|テーブル|テーブルは固定され、データ キャッシュに確保されています。<br /><br /> 0 = False<br /><br /> この機能は [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降ではサポートされていません。|  
|TableTextInRowLimit|テーブル|text in row に許可されている最大バイト数。<br /><br /> text in row オプションが設定されていない場合は 0 です。|  
|TableUpdateTrigger|テーブル|テーブルに UPDATE トリガーがあります。<br /><br /> > 1 = 指定された種類の最初のトリガーの ID。|  
|TableUpdateTriggerCount|テーブル|テーブルには指定された数の UPDATE トリガーがあります。<br /><br /> > 0 = UPDATE トリガーの数。|  
|TableHasColumnSet|テーブル|テーブルに列セットがあります。<br /><br /> 0 = False<br /><br /> 1 = True<br /><br /> 詳細については、「 [列セットの使用](../../relational-databases/tables/use-column-sets.md)」を参照してください。|  
|TableTemporalType|テーブル|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降。<br /><br /> テーブルの種類を指定します。<br /><br /> 0 = 非テンポラル テーブル<br /><br /> 1 = システムのバージョン情報のテーブルの履歴テーブル<br /><br /> 2 = システムのバージョン情報のテンポラル テーブル|  
  
## <a name="return-types"></a>戻り値の型  
 **int**  
  
## <a name="exceptions"></a>例外  
 エラーが発生した場合、または呼び出し元にオブジェクトの表示権限がない場合は、NULL が返されます。  
  
 ユーザーが所有しているか、または権限を与えられている、セキュリティ保護可能なリソースのメタデータのみを表示できます。 つまり、オブジェクトに対する権限がユーザーに与えられていない場合、メタデータを生成する組み込み関数 (OBJECTPROPERTY など) が NULL を返す可能性があります。 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="remarks"></a>解説  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)]  を前提としています *object_id* が現在のデータベース コンテキストでします。 別のデータベースの *object_id* を参照するクエリは、NULL または正しくない値を返します。 たとえば、次のクエリでは、現在のデータベース コンテキストは master データベースです。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]は、クエリ内で指定されたデータベースではなく、このデータベースの指定された *object_id* のプロパティ値を返します。 ビュー `vEmployee` は master データベース内にないため、このクエリでは正しくない結果が返されます。  
  
```sql  
USE master;  
GO  
SELECT OBJECTPROPERTY(OBJECT_ID(N'AdventureWorks2012.HumanResources.vEmployee'), 'IsView');  
GO  
```  
  
 OBJECTPROPERTY(*view_id*, 'IsIndexable') は、多くのコンピューター リソースを使用する可能性があります。これは、IsIndexable プロパティを評価するために、ビュー定義、正規化、および部分最適化の解析が必要なためです。 IsIndexable プロパティではインデックスを作成できるテーブルまたはビューを指定しますが、特定のインデックス キーの要件が満たされない場合、インデックスの実際の作成は失敗します。 詳細については、「[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)」を参照してください。  
  
 OBJECTPROPERTY(*table_id*, 'TableHasActiveFulltextIndex') は、テーブルの少なくとも 1 つの列にインデックスが作成されている場合は、1 (TRUE) を返します。 インデックス作成で先頭の列が追加されるとすぐ、フルテキスト インデックス作成が自動的にアクティブになります。  
  
 テーブルを作成するときに QUOTED IDENTIFIER オプションが OFF に設定されている場合でも、作成されるテーブルのメタデータでは、このオプションは常に ON として格納されます。 したがって、OBJECTPROPERTY(*table_id*, 'IsQuotedIdentOn') は常に 1 (TRUE) を返します。  
  
## <a name="examples"></a>例  
  
### <a name="a-verifying-that-an-object-is-a-table"></a>A. オブジェクトがテーブルかどうかを確認する  
 次の例では、`UnitMeasure` が [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] データベースのテーブルかどうかをテストします。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF OBJECTPROPERTY (OBJECT_ID(N'Production.UnitMeasure'),'ISTABLE') = 1  
   PRINT 'UnitMeasure is a table.'  
ELSE IF OBJECTPROPERTY (OBJECT_ID(N'Production.UnitMeasure'),'ISTABLE') = 0  
   PRINT 'UnitMeasure is not a table.'  
ELSE IF OBJECTPROPERTY (OBJECT_ID(N'Production.UnitMeasure'),'ISTABLE') IS NULL  
   PRINT 'ERROR: UnitMeasure is not a valid object.';  
GO  
```  
  
### <a name="b-verifying-that-a-scalar-valued-user-defined-function-is-deterministic"></a>B. スカラー値ユーザー定義関数の決定性を確認する  
 次の例では、**money** 値を返すユーザー定義のスカラー値関数である `ufnGetProductDealerPrice` が決定的であるかどうかをテストします。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT OBJECTPROPERTY(OBJECT_ID('dbo.ufnGetProductDealerPrice'), 'IsDeterministic');  
GO  
```  
  
 結果セットは、`ufnGetProductDealerPrice` が決定論的関数でないことを示しています。  
  
 ```
-----  
0
```  
  
### <a name="c-finding-the-tables-that-belong-to-a-specific-schema"></a>C: 特定のスキーマに属するテーブルを見つける  
 次の例は、dbo スキーマのすべてのテーブルを返します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT name, object_id, type_desc  
FROM sys.objects   
WHERE OBJECTPROPERTY(object_id, N'SchemaId') = SCHEMA_ID(N'dbo')  
ORDER BY type_desc, name;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-verifying-that-an-object-is-a-table"></a>D: オブジェクトがテーブルかどうかを確認する  
 次の例では、`dbo.DimReseller` が [!INCLUDE[ssawPDW](../../includes/ssawpdw-md.md)] データベースのテーブルかどうかをテストします。  
  
```sql  
-- Uses AdventureWorks  
  
IF OBJECTPROPERTY (OBJECT_ID(N'dbo.DimReseller'),'ISTABLE') = 1  
   SELECT 'DimReseller is a table.'  
ELSE   
   SELECT 'DimReseller is not a table.';  
GO  
```  
  
## <a name="see-also"></a>関連項目  
 [COLUMNPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/columnproperty-transact-sql.md)   
 [メタデータ関数 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
 [OBJECTPROPERTYEX &#40;Transact-SQL&#41;](../../t-sql/functions/objectpropertyex-transact-sql.md)   
 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)   
 [TYPEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/typeproperty-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)  
  
  

