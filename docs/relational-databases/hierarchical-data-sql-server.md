---
description: 階層データ (SQL Server)
title: 階層データ (SQL Server) | Microsoft Docs
ms.custom: ''
ms.date: 10/04/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- hierarchies [SQL Server], tables to support
- hierarchyid [Database Engine], concepts
- hierarchical tables [Database Engine]
- SqlHierarchyId
- hierarchyid [Database Engine]
- hierarchical queries [SQL Server], using hierarchyid data type
ms.assetid: 19aefa9a-fbc2-4b22-92cf-67b8bb01671c
author: rothja
ms.author: jroth
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 7fcefb597fe97e90bfda78fe934c83646414c23f
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611182"
---
# <a name="hierarchical-data-sql-server"></a>階層データ (SQL Server)

[!INCLUDE [SQL Server Azure SQL Database](../includes/applies-to-version/sql-asdb.md)]

  組み込み **hierarchyid** データ型を使用すると、階層データの格納とクエリが容易になります。 **hierarchyid** は、最も一般的な階層データであるツリー構造を表すために最適化されています。  
  
 階層データは、階層リレーションシップで相互に関連付けられたデータ アイテムのセットとして定義されます。 あるデータ アイテムが別のアイテムの親となる場合は、そこに階層リレーションシップが存在します。 データベースに一般的に格納される階層データの例を次に示します。  
  
-   組織構造  
  
-   ファイル システム  
  
-   プロジェクト内のタスクのセット  
  
-   言語の用語の分類  
  
-   Web ページ間のリンクのグラフ  
  
 階層構造を持つテーブルを作成したり、別の場所に格納されているデータの階層構造を表したりするには、 [hierarchyid](../t-sql/data-types/hierarchyid-data-type-method-reference.md) を使用します。 階層データのクエリや管理を実行するには、 [!INCLUDE[tsql](../includes/tsql-md.md)] の [hierarchyid 関数](../t-sql/data-types/hierarchyid-data-type-method-reference.md) を使用します。  
  
##  <a name="key-properties-of-hierarchyid"></a><a name="keyprops"></a> hierarchyid の主要な特性  
 値、 **hierarchyid** データ型は、ツリー階層内の位置を表します。 値を **hierarchyid** 、次のプロパティがあります。  
  
-   非常にコンパクト  
  
     *n* 個のノードを持つツリー内の、1 つのノードを表すために必要な平均ビット数は、平均ファンアウト (ノードあたりの子の平均数) によって決まります。 ファンアウトが小さい場合 (0 ～ 7)、サイズは約 6\*logA *n* ビットです (A は平均ファンアウト)。 平均ファンアウトが 6 レベルで、100,000 人から成る組織階層の場合、1 つのノードには約 38 ビットが必要です。 格納時には、これが 40 ビット (5 バイト) に切り上げられます。  
  
-   深さ優先順で比較  
  
     **a** と **b** の 2 つの **hierarchyid** 値がある場合、**a<b** は、ツリーの深さ優先検査において a が b の前に来ることを意味します。 インデックス **hierarchyid** に深さ優先順では、データ型があり、深さ優先検査で近接ノードは互いに近い格納します。 たとえば、あるレコードの子は、そのレコードに隣接して格納されます。  
  
-   任意の挿入および削除のサポート  
  
     [GetDescendant](../t-sql/data-types/getdescendant-database-engine.md) メソッドを使用すると、指定したノードの右側や左側、または任意の 2 つの兄弟間に、いつでも兄弟を生成できます。 階層に対して任意の数のノードを挿入または削除しても、比較の特性は維持されます。 ほとんどの挿入や削除では、コンパクトさも維持されます。 ただし、2 ノード間に挿入した場合は、hierarchyid 値のコンパクトさがやや失われます。  
  
  
##  <a name="limitations-of-hierarchyid"></a><a name="limits"></a> hierarchyid の制限事項  
 **hierarchyid** データ型には、以下の制限事項があります。  
  
