---
description: 列セットの使用
title: 列セットの使用 | Microsoft Docs
ms.custom: ''
ms.date: 07/30/2015
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- sparse columns, column sets
- column sets
ms.assetid: a4f9de95-dc8f-4ad8-b957-137e32bfa500
author: stevestein
ms.author: sstein
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 8585b1274536856fc1b54a7cdb4cf96700693304
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747692"
---
# <a name="use-column-sets"></a>列セットの使用
[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

  スパース列を使用するテーブルでは、テーブル内のすべてのスパース列を返すための列セットを指定できます。 列セットは、型指定されていない XML 表記であり、テーブルのすべてのスパース列を 1 つにまとめて構造化した出力です。 列セットは、テーブルに物理的に保存されないという点で計算列に似ています。 計算列と異なるのは、列セットが直接更新できる点です。  
  
 テーブルに多数の列があり、その個別操作が煩雑である場合は、列セットの使用を検討してください。 多数の列を含むテーブルで列セットを使用してデータの選択や挿入を行うと、アプリケーションのパフォーマンスがある程度向上する場合があります。 ただし、テーブル内の列に多数のインデックスが定義されている場合は、列セットのパフォーマンスが低下することがあります。 これは、実行プランに必要なメモリ容量が増えるためです。  
  
 列セットを定義するには、[CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md) または [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md) ステートメントで *<column_set_name>* FOR ALL_SPARSE_COLUMNS キーワードを使用します。  
  
## <a name="guidelines-for-using-column-sets"></a>列セットの使用に関するガイドライン  
 列セットを使用する場合は、次のガイドラインを考慮してください。  
  
-   スパース列と列セットは、同じステートメントの一部として追加できます。  
  
-   既にスパース列が含まれているテーブルには列セットを追加できません。  
  
-   列セットは変更できません。 列セットを変更するには、スパース列と列セットを削除してから再作成する必要があります。  
  
-   スパース列を含んでいないテーブルには列セットを追加できます。 テーブルに後からスパース列が追加された場合は、列セット内に配置されます。  
  
-   1 つのテーブルに作成できる列セットは 1 つだけです。  
  
-   列セットはオプションであり、スパース列を使用するために必須ではありません。  
  
-   列セットには制約や既定値を定義できません。  
  
-   計算列に列セットの列を含めることはできません。  
  
-   列セットを含むテーブルでは、分散クエリがサポートされません。  
  
-   レプリケーションでは列セットはサポートされません。  
  
-   変更データ キャプチャでは列セットはサポートされません。  
  
-   列セットは、どの種類のインデックスにも組み込むことができません。 これには、XML インデックス、フルテキスト インデックス、およびインデックス付きビューが含まれます。 列セットをインデックスの付加列として追加することはできません。  
  
-   フィルター選択されたインデックスまたはフィルター選択された統計情報のフィルター式では、列セットを使用できません。  
  
-   ビューに含まれている列セットは、そのビューで XML 列として表示されます。  
  
-   インデックス付きビューの定義に列セットを含めることはできません。  
  
-   列セットを含むテーブルを含んでいるパーティション ビューは、そのビューでスパース列が名前で指定されている場合に更新できます。 列セットを参照しているパーティション ビューは更新できません。  
  
-   列セットを参照するクエリ通知は許可されません。  
  
-   XML データのサイズは 2 GB に制限されます。 行内の NULL ではないすべてのスパース列のデータの合計がこの制限を超えると、クエリまたは DML 操作でエラーが発生します。  
  
-   COLUMNS_UPDATED 関数から返されるデータの詳細については、「 [スパース列の使用](../../relational-databases/tables/use-sparse-columns.md)」を参照してください。  
  
## <a name="guidelines-for-selecting-data-from-a-column-set"></a>列セットからのデータの選択に関するガイドライン  
 列セットからデータを選択する場合は、次のガイドラインを考慮してください。  
  
-   列セットは、概念的には更新可能な XML 計算列の一種であり、基になる一連のリレーショナル列を単一の XML 表記に集約します。 列セットは、ALL_SPARSE_COLUMNS プロパティのみをサポートします。 このプロパティは、特定の行のすべてのスパース列から NULL 以外の値を集計するために使用されます。  
  
-   [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] テーブル エディターでは、列セットは編集可能な XML フィールドとして表示されます。 列セットは次の形式で定義します。  
  
    ```  
    <column_name_1>value1</column_name_1><column_name_2>value2</column_name_2>...  
    ```  
  
     列セットの値の例を次に示します。  
  
    -   `<sparseProp1>10</sparseProp1><sparseProp3>20</sparseProp3>`  
  
    -   `<DocTitle>Bicycle Parts List</DocTitle><Region>West</Region>`  
  
-   NULL 値が格納されたスパース列は、列セットの XML 表記で省略されます。  
  
> [!WARNING]  
>  列セットを追加すると、`SELECT *` クエリの動作が変わります。 クエリから列セットが XML 列として返されるようになり、個々のスパース列は返されなくなります。 スキーマ設計者とソフトウェア開発者は、既存のアプリケーションが動作不能とならないように注意する必要があります。  
  
## <a name="inserting-or-modifying-data-in-a-column-set"></a>列セットのデータの挿入または変更  
 スパース列のデータ操作を実行するには、個々の列の名前を使用するか、または列セットの名前を参照し、列セットの XML 形式を使用して列セットの値を指定します。 XML 列では、スパース列を任意の順序で配置できます。  
  
 XML 列セットを使用してスパース列の値を挿入または更新すると、基になるスパース列に挿入された値が **xml** データ型から暗黙的に変換されます。 数値列の場合、数値列の XML に含まれる空白値は空白の文字列に変換されます。 これにより、次の例のように、数値列にゼロが挿入されます。  
  
```sql  
CREATE TABLE t (i int SPARSE, cs xml column_set FOR ALL_SPARSE_COLUMNS);  
GO  
INSERT t(cs) VALUES ('<i/>');  
GO  
SELECT i FROM t;  
GO  
```  
  
 この例では、 `i`列に値が指定されていませんが、値 `0` が挿入されています。  
  
## <a name="using-the-sql_variant-data-type"></a>sql_variant データ型の使用  
 **sql_variant** データ型には、 **int**、 **char**、 **date** などの種類の異なる複数のデータ型を格納できます。 列セットは、 **sql_variant** 値に関連付けられている小数点以下桁数、有効桁数、ロケール情報などのデータ型情報を、生成された XML 列に属性として出力します。 これらの属性を、列セットでの挿入または更新操作の入力としてカスタム生成 XML ステートメントで指定しようとすると、一部の属性が必須となり、一部の属性に既定値が割り当てられます。 値が指定されなかったときにサーバーで生成されるデータ型と既定値の一覧を次の表に示します。  
  
|データ型|localeID*|sqlCompareOptions|sqlCollationVersion|SqlSortId|最大長|有効桁数|スケール|  
|---------------|----------------|-----------------------|-------------------------|---------------|--------------------|---------------|-----------|  
|**char**、 **varchar**、 **binary**|-1|'Default'|0|0|8000|なし**|適用なし|  
|**nvarchar**|-1|'Default'|0|0|4000|適用なし|適用なし|  
|**decimal**、 **float**、 **real**|適用なし|適用なし|適用なし|適用なし|適用なし|18|0|  
|**int**、 **bigint**、 **tinyint**、 **smallint**|適用なし|適用なし|適用なし|適用なし|適用なし|適用なし|適用なし|  
|**datetime2**|適用なし|適用なし|適用なし|適用なし|適用なし|適用なし|7|  
|**datetimeoffset**|適用なし|適用なし|適用なし|適用なし|適用なし|適用なし|7|  
|**datetime**、 **date**、 **smalldatetime**|適用なし|適用なし|適用なし|適用なし|適用なし|適用なし|適用なし|  
|**money**、 **smallmoney**|適用なし|適用なし|適用なし|適用なし|適用なし|適用なし|適用なし|  
|**time**|適用なし|適用なし|適用なし|適用なし|適用なし|適用なし|7|  
  
 \*  localeID -1 は既定のロケールを意味します。 英語ロケールは 1033 です。  
  
 **  なし = 列セットでの選択操作時に、対象の属性に対して値が出力されません。 挿入または更新操作で列セットに対して指定された XML 表記で呼び出し元がこの属性に値を指定すると、エラーが発生します。  
  
## <a name="security"></a>セキュリティ  
 列セットのセキュリティ モデルは、テーブルと列の間に介在するセキュリティ モデルと同じように機能します。 列セットはミニテーブルとして視覚化できます。選択操作は、このミニテーブルに対する SELECT * 操作と同様です。 ただし、列セットとスパース列は、厳密なコンテナーではなくグループ化の関係にあります。 セキュリティ モデルでは、列セットの列に対してセキュリティがチェックされ、基になるスパース列で DENY 操作が適用されます。 セキュリティ モデルには、これ以外に次のような特性があります。  
  
-   列セットの列に対し、テーブル内の他の列と同様に、セキュリティ権限を与えたり取り消したりすることができます。  
  
-   列セットの列に対して SELECT、INSERT、UPDATE、DELETE、および REFERENCES 権限の GRANT または REVOKE を実行しても、そのセットの基になるメンバー列には反映されません。 これは、列セットの列を使用する場合にのみ当てはまります。 列セットに対する DENY 権限は、テーブルの基になるスパース列に反映されます。  
  
-   列セットの列に対して SELECT、INSERT、UPDATE、DELETE の各ステートメントを実行するには、ユーザーはその列セットの列についての対応する権限、およびテーブル内のすべてのスパース列についての対応する権限を持っている必要があります。 列セットはテーブル内のすべてのスパース列を表すため、すべてのスパース列についての権限が必要になります。これには、変更の対象ではないスパース列も含まれます。  
  
-   スパース列または列セットに対して REVOKE ステートメントを実行すると、セキュリティは、既定でその親オブジェクトのセキュリティに設定されます。  
  
## <a name="examples"></a>例  
 次の例では、ドキュメント テーブルに `DocID` 列と `Title`列のセットが共通で含まれています。 製造グループは、すべての製造ドキュメントに `ProductionSpecification` 列と `ProductionLocation` 列を必要とします。 マーケティング グループは、マーケティング ドキュメントに `MarketingSurveyGroup` 列を必要とします。  
  
### <a name="a-creating-a-table-that-has-a-column-set"></a>A. 列セットを含むテーブルを作成する  
 次の例では、スパース列を使用し、列セット `SpecialPurposeColumns`を含むテーブルを作成します。 例では、テーブルに 2 つの行を挿入した後に、テーブルからデータを選択します。  
  
> [!NOTE]  
>  このテーブルは、簡単に表示して確認できるように、5 つの列のみで構成されています。  
  
```sql  
USE AdventureWorks2012;  
GO  
  
CREATE TABLE DocumentStoreWithColumnSet  
    (DocID int PRIMARY KEY,  
     Title varchar(200) NOT NULL,  
     ProductionSpecification varchar(20) SPARSE NULL,  
     ProductionLocation smallint SPARSE NULL,  
     MarketingSurveyGroup varchar(20) SPARSE NULL,  
     MarketingProgramID int SPARSE NULL,  
     SpecialPurposeColumns XML COLUMN_SET FOR ALL_SPARSE_COLUMNS);  
GO  
```  
  
### <a name="b-inserting-data-to-a-table-by-using-the-names-of-the-sparse-columns"></a>B. スパース列の名前を使用してテーブルにデータを挿入する  
 次の例では、例 A で作成したテーブルに 2 つの行を挿入します。列セットは参照せず、スパース列の名前を使用します。  
  
```sql  
INSERT DocumentStoreWithColumnSet (DocID, Title, ProductionSpecification, ProductionLocation)  
VALUES (1, 'Tire Spec 1', 'AXZZ217', 27);  
GO  
  
INSERT DocumentStoreWithColumnSet (DocID, Title, MarketingSurveyGroup)  
VALUES (2, 'Survey 2142', 'Men 25 - 35');  
GO  
```  
  
### <a name="c-inserting-data-to-a-table-by-using-the-name-of-the-column-set"></a>C. 列セットの名前を使用してテーブルにデータを挿入する  
 次の例では、例 A で作成したテーブルに 3 つ目の行を挿入します。今回はスパース列の名前を使用しません。 代わりに、列セットの名前を使用し、挿入操作によって 4 つのスパース列の 2 つに XML 形式で値を挿入します。  
  
```sql  
INSERT DocumentStoreWithColumnSet (DocID, Title, SpecialPurposeColumns)  
VALUES (3, 'Tire Spec 2', '<ProductionSpecification>AXW9R411</ProductionSpecification><ProductionLocation>38</ProductionLocation>');  
GO  
```  
  
### <a name="d-observing-the-results-of-a-column-set-when-select--is-used"></a>D. SELECT * を使用したときの列セットの結果を確認する  
 次の例では、列セットを含むテーブルからすべての列を選択します。 この操作で、スパース列の値の組み合わせを含んだ XML 列が返されます。 スパース列が個別に返されることはありません。  
  
```sql  
SELECT DocID, Title, SpecialPurposeColumns FROM DocumentStoreWithColumnSet ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 DocID  Title        SpecialPurposeColumns  
 1      Tire Spec 1  <ProductionSpecification>AXZZ217</ProductionSpecification><ProductionLocation>27</ProductionLocation>  
 2      Survey 2142  <MarketingSurveyGroup>Men 25 - 35</MarketingSurveyGroup>  
 3      Tire Spec 2  <ProductionSpecification>AXW9R411</ProductionSpecification><ProductionLocation>38</ProductionLocation> 
 ```
  
### <a name="e-observing-the-results-of-selecting-the-column-set-by-name"></a>E. 列セットを名前で選択した場合の結果を確認する  
 製造部門にマーケティング データは必要ないため、この例では `WHERE` 句を追加して出力を制限します。 例では、列セットの名前が使用されています。  
  
```sql  
SELECT DocID, Title, SpecialPurposeColumns  
FROM DocumentStoreWithColumnSet  
WHERE ProductionSpecification IS NOT NULL ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 DocID  Title        SpecialPurposeColumns  
 1      Tire Spec 1  <ProductionSpecification>AXZZ217</ProductionSpecification><ProductionLocation>27</ProductionLocation>  
 3      Tire Spec 2  <ProductionSpecification>AXW9R411</ProductionSpecification><ProductionLocation>38</ProductionLocation>  
 ```
 
### <a name="f-observing-the-results-of-selecting-sparse-columns-by-name"></a>F. スパース列を名前で選択した場合の結果を確認する  
 テーブルに列セットが含まれている場合でも、次の例のように個々の列名を使用してテーブルに対してクエリを実行できます。  
  
```sql  
SELECT DocID, Title, ProductionSpecification, ProductionLocation   
FROM DocumentStoreWithColumnSet  
WHERE ProductionSpecification IS NOT NULL ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 DocID  Title        ProductionSpecification ProductionLocation`  
 1      Tire Spec 1  AXZZ217                 27`  
 3      Tire Spec 2  AXW9R411                38`  
 ```
 
### <a name="g-updating-a-table-by-using-a-column-set"></a>G. 列セットを使用してテーブルを更新する  
 次の例では、3 番目のレコードを、その行で使用されている 2 つのスパース列の新しい値で更新します。  
  
```sql  
UPDATE DocumentStoreWithColumnSet  
SET SpecialPurposeColumns = '<ProductionSpecification>ZZ285W</ProductionSpecification><ProductionLocation>38</ProductionLocation>'  
WHERE DocID = 3 ;  
GO  
```  
  
> [!IMPORTANT]  
>  UPDATE ステートメントで列セットを使用すると、テーブル内のすべてのスパース列が更新されます。 参照されていないスパース列は、NULL に更新されます。  
  
 次の例では、3 番目のレコードを更新しますが、作成された 2 つの列の 1 つにのみ値を指定します。 2 番目の列である `ProductionLocation` は `UPDATE` ステートメントに含まれず、NULL に更新されます。  
  
```sql  
UPDATE DocumentStoreWithColumnSet  
SET SpecialPurposeColumns = '<ProductionSpecification>ZZ285W</ProductionSpecification>'  
WHERE DocID = 3 ;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [スパース列の使用](../../relational-databases/tables/use-sparse-columns.md)  
  
  
