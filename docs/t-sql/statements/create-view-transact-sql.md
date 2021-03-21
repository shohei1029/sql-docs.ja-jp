---
description: CREATE VIEW (Transact-SQL)
title: CREATE VIEW (Transact-SQL)
ms.custom: ''
ms.date: 04/16/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE VIEW
- VIEW_TSQL
- VIEW
- CREATE_VIEW_TSQL
- SCHEMABINDING_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- table creation [SQL Server], CREATE VIEW
- views [SQL Server], creating
- CREATE VIEW statement
- updatable partitioned views
- tables [SQL Server], virtual
- updatable views
- modifying partitioned views
- virtual tables [SQL Server]
- number of columns per view
- partitioned views [SQL Server], creating
- WITH ENCRYPTION clause
- WITH CHECK OPTION clause
- partitioned views [SQL Server], modifying
- partitioned views [SQL Server], replication
- distributed partitioned views [SQL Server]
- views [SQL Server], indexed views
- maximum number of columns per view
ms.assetid: aecc2f73-2ab5-4db9-b1e6-2f9e3c601fb9
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e8ec37b3599a9fd10a3cf9e7d42402f875851e42
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755442"
---
# <a name="create-view-transact-sql"></a>CREATE VIEW (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  クエリによって内容 (列と行) が定義される仮想テーブルを作成します。 このステートメントを使用して、データベースの 1 つまたは複数のテーブル内のデータのビューを作成します。 たとえば、ビューは次の目的で使用できます。  
  
-   各ユーザーのデータベースに対する認識を特化、簡素化、およびカスタマイズする。  
  
-   基になるベース テーブルに直接アクセスする権限をユーザーに与えずに、ユーザーがビューを介してデータにアクセスできるように設定することにより、セキュリティのメカニズムとして使用する。  
  
-   テーブルのスキーマが変更された場合に、そのテーブルをエミュレートするための後方互換性インターフェイスを提供する。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
CREATE [ OR ALTER ] VIEW [ schema_name . ] view_name [ (column [ ,...n ] ) ]
[ WITH <view_attribute> [ ,...n ] ]
AS select_statement
[ WITH CHECK OPTION ]
[ ; ]  
  
<view_attribute> ::=
{  
    [ ENCRYPTION ]  
    [ SCHEMABINDING ]  
    [ VIEW_METADATA ]
}
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
CREATE VIEW [ schema_name . ] view_name [  ( column_name [ ,...n ] ) ]   
AS <select_statement>   
[;]  
  
<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

OR ALTER  
 **適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] および [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP1 以降)。   
  
 ビューが既に存在する場合にのみ、条件付きでビューを変更します。 
 
 *schema_name*  
 ビューが所属するスキーマの名前を指定します。  
  
 *view_name*  
 ビューの名前です。 ビュー名は、識別子のルールに従っている必要があります。 ビューの所有者名の指定は省略可能です。  
  
 *column*  
 ビューの列に付ける名前を指定します。 列名が必要なのは、算術式、関数、または定数から列を導いた場合と、名前を付けないと (通常、結合によって) 2 つ以上の列の名前が同じになる場合、および、ビュー内の列に派生元と異なる列名を付ける場合に限られます。 列名は、SELECT ステートメントで指定することもできます。  
  
 *column* を指定しなかった場合は、SELECT ステートメントの列の名前が、このビューの列名として使用されます。  
  
> [!NOTE]  
>  ビューの列では、基底となるデータのソースにかかわらず、列名に対する権限は、CREATE VIEW ステートメントまたは ALTER VIEW ステートメントを超えて適用されます。 たとえば、CREATE VIEW ステートメントで **SalesOrderID** 列に対して権限が与えられる場合、ALTER VIEW ステートメントでは **SalesOrderID** 列に **OrderRef** などの異なる列名を付けることができ、その後も **SalesOrderID** を使用してビューに関連付けられた権限を保持します。  
  
 AS  
 ビューが行う動作を指定します。  
  
 *select_statement*  
 ビューを定義している SELECT ステートメントを指定します。 このステートメントでは、複数のテーブルや他のビューを使用することもできます。 作成するビューの SELECT 句で参照されるオブジェクトから選択するには、適切な権限が必要です。  
  
 ビューは、1 つの特定のテーブルの行と列の簡単なサブセットである必要はありません。 複雑な SELECT 句で、複数のテーブルまたは他のビューを使用するビューを作成することもできます。  
  
 インデックス付きビュー定義では、SELECT ステートメントは、単一のテーブル ステートメントまたは複数のテーブルの JOIN (集計は省略可) にする必要があります。  
  
 ビュー定義の SELECT 句に、次を含めることはできません。  
  