-   **hierarchyid** 型の列が自動的にツリーを表すことはありません。 行と行の間に必要なリレーションシップが反映されるよう、 **hierarchyid** 値を生成して割り当てるのは、アプリケーションの役割です。 アプリケーションによっては、別のテーブルに定義されている階層内の位置を示す **hierarchyid** 型の列を持つ場合もあります。  
  
-   **hierarchyid** 値の生成と割り当てにおいて、コンカレンシーを管理するのはアプリケーションの役割です。 アプリケーションで一意キー制約を使用したり、独自のロジックで一意性を適用したりしない限り、列内の **hierarchyid** 値の一意性は保証されません。  
  
-   **hierarchyid** 値で表される階層リレーションシップは、外部キー リレーションシップとは適用方法が異なります。 階層リレーションシップでは、A に子 B があるとき、A だけを削除し、存在しないレコードに対するリレーションシップを B が引き続き保持することも可能であり、これが適切な場合もあります。 この動作を許容しない場合は、親を削除する前に、アプリケーションで子孫に対するクエリを実行する必要があります。  
  
  
##  <a name="when-to-use-alternatives-to-hierarchyid"></a><a name="alternatives"></a> hierarchyid に代わる方法を使用する場合  
 **hierarchyid** を使用せずに階層データを表すためには、次の 2 つの方法があります。  
  
-   親/子  
  
-   XML  
  
 通常、これらの方法よりも **hierarchyid** の方が優れています。 しかし、次のような状況では、これらの代替方法を使用した方がよい場合があります。  
  
### <a name="parentchild"></a>親/子  
 親/子の方法を使用すると、各行に親への参照が含まれます。 次のテーブルでは、親/子リレーションシップにある親と子の行を含めるための、一般的なテーブルを定義します。  
  
```sql
USE AdventureWorks2012 ;  
GO  
  
CREATE TABLE ParentChildOrg  
   (  
    BusinessEntityID int PRIMARY KEY,  
    ManagerId int REFERENCES ParentChildOrg(BusinessEntityID),  
    EmployeeName nvarchar(50)   
   ) ;  
GO  
```  
  
 一般的な操作に関する親/子と **hierarchyid** の比較  
  
-   サブツリーのクエリは、 **hierarchyid** を使用した方がはるかに高速です。  
  
-   直接の子孫のクエリは、 **hierarchyid** を使用するとわずかに遅くなります。  
  
-   非リーフ ノードの移動は、 **hierarchyid** を使用すると遅くなります。  
  
-   非リーフ ノードを挿入する場合、およびリーフ ノードを挿入または移動する場合も、 **hierarchyid** を使用する場合と同様に複雑になります。  
  
 次の条件に当てはまるときは、親/子を使用した方がよい場合があります。  
  
-   キーのサイズが非常に重要なとき。 同じノード数に対して、 **hierarchyid** 値が整数系 (**smallint**、 **int**、 **bigint**) の値以上であるとき。 これが、ごくまれに親/子を使用する場合の唯一の理由です。親/子構造の使用時に必要な共通テーブル式よりも、 **hierarchyid** の方が、I/O の局所性と CPU の複雑さにおいてはるかに優れているためです。  
  
-   階層の複数セクションにわたるクエリをめったに実行しないとき。 つまり、通常のクエリが、階層内の単一ポイントのみを対象とするとき。 このようなケースでは、同じ場所への配置は重要でありません。 たとえば、個々の従業員の給与処理のみに組織テーブルを使用する場合、親/子の方が優れています。  
  
-   非リーフ サブツリーが頻繁に移動し、かつパフォーマンスが非常に重要なとき。 親/子表現では、階層内の行の場所を変更すると、1 行のみが影響を受けます。 **hierarchyid** 使用時に行の場所を変更すると、 *n* 行が影響を受けます ( *n* は移動されるサブツリー内のノード数)。  
  
     非リーフ サブツリーが頻繁に移動し、かつパフォーマンスが重要だが、ほとんどの移動が正しく定義された階層レベルで行われるときは、上位レベルと下位レベルを 2 つの階層に分割することを検討してください。 こうすると、すべての移動が上位階層のリーフ レベルになります。 たとえば、サービスによってホストされている Web サイトの階層があるとします。 サイトには、階層状に配置された多くのページが含まれています。 ホストされているサイトは、サイト階層内の他の場所に移動される可能性がありますが、下位ページの配置が変更されることはまれです。 これは、次のように表すことができます。  
  
    ```sql
    CREATE TABLE HostedSites   
       (  
        SiteId hierarchyid, PageId hierarchyid  
       ) ;  
    GO  
    ```  
  
  
