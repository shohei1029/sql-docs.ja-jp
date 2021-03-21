---
description: OBJECTPROPERTYEX (Transact-SQL)
title: OBJECTPROPERTYEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- OBJECTPROPERTYEX
- OBJECTPROPERTYEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- displaying schema-scoped object information
- viewing schema-scoped object information
- OBJECTPROPERTYEX function
- schema-scoped objects [SQL Server]
- objects [SQL Server], schema-scoped
ms.assetid: be36b3e3-3309-4332-bfb5-c7e9cf8dc8bd
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e0802ac92d900b3ef4ddacaf714e377ce95a85b8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754452"
---
# <a name="objectpropertyex-transact-sql"></a>OBJECTPROPERTYEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベース内のスキーマ スコープ オブジェクトに関する情報を返します。 これらのオブジェクトの一覧については、「[sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)」をご覧ください。 OBJECTPROPERTYEX は、データ定義言語 (DDL) トリガーやイベント通知などの非スキーマ スコープ オブジェクトには使用できません。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
OBJECTPROPERTYEX ( id , property )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *id*  
 現在のデータベース内のオブジェクトの ID を表す式を指定します。 *id* のデータ型は **int** で、現在のデータベース コンテキストでのスキーマ スコープ オブジェクトであることが前提となっています。  
  
 *property*  
 ID で指定したオブジェクトについて、返される情報を含む式を指定します。戻り値の型は **sql_variant** です。 次の表に、各プロパティ値に対する基本のデータ型を示します。  
  
> [!NOTE]  
>  *property* が有効なプロパティ名でない場合、*id* が有効なオブジェクト ID でない場合、*id* が指定した *property* でサポートされていないオブジェクトの種類であった場合、または呼び出し側にオブジェクトのメタデータを表示する権限がない場合は、特に指定のない限り、NULL が返されます。  
  
