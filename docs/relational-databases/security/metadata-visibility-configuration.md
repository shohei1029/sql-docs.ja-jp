---
title: メタデータ表示の構成 | Microsoft Docs
description: SQL Server でユーザーが所有する、またはアクセス許可を付与されているセキュリティ保護可能なリソースのメタデータの表示を構成する方法について説明します。
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- subcomponents visibility [SQL Server]
- metadata [SQL Server], visibility
- permissions [SQL Server], metadata access
- viewing metadata
- objects [SQL Server], metadata
- displaying metadata
- database metadata [SQL Server]
- metadata [SQL Server], permissions
ms.assetid: 50d2e015-05ae-4014-a1cd-4de7866ad651
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 65eeb429fbef85f1e23a79a8f06299a64a5179d2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755862"
---
# <a name="metadata-visibility-configuration"></a>メタデータ表示の構成
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  メタデータの表示は、ユーザーが所有するセキュリティ保護可能なリソース、またはそのユーザーが権限を許可されているセキュリティ保護可能なリソースに制限されています。 たとえば、次のクエリを実行すると、ユーザーがテーブル `myTable`に対する SELECT または INSERT 権限を許可されている場合は、1 行が返されます。  
  
```  
SELECT name, object_id  
FROM sys.tables  
WHERE name = N'myTable';  
GO  
```  
  
 ただし、ユーザーが `myTable`での権限を許可されていない場合は、空の結果セットが返されます。  
  
## <a name="scope-and-impact-of-metadata-visibility-configuration"></a>メタデータ表示の構成のスコープと影響  
 メタデータ表示の構成は、次のセキュリティ保護可能なメタデータにのみ適用されます。  

:::row:::
    :::column:::
        カタログ ビュー

        メタデータ公開組み込み関数

        互換性ビュー
    :::column-end:::
    :::column:::
         [!INCLUDE[ssDE](../../includes/ssde-md.md)] **sp_help** ストアド プロシージャ

        情報スキーマ ビュー

        拡張プロパティ
    :::column-end:::
:::row-end:::
  
 メタデータ表示の構成は、次のセキュリティ保護可能なメタデータには適用されません。  