### <a name="xml"></a>XML  
 XML ドキュメントはツリーです。このため、XML データ型の 1 つのインスタンスで、完全な階層を表すことができます。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] で XML インデックスを作成する際は、階層内の位置を表す **hierarchyid** 値が内部で使用されます。  
  
 次のすべての条件に当てはまるときは、XML データ型を使用した方がよい場合があります。  
  
-   完全な階層が常に格納および取得されるとき。  
  
-   アプリケーションによって XML 形式でデータが消費されるとき。  
  
-   述語検索が非常に限られており、かつパフォーマンスが重要でないとき。  
  
 たとえば、アプリケーションで複数の組織を追跡し、完全な組織階層を常に格納および取得し、かつ単一の組織に対するクエリを実行しないときには、次の形式のテーブルが効果的な場合があります。  
  
```sql
CREATE TABLE XMLOrg   
    (  
    Orgid int,  
    Orgdata xml  
    ) ;  
GO  
```  
  
  
##  <a name="indexing-strategies-for-hierarchical-data"></a><a name="indexing"></a> 階層データのインデックス作成方法  
 階層データのインデックスを作成する方法には、次の 2 つがあります。  
  
-   **深さ優先**  
  
     深さ優先のインデックスで、サブツリー内の行が相互に近接して格納されます。 たとえば、ある管理者の管理責任下にあるすべての従業員が、管理者のレコードの近くに格納されます。  
  
     深さ優先のインデックスでは、ノードのサブツリー内のすべてのノードが同じ場所に配置されます。 このため、"このフォルダーとそのサブフォルダーにあるファイルをすべて検索する" など、サブツリーに関するクエリに応答するには、深さ優先インデックスが効率的です。  
  
-   **幅優先**  
  
     幅優先インデックスでは、階層の各レベルの行が一緒に格納されます。 たとえば、同一の管理者に直属する従業員のレコードが、相互に近接して格納されます。  
  
     幅優先のインデックスでは、ノードの直接の子すべてが同じ場所に配置されます。 このため、"この管理者に直属するすべての従業員を検索する" など、直下の子に関するクエリに応答するには、幅優先インデックスが効率的です。  
  
 深さ優先、幅優先、またはこれらの両方を使用するか、また、どちらをクラスター化キーとするか (該当する場合) は、上記の種類のクエリの相対的重要度と、SELECT 操作と DML 操作の相対的重要度によって決まります。 インデックス作成方法の詳細な例については、「 [チュートリアル : hierarchyid データ型の使用](../relational-databases/tables/tutorial-using-the-hierarchyid-data-type.md)」を参照してください。  
  
  
### <a name="creating-indexes"></a>インデックスの作成  
 幅優先順を作成するには、GetLevel() メソッドを使用します。 次の例では、幅優先と深さ優先の両方のインデックスを作成します。  
  
```sql
USE AdventureWorks2012 ;   -- wmimof
GO  
  
CREATE TABLE Organization  
   (  
    BusinessEntityID hierarchyid,  
    OrgLevel as BusinessEntityID.GetLevel(),   
    EmployeeName nvarchar(50) NOT NULL  
   ) ;  
GO  
  
CREATE CLUSTERED INDEX Org_Breadth_First   
    ON Organization(OrgLevel,BusinessEntityID) ;  
GO  
  
CREATE UNIQUE INDEX Org_Depth_First   
    ON Organization(BusinessEntityID) ;  
GO  
```  
  
  
## <a name="examples"></a>例  
  
### <a name="simple-example"></a>簡単な例  
 作業を簡単に開始できるよう意図的に簡潔化された例を次に示します。 最初に、geography データを保持するテーブルを作成します。  
  