|プロパティ名|オブジェクトの種類|説明と戻り値|  
|-------------------|-----------------|-------------------------------------|  
|BaseType|任意のスキーマ スコープ オブジェクト|オブジェクトの基本の種類。 指定したオブジェクトが SYNONYM の場合、基になるオブジェクトの基本の種類が返されます。<br /><br /> NULL 以外 = オブジェクトの種類<br /><br /> 基本データ型: **char(2)**|  
|CnstIsClustKey|制約|クラスター化インデックスを指定した PRIMARY KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|CnstIsColumn|制約|単一列に対する CHECK、DEFAULT、または FOREIGN KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|CnstIsDeleteCascade|制約|ON DELETE CASCADE オプションを指定した FOREIGN KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|CnstIsDisabled|制約|制約の無効化。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|CnstIsNonclustKey|制約|非クラスター化インデックスを指定した PRIMARY KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|CnstIsNotRepl|制約|制約を NOT FOR REPLICATION キーワードを使って定義。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|CnstIsNotTrusted|制約|既存の行を確認せずに制約が有効化されていました。 すべての行に制約が保持されない可能性があります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|CnstIsUpdateCascade|制約|ON UPDATE CASCADE オプションを指定した FOREIGN KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsAfterTrigger|トリガー|AFTER トリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsAnsiNullsOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー|作成時における ANSI_NULLS の設定。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsDeleteTrigger|トリガー|DELETE トリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsFirstDeleteTrigger|トリガー|テーブルに対して DELETE を実行するときに最初に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsFirstInsertTrigger|トリガー|テーブルに対して INSERT を実行するときに最初に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsFirstUpdateTrigger|トリガー|テーブルに対して UPDATE を実行するときに最初に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsInsertTrigger|トリガー|INSERT トリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsInsteadOfTrigger|トリガー|INSTEAD OF トリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsLastDeleteTrigger|トリガー|テーブルに対して DELETE を実行するときに最後に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsLastInsertTrigger|トリガー|テーブルに対して INSERT を実行するときに最後に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsLastUpdateTrigger|トリガー|テーブルに対して UPDATE を実行するときに最後に起動されるトリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsQuotedIdentOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー|作成時における QUOTED_IDENTIFIER の設定。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsStartup|手順|スタートアップ プロシージャ。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsTriggerDisabled|トリガー|トリガーの無効化。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsTriggerNotForRepl|トリガー|NOT FOR REPLICATION として定義されているトリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsUpdateTrigger|トリガー|UPDATE トリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|ExecIsWithNativeCompilation|[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> プロシージャはネイティブでコンパイルされます。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|HasAfterTrigger|テーブル、ビュー|テーブルまたはビューに AFTER トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|HasDeleteTrigger|テーブル、ビュー|テーブルまたはビューに DELETE トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|HasInsertTrigger|テーブル、ビュー|テーブルまたはビューに INSERT トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|HasInsteadOfTrigger|テーブル、ビュー|テーブルまたはビューに INSTEAD OF トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|HasUpdateTrigger|テーブル、ビュー|テーブルまたはビューに UPDATE トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsAnsiNullsOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、テーブル、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー|テーブルに対する ANSI NULLS オプション設定がオンになるように指定します。これにより、NULL 値に対するすべての比較が UNKNOWN として評価されます。 この設定は、テーブルが存在する限り、計算列や制約をはじめとするテーブル定義内のすべての式に適用されます。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsCheckCnst|任意のスキーマ スコープ オブジェクト|CHECK 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsConstraint|任意のスキーマ スコープ オブジェクト|制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsDefault|任意のスキーマ スコープ オブジェクト|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> 既定のバインド。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsDefaultCnst|任意のスキーマ スコープ オブジェクト|DEFAULT 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsDeterministic|スカラーおよびテーブル値関数、ビュー|関数またはビューの決定性を示すプロパティ。<br /><br /> 1 = 決定的<br /><br /> 0 = 非決定的<br /><br /> 基本データ型: **int**|  
|IsEncrypted|[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、テーブル、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー|モジュール ステートメントの元のテキストが、暗号化した形式に変換されたことを示します。 暗号化した形式の出力は、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 内のどのカタログ ビューでも直接見ることはできません。 システム テーブルまたはデータベース ファイルへのアクセス権を持たないユーザーは、暗号化した形式のテキストを取得できません。 ただし、[DAC ポート](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)経由でシステム テーブルにアクセスする権限、または直接データベース ファイルにアクセスする権限を持っているユーザーは、このテキストを使用できます。 また、サーバー プロセスにデバッガーをアタッチできるユーザーは、実行時、元のプロシージャをメモリから取得できます。<br /><br /> 1 = 暗号化<br /><br /> 0 = 暗号化なし<br /><br /> 基本データ型: **int**|  
|IsExecuted|任意のスキーマ スコープ オブジェクト|オブジェクトは実行可能 (ビュー、プロシージャ、関数、またはトリガー)。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsExtendedProc|任意のスキーマ スコープ オブジェクト|拡張プロシージャ。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsForeignKey|任意のスキーマ スコープ オブジェクト|FOREIGN KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsIndexed|テーブル、ビュー|インデックス付きのテーブルまたはビュー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsIndexable|テーブル、ビュー|インデックスを作成できるテーブルまたはビュー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsInlineFunction|機能|インライン関数。<br /><br /> 1 = インライン関数<br /><br /> 0 = インライン関数ではない<br /><br /> 基本データ型: **int**|  
|IsMSShipped|任意のスキーマ スコープ オブジェクト|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール中に作成されたオブジェクト。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsPrecise|計算列、関数、ユーザー定義型、ビュー|浮動小数点演算など、不正確な計算を含むオブジェクトかどうかを示します。<br /><br /> 1 = 正確<br /><br /> 0 = 不正確<br /><br /> 基本データ型: **int**|  
|IsPrimaryKey|任意のスキーマ スコープ オブジェクト|PRIMARY KEY 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsProcedure|任意のスキーマ スコープ オブジェクト|プロシージャ。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsQuotedIdentOn|CHECK 制約、DEFAULT 定義、[!INCLUDE[tsql](../../includes/tsql-md.md)] 関数、[!INCLUDE[tsql](../../includes/tsql-md.md)] プロシージャ、テーブル、[!INCLUDE[tsql](../../includes/tsql-md.md)] トリガー、ビュー|オブジェクトの引用符で囲まれた識別子の設定をオンに指定します。オブジェクト定義に含まれるすべての式の識別子は、二重引用符によって区切られます。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsQueue|任意のスキーマ スコープ オブジェクト|Service Broker キュー<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsReplProc|任意のスキーマ スコープ オブジェクト|レプリケーション プロシージャ。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsRule|任意のスキーマ スコープ オブジェクト|ルールのバインド。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsScalarFunction|機能|スカラー値関数。<br /><br /> 1 = スカラー値関数<br /><br /> 0 = スカラー値関数ではない<br /><br /> 基本データ型: **int**|  
|IsSchemaBound|関数、プロシージャ、ビュー|SCHEMABINDING を使用して作成されたスキーマ バインド関数またはビュー。<br /><br /> 1 = スキーマ バインド<br /><br /> 0 = 非スキーマ バインド<br /><br /> 基本データ型: **int**|  
|IsSystemTable|テーブル|システム テーブル。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsSystemVerified|計算列、関数、ユーザー定義型、ビュー|オブジェクトの精度と決定性のプロパティを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で確認できます。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsTable|テーブル|テーブル。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsTableFunction|機能|テーブル値関数。<br /><br /> 1 = テーブル値関数<br /><br /> 0 = テーブル値関数ではない<br /><br /> 基本データ型: **int**|  
|IsTrigger|任意のスキーマ スコープ オブジェクト|トリガー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsUniqueCnst|任意のスキーマ スコープ オブジェクト|UNIQUE 制約。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsUserTable|テーブル|ユーザー定義テーブル。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|IsView|View|ビュー。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|OwnerId|任意のスキーマ スコープ オブジェクト|オブジェクトの所有者。<br /><br /> **注:** スキーマ所有者はオブジェクト所有者である必要はありません。 たとえば、子オブジェクト (*parent_object_id* が NULL でないオブジェクト) では、常に親オブジェクトと同じ所有者 ID が返されます。<br /><br /> NULL 以外 = オブジェクト所有者のデータベース ユーザー ID<br /><br /> NULL = サポートされていないオブジェクトの種類、またはオブジェクト ID が有効でない<br /><br /> 基本データ型: **int**|  
|SchemaId|任意のスキーマ スコープ オブジェクト|オブジェクトに関連付けられているスキーマの ID。<br /><br /> NULL 以外 = オブジェクトのスキーマ ID。<br /><br /> 基本データ型: **int**|  
|SystemDataAccess|関数、ビュー|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のローカル インスタンス内にある、システム データ、システム カタログ、または仮想システム テーブルにオブジェクトがアクセスします。<br /><br /> 0 = なし<br /><br /> 1 = 読み取り<br /><br /> 基本データ型: **int**|  
|TableDeleteTrigger|テーブル|テーブルに DELETE トリガーがあります。<br /><br /> >1 = 指定された種類の最初のトリガーの ID。<br /><br /> 基本データ型: **int**|  
|TableDeleteTriggerCount|テーブル|テーブルに、指定された数の DELETE トリガーがあります。<br /><br /> NULL 以外 = DELETE トリガーの数<br /><br /> 基本データ型: **int**|  
|TableFullTextMergeStatus|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> 現在マージ中のフルテキスト インデックスがテーブルにあるかどうかを示します。<br /><br /> 0 = テーブルにフルテキスト インデックスがないか、フルテキスト インデックスがマージ中ではない<br /><br /> 1 = フルテキスト インデックスがマージ中。|  
|TableFullTextBackgroundUpdateIndexOn|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> テーブルでは、フルテキスト インデックスのバックグラウンド更新 (自動変更の追跡) が有効になっています。<br /><br /> 1 = TRUE<br /><br /> 0 = FALSE<br /><br /> 基本データ型: **int**|  
|TableFulltextCatalogId|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> テーブルのフルテキスト インデックス データが存在する、フルテキスト カタログの ID。<br /><br /> 0 以外 = フルテキスト インデックス テーブル内の行を識別する一意なインデックスに関連付けられた、フルテキスト カタログ ID。<br /><br /> 0 = テーブルにフルテキスト インデックスはない。<br /><br /> 基本データ型: **int**|  
|TableFullTextChangeTrackingOn|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> テーブルでは、フルテキストの変更の追跡が有効になっています。<br /><br /> 1 = TRUE<br /><br /> 0 = FALSE<br /><br /> 基本データ型: **int**|  
|TableFulltextDocsProcessed|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> フルテキスト インデックス作成の開始以降に処理された行の数。 フルテキスト検索用にインデックスが作成されるテーブルでは、1 行のすべての列が、インデックスが作成される 1 つのドキュメントの一部と見なされます。<br /><br /> 0 = アクティブ クロールまたはフルテキスト インデックス作成は完了していない。<br /><br /> > 0 = 次のいずれか (A または B): A) 完全、増分、または手動による変更追跡の作成開始以降、挿入操作や更新操作で処理されたドキュメントの数です。B) バックグラウンド更新インデックス作成の有効化、フルテキスト インデックス スキーマの変更、フルテキスト カタログの再構築、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスの再起動などの変更の追跡以降、挿入操作や更新操作で処理された行数。<br /><br /> NULL = テーブルにフルテキスト インデックスはない。<br /><br /> 基本データ型: **int**<br /><br /> **注** このプロパティは、削除された行を監視またはカウントしません。|  
|TableFulltextFailCount|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> フルテキスト検索でインデックスが作成されなかった行の数。<br /><br /> 0 = 作成完了<br /><br /> > 0 = 次のいずれか (A または B): A) 完全、増分、または手動更新による変更追跡の作成開始以降に、インデックスが作成されなかったドキュメントの数。B) インデックスのバックグラウンド更新による変更追跡の場合、作成の開始または再開以降にインデックスが作成されなかった行数。 これは、スキーマ変更、カタログの再構築、サーバーの再起動などにより発生する場合があります。<br /><br /> NULL = テーブルにフルテキスト インデックスはない<br /><br /> 基本データ型: **int**|  
|TableFulltextItemCount|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> NULL 以外 = フルテキスト インデックスが正常に作成された行の数。<br /><br /> NULL = テーブルにフルテキスト インデックスはない。<br /><br /> 基本データ型: **int**|  
|TableFulltextKeyColumn|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> フルテキスト インデックスとセマンティック インデックスの定義の一部である、一意な単一列インデックスに関連付けられた列の ID。<br /><br /> 0 = テーブルにフルテキスト インデックスはない。<br /><br /> 基本データ型: **int**|  
|TableFulltextPendingChanges|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> 変更の追跡が処理されていないエントリ数。<br /><br /> 0 = 変更の追跡は有効でない<br /><br /> NULL = テーブルにフルテキスト インデックスはない。<br /><br /> 基本データ型: **int**|  
|TableFulltextPopulateStatus|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> 0 = アイドル状態<br /><br /> 1 = 全体を作成中<br /><br /> 2 = 増分作成中<br /><br /> 3 = 追跡した変更を伝達中<br /><br /> 4 = 自動変更の追跡など、インデックスのバックグラウンド更新が進行中<br /><br /> 5 = フルテキスト インデックス作成が絞り込みまたは停止された<br /><br /> 6 = エラーが発生しました。 詳細については、クロール ログを確認します。 詳しくは、「[フルテキスト インデックスの作成](../../relational-databases/search/populate-full-text-indexes.md)」の「**フルテキスト作成 (クロール) で発生したエラーのトラブルシューティング**」セクションをご覧ください。<br /><br /> 基本データ型: **int**|  
|TableFullTextSemanticExtraction|テーブル|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> テーブルでセマンティック インデックス作成が有効になっています。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasActiveFulltextIndex|テーブル|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> テーブルにアクティブなフルテキスト インデックスがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasCheckCnst|テーブル|テーブルに CHECK 制約があります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasClustIndex|テーブル|テーブルにクラスター化インデックスがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasDefaultCnst|テーブル|テーブルに DEFAULT 制約があります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasDeleteTrigger|テーブル|テーブルに DELETE トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasForeignKey|テーブル|テーブルに FOREIGN KEY 制約があります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasForeignRef|テーブル|テーブルは FOREIGN KEY 制約により参照されています。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasIdentity|テーブル|テーブルに ID 列があります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasIndex|テーブル|テーブルに任意の種類のインデックスがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasInsertTrigger|テーブル|オブジェクトに INSERT トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasNonclustIndex|テーブル|テーブルに非クラスター化インデックスがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasPrimaryKey|テーブル|テーブルに主キーがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasRowGuidCol|テーブル|テーブルには、**uniqueidentifier** 列用の ROWGUIDCOL があります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasTextImage|テーブル|テーブルには、**text**、**ntext**、または **image** 列があります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasTimestamp|テーブル|テーブルには、**timestamp** 列があります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasUniqueCnst|テーブル|テーブルに UNIQUE 制約があります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasUpdateTrigger|テーブル|オブジェクトに UPDATE トリガーがあります。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableHasVarDecimalStorageFormat|テーブル|テーブルで **vardecimal** ストレージ形式が有効になっています。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableInsertTrigger|テーブル|テーブルに INSERT トリガーがあります。<br /><br /> >1 = 指定された種類の最初のトリガーの ID。<br /><br /> 基本データ型: **int**|  
|TableInsertTriggerCount|テーブル|テーブルに、指定された数の INSERT トリガーがあります。<br /><br /> >0 = INSERT トリガーの数。<br /><br /> 基本データ型: **int**|  
|TableIsFake|テーブル|テーブルは実在せず、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]によって、要求時に実際の領域が内部で確保されます。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableIsLockedOnBulkLoad|テーブル|**bcp** または BULK INSERT ジョブによってテーブルがロックされています。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**|  
|TableIsMemoryOptimized|テーブル|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> テーブルはメモリ最適化されています<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基本データ型: **int**<br /><br /> 詳細については、「[インメモリ OLTP &#40;インメモリ最適化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)」を参照してください。|  
|TableIsPinned|テーブル|テーブルは固定され、データ キャッシュに確保されています。<br /><br /> 0 = False<br /><br /> この機能は [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降のバージョンではサポートされていません。|  
|TableTextInRowLimit|テーブル|テーブルに text in row オプション セットがあります。<br /><br /> > 0 = text in row に許可されている最大バイト数。<br /><br /> 0 = text in row オプションは設定されていません。<br /><br /> 基本データ型: **int**|  
|TableUpdateTrigger|テーブル|テーブルに UPDATE トリガーがあります。<br /><br /> > 1 = 指定された種類の最初のトリガーの ID。<br /><br /> 基本データ型: **int**|  
|TableUpdateTriggerCount|テーブル|テーブルに、指定された数の UPDATE トリガーがあります。<br /><br /> > 0 = UPDATE トリガーの数。<br /><br /> 基本データ型: **int**|  
|UserDataAccess|関数、ビュー|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のローカル インスタンス内にある、ユーザー データやユーザー テーブルにオブジェクトがアクセスします。<br /><br /> 1 = 読み取り<br /><br /> 0 = なし<br /><br /> 基本データ型: **int**|  
|TableHasColumnSet|テーブル|テーブルに列セットがあります。<br /><br /> 0 = False<br /><br /> 1 = True<br /><br /> 詳細については、「 [列セットの使用](../../relational-databases/tables/use-column-sets.md)」を参照してください。|  
|カーディナリティ|テーブル (システムまたはユーザー定義)、ビュー、またはインデックス|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> 指定されたオブジェクト内の行数。|  
|TableTemporalType|テーブル|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降。<br /><br /> テーブルの種類を指定します。<br /><br /> 0 = 非テンポラル テーブル<br /><br /> 1 = システムのバージョン情報のテーブルの履歴テーブル<br /><br /> 2 = システムのバージョン情報のテンポラル テーブル|  
  
## <a name="return-types"></a>戻り値の型  
 **sql_variant**  
  
## <a name="exceptions"></a>例外  
 エラーが発生した場合、または呼び出し元にオブジェクトの表示権限がない場合は、NULL が返されます。  
  
 ユーザーが所有しているか、または権限を与えられている、セキュリティ保護可能なリソースのメタデータのみを表示できます。 つまり、オブジェクトに対する権限がユーザーに与えられていない場合、メタデータを生成する組み込み関数 (OBJECTPROPERTYEX など) が NULL を返す可能性があります。 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="remarks"></a>解説  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)]  を前提としています *object_id* が現在のデータベース コンテキストでします。 別のデータベースの *object_id* を参照するクエリは、NULL または正しくない値を返します。 たとえば、次のクエリでは、現在のデータベース コンテキストは master データベースです。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]は、クエリ内で指定されたデータベースではなく、このデータベースの指定された *object_id* のプロパティ値を返します。 ビュー `vEmployee` は master データベース内にないため、このクエリでは正しくない結果が返されます。  
  
