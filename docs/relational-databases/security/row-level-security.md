---
title: 行レベルのセキュリティ
description: 行レベルのセキュリティで、グループ メンバーシップや実行コンテキストを使用して、SQL Server のデータベース テーブル内の行へのアクセスを制御する方法について説明します。
ms.custom: ''
ms.date: 09/01/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- access control predicates
- row level security
- security [SQL Server], predicate based access control
- row level security described
- predicate based security
author: VanMSFT
ms.author: vanto
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: cce49516f92db523dfd25f8ea651ab217ef13619
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104739942"
---
# <a name="row-level-security"></a>行レベルのセキュリティ

[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  ![行レベル セキュリティの図](../../relational-databases/security/media/row-level-security-graphic.png "行レベル セキュリティの図")  
  
行レベルのセキュリティでは、グループ メンバーシップや実行コンテキストを使用して、データベース テーブル内の行へのアクセスを制御することができます。
  
Row-Level Security (RLS) は、アプリケーションでセキュリティの設計やコーディングを簡略化します。 RLS は、データ行アクセスに対して制限を実装するのに役立ちます。 たとえば、作業者が自分の部署に関連するデータ行にしかアクセスしないようにすることができます。 別の例として、顧客のデータ アクセスをその顧客の会社に関連するデータだけに制限することがあります。  
  
アクセスの制限のロジックは、別のアプリケーション層のデータから離れてではなく、データベース層にあります。 任意の層からデータへのアクセスが試行されるたびに、データベース システムにはアクセス制限が適用されます。 これによりセキュリティ システムの外部からのアクセスが減り、そのシステムの信頼性と堅牢性が向上します。  
  
[CREATE SECURITY POLICY](../../t-sql/statements/create-security-policy-transact-sql.md)[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを使用して RLS を実装すると、[インライン テーブル値関数](../../relational-databases/user-defined-functions/create-user-defined-functions-database-engine.md)として述語が作成されます。  

[!INCLUDE [SQL Server 2016 and later](../../includes/applies-to-version/sqlserver2016.md)]、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] ([こちらで入手してください](/azure/azure-sql/database/features-comparison?WT.mc_id=TSQL_GetItTag))、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)]。
  
> [!NOTE]
> Azure Synapse では、フィルター述語のみがサポートされています。 ブロック述語は現在、Azure Synapse でサポートされていません。

## <a name="description"></a><a name="Description"></a> 説明

RLS では、2 種類のセキュリティ述語をサポートしています。  
  
- フィルター述語は、読み取り操作 (SELECT、UPDATE、DELETE) が可能な行を通知なしにフィルター処理します。  
  
- ブロック述語は、その述語に違反する書き込み操作 (AFTER INSERT、AFTER UPDATE、BEFORE UPDATE、BEFORE DELETE) を明示的に禁止します。  
  
 テーブルの行レベルのデータへのアクセスは、インライン テーブル値関数として定義されたセキュリティ述語によって制限されます。 関数が呼び出され、セキュリティ ポリシーによって適用されます。 フィルター述語の場合、結果セットからフィルター処理されている行はアプリケーションによって認識されません。 すべての行がフィルター処理されると、null セットが返されます。 ブロック述語の場合、その述語に違反するすべての操作がエラーで失敗します。  
  
 フィルター述語は、ベース テーブルからのデータの読み取り中に適用されます。 次のすべての get 操作に影響します:**SELECT**、**DELETE**、**UPDATE**。 ユーザーは、フィルター処理される行を選択または削除できません。 ユーザーは、フィルター処理される行を更新できません。 しかし、後でフィルター処理されるようにすれば行を更新できます。 ブロック述語はすべての書き込み操作に影響します。  
  
- AFTER INSERT と AFTER UPDATE の各述語は、ユーザーが行を述語に違反する値に更新できないようにします。  
  
- BEFORE UPDATE 述語は、ユーザーが現在述語に違反している行を更新できないようにします。  
  
- BEFORE DELETE 述語は削除操作を禁止します。  
  
 フィルター述語とブロック述語およびセキュリティ ポリシーの動作は次のとおりです。  
  
- 別のテーブルとの結合や関数の呼び出しを実行する述語関数を定義できます。 セキュリティ ポリシーが `SCHEMABINDING = ON` (既定) を使用して作成されている場合、結合または関数にはクエリからアクセスでき、追加のアクセス許可の確認を必要とせず、期待どおりに動作します。 セキュリティ ポリシーが `SCHEMABINDING = OFF` を使用して作成されている場合、ターゲット テーブルに対してクエリを実行するには、ユーザーに、これらの追加のテーブルと関数に対する **SELECT** 権限が必要になります。 述語関数で CLR スカラー値関数を呼び出す場合は、**EXECUTE** 権限も必要になります。
  