```sql
CREATE TABLE SimpleDemo  
(
    Level hierarchyid NOT NULL,  
    Location nvarchar(30) NOT NULL,  
    LocationType nvarchar(9) NULL
);
```  
  
 次に、いくつかの大陸、国、州、および都市のデータを挿入します。  
  
```sql
INSERT SimpleDemo  
    VALUES   
('/1/', 'Europe', 'Continent'),  
('/2/', 'South America', 'Continent'),  
('/1/1/', 'France', 'Country'),  
('/1/1/1/', 'Paris', 'City'),  
('/1/2/1/', 'Madrid', 'City'),  
('/1/2/', 'Spain', 'Country'),  
('/3/', 'Antarctica', 'Continent'),  
('/2/1/', 'Brazil', 'Country'),  
('/2/1/1/', 'Brasilia', 'City'),  
('/2/1/2/', 'Bahia', 'State'),  
('/2/1/2/1/', 'Salvador', 'City'),  
('/3/1/', 'McMurdo Station', 'City');  
```  
  
 Level データを理解しやすいテキスト値に変換する列を追加するデータを選択します。 また、このクエリは、 **hierarchyid** データ型で結果を並べ替えます。  
  
```sql
SELECT CAST(Level AS nvarchar(100)) AS [Converted Level], *   
    FROM SimpleDemo ORDER BY Level;  
```  
  
 [!INCLUDE[ssResult](../includes/ssresult-md.md)]  
  
```  
Converted Level  Level     Location         LocationType  
/1/              0x58      Europe           Continent  
/1/1/            0x5AC0    France           Country  
/1/1/1/          0x5AD6    Paris            City  
/1/2/            0x5B40    Spain            Country  
/1/2/1/          0x5B56    Madrid           City  
/2/              0x68      South America    Continent  
/2/1/            0x6AC0    Brazil           Country  
/2/1/1/          0x6AD6    Brasilia         City  
/2/1/2/          0x6ADA    Bahia            State  
/2/1/2/1/        0x6ADAB0  Salvador         City  
/3/              0x78      Antarctica       Continent  
/3/1/            0x7AC0    McMurdo Station  City  
```  
  
 内部に矛盾があるが、階層の構造は有効であることに注意してください。 州は Bahia だけです。 これは、都市 Brasilia のピアとして階層に表示されます。 同様に、McMurdo Station に親の国はありません。 ユーザーは、この階層がそれぞれの用途に適しているかどうかを判断する必要があります。  
  
 別の行を追加し、結果を選択します。  
  
```sql
INSERT SimpleDemo  
    VALUES ('/1/3/1/', 'Kyoto', 'City'), ('/1/3/1/', 'London', 'City');  
SELECT CAST(Level AS nvarchar(100)) AS [Converted Level], * FROM SimpleDemo ORDER BY Level;  
```  
  
 これにより、考えられる問題が示されます。 Kyoto は、親レベル `/1/3/1/` がなくても、レベル `/1/3/`として挿入できます。 London と Kyoto の両方に同じ値の **hierarchyid** があります。 ここでもユーザーはこの階層がそれぞれの用途に適しているかどうかを判断して、それぞれの用途に適していない値をブロックする必要があります。  
  
 また、このテーブルは、階層 `'/'`の上部を使用していません。 すべての大陸に共通する親が存在しないため、省略されています。 地球を追加することで 1 を追加できます。  
  
```sql
INSERT SimpleDemo  
    VALUES ('/', 'Earth', 'Planet');  
```  
  
##  <a name="related-tasks"></a><a name="tasks"></a> 関連タスク  
  
###  <a name="migrating-from-parentchild-to-hierarchyid"></a><a name="migrating"></a> 親/子から hierarchyid への移行  
 現在、ほとんどのツリーは親/子を使用して表されます。 親/子構造から **hierarchyid** を使用したテーブルに移行する最も簡単な方法は、一時列または一時テーブルを使用して、階層の各レベルのノード数を追跡する方法です。 親/子テーブルの移行例については、「 [チュートリアル : hierarchyid データ型の使用](../relational-databases/tables/tutorial-using-the-hierarchyid-data-type.md)」のレッスン 1 を参照してください。  
  
  