-   ORDER BY 句。ただし、SELECT ステートメントの選択リストに TOP 句が同時に含まれている場合は例外です。  
  
    > [!IMPORTANT]  
    >  ORDER BY 句は、ビュー定義において TOP 句または OFFSET 句によって返される行を決定する場合にのみ使用されます。 クエリ自体にも ORDER BY を指定しない限り、ビューをクエリしたときに、ORDER BY 句で順序どおりの結果が得られるかどうかは保証されません。  
  
-   INTO キーワード  
  
-   OPTION 句。  
  
-   一時テーブルまたはテーブル変数の参照。  
  
 *select_statement* では SELECT ステートメントが使用されるため、FROM 句で指定される \<join_hint> および \<table_hint> の各ヒントを使用できます。 詳細については、「[FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)」および「[SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)」を参照してください。 
  
 *select_statement* では、関数と複数の SELECT ステートメントを UNION または UNION ALL で区切って使用できます。  
  
 CHECK OPTION  
 ビューに対して実行されるすべてのデータ変更ステートメントについて、*select_statement* 内で設定される条件に従うよう強制します。 ビューを介して行を変更する場合は、WITH CHECK OPTION を使用すると、変更がコミットされた後もビューを介して確実にデータを表示できます。  
  
> [!NOTE]
>  CHECK OPTION は、ビューを介して行われた更新にのみ適用されます。 ビューの基になるテーブルに対して直接実行された更新には適用されません。  
  
 ENCRYPTION  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 CREATE VIEW ステートメントのテキストが含まれている [sys.syscomments](../../relational-databases/system-compatibility-views/sys-syscomments-transact-sql.md) のエントリを暗号化します。 WITH ENCRYPTION を使用すると、そのビューを SQL Server レプリケーションの一部として発行できなくなります。  
  
 SCHEMABINDING  
 基になるテーブルのスキーマにビューをバインドします。 SCHEMABINDING を指定した場合、ベース テーブルに対してビュー定義に影響を与えるような変更を行うことはできません。 まずビュー定義を変更または削除して、変更するテーブルとの依存関係を解消する必要があります。 SCHEMABINDING を使用する場合は、*select_statement* に、参照されるテーブル、ビュー、またはユーザー定義関数の名前として、2 つの部分から構成される名前 (_schema_ **.** _object_) を指定する必要があります。 参照されるオブジェクトは、すべて同じデータベース内にあることが必要です。  
  
 SCHEMABINDING 句を指定して作成したビューに参加しているビューまたはテーブルは、そのビューが削除または変更されてスキーマ バインドがなくならない限り削除できません。 スキーマ バインドが残っている場合は、[!INCLUDE[ssDE](../../includes/ssde-md.md)]からエラーが返されます。 また、ビュー定義に影響を与える ALTER TABLE ステートメントを、スキーマ バインドを持つビューに参加しているテーブルに対して実行しても、ステートメントは失敗します。  
  
 VIEW_METADATA  
 ビューを参照するクエリ用にブラウズ モード メタデータが要求されている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスは、DB-Library、ODBC、および OLE DB API に対して、ベース テーブルではなくビューに関するメタデータ情報を返します。 ブラウズ モード メタデータは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスからクライアント側 API に返される追加のメタデータです。 クライアント側 API ではこのメタデータによって、更新可能なクライアント側カーソルを実装できます。 ブラウズ モード メタデータには、結果セット内の列が属するベース テーブルの情報が含まれています。  
  
 VIEW_METADATA で作成したビューの場合、ブラウズ モード メタデータでは結果セット内のビューの列の説明で、ベース テーブル名ではなくビュー名が返されます。  
  
 WITH VIEW_METADATA を使用してビューを作成するとき、**timestamp** 列を除くすべての列は、ビューに INSTEAD OF INSERT または INSTEAD OF UPDATE トリガーが含まれている場合に更新可能になります。 更新可能なビューの詳細については、「解説」を参照してください。  
  