- セキュリティ述語は定義されているが無効になっているテーブルに対してクエリを発行できます。 フィルター処理またはブロックされている行には影響しません。  
  
- dbo ユーザー、**db_owner** ロールのメンバー、またはテーブルの所有者が、セキュリティ ポリシーが定義され有効になっているテーブルに対してクエリを実行すると、セキュリティ ポリシーでの定義に従って行がフィルター処理またはブロックされます。  
  
- スキーマ バインドされたセキュリティ ポリシーによってバインドされているテーブルのスキーマを変更しようとすると、エラーが発生します。 ただし、述語で参照されていない列は変更できます。  
  
- 指定された操作に対する述語が既に定義されているテーブルに述語を追加しようとすると、エラーが発生します。 これは、述語が有効になっているかどうかを問わず発生します。
  
- スキーマ バインドされたセキュリティ ポリシー内のテーブルで述語として使用されている関数を変更しようとすると、エラーが発生します。  
  
- 重複しない述語が含まれる複数のアクティブなセキュリティ ポリシーの定義は、行うことができます。  
  
 フィルター述語の動作は次のとおりです。  
  
- テーブルの行をフィルター処理するセキュリティ ポリシーを定義します。 アプリケーションでは、**SELECT**、**UPDATE**、**DELETE** 操作の場合、フィルター処理された行はいずれも認識されません。 これには、すべての行がフィルター処理された状況も含まれます。アプリケーションでは、他の操作中にフィルター処理される場合でも、行の **INSERT** を行うことができます。  
  
 ブロック述語の動作は次のとおりです。  
  
- UPDATE のブロック述語は、BEFORE と AFTER の個別の操作に分けられています。 そのため、たとえば、ユーザーが行を更新して現在の値よりも大きい値を含めることを禁止することはできません。 このようなロジックが必要な場合は、[DELETED および INSERTED](../triggers/use-the-inserted-and-deleted-tables.md) 中間テーブルでトリガーを使用して、古い値と新しい値を一緒に参照する必要があります。  
  
- 述語関数で使用されている列が変更されていない場合、オプティマイザーでは AFTER UPDATE ブロック述語がチェックされません。 次に例を示します。Alice は、給与を 100,000 より大きく変更することはできません。 Alice は、述語内で参照される列が変更されていない場合に限り、給与が既に 100,000 を超えている従業員のアドレスを変更できます。  
  
- BULK INSERT などの Bulk API は変更されていません。 つまり、AFTER INSERT ブロック述語は、通常の挿入操作と同様に一括挿入操作に適用されます。  
  
## <a name="use-cases"></a><a name="UseCases"></a> 例

 RLS をどのように使用するかの設計例を次に示します。  
  
- 病院では、看護師が自分の患者のデータ行のみを見ることができるようなセキュリティ ポリシーを作成できます。  
  
- 銀行では、従業員のビジネス部門や会社における役割に基づき、財務データの行へのアクセスを制限するポリシーを作成できます。  
  
- マルチ テナント アプリケーションでは、他のすべてのテナントの行から各テナントのデータ行を論理的に分離するポリシーを作成できます。 1 つのテーブルで多くのテナントのデータを保存することで効率性が高まります。 各テナントはそのデータ行のみ表示できます。  
  
 RLS フィルター述語は機能的には **WHERE** 句の追加と同等です。 述語はビジネス プラクティスの規定と同じくらいに洗練されたものであり、句は `WHERE TenantId = 42`と同じくらいに簡単です。  
  
 より形式的に表現すると、RLS はアクセス制御に基づく述語を採用しています。 柔軟で、集中管理された、述語ベースの評価を備えています。 述語は、管理者が適切に決定したメタデータや他の条件に基づくことができます。 述語は、ユーザーがその属性に基づいて適切にデータにアクセスできるかどうかを決定する条件として使用されます。 述語ベースのアクセス制御を使用することで、ラベルベースのアクセス制御を実装できます。  
  
## <a name="permissions"></a><a name="Permissions"></a> Permissions

 セキュリティ ポリシーの作成、変更、削除には、 **ALTER ANY SECURITY POLICY** 権限が必要です。 セキュリティ ポリシーの作成か削除には、スキーマ上で **ALTER** 権限が必要です。  
  
 また、追加される各述語に関しては次のアクセス許可も必要になります。  
  
- 述語として使用している関数に関する **SELECT** および **REFERENCES** 権限。  
  