###  <a name="managing-a-tree-using-hierarchyid"></a><a name="BKMK_ManagingTrees"></a> hierarchyid を使用したツリーの管理  
 必ずしも **hierarchyid** 列がツリーを表すとは限りませんが、アプリケーションでは簡単に表すようにできます。  
  
-   新しい値を生成する場合は、次のいずれかの操作を行います。  
  
    -   親行の最後の子の番号を追跡します。  
  
    -   最後の子を計算します。 効率的に計算するには、幅優先のインデックスが必要です。  
  
-   列の一意のインデックスを作成することで (たとえばクラスター化キーの一部として)、一意性を適用します。 一意の値が挿入されるようにするには、次のいずれかの操作を行います。  
  
    -   一意キー違反エラーを検出して、再試行します。  
  
    -   それぞれの新しい子ノードの一意性を特定し、シリアル化可能なトランザクションの一部として挿入します。  
  
  
#### <a name="example-using-error-detection"></a>エラー検出の使用例  
 次の例のサンプル コードは、新しい子 **EmployeeId** 値を計算してキー違反を検出し、 **INS_EMP** マーカーに戻って新しい行の **EmployeeId** 値を再計算します。  
  
```sql
USE AdventureWorks ;  
GO  
  
CREATE TABLE Org_T1  
   (  
    EmployeeId hierarchyid PRIMARY KEY,  
    OrgLevel AS EmployeeId.GetLevel(),  
    EmployeeName nvarchar(50)   
   ) ;  
GO  
  
CREATE INDEX Org_BreadthFirst ON Org_T1(OrgLevel, EmployeeId);
GO  
  
CREATE PROCEDURE AddEmp(@mgrid hierarchyid, @EmpName nvarchar(50) )   
AS  
BEGIN  
    DECLARE @last_child hierarchyid;
INS_EMP:   
    SELECT @last_child = MAX(EmployeeId) FROM Org_T1   
        WHERE EmployeeId.GetAncestor(1) = @mgrid;
    INSERT INTO Org_T1 (EmployeeId, EmployeeName)  
        SELECT @mgrid.GetDescendant(@last_child, NULL), @EmpName;
-- On error, return to INS_EMP to recompute @last_child  
IF @@error <> 0 GOTO INS_EMP   
END ;  
GO  
```  
  
  
#### <a name="example-using-a-serializable-transaction"></a>シリアル化可能なトランザクションの使用例  
 **Org_BreadthFirst** インデックスによって、**\@last_child** が範囲シークを使用するかどうかを判断できるようになります。 アプリケーションでチェックできるその他のエラーの場合だけでなく、挿入後の重複キー違反は、同じ ID を持つ複数の従業員を追加しようとしていることを示します。したがって、 **\@last_child** を再計算する必要があります。 次のコードでは、シリアル化可能なトランザクション内の新しいノード値が計算されます。  
  
```sql
CREATE TABLE Org_T2  
    (  
    EmployeeId hierarchyid PRIMARY KEY,  
    LastChild hierarchyid,   
    EmployeeName nvarchar(50)   
    ) ;  
GO  
  
CREATE PROCEDURE AddEmp(@mgrid hierarchyid, @EmpName nvarchar(50))   
AS  
BEGIN  
DECLARE @last_child hierarchyid  
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE  
BEGIN TRANSACTION   
  
SELECT @last_child  =  EmployeeId.GetDescendant(LastChild,NULL)
FROM Org_T2
WHERE EmployeeId = @mgrid

UPDATE Org_T2 SET LastChild = @last_child  WHERE EmployeeId = @mgrid

INSERT Org_T2 (EmployeeId, EmployeeName)   
    VALUES(@last_child, @EmpName)  
COMMIT  
END ;  
```  
  
 次のコードは、テーブルに 3 つの行を挿入して、その結果を返します。  
  