```sql  
USE master;  
GO  
SELECT OBJECTPROPERTYEX(OBJECT_ID(N'AdventureWorks2012.HumanResources.vEmployee'), 'IsView');  
GO  
```  
  
 OBJECTPROPERTYEX(*view_id*, 'IsIndexable') は、多くのコンピューター リソースを使用する可能性があります。これは、IsIndexable プロパティを評価するために、ビュー定義、正規化、および部分最適化の解析が必要なためです。 IsIndexable プロパティではインデックスを作成できるテーブルまたはビューを指定しますが、特定のインデックス キーの要件が満たされない場合、インデックスの実際の作成は失敗します。 詳細については、「[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)」を参照してください。  
  
 OBJECTPROPERTYEX(*table_id*, 'TableHasActiveFulltextIndex') は、テーブルの少なくとも 1 つの列にインデックスが作成されている場合は、1 (TRUE) を返します。 インデックス作成で先頭の列が追加されるとすぐ、フルテキスト インデックス作成が自動的にアクティブになります。  
  
 メタデータの表示に関する制限は、結果セットに適用されます。 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-finding-the-base-type-of-an-object"></a>A.  オブジェクトの基本の種類を検索する  
 次の例では、[!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] データベース内の `MyEmployeeTable` テーブルに対して SYNONYM `Employee` を作成した後、SYNONYM の基本の種類を返します。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE SYNONYM MyEmployeeTable FOR HumanResources.Employee;  