- ポリシーにバインドしているターゲット テーブルに対する **REFERENCES** 権限。  
  
- 引数として使用しているターゲット テーブルのすべての列に対する **REFERENCES** 権限。  
  
 セキュリティ ポリシーは、データベースの dbo ユーザーを含めてすべてのユーザーに適用されます。 dbo ユーザーはセキュリティ ポリシーを変更したり削除したりできますが、セキュリティ ポリシーに加えた変更は監査することができます。 sysadmin や db_owner などの高い権限を持つユーザーがトラブルシューティングやデータ検証のためにすべての行を表示する必要がある場合は、これを許可するセキュリティ ポリシーを作成する必要があります。  
  
 `SCHEMABINDING = OFF`でセキュリティ ポリシーが作成された場合、ユーザーは、対象テーブルにクエリを実行するために、述語関数とその述語関数で使用される追加のテーブル、ビュー、または関数に対する  **SELECT** 権限または **EXECUTE** 権限が必要です。 `SCHEMABINDING = ON` (既定) でセキュリティ ポリシーが作成された場合、ユーザーが対象テーブルに対してクエリを実行すると、これらの権限チェックは迂回されます。  
  
## <a name="best-practices"></a><a name="Best"></a> ベスト プラクティス  
  
- RLS オブジェクト、述語関数、セキュリティ ポリシーに対して別のスキーマを作成することを強くお勧めします。 これにより、これらの特殊なオブジェクトに必要な権限をターゲット テーブルから分離できます。 マルチテナントデータベースでは、さまざまなポリシーと述語関数のさらなる分離が必要になる場合がありますが、すべてのケースで標準というわけではありません。
  
- **ALTER ANY SECURITY POLICY** 権限は、セキュリティ ポリシー マネージャーなどの高い権限を持つユーザーを対象としています。 セキュリティ ポリシー マネージャーには、保護しているテーブルでは **SELECT** 権限は必要とされません。  
  
- 潜在的なランタイム エラーを回避する述語関数では型変換しないようにします。  
  
- パフォーマンスの低下を避けるため、可能な場合は、述語関数では再帰しないようにします。 クエリ オプティマイザーでは直接再帰の検出を試みますが、間接再帰の検出は保証されません。 間接再帰では、2 番目の関数によって述語関数が呼び出されます。  
  
- パフォーマンスを最適化する述語関数で過剰にテーブルを結合しないようにします。  
  
 セッション固有の [SET オプション](../../t-sql/statements/set-statements-transact-sql.md)に依存する述語ロジックは避けます。実用的なアプリケーションで使用されることはほとんどありませんが、ロジックが特定のセッション固有の **SET** オプションに依存する述語関数では、ユーザーが任意のクエリを実行できる場合に情報が漏洩する可能性があります。 たとえば、文字列を **datetime** に暗黙的に変換する述語関数は、現在のセッションの **SET DATEFORMAT** オプションに基づいてさまざまな行をフィルター処理する可能性があります。 一般に、述語関数は次のルールに従う必要があります。  
  