## <a name="remarks"></a>解説  
 ビューは現在のデータベースでのみ作成できます。 CREATE VIEW は、クエリ バッチの最初のステートメントであることが必要です。 1 つのビューで保持できる列の数は最大 1,024 です。  
  
 ビューからクエリを実行すると、[!INCLUDE[ssDE](../../includes/ssde-md.md)] では、ステートメントで参照されているデータベース オブジェクトがすべて存在すること、データベース オブジェクトがステートメントのコンテキストで有効であること、およびデータ変更ステートメントがデータの整合性規則に違反していないことが確認されます。 確認に失敗すると、エラー メッセージが返されます。 確認に成功すると、そのアクションが、基になるテーブルに対するアクションに変換されます。  
  
 削除されたテーブル (またはビュー) に従属しているビューを使用すると、[!INCLUDE[ssDE](../../includes/ssde-md.md)] ではエラー メッセージが返されます。 テーブルの構造が以前のベース テーブルから変わっていなければ、削除されたテーブルやビューの代わりになる、新しいテーブルまたはビューを作成すると、ビューは再び使用可能になります。 新しいテーブルまたはビューの構造が変化した場合は、ビューを削除し、再作成する必要があります。  
  
 ビューが SCHEMABINDING 句を使用して作成したものでない場合、ビューの基になっているオブジェクトに対して、ビューの定義に影響するような変更が行われた際には、[sp_refreshview](../../relational-databases/system-stored-procedures/sp-refreshview-transact-sql.md) を実行します。 それ以外の場合は、ビューのクエリ時に、予期しない結果が生成される可能性があります。  
  
 ビューが作成されると、ビューについての情報がカタログ ビュー [sys.views](../../relational-databases/system-catalog-views/sys-views-transact-sql.md)、[sys.columns](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)、[sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md) に格納されます。 CREATE VIEW ステートメントのテキストは、[sys.sql_modules](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md) カタログ ビューに格納されます。  
  
 **numeric** または **float** 型の式で定義されたビューのインデックスを使用するクエリでは、ビューのインデックスを使用しない類似したクエリとは、異なる結果セットが返されます。 この相違は、基になるテーブルに対して INSERT、DELETE、または UPDATE アクションを行った場合の丸め誤差によって発生することがあります。  
  
 ビューを作成すると、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、SET QUOTED_IDENTIFIER と SET ANSI_NULLS の設定が保存されます。 これらの元の設定は、ビューの使用時に、ビューの解析で使用されます。 したがって、ビューへアクセスするとき、SET QUOTED_IDENTIFIER と SET ANSI_NULLS のクライアント セッションの設定によってビュー定義に影響が生じることはありません。  
  
## <a name="updatable-views"></a>更新可能なビュー  
 次の条件を満たす場合、ビューから基になるベース テーブルのデータを変更できます。  
  
-   UPDATE、INSERT、DELETE ステートメントなどの変更で、1 つのベース テーブルのみの列を参照している。  
  
-   ビューにある変更対象の列が、テーブルの列内にある基になるデータを直接参照している。 ただし次のような方法では、列は派生されません。  
  
    -   集計関数:AVG、COUNT、SUM、MIN、MAX、GROUPING、STDEV、STDEVP、VAR、VARP。  
  
    -   計算。 他の列を使用する式を基にして列を計算することはできません。 計算に相当する set 演算子 UNION、UNION ALL、CROSSJOIN、EXCEPT、INTERSECT を使用して形成される列も計算されません。  
  
-   変更される列が、GROUP BY、HAVING、または DISTINCT 句の影響を受けない。  
  
-   ビューの *select_statement* 内で、TOP が WITH CHECK OPTION 句と共に使用されていない。  
  
 以前の制限は、ビュー自体に適用されるのと同様に、ビューの FROM 句のサブクエリにも適用されます。 通常は、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、ビュー定義からベース テーブルへの変更を正確にトレースできる必要があります。 詳細については、「[ビューを使用したデータ変更](../../relational-databases/views/modify-data-through-a-view.md)」を参照してください。  
  
 以前の制約によってビューから直接データを変更できない場合は、次の方法を試してください。  
  