```sql
INSERT Org_T2 (EmployeeId, EmployeeName)   
    VALUES(hierarchyid::GetRoot(), 'David') ;  
GO  
AddEmp 0x , 'Sariya'  
GO  
AddEmp 0x58 , 'Mary'  
GO  
SELECT * FROM Org_T2  
```  
  
 [!INCLUDE[ssResult](../includes/ssresult-md.md)]  
  
```  
EmployeeId LastChild EmployeeName  
---------- --------- ------------  
0x        0x58       David  
0x58      0x5AC0     Sariya  
0x5AC0    NULL       Mary  
```  
  
  
###  <a name="enforcing-a-tree"></a><a name="BKMK_EnforcingTrees"></a> ツリーの強制  
 上記の例では、アプリケーションでツリーが保持されるようにする方法を示しています。 制約を使用してツリーを強制するには、主キー ID を参照する外部キー制約を使用して、各ノードの親を定義する計算列を作成します。  
  
```sql
CREATE TABLE Org_T3  
(  
   EmployeeId hierarchyid PRIMARY KEY,  
   ParentId AS EmployeeId.GetAncestor(1) PERSISTED    
      REFERENCES Org_T3(EmployeeId),  
   LastChild hierarchyid,   
   EmployeeName nvarchar(50)  
)  
GO  
```  
  
 リレーションシップを適用するこの方法は、階層ツリーを保持するための信頼がないコードにテーブルへの直接 DML アクセス権がある場合に適しています。 ただし、このメソッドでは、すべての DML 操作で制約をチェックする必要があるため、パフォーマンスが低下することがあります。  
  
  
###  <a name="finding-ancestors-by-using-the-clr"></a><a name="findclr"></a> CLR を使用した先祖の検索  
 階層内の 2 つのノードに関連する一般的な操作は、最下位の共通の先祖を見つけることです。 **hierarchyid** 型は [!INCLUDE[tsql](../includes/tsql-md.md)] と CLR の両方で使用できるので、どちらでもこの操作を記述できます。 パフォーマンスが向上するため、CLR の使用をお勧めします。  
  
 次の CLR コードを使用すると、先祖を一覧表示し、最下位の共通の先祖を見つけることができます。  
  
```csharp
using System;  
using System.Collections;  
using System.Text;  
using Microsoft.SqlServer.Server;  // SqlFunction Attribute
using Microsoft.SqlServer.Types;   // SqlHierarchyId
  
public partial class HierarchyId_Operations  
{  
    [SqlFunction(FillRowMethodName = "FillRow_ListAncestors")]
    public static IEnumerable ListAncestors(SqlHierarchyId h)
    {  
        while (!h.IsNull)  
        {  
            yield return (h);  
            h = h.GetAncestor(1);  
        }  
    }  
    public static void FillRow_ListAncestors(
        Object obj,
        out SqlHierarchyId ancestor
        )
    {  
        ancestor = (SqlHierarchyId)obj;  
    }  
  
    public static HierarchyId CommonAncestor(
        SqlHierarchyId h1,
        HierarchyId h2
        )  
    {  
        while (!h1.IsDescendantOf(h2))  
            h1 = h1.GetAncestor(1);  
  
        return h1;  
    }  
}  
```  
  
 以下の [!INCLUDE[tsql](../includes/tsql-md.md)] の例で **ListAncestor** メソッドおよび **CommonAncestor** メソッドを使用するには、DLL をビルドし、次のようなコードを実行して [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] の **HierarchyId_Operations** アセンブリを作成します。  
  
```sql
CREATE ASSEMBLY HierarchyId_Operations   
    FROM '<path to DLL>\ListAncestors.dll';
GO  
```  
  
  
###  <a name="listing-ancestors"></a><a name="ancestors"></a> 先祖の一覧表示  
 ノードの先祖のリストの作成は、組織内での位置を表示するなどの一般的な操作です。 これを実行するには、上で定義した **HierarchyId_Operations** クラスを使用して、テーブル値関数を使用するのが 1 つの方法です。  
  
 [!INCLUDE[tsql](../includes/tsql-md.md)]の使用  
  