- 述語関数では、文字列を **date**、**smalldatetime**、**datetime**、**datetime2**、または **datetimeoffset** に暗黙的に変換しないようにする必要があります。逆の場合も同様です。これらの変換は、[SET DATEFORMAT &#40;Transact-SQL&#41;](../../t-sql/statements/set-dateformat-transact-sql.md) オプションと [SET LANGUAGE &#40;Transact-SQL&#41;](../../t-sql/statements/set-language-transact-sql.md) オプションの影響を受けるためです。 代わりに、 **CONVERT** 関数を使用し、スタイル パラメーターを明示的に指定します。  
  
- 述語関数は、週の最初の日の値に依存しないようにする必要があります。この値は [SET DATEFIRST &#40;Transact-SQL&#41;](../../t-sql/statements/set-datefirst-transact-sql.md) オプションの影響を受けるためです。  
  
- 述語関数は、エラー (オーバーフローやゼロ除算など) が発生した場合に **NULL** を返す算術式や集計式に依存しないようにする必要があります。この動作は [SET ANSI_WARNINGS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-warnings-transact-sql.md)、[SET NUMERIC_ROUNDABORT &#40;Transact-SQL&#41;](../../t-sql/statements/set-numeric-roundabort-transact-sql.md)、[SET ARITHABORT &#40;Transact-SQL&#41;](../../t-sql/statements/set-arithabort-transact-sql.md) の各オプションの影響を受けるためです。  
  
- 述語関数では、連結文字列を **NULL** と比較しないようにする必要があります。この動作は、[SET CONCAT_NULL_YIELDS_NULL &#40;Transact-SQL&#41;](../../t-sql/statements/set-concat-null-yields-null-transact-sql.md) オプションの影響を受けるためです。  

## <a name="security-note-side-channel-attacks"></a><a name="SecNote"></a> セキュリティに関する注意:サイドチャネル攻撃

### <a name="malicious-security-policy-manager"></a>悪意のあるセキュリティ ポリシー マネージャー

悪意のあるセキュリティ ポリシー マネージャーを監視することが重要です。これは、機密性の高い列にセキュリティ ポリシーを作成する十分な権限と、インライン テーブル値関数を作成したり、変更したりする権限がそのセキュリティ ポリシー マネージャーにある場合、テーブルで選択権限を持つ別のユーザーと共謀し、サイドチャネル攻撃を使ってデータを推測するインライン テーブル値関数を作成して、悪意を持ってデータを流出させることも可能になるためです。 このような攻撃では、共謀 (または過剰な権限を悪意のあるユーザーに与えること) が必要で、ポリシーの変更が何度も必要になる可能性が高く (スキーマ バインドを解除するために述語を削除する権限を要求する)、インライン テーブル値関数が変更され、対象のテーブルに対して select ステートメントが繰り返し実行されることになります。 必要に応じてアクセス許可を制限し、疑わしいアクティビティを監視することをお勧めします。 行レベルのセキュリティに関連して、絶えず変化するポリシーやインライン テーブル値関数などのアクティビティを監視する必要があります。  
  
### <a name="carefully-crafted-queries"></a>慎重に作成されたクエリ

慎重に作成されたクエリを通じて、情報漏えいが発生する可能性があります。 たとえば、 `SELECT 1/(SALARY-100000) FROM PAYROLL WHERE NAME='John Doe'` というクエリで、悪意のあるユーザーに John doe さんの給与が 100,000 ドルであることが知らされました。 悪意のあるユーザーが他のユーザーの給与を直接照会するような事態を防ぐため、セキュリティ述語がある場合でも、ゼロ除算の例外がクエリ結果として返されることで、悪意のあるユーザーによって知られてしまいます。  

## <a name="cross-feature-compatibility"></a><a name="Limitations"></a> 機能間の互換性

 一般に、行レベルのセキュリティは機能間で予想どおりに機能します。 ただし、例外がいくつかあります。 ここでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の他の特定の機能で行レベルのセキュリティを使用する場合の注意事項について説明します。  
  
- **DBCC SHOW_STATISTICS** からはフィルター処理されていないデータに対する統計が報告されるため、セキュリティ ポリシーによって保護されていない場合に情報が漏洩する可能性があります。 このため、行レベルのセキュリティ ポリシーを持つテーブルの統計オブジェクトを表示するアクセス権は制限されます。 ユーザーがテーブルを所有しているか、ユーザーが sysadmin 固定サーバー ロール、db_owner 固定データベース ロール、または db_ddladmin 固定データベース ロールのメンバーである必要があります。  
  
- **Filestream:** RLS は Filestream と互換性がありません。  
  
- **PolyBase:** RLS は、Azure Synapse および SQL Server 2019 CU7 以降の外部テーブルでサポートされています。 

- **メモリ最適化テーブル:** メモリ最適化テーブルでセキュリティ述語として使用されるインライン テーブル値関数は、`WITH NATIVE_COMPILATION` オプションを使用して定義する必要があります。 このオプションを使用すると、メモリ最適化テーブルでサポートされていない言語機能が禁止され、作成時に該当するエラーが発行されます。 詳細については、「 **メモリ最適化テーブルの概要** 」の「 [メモリ最適化テーブルの行レベルのセキュリティ](../../relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables.md)」をご覧ください。  
  
- **インデックス付きビュー:** 一般に、セキュリティ ポリシーはビューに対して作成でき、ビューはセキュリティ ポリシーによってバインドされたテーブルに作成できます。 ただし、インデックスによる行の参照はポリシーを回避するため、セキュリティ ポリシーが適用されたテーブルにインデックス付きビューを作成することはできません。  
  
- **Change Data Capture:** Change Data Capture では、**db_owner** のメンバー、またはテーブルで CDC が有効になっているときに指定された "ゲーティング" ロールのメンバーであるユーザーに、フィルター処理する必要があるすべての行が漏洩する可能性があります (注: この関数を明示的に **NULL** に設定すると、すべてのユーザーが変更データにアクセスできるようになります)。 実際には、 **db_owner** と、このゲーティング ロールのメンバーは、テーブルにセキュリティ ポリシーが存在する場合でも、テーブルのすべてのデータ変更を表示できます。  
  
- **Change Tracking:** Change Tracking では、**SELECT** と **VIEW CHANGE TRACKING** の両方の権限を持つユーザーに、フィルター処理する必要がある行の主キーが漏洩する可能性があります。 実際のデータ値は漏洩しません。漏洩するのは、主キー B を持つ行の列 A が更新/挿入/削除されたという事実だけです。 これは、主キーに社会保障番号などの機密要素が含まれている場合に問題になります。 ただし、実際には、この **CHANGETABLE** は、最新のデータを取得するために、ほとんどの場合、元のテーブルと結合されます。  
  
- **フルテキスト検索:** 次のフルテキスト検索関数やセマンティック検索関数を使用したクエリでは、行レベルのセキュリティを適用し、フィルター処理する必要のある行の主キーの漏洩を防ぐために余分な結合が発生するため、パフォーマンスが低下することが予想されます。**CONTAINSTABLE**、**FREETEXTTABLE**、semantickeyphrasetable、semanticsimilaritydetailstable、semanticsimilaritytable。  
  
- **列ストア インデックス:** RLS は、クラスター化列ストア インデックスと非クラスター化列ストア インデックスの両方と互換性があります。 ただし、行レベルのセキュリティによって関数が適用されるため、オプティマイザーではバッチ モードを使用しないようにクエリ プランが変更される可能性があります。  
  
- **パーティション ビュー:** パーティション ビューでブロック述語を定義することはできません。また、ブロック述語を使用するテーブルにパーティション ビューを作成することはできません。 フィルター述語はパーティション ビューと互換性があります。  
  
- **テンポラル テーブル:** テンポラル テーブルは RLS と互換性があります。 ただし、現在のテーブルのセキュリティ述語は、履歴テーブルに自動的にはレプリケートされません。 現在のテーブルと履歴テーブルの両方にセキュリティ ポリシーを適用するには、テーブルごとにセキュリティ述語を個別に追加する必要があります。  
  
## <a name="examples"></a><a name="CodeExamples"></a> 使用例  
  
### <a name="a-scenario-for-users-who-authenticate-to-the-database"></a><a name="Typical"></a> A. データベースに対して認証するユーザーのシナリオ

 この例では、3 人のユーザーを作成し、6 行のテーブルを作成して設定します。 その後、インライン テーブル値関数とテーブルのセキュリティ ポリシーが作成されます。 さらにこの例では、select ステートメントがさまざまなユーザーに対してどのようにフィルター処理されるかが示されます。  
  
 別のアクセス機能を示す 3 つのユーザー アカウントを作成します。  


```sql  
CREATE USER Manager WITHOUT LOGIN;  
CREATE USER Sales1 WITHOUT LOGIN;  
CREATE USER Sales2 WITHOUT LOGIN;  
```

データを保持するテーブルを作成します。  

```sql
CREATE TABLE Sales  
    (  
    OrderID int,  
    SalesRep sysname,  
    Product varchar(10),  
    Qty int  
    );  
```

 そのテーブルに、各営業担当者の 3 つの注文を表示する、6 つのデータ行を設定します。  

```sql
INSERT INTO Sales VALUES (1, 'Sales1', 'Valve', 5);
INSERT INTO Sales VALUES (2, 'Sales1', 'Wheel', 2);
INSERT INTO Sales VALUES (3, 'Sales1', 'Valve', 4);
INSERT INTO Sales VALUES (4, 'Sales2', 'Bracket', 2);
INSERT INTO Sales VALUES (5, 'Sales2', 'Wheel', 5);
INSERT INTO Sales VALUES (6, 'Sales2', 'Seat', 5);
-- View the 6 rows in the table  
SELECT * FROM Sales;
```

各ユーザーに、テーブルに対する読み取りアクセス権を付与します。  

```sql
GRANT SELECT ON Sales TO Manager;  
GRANT SELECT ON Sales TO Sales1;  
GRANT SELECT ON Sales TO Sales2;  
```

新しいスキーマと、インライン テーブル値関数を作成します。 SalesRep 列内の行がクエリを実行しているユーザーと同じである場合 (`@SalesRep = USER_NAME()`)、またはクエリを実行しているユーザーがマネージャー ユーザーである場合 (`USER_NAME() = 'Manager'`)、関数は 1 を返します

```sql
CREATE SCHEMA Security;  
GO  
  
CREATE FUNCTION Security.fn_securitypredicate(@SalesRep AS sysname)  
    RETURNS TABLE  
WITH SCHEMABINDING  
AS  
    RETURN SELECT 1 AS fn_securitypredicate_result
WHERE @SalesRep = USER_NAME() OR USER_NAME() = 'Manager';  
```

フィルター述語として関数を追加するセキュリティ ポリシーを作成します。 状態を ON に設定してポリシーを有効にする必要があります。

```sql
CREATE SECURITY POLICY SalesFilter  
ADD FILTER PREDICATE Security.fn_securitypredicate(SalesRep)
ON dbo.Sales  
WITH (STATE = ON);  
```

fn_securitypredicate 関数に対する SELECT 権限を許可する 
```sql
GRANT SELECT ON security.fn_securitypredicate TO Manager;  
GRANT SELECT ON security.fn_securitypredicate TO Sales1;  
GRANT SELECT ON security.fn_securitypredicate TO Sales2;  
```

各ユーザーとして Sales テーブルから選択されたフィルター述語を今すぐテストしてみましょう。

```sql
EXECUTE AS USER = 'Sales1';  
SELECT * FROM Sales;
REVERT;  
  
EXECUTE AS USER = 'Sales2';  
SELECT * FROM Sales;
REVERT;  
  
EXECUTE AS USER = 'Manager';  
SELECT * FROM Sales;
REVERT;  
```

マネージャーには、6 つの行すべてが表示されるはずです。 Sales1 と Sales2 のユーザーには、それぞれの売上のみ表示されます。

セキュリティ ポリシーを変更してポリシーを無効にします。

```sql
ALTER SECURITY POLICY SalesFilter  
WITH (STATE = OFF);  
```

これで、Sales1 と Sales2 のユーザーに 6 つの行すべてが表示されます。

リソースをクリーンアップする SQL データベースに接続します

```sql
DROP USER Sales1;
DROP USER Sales2;
DROP USER Manager;

DROP SECURITY POLICY SalesFilter;
DROP TABLE Sales;
DROP FUNCTION Security.fn_securitypredicate;
DROP SCHEMA Security;
```

### <a name="b-scenarios-for-using-row-level-security-on-an-azure-synapse-external-table"></a><a name="external"></a> B. Azure Synapse 外部テーブルに対して行レベルのセキュリティを使用する場合のシナリオ

この簡単な例では、3 人のユーザーと 6 行の外部テーブルを作成します。 その後、インライン テーブル値関数と外部テーブルのセキュリティ ポリシーが作成されます。 この例では、select ステートメントがさまざまなユーザーに対してどのようにフィルター処理されるかが示されます。 

### <a name="prerequisites"></a>前提条件

1. 専用 SQL プールが必要です。 [専用 SQL プールの作成](/azure/synapse-analytics/sql-data-warehouse/create-data-warehouse-portal)に関する記事を参照してください
1. 専用 SQL プールをホストしているサーバーは AAD に登録されている必要があります。また、ストレージ BLOB データ共同作成者のアクセス許可がある Azure Storage アカウントを持っている必要があります。 それには、[こちら](/azure/azure-sql/database/vnet-service-endpoint-rule-overview#steps) の手順に従います。
1. 自分の Azure Storage アカウントにファイル システムを作成します。 Storage Explorer を使用して自分のストレージ アカウントを表示します。 コンテナーを右クリックし、 *[ファイル システムの作成]* を選択します。  

前提条件を満たしたら、別のアクセス機能を示す 3 つのユーザー アカウントを作成します。

```sql
--run in master
CREATE LOGIN Manager WITH PASSWORD = '<user_password>'
GO
CREATE LOGIN Sales1 WITH PASSWORD = '<user_password>'
GO
CREATE LOGIN Sales2 WITH PASSWORD = '<user_password>'
GO

--run in master and your dedicated SQL pool database
CREATE USER Manager FOR LOGIN Manager;  
CREATE USER Sales1  FOR LOGIN Sales1;  
CREATE USER Sales2  FOR LOGIN Sales2 ;
```

データを保持するテーブルを作成します。  

```sql
CREATE TABLE Sales  
    (  
    OrderID int,  
    SalesRep sysname,  
    Product varchar(10),  
    Qty int  
    );  
```

そのテーブルに、各営業担当者の 3 つの注文を表示する、6 つのデータ行を設定します。  

```sql
INSERT INTO Sales VALUES (1, 'Sales1', 'Valve', 5);
INSERT INTO Sales VALUES (2, 'Sales1', 'Wheel', 2);
INSERT INTO Sales VALUES (3, 'Sales1', 'Valve', 4);
INSERT INTO Sales VALUES (4, 'Sales2', 'Bracket', 2);
INSERT INTO Sales VALUES (5, 'Sales2', 'Wheel', 5);
INSERT INTO Sales VALUES (6, 'Sales2', 'Seat', 5);
-- View the 6 rows in the table  
SELECT * FROM Sales;
```

作成した Sales テーブルから Azure Synapse 外部テーブルを作成します。

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<user_password>';

CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';

CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://<file_system_name@storage_account>.dfs.core.windows.net', CREDENTIAL = msi_cred);

CREATE EXTERNAL FILE FORMAT MSIFormat  WITH (FORMAT_TYPE=DELIMITEDTEXT);
  
CREATE EXTERNAL TABLE Sales_ext WITH (LOCATION='<your_table_name>', DATA_SOURCE=ext_datasource_with_abfss, FILE_FORMAT=MSIFormat, REJECT_TYPE=Percentage, REJECT_SAMPLE_VALUE=100, REJECT_VALUE=100)
AS SELECT * FROM sales;
```

作成した Sales_ext 外部テーブルで、3 人のユーザーに SELECT を付与します。

```sql
GRANT SELECT ON Sales_ext TO Sales1;  
GRANT SELECT ON Sales_ext TO Sales2;  
GRANT SELECT ON Sales_ext TO Manager;
```

新しいスキーマとインライン テーブル値関数を作成します。後者は例 A で完成しているかもしれません。SalesRep 列内の行がクエリを実行しているユーザーと同じである場合 (`@SalesRep = USER_NAME()`)、またはクエリを実行しているユーザーがマネージャー ユーザーである場合 (`USER_NAME() = 'Manager'`)、関数は 1 を返します。

```sql
CREATE SCHEMA Security;  
GO  
  
CREATE FUNCTION Security.fn_securitypredicate(@SalesRep AS sysname)  
    RETURNS TABLE  
WITH SCHEMABINDING  
AS  
    RETURN SELECT 1 AS fn_securitypredicate_result
WHERE @SalesRep = USER_NAME() OR USER_NAME() = 'Manager';  
```

インライン テーブル値関数をフィルター述語として使用して外部テーブル上にセキュリティ ポリシーを作成します。 状態を ON に設定してポリシーを有効にする必要があります。

```sql
CREATE SECURITY POLICY SalesFilter_ext
ADD FILTER PREDICATE Security.fn_securitypredicate(SalesRep)
ON dbo.Sales_ext  
WITH (STATE = ON);
```

次に、Sales_ext 外部テーブルから選択することで、フィルター述語をテストします。 各ユーザー (Sales1、Sales2、およびマネージャー) としてサインインします。 次のコマンドを各ユーザーとして実行します。

```sql
SELECT * FROM Sales_ext;
```

マネージャーには、6 つの行すべてが表示されるはずです。 Sales1 と Sales2 のユーザーには、各自の売上のみ表示されます。

セキュリティ ポリシーを変更してポリシーを無効にします。

```sql
ALTER SECURITY POLICY SalesFilter_ext  
WITH (STATE = OFF);  
```

これで、Sales1 と Sales2 のユーザーに 6 つの行すべてが表示されます。

リソースをクリーンアップする Azure Synapse データベースに接続します

```sql
DROP USER Sales1;
DROP USER Sales2;
DROP USER Manager;

DROP SECURITY POLICY SalesFilter_ext;
DROP TABLE Sales;
DROP EXTERNAL TABLE Sales_ext;
DROP EXTERNAL DATA SOURCE ext_datasource_with_abfss ;
DROP EXTERNAL FILE FORMAT MSIFormat;
DROP DATABASE SCOPED CREDENTIAL msi_cred; 
DROP MASTER KEY;
```

論理マスターに接続してリソースをクリーンアップします。

```sql
DROP LOGIN Sales1;
DROP LOGIN Sales2;
DROP LOGIN Manager;
```

### <a name="c-scenario-for-users-who-connect-to-the-database-through-a-middle-tier-application"></a><a name="MidTier"></a> C. 中間層アプリケーションからデータベースに接続するユーザーのシナリオ

> [!NOTE]
> この例の場合、Azure Synapse では、ブロック述語機能が現在サポートされていないため、正しくないユーザー ID の行の挿入がブロックされません。

この例では、アプリケーション ユーザー (またはテナント) が同じ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ユーザー (アプリケーション) を共有している場合、中間層のアプリケーションが接続フィルタリングを実装する方法を示します。 アプリケーションは、データベースに接続した後、 [SESSION_CONTEXT &#40;Transact-SQL&#41;](../../t-sql/functions/session-context-transact-sql.md) で現在のアプリケーション ユーザー ID を設定します。その後、セキュリティ ポリシーによって、この ID に対して表示しない行が透過的にフィルター処理されます。また、ユーザーが間違ったユーザー ID の行を挿入できないようにします。 その他のアプリケーションの変更は必要ありません。  
  
 データを保持するテーブルを作成します。

```sql
CREATE TABLE Sales (  
    OrderId int,  
    AppUserId int,  
    Product varchar(10),  
    Qty int  
);  
```

そのテーブルに、各アプリケーション ユーザーの 3 つの注文を表示する、6 つのデータ行を設定します。

```sql
INSERT Sales VALUES
    (1, 1, 'Valve', 5),
    (2, 1, 'Wheel', 2),
    (3, 1, 'Valve', 4),  
    (4, 2, 'Bracket', 2),
    (5, 2, 'Wheel', 5),
    (6, 2, 'Seat', 5);  
```

アプリケーションが接続に使用する権限の低いユーザーを作成します。

```sql
-- Without login only for demo  
CREATE USER AppUser WITHOUT LOGIN;
GRANT SELECT, INSERT, UPDATE, DELETE ON Sales TO AppUser;  
  
-- Never allow updates on this column  
DENY UPDATE ON Sales(AppUserId) TO AppUser;  
```

**SESSION_CONTEXT** に格納されたアプリケーション ユーザー ID を使用して行をフィルター処理する、新しいスキーマと述語関数を作成します。

```sql
CREATE SCHEMA Security;  
GO  
  
CREATE FUNCTION Security.fn_securitypredicate(@AppUserId int)  
    RETURNS TABLE  
    WITH SCHEMABINDING  
AS  
    RETURN SELECT 1 AS fn_securitypredicate_result  
    WHERE  
        DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('AppUser')
        AND CAST(SESSION_CONTEXT(N'UserId') AS int) = @AppUserId;
GO  
```

`Sales`のフィルター述語およびブロック述語としてこの関数を追加するセキュリティ ポリシーを作成します。 **BEFORE UPDATE** と **BEFORE DELETE** は既にフィルター処理されているため、ブロック述語に必要なのは **AFTER INSERT** だけです。また、以前に設定した列権限により、 **列は他の値に更新できないため、** AFTER UPDATE `AppUserId` は不要です。

```sql
CREATE SECURITY POLICY Security.SalesFilter  
    ADD FILTER PREDICATE Security.fn_securitypredicate(AppUserId)
        ON dbo.Sales,  
    ADD BLOCK PREDICATE Security.fn_securitypredicate(AppUserId)
        ON dbo.Sales AFTER INSERT
    WITH (STATE = ON);  
```

`Sales` SESSION_CONTEXT **でさまざまなユーザー ID を設定した後、** テーブルから選択することで、接続フィルタリングをシミュレートできます。 実際には、アプリケーションが、接続を開いた後に **SESSION_CONTEXT** で現在のユーザー ID を設定します。

```sql
EXECUTE AS USER = 'AppUser';  
EXEC sp_set_session_context @key=N'UserId', @value=1;  
SELECT * FROM Sales;  
GO  
  
/* Note: @read_only prevents the value from changing again until the connection is closed (returned to the connection pool)*/
EXEC sp_set_session_context @key=N'UserId', @value=2, @read_only=1;
  
SELECT * FROM Sales;  
GO  
  
INSERT INTO Sales VALUES (7, 1, 'Seat', 12); -- error: blocked from inserting row for the wrong user ID  
GO  
  
REVERT;  
GO  
```

データベース リソースをクリーンアップします。

```sql
DROP USER AppUser;

DROP SECURITY POLICY Security.SalesFilter;
DROP TABLE Sales;
DROP FUNCTION Security.fn_securitypredicate;
DROP SCHEMA Security;
```

## <a name="see-also"></a>参照

 [CREATE SECURITY POLICY &#40;Transact-SQL&#41;](../../t-sql/statements/create-security-policy-transact-sql.md)</br>
 [ALTER SECURITY POLICY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-security-policy-transact-sql.md)</br>
 [DROP SECURITY POLICY &#40;Transact-SQL&#41;](../../t-sql/statements/drop-security-policy-transact-sql.md)</br>
 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)</br>
 [SESSION_CONTEXT &#40;Transact-SQL&#41;](../../t-sql/functions/session-context-transact-sql.md)</br>
 [sp_set_session_context &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-set-session-context-transact-sql.md)</br>
 [sys.security_policies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-security-policies-transact-sql.md)</br>
 [sys.security_predicates &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-security-predicates-transact-sql.md)</br>
 [ユーザー定義関数の作成 &#40;データベース エンジン&#41;](../../relational-databases/user-defined-functions/create-user-defined-functions-database-engine.md)