GO  
SELECT OBJECTPROPERTYEX ( object_id(N'MyEmployeeTable'), N'BaseType')AS [Base Type];  
GO  
```  
  
 結果セットでは、基になるオブジェクトである `Employee` テーブルの基本の種類が、ユーザー テーブルであることが示されます。  
  
 ```
Base Type 
--------  
U
```  
  
### <a name="b-returning-a-property-value"></a>B. プロパティ値を返す  
 次の例では、指定されたテーブルの UPDATE トリガー数を返します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT OBJECTPROPERTYEX(OBJECT_ID(N'HumanResources.Employee'), N'TABLEUPDATETRIGGERCOUNT');  
GO  
```  
  
### <a name="c-finding-tables-that-have-a-foreign-key-constraint"></a>C. FOREIGN KEY 制約を含むテーブルを探す  
 次の例では、`TableHasForeignKey` プロパティを使用し、FOREIGN KEY 制約を含むすべてのテーブルを返します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT name, object_id, schema_id, type_desc  
FROM sys.objects   
WHERE OBJECTPROPERTYEX(object_id, N'TableHasForeignKey') = 1  
ORDER BY name;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-finding-the-base-type-of-an-object"></a>D: オブジェクトの基本型を検索する  
 次の例では、`dbo.DimReseller` オブジェクトの基本型を返します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT OBJECTPROPERTYEX ( object_id(N'dbo.DimReseller'), N'BaseType')AS BaseType;  
```  
  
 結果セットでは、基になるオブジェクトである `dbo.DimReseller` テーブルの基本の種類が、ユーザー テーブルであることが示されます。  
  
```  
BaseType   
--------   
U   
```  
  
## <a name="see-also"></a>関連項目  
 [CREATE SYNONYM &#40;Transact-SQL&#41;](../../t-sql/statements/create-synonym-transact-sql.md)   
 [メタデータ関数 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
 [OBJECT_DEFINITION &#40;Transact-SQL&#41;](../../t-sql/functions/object-definition-transact-sql.md)   
 [OBJECT_ID &#40;Transact-SQL&#41;](../../t-sql/functions/object-id-transact-sql.md)   
 [OBJECT_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/object-name-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)   
 [TYPEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/typeproperty-transact-sql.md)  
  
  