```sql
CREATE FUNCTION ListAncestors (@node hierarchyid)  
RETURNS TABLE (node hierarchyid)  
AS  
EXTERNAL NAME HierarchyId_Operations.HierarchyId_Operations.ListAncestors  
GO  
```  
  
 使用例:  
  
```sql
DECLARE @h hierarchyid  
SELECT @h = OrgNode   
FROM HumanResources.EmployeeDemo    
WHERE LoginID = 'adventure-works\janice0' -- /1/1/5/2/  
  
SELECT LoginID, OrgNode.ToString() AS LogicalNode  
FROM HumanResources.EmployeeDemo AS ED  
JOIN ListAncestors(@h) AS A   
   ON ED.OrgNode = A.Node  
GO  
```  
  
  
###  <a name="finding-the-lowest-common-ancestor"></a><a name="lowestcommon"></a> 最下位の共通の先祖の検索  
 上で定義した **HierarchyId_Operations** クラスを使用して、次の [!INCLUDE[tsql](../includes/tsql-md.md)] 関数を作成し、階層内の 2 つのノードに関連する最下位の共通の先祖を見つけます。  
  
```sql
CREATE FUNCTION CommonAncestor (@node1 hierarchyid, @node2 hierarchyid)  
RETURNS hierarchyid  
AS  
EXTERNAL NAME HierarchyId_Operations.HierarchyId_Operations.CommonAncestor  
GO  
```  
  
 使用例:  
  
```sql
DECLARE @h1 hierarchyid, @h2 hierarchyid;
  
SELECT @h1 = OrgNode   
FROM  HumanResources.EmployeeDemo   
WHERE LoginID = 'adventure-works\jossef0'; -- Node is /1/1/3/  
  
SELECT @h2 = OrgNode   
FROM HumanResources.EmployeeDemo    
WHERE LoginID = 'adventure-works\janice0'; -- Node is /1/1/5/2/  
  
SELECT OrgNode.ToString() AS LogicalNode, LoginID   
FROM HumanResources.EmployeeDemo    
WHERE OrgNode = dbo.CommonAncestor(@h1, @h2) ;  
```  
  
 結果ノードは /1/1/  
  
  
###  <a name="moving-subtrees"></a><a name="BKMK_MovingSubtrees"></a> サブツリーの移動  
 もう 1 つの一般的な操作は、サブツリーの移動です。 次の手順では、 **\@oldMgr** のサブツリーを取得し、それ ( **\@oldMgr** を含む) を **\@newMgr** のサブツリーにしています。  
  
```sql
CREATE PROCEDURE MoveOrg(@oldMgr nvarchar(256), @newMgr nvarchar(256) )  
AS  
BEGIN  
DECLARE @nold hierarchyid, @nnew hierarchyid  
SELECT @nold = OrgNode FROM HumanResources.EmployeeDemo WHERE LoginID = @oldMgr ;  
  
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE  
BEGIN TRANSACTION  
SELECT @nnew = OrgNode FROM HumanResources.EmployeeDemo WHERE LoginID = @newMgr ;  
  
SELECT @nnew = @nnew.GetDescendant(max(OrgNode), NULL)   
FROM HumanResources.EmployeeDemo WHERE OrgNode.GetAncestor(1)=@nnew ;  
  
UPDATE HumanResources.EmployeeDemo    
SET OrgNode = OrgNode.GetReparentedValue(@nold, @nnew)  
WHERE OrgNode.IsDescendantOf(@nold) = 1 ;  
  
COMMIT TRANSACTION;
END ;  
GO  
```  
  
  
## <a name="see-also"></a>関連項目  
 [hierarchyid データ型メソッド リファレンス](../t-sql/data-types/hierarchyid-data-type-method-reference.md)   
 [チュートリアル:hierarchyid データ型の使用](../relational-databases/tables/tutorial-using-the-hierarchyid-data-type.md)   
 [hierarchyid &#40;Transact-SQL&#41;](../t-sql/data-types/hierarchyid-data-type-method-reference.md)  
  