:::row:::
    :::column:::
        ログ配布システム テーブル

        データベース メンテナンス プラン システム テーブル

        レプリケーション システム テーブル
    :::column-end:::
    :::column:::
         [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント システム テーブル

        バックアップ システム テーブル

        レプリケーション ストアド プロシージャと [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントの **sp_help** ストアド プロシージャ
    :::column-end:::
:::row-end:::

 メタデータへのアクセスを制限すると、次のような影響があります。  
  
-   メタデータへの **パブリック** なアクセスを前提とするアプリケーションは機能しません。  
  
-   システム ビューへのクエリを実行しても、行のサブセットまたは空の結果セットしか返されないことがあります。  
  
-   OBJECTPROPERTYEX などのメタデータ生成組み込み関数を実行すると、NULL 値が返されることがあります。  
  
-   [!INCLUDE[ssDE](../../includes/ssde-md.md)] **sp_help** ストアド プロシージャを実行しても、行のサブセットまたは NULL 値しか返されないことがあります。  
  
 ストアド プロシージャやトリガーなどの SQL モジュールは、呼び出し元のセキュリティ コンテキストで実行されるので、メタデータへのアクセスが制限されます。 たとえば次のコードでは、ストアド プロシージャから、呼び出し元に権限のないテーブル `myTable` のメタデータへのアクセスが試行されると、空の結果セットが返されます。 以前のリリースの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]では行が返されます。  
  
```  
CREATE PROCEDURE assumes_caller_can_access_metadata  
BEGIN  
SELECT name, object_id   
FROM sys.objects   
WHERE name = N'myTable';  
END;  
GO  
```  
  
 呼び出し元でのメタデータの表示を可能にするには、呼び出し元にオブジェクト レベル、データベース レベル、サーバー レベルのいずれかの適切なスコープで VIEW DEFINITION 権限を許可できます。 したがって、上記の例では、呼び出し元に `myTable`に対する VIEW DEFINITION 権限が許可されていると、ストアド プロシージャから行が返されます。 詳細については、「[GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)」と「[GRANT &#40;データベースの権限の許可&#41; &#40;Transact-SQL&#41;](../../t-sql/statements/grant-database-permissions-transact-sql.md)」を参照してください。  
  
 また、ストアド プロシージャを所有者の資格情報で実行するように変更することもできます。 プロシージャの所有者とテーブルの所有者が同じ場合は、所有権の継承が適用されます。したがって、プロシージャの所有者のセキュリティ コンテキストにより、 `myTable`のメタデータにアクセスできます。 このシナリオで次のコードを実行すると、メタデータの行が呼び出し元に返されます。  
  
> [!NOTE]  
>  次の例で使用するのは、 [sys.sysobjects](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) 互換性ビューではなく [sys.objects](../../relational-databases/system-compatibility-views/sys-sysobjects-transact-sql.md) カタログ ビューです。  
  
```  
CREATE PROCEDURE does_not_assume_caller_can_access_metadata  
WITH EXECUTE AS OWNER  
AS  
BEGIN  
SELECT name, object_id  
FROM sys.objects   
WHERE name = N'myTable'   
END;  
GO  
```  
  
> [!NOTE]  
>  EXECUTE AS を使用すると、一時的に呼び出し元のセキュリティ コンテキストに切り替えることができます。 詳細については、「[EXECUTE AS &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-transact-sql.md)」を参照してください。  
  
## <a name="benefits-and-limits-of-metadata-visibility-configuration"></a>メタデータ表示構成の利点と制限事項  
 メタデータ表示の構成は、セキュリティ計画全体にとって重要な役割を果たします。 ただし、上級ユーザーや事前に指定されたユーザーが一部のメタデータを強制的に公開できる場合もあります。 メタデータの権限の配置は、数ある堅牢な防御策の 1 つとして行うことをお勧めします。  
  
 理論上は、クエリで述語の評価の順序を操作することで、エラー メッセージ内にメタデータの生成を強制することも可能です。 このような *試行錯誤による攻撃* は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]に固有のものではありません。 関係代数で許可されている結合変換および相互変換に伴う場合もあります。 このリスクは、エラー メッセージで返される情報を制限することで緩和できます。 この方法でメタデータの表示を制限するには、トレース フラグ 3625 を使用してサーバーを起動します。 このトレース フラグは、エラー メッセージで表示される情報の量を制限します。 そのため、この方法を使用することで、メタデータの公開が強制されることを防ぐことができます。 その代わりに、エラー メッセージは簡潔になり、デバッグに使用するのは困難になる可能性があります。 詳細については、「[データベース エンジン サービスのスタートアップ オプション](../../database-engine/configure-windows/database-engine-service-startup-options.md)」と「[トレース フラグ &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)」を参照してください。  
  
 次のメタデータは公開を強制されません。  
  
-   **sys.servers** の **provider_string** 列に格納されている値。 ALTER ANY LINKED SERVER 権限が許可されていないユーザーには、この列に NULL 値が表示されます。  
  
-   ストアド プロシージャやトリガーなどのユーザー定義オブジェクトのソース定義。 ソース コードは、次のいずれかの条件を満たしている場合のみ表示されます。  
  
    -   ユーザーがオブジェクトに対する VIEW DEFINITION 権限を許可されている場合。  
  
    -   ユーザーがオブジェクトに対する VIEW DEFINITION 権限を拒否されておらず、そのオブジェクトに対する CONTROL、ALTER、TAKE OWNERSHIP のいずれかの権限を許可されている場合。 他のすべてのユーザーには NULL 値が表示されます。  
  
-   次のカタログ ビューの定義列。  

    - **sys.all_sql_modules**  
    - **sys.server_sql_modules**  
    - **sys.default_constraints**
    - **sys.numbered_procedures**
    - **sys.sql_modules**
    - **sys.check_constraints**
    - **sys.computed_columns**

-   **syscomments** 互換性ビューの **ctext** 列。  
  
-   **sp_helptext** プロシージャの出力。  
  
-   情報スキーマ ビューの次の列。

    - INFORMATION_SCHEMA.CHECK_CONSTRAINTS.CHECK_CLAUSE
    - INFORMATION_SCHEMA.DOMAINS.DOMAIN_DEFAULT
    - INFORMATION_SCHEMA.ROUTINES.ROUTINE_DEFINITION
    - INFORMATION_SCHEMA.COLUMNS.COLUMN_DEFAULT
    - INFORMATION_SCHEMA.ROUTINE_COLUMNS.COLUMN_DEFAULT
    - INFORMATION_SCHEMA.VIEWS.VIEW_DEFINITION

-   OBJECT_DEFINITION() 関数  
  
-   **sys.sql_logins** の password_hash 列に格納された値。  CONTROL SERVER 権限が許可されていないユーザーには、この列に NULL 値が表示されます。  
  
> [!NOTE]  
>  組み込みシステム プロシージャと組み込みシステム関数の SQL 定義は、 **sys.system_sql_modules** カタログ ビュー、 **sp_helptext** ストアド プロシージャ、および OBJECT_DEFINITION() 関数を使用すると公開されます。  
  
## <a name="general-principles-of-metadata-visibility"></a>メタデータ表示の一般原則  
 次に、メタデータの表示に関して考慮する必要がある一般原則を示します。  
  
-   固定ロールの暗黙の権限  
  
-   権限のスコープ  
  
-   DENY の優先  
  
-   サブコンポーネントのメタデータの表示  
  
### <a name="fixed-roles-and-implicit-permissions"></a>固定ロールと暗黙の権限  
 固定ロールでアクセスできるメタデータは、それらのメタデータに対応する暗黙の権限によって決まります。  
  
### <a name="scope-of-permissions"></a>権限のスコープ  
 あるスコープで権限が許可されていると、そのスコープのメタデータと、そのスコープに含まれるすべてのスコープのメタデータを表示できることになります。 たとえば、あるスキーマに対する SELECT 権限が許可されると、そのスキーマに含まれるすべてのセキュリティ保護可能なメタデータに対する SELECT 権限がユーザーに許可されることになります。 したがって、あるスキーマに対する SELECT 権限を許可すると、ユーザーはそのスキーマと、スキーマ内のすべてのテーブル、ビュー、関数、プロシージャ、キュー、シノニム、型、および XML スキーマ コレクションのメタデータも表示できます。 スコープの詳細については、「[権限の階層 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)」を参照してください。  
  
### <a name="precedence-of-deny"></a>DENY の優先  
 通常、DENY は他の権限よりも優先されます。 たとえば、データベース ユーザーがあるスキーマに対する EXECUTE 権限を許可されていても、そのスキーマ内にあるストアド プロシージャに対する EXECUTE 権限が拒否されている場合は、そのストアド プロシージャのメタデータを表示できません。  
  
 また、ユーザーがあるスキーマに対する EXECUTE 権限を拒否されている場合は、そのスキーマ内のストアド プロシージャに対して EXECUTE 権限を許可されていても、そのストアド プロシージャのメタデータを表示できません。  
  
 もう 1 つの例として、ユーザーがあるストアド プロシージャに対する EXECUTE 権限を許可および拒否されている場合 (さまざまなロールのメンバーシップに属しているとこのような状況が生じます)、DENY が優先されるので、そのストアド プロシージャのメタデータを表示できません。  
  
### <a name="visibility-of-subcomponent-metadata"></a>サブコンポーネントのメタデータの表示  
 インデックス、CHECK 制約、トリガーなどのサブコンポーネントが表示されるかどうかは、そのサブコンポーネントの親に対する権限によって決まります。 これらのサブコンポーネントには、権限を許可できません。 たとえば、ユーザーがあるテーブルに対していくつかの権限を許可されていると、そのテーブル、列、インデックス、CHECK 制約、トリガー、およびその他のサブコンポーネントのメタデータを表示できます。  
  
#### <a name="metadata-that-is-accessible-to-all-database-users"></a>すべてのデータベース ユーザーがアクセスできるメタデータ  
 一部のメタデータは、特定のデータベースのすべてのユーザーがアクセスできる必要があります。 たとえば、ファイル グループには許可できる権限がないので、ファイル グループのメタデータを表示するための権限をユーザーに許可できません。 ただし、テーブルを作成できるすべてのユーザーは、ファイル グループのメタデータにアクセスして、CREATE TABLE ステートメントの ON *filegroup* 句または TEXTIMAGE_ON *filegroup* 句を使用できる必要があります。  
  
 DB_ID() 関数と DB_NAME() 関数で返されるメタデータは、すべてのユーザーが表示できます。  
  
 これは **public** ロールで表示できるカタログ ビューの一覧です。  

:::row:::
    :::column:::
        **sys.partition_functions**

        **sys.partition_schemes**

        **sys.filegroups**

        **sys.database_files**

        **sys.partitions**

        **sys.schemas**

        **sys.sql_dependencies**

        **sys.parameter_type_usages**
    :::column-end:::
    :::column:::
        **sys.partition_range_values**

        **sys.data_spaces**

        **sys.destination_data_spaces**

        **sys.allocation_units**

        **sys.messages**

        **sys.configurations**

        **sys.type_assembly_usages**

        **sys.column_type_usages**
    :::column-end:::
:::row-end:::

## <a name="see-also"></a>参照  
 [GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)   
 [DENY &#40;Transact-SQL&#41;](../../t-sql/statements/deny-transact-sql.md)   
 [REVOKE &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-transact-sql.md)   
 [EXECUTE AS 句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [互換性ビュー &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