-   **INSTEAD OF トリガー**  
  
     ビューに INSTEAD OF トリガーを作成して、そのビューを更新可能にできます。 INSTEAD OF トリガーは、そのトリガーが定義されているデータ変更ステートメントの代わりに実行されます。 このトリガーで、データ変更ステートメントを処理するために発生させる必要がある、一連のアクションを指定できます。 したがって、特定のデータ変更ステートメント (INSERT、UPDATE、または DELETE) に対する INSTEAD OF トリガーがビューに存在する場合は、そのステートメントを介して対応するビューを更新できます。 INSTEAD OF トリガーの詳細については、「[DML トリガー](../../relational-databases/triggers/dml-triggers.md)」を参照してください。  
  
-   **パーティション ビュー**  
  
     ビューがパーティション ビューである場合は、特定の制限の範囲内で更新可能です。 必要に応じて、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、ローカル パーティション ビュー (参加しているすべてのテーブルとビューが同じ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンス上にあるビュー) と分散パーティション ビュー (ビュー内の少なくとも 1 つのテーブルが別のサーバーまたはリモート サーバー上にあるビュー) が区別されます。  
  
## <a name="partitioned-views"></a>パーティション ビュー  
 パーティション ビューは、同じ構造のメンバー テーブルの UNION ALL によって定義されるビューです。ただし、これらのメンバー テーブルは、同じ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンス内、または連合データベース サーバーと呼ばれる [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サーバー インスタンスの独立したグループ内に、複数のテーブルとして個別に格納されます。  
  
> [!NOTE]  
>  1 台のサーバーに対してローカルでデータをパーティション分割する方法としては、パーティション テーブルをお勧めします。 詳細については、「 [Partitioned Tables and Indexes](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」を参照してください。  
  
 パーティション分割構成の設計時には、各パーティションに所属するデータを明確にする必要があります。 たとえば、`Customers` テーブルのデータは、3 つのサーバー位置にある 3 つのメンバー テーブル、つまり、`Server1` の `Customers_33`、`Server2` の `Customers_66`、`Server3` の `Customers_99` に配分されます。  
  
 `Server1` のパーティション ビューは次のように定義されます。  
  
```sql
--Partitioned view as defined on Server1  
CREATE VIEW Customers  
AS  
--Select from local member table.  
SELECT *  
FROM CompanyData.dbo.Customers_33  
UNION ALL  
--Select from member table on Server2.  
SELECT *  
FROM Server2.CompanyData.dbo.Customers_66  
UNION ALL  
--Select from member table on Server3.  
SELECT *  
FROM Server3.CompanyData.dbo.Customers_99;  
```  
  
 一般に、次の形式の場合、ビューをパーティション ビューといいます。  
  
```syntaxsql
SELECT <select_list1>  
FROM T1  
UNION ALL  
SELECT <select_list2>  
FROM T2  
UNION ALL  
...  
SELECT <select_listn>  
FROM Tn;  
```  
  
## <a name="conditions-for-creating-partitioned-views"></a>パーティション ビューを作成する条件  
  
1.  選択リスト (`list`  
  
    -   ビュー定義の列リストで、メンバー テーブルのすべての列を選択します。  
  
    -   それぞれの `select list` の同じ位置にある列は、照合順序も含めて同じ型にします。 一般的に UNION の場合のように、列が暗黙的に変換される型にするだけでは十分ではありません。  
  
         また、すべての選択リストの同じ位置に、少なくとも 1 つの列 (たとえば `<col>`) が指定されている必要があります。 `<col>` は、メンバー テーブル `T1, ..., Tn` の `<col>` にそれぞれ CHECK 制約 `C1, ..., Cn` が定義されるように定義します。  
  
         テーブル `C1` の制約 `T1` は、次の形式で定義する必要があります。  
  
        ```syntaxsql
        C1 ::= < simple_interval > [ OR < simple_interval > OR ...]  
        < simple_interval > :: =   
        < col > { < | > | \<= | >= | = < value >}   
        | < col > BETWEEN < value1 > AND < value2 >  
        | < col > IN ( value_list )  
        | < col > { > | >= } < value1 > AND  
        < col > { < | <= } < value2 >  
        ```
  
    -   制約は、連続せずかつ重複しない間隔を持つ制約セットを形成されるよう、`<col>` に指定したすべての値が `C1, ..., Cn` の制約の 1 つにのみ該当するような形式にする必要があります。 連続しない制約が定義されている列 `<col>` は、パーティション分割列と呼ばれます。 パーティション分割列は、基になるテーブルではそれぞれ異なる名前が付いている場合があります。 前に示したパーティション分割列の条件を満たすには、パーティション分割列に対して制約が有効かつ信頼されている必要があります。 制約が無効の場合は、ALTER TABLE の CHECK CONSTRAINT *constraint_name* オプションを使用して制約チェックを再度有効にし、WITH CHECK オプションを使用して制約を検証します。  
  
         次は、有効な制約のセットの例です。  
  
        ```syntaxsql
        { [col < 10], [col between 11 and 20] , [col > 20] }  
        { [col between 11 and 20], [col between 21 and 30], [col between 31 and 100] }  
        ```  
  
    -   選択リストで同じ列を複数回使用することはできません。  
  
2.  パーティション分割列  
  
    -   パーティション分割列は、テーブルの PRIMARY KEY の一部です。  
  
    -   計算列、ID 列、既定の列、または **timestamp** 列に対して指定することはできません。  
  
    -   メンバー テーブルの同じ列に複数の制約が定義されている場合、データベース エンジンではすべての制約が無視され、ビューがパーティション ビューであるかどうかを判断する際にそれらの制約は考慮されません。 パーティション ビューの条件を満たすには、パーティション分割列にパーティション分割制約を 1 つだけにします。  
  
    -   パーティション分割列の更新可能性に制限はありません。  
  
3.  メンバー テーブルまたは基になるテーブル `T1, ..., Tn`  
  
    -   テーブルは、ローカル テーブルまたは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が実行されている他のコンピューター上のテーブルのいずれかになります。他のコンピューター上のテーブルの場合は、4 つの要素で構成される名前か、OPENDATASOURCE ベースまたは OPENROWSET ベースの名前で参照されます。 OPENDATASOURCE および OPENROWSET の構文では、テーブル名は指定できますが、パススルー クエリは指定できません。 詳細については、「[OPENDATASOURCE &#40;Transact-SQL&#41;](../../t-sql/functions/opendatasource-transact-sql.md)」および「[OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)」を参照してください。  
  
         1 つ以上のメンバー テーブルがリモートにある場合、そのビューは分散パーティション ビューと呼ばれ、さらに条件が適用されます。 これらについてはこのセクションの後で説明します。  
  
    -   UNION ALL ステートメントで組み合わせるテーブルのセットに、同じテーブルを 2 回表示することはできません。  
  
    -   メンバー テーブルでは、テーブル内の計算列上にインデックスを作成することはできません。  
  
    -   メンバー テーブルでは、すべての PRIMARY KEY 制約が同じ数の列に対して与えられます。  
  
    -   ビューのすべてのメンバー テーブルには、同じ ANSI PADDING 設定が与えられます。 これは、**sp_configure** の **user options** オプションまたは SET ステートメントを使用して設定できます。  
  
## <a name="conditions-for-modifying-data-in-partitioned-views"></a>パーティション ビューのデータを変更する条件  
 パーティション ビューのデータを変更するステートメントには、次の制限が適用されます。  
  
-   INSERT ステートメントでは、基になるメンバー テーブルで列に DEFAULT 制約があるか、NULL 値が許可されている場合でも、ビューのすべての列に値を提供します。 メンバー テーブルの列に DEFAULT 定義がある場合、ステートメントで明示的に DEFAULT キーワードを使用することはできません。  
  
-   パーティション分割列に挿入する値は、基になる制約の少なくとも 1 つを満たします。満たしていない場合、INSERT アクションは制約違反で失敗します。  
  
-   UPDATE ステートメントでは、対応するメンバー テーブルで列の DEFAULT 値が定義されている場合でも、SET 句の値として DEFAULT キーワードを指定することはできません。  
  
-   1 つまたは複数のメンバー テーブルで ID 列になっているビューの列は、INSERT ステートメントまたは UPDATE ステートメントを使用して変更することはできません。  
  
-   メンバー テーブルのいずれかに **timestamp** 型の列が含まれている場合、データを INSERT または UPDATE ステートメントで変更することはできません。  
  
-   メンバー テーブルに、トリガー、ON UPDATE CASCADE/SET NULL/SET DEFAULT 制約、または ON DELETE CASCADE/SET NULL/SET DEFAULT 制約が含まれている場合、ビューを変更することはできません。  
  
-   ステートメント内に、同じビューまたはいずれかのメンバー テーブルとの自己結合が指定された場合、パーティション ビューに対して INSERT、UPDATE、DELETE アクションは許可されません。  
  
-   パーティション ビューへのデータの一括インポートは、**bcp**、または BULK INSERT ステートメントや INSERT ...SELECT * FROM OPENROWSET(BULK...) ステートメントを使用してデータを一括インポートする場合のフォーマット ファイルの使用方法を示します。 しかし、[INSERT](../../t-sql/statements/insert-transact-sql.md) ステートメントを使用することにより、パーティション ビューに複数の行を挿入できます。  
  
    > [!NOTE]  
    >  パーティション ビューを更新するには、ユーザーはメンバー テーブルに対して INSERT、UPDATE、DELETE の各権限を持っている必要があります。  
  
## <a name="additional-conditions-for-distributed-partitioned-views"></a>分散パーティション ビューの追加条件  
 分散パーティション ビュー (1 つまたは複数のメンバー テーブルがリモートの場合) では、次の追加条件が適用されます。  
  
-   更新によって影響を受けるすべてのノードを超えて原子性を保証するため、分散トランザクションが起動されます。  
  
-   INSERT、UPDATE、または DELETE ステートメントが動作するには、XACT_ABORT SET オプションを ON に設定します。  
  
-   パーティション ビューで参照されるリモート テーブルの **smallmoney** 型の列は、**money** としてマップされます。 このため、ローカル テーブルの対応する列 (選択リストの同じ順番にある列) も、**money** 型であることが必要です。  
  
-   データベース互換性レベル 110 以上では、パーティション ビューで参照されるリモート テーブルの **smalldatetime** 型の列は、**smalldatetime** としてマップされます。 ローカル テーブルの対応する列 (選択リストの同じ順番にある列) は、**smalldatetime** であることが必要です。 この動作は、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] から変更されています。以前のバージョンでは、パーティション ビューで参照されるリモート テーブルの **smalldatetime** 型の列は **datetime** としてマップされ、ローカル テーブルの対応する列は **datetime** 型であることが必要でした。 詳細については、「[ALTER DATABASE 互換性レベル &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)」を参照してください。  
  
-   パーティション ビューのリンク サーバーは、ループバック リンク サーバーにすることはできません。 ループバック リンク サーバーは、同じ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスを指すリンク サーバーです。  
  
 更新可能なパーティション ビューとリモート テーブルに関連する INSERT、UPDATE、DELETE アクションでは、SET ROWCOUNT オプションの設定は無視されます。  
  
 メンバー テーブルとビュー定義が準備されると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のクエリ オプティマイザーでは、クエリを効率的に使用してメンバー テーブルからデータにアクセスする高度なプランが構築されます。 クエリ プロセッサでは、CHECK 制約の定義を使用して、キー値の分布が複数のメンバー テーブルにマップされます。 ユーザーがクエリを実行すると、クエリ プロセッサでは、マップと WHERE 句で指定した値とが比較され、メンバー サーバー間のデータ転送が最も少なくなる実行プランが構築されます。 したがって、いくつかのメンバー テーブルがリモート サーバー上にある場合でも、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスでは転送の対象となる分散データの量が最も少なくなる方法で分散クエリが解決されます。  
  
## <a name="considerations-for-replication"></a>レプリケーションに関する注意点  
 レプリケーションに関係するメンバー テーブルのパーティション ビューを作成するには、次の点に注意してください。  
  
-   基になるテーブルが、更新サブスクライバーとのマージ レプリケーションまたはトランザクション レプリケーションに関係する場合、選択リストには **uniqueidentifier** 列も含まれるようにします。 
  
     パーティション ビューに対する INSERT 操作では、**uniqueidentifier** 列の NEWID() 値を指定する必要があります。 **uniqueidentifier** 列に対する UPDATE 操作では、DEFAULT キーワードを使用できないので、NEWID() を値として指定する必要があります。  
  
-   ビューを使用した更新のレプリケーションは、2 つの異なるデータベースでのテーブルのレプリケーションと同じです。つまり、テーブルは異なるレプリケーション エージェントで管理されるため、更新の順序は保証されません。  
  
## <a name="permissions"></a>アクセス許可  
 データベースの CREATE VIEW 権限と、ビューが作成されているスキーマの ALTER 権限が必要です。  
  
## <a name="examples"></a>例  

次の例では、AdventureWorks 2012 または AdventureWorksDW データベースを使用します。  

### <a name="a-using-a-simple-create-view"></a>A. シンプルな CREATE VIEW を使用する  
 次の例では、単純な `SELECT` ステートメントを使用してビューを作成します。 簡易ビューは、列の組み合わせを頻繁にクエリする場合に便利です。 このビューのデータは、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `HumanResources.Employee` テーブルと `Person.Person` テーブルから取得されます。 このデータには、[!INCLUDE[ssSampleDBCoFull](../../includes/sssampledbcofull-md.md)] の従業員の名前と採用日の情報が含まれています。 従業員の勤続祝いの担当者用にビューを作成することができますが、この担当者はテーブルのすべてのデータにアクセスできるわけではありません。  
  
```sql
CREATE VIEW hiredate_view  
AS   
SELECT p.FirstName, p.LastName, e.BusinessEntityID, e.HireDate  
FROM HumanResources.Employee e   
JOIN Person.Person AS p ON e.BusinessEntityID = p.BusinessEntityID ;  
GO  
  
```  
  
### <a name="b-using-with-encryption"></a>B. 暗号化を使用する  
 次の例では、`WITH ENCRYPTION` オプションを使用して、計算列、名前変更された列、複数列を表示します。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降と [!INCLUDE[ssSDS](../../includes/sssds-md.md)]。  
  
```sql
CREATE VIEW Purchasing.PurchaseOrderReject  
WITH ENCRYPTION  
AS  
SELECT PurchaseOrderID, ReceivedQty, RejectedQty,   
    RejectedQty / ReceivedQty AS RejectRatio, DueDate  
FROM Purchasing.PurchaseOrderDetail  
WHERE RejectedQty / ReceivedQty > 0  
AND DueDate > CONVERT(DATETIME,'20010630',101) ;  
GO  
  
```  
  
### <a name="c-using-with-check-option"></a>C. WITH CHECK OPTION を使用する  
 次の例では、5 つのテーブルを参照する `SeattleOnly` というビューを表示し、シアトル在住の従業員だけにデータ変更を許可します。  
  
```sql
CREATE VIEW dbo.SeattleOnly  
AS  
SELECT p.LastName, p.FirstName, e.JobTitle, a.City, sp.StateProvinceCode  
FROM HumanResources.Employee e  
INNER JOIN Person.Person p  
ON p.BusinessEntityID = e.BusinessEntityID  
    INNER JOIN Person.BusinessEntityAddress bea   
    ON bea.BusinessEntityID = e.BusinessEntityID   
    INNER JOIN Person.Address a   
    ON a.AddressID = bea.AddressID  
    INNER JOIN Person.StateProvince sp   
    ON sp.StateProvinceID = a.StateProvinceID  
WHERE a.City = 'Seattle'  
WITH CHECK OPTION ;  
GO  
```  
  
### <a name="d-using-built-in-functions-within-a-view"></a>D. ビュー内の組み込み関数を使用する  
 次の例では、組み込み関数を含むビュー定義を示しています。 関数を使用するときには、派生列に列名を指定する必要があります。  
  
```sql
CREATE VIEW Sales.SalesPersonPerform  
AS  
SELECT TOP (100) SalesPersonID, SUM(TotalDue) AS TotalSales  
FROM Sales.SalesOrderHeader  
WHERE OrderDate > CONVERT(DATETIME,'20001231',101)  
GROUP BY SalesPersonID;  
GO  

```  
  
### <a name="e-using-partitioned-data"></a>E. パーティション分割されたデータを使用する  
 次の例では、`SUPPLY1`、`SUPPLY2`、`SUPPLY3`、`SUPPLY4` というテーブルを使用します。 これらのテーブルは、異なる国や地域にある 4 か所のオフィスの仕入れ先テーブルに対応しています。  
  
```sql
--Create the tables and insert the values.  
CREATE TABLE dbo.SUPPLY1 (  
supplyID INT PRIMARY KEY CHECK (supplyID BETWEEN 1 and 150),  
supplier CHAR(50)  
);  
CREATE TABLE dbo.SUPPLY2 (  
supplyID INT PRIMARY KEY CHECK (supplyID BETWEEN 151 and 300),  
supplier CHAR(50)  
);  
CREATE TABLE dbo.SUPPLY3 (  
supplyID INT PRIMARY KEY CHECK (supplyID BETWEEN 301 and 450),  
supplier CHAR(50)  
);  
CREATE TABLE dbo.SUPPLY4 (  
supplyID INT PRIMARY KEY CHECK (supplyID BETWEEN 451 and 600),  
supplier CHAR(50)  
);  
GO  
--Create the view that combines all supplier tables.  
CREATE VIEW dbo.all_supplier_view  
WITH SCHEMABINDING  
AS  
SELECT supplyID, supplier  
  FROM dbo.SUPPLY1  
UNION ALL  
SELECT supplyID, supplier  
  FROM dbo.SUPPLY2  
UNION ALL  
SELECT supplyID, supplier  
  FROM dbo.SUPPLY3  
UNION ALL  
SELECT supplyID, supplier  
  FROM dbo.SUPPLY4;  
GO
INSERT dbo.all_supplier_view VALUES ('1', 'CaliforniaCorp'), ('5', 'BraziliaLtd')    
, ('231', 'FarEast'), ('280', 'NZ')  
, ('321', 'EuroGroup'), ('442', 'UKArchip')  
, ('475', 'India'), ('521', 'Afrique');  
GO  
```  
  
## <a name="examples-sssdw-and-sspdw"></a>例: [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="f-creating-a-simple-view"></a>F. 単純なビューを作成する  
 次の例では、ソース テーブルから一部の列のみを選択することで、ビューを作成します。  
  
```sql
CREATE VIEW DimEmployeeBirthDates AS  
SELECT FirstName, LastName, BirthDate   
FROM DimEmployee;  
```  
  
### <a name="g-create-a-view-by-joining-two-tables"></a>G. 2 つのテーブルを結合することでビューを作成する  
 次の例では、`OUTER JOIN` と共に `SELECT` ステートメントを使用することで、ビューを作成します。 結合クエリの結果によって、ビューが設定されます。  
  
```sql
CREATE VIEW view1  
AS 
SELECT fis.CustomerKey, fis.ProductKey, fis.OrderDateKey, 
  fis.SalesTerritoryKey, dst.SalesTerritoryRegion  
FROM FactInternetSales AS fis   
LEFT OUTER JOIN DimSalesTerritory AS dst   
ON (fis.SalesTerritoryKey=dst.SalesTerritoryKey);  
```  
  
## <a name="see-also"></a>参照  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [ALTER VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/alter-view-transact-sql.md)   
 [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)   
 [DROP VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/drop-view-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [ストアド プロシージャの作成](../../relational-databases/stored-procedures/create-a-stored-procedure.md)   
 [sys.dm_sql_referenced_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referenced-entities-transact-sql.md)   
 [sys.dm_sql_referencing_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referencing-entities-transact-sql.md)   
 [sp_help &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-transact-sql.md)   
 [sp_helptext &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptext-transact-sql.md)   
 [sp_refreshview &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-refreshview-transact-sql.md)   
 [sp_rename &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-rename-transact-sql.md)   
 [sys.views &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-views-transact-sql.md)   
 [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  

