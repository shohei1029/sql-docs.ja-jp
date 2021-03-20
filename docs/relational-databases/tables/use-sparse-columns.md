---
description: スパース列の使用
title: スパース列の使用 | Microsoft Docs
ms.custom: ''
ms.date: 03/22/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- sparse columns, described
- null columns
- sparse columns
ms.assetid: ea7ddb87-f50b-46b6-9f5a-acab222a2ede
author: stevestein
ms.author: sstein
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: e8e5fd6a1e7d6a67295dcbce634bbff5761caff7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755802"
---
# <a name="use-sparse-columns"></a>スパース列の使用

[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

  スパース列は、NULL 値用にストレージが最適化されている通常の列です。 スパース列によって、NULL 以外の値を取得するためのオーバーヘッドは増大しますが、NULL 値に必要となる領域は削減されます。 少なくとも 20 ～ 40% の領域を削減できる場合は、スパース列の使用を検討してください。 スパース列および列セットを定義するには、 [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md) または [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md) ステートメントを使用します。  
  
 スパース列は、列セットおよびフィルター選択されたインデックスと併用できます。  
  
-   列セット  
  
     INSERT、UPDATE、DELETE の各ステートメントは、スパース列を名前で参照できます。 ただし、テーブルのすべてのスパース列を 1 つの XML 列に結合して表示および操作することもできます。 この列を列セットと呼びます。 列セットの詳細については、「 [列セットの使用](../../relational-databases/tables/use-column-sets.md)」を参照してください。  
  
-   フィルター選択されたインデックス  
  
     スパース列は、NULL 値の行が多数あるため、フィルター選択されたインデックスに特に適しています。 スパース列でフィルター選択されたインデックスを使用すると、値が設定された行にのみインデックスを作成できます。 これにより、より小さく効率的なインデックスが作成されます。 詳細については、「 [Create Filtered Indexes](../../relational-databases/indexes/create-filtered-indexes.md)」を参照してください。  
  
 スパース列とフィルター選択されたインデックスを併用することで、 [!INCLUDE[winSPServ](../../includes/winspserv-md.md)]などのアプリケーションでは、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]を使用して多数のユーザー定義プロパティを効率よく格納およびアクセスできます。  
  
## <a name="properties-of-sparse-columns"></a>スパース列のプロパティ  
 スパース列には次の特性があります。  
  
-   [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] は、列定義で SPARSE キーワードを使用して、その列での値のストレージを最適化します。 このため、列値が NULL である行がテーブルに含まれている場合、その値はストレージを必要としません。  
  
-   スパース列を含んでいるテーブルのカタログ ビューは、一般的なテーブルのものと同じです。 sys.columns カタログ ビューは、テーブル内の各列の行で構成され、列セットが定義されていればそれを含んでいます。  
  
-   スパース列は、論理テーブルではなく、ストレージ層のプロパティです。 したがって、SELECT...INTO ステートメントは、スパース列のプロパティを新しいテーブルにコピーしません。  
  
-   COLUMNS_UPDATED 関数は、DML アクションで更新されたすべての列を示す **varbinary** 値を返します。 COLUMNS_UPDATED 関数から返されるビットは次のとおりです。  
  
    -   スパース列が明示的に更新された場合は、そのスパース列の対応するビットが 1 に設定され、列セットのビットが 1 に設定されます。  
  
    -   列セットが明示的に更新された場合は、列セットのビットが 1 に設定され、そのテーブル内のすべてのスパース列のビットが 1 に設定されます。  
  
    -   挿入操作では、すべてのビットが 1 に設定されます。  
  
     列セットの詳細については、「 [列セットの使用](../../relational-databases/tables/use-column-sets.md)」を参照してください。  
  
 次のデータ型は SPARSE と指定できません。  
  
|||  
|-|-|  
|**geography**|**text**|  
|**geometry**|**timestamp**|  
|**image**|**ユーザー定義データ型**|  
|**ntext**||  
  
## <a name="estimated-space-savings-by-data-type"></a>領域を節約するためのデータ型別推定値  
 スパース列は、同一データが SPARSE とマークされていない場合に比べて、NULL 以外の値により多くのストレージ領域を必要とします。 次の表は、各データ型の使用領域を示したものです。 **NULL の比率** 列は、正味 40% の領域を節約するために必要な NULL データの割合を示します。  
  
 **固定長データ型**  
  
|データ型|非スパース バイト数|スパース バイト数|NULL の比率|  
|---------------|---------------------|------------------|---------------------|  
|**bit**|0.125|5|98%|  
|**tinyint**|1|5|86%|  
|**smallint**|2|6|76%|  
|**int**|4|8|64%|  
|**bigint**|8|12|52%|  
|**real**|4|8|64%|  
|**float**|8|12|52%|  
|**smallmoney**|4|8|64%|  
|**money**|8|12|52%|  
|**smalldatetime**|4|8|64%|  
|**datetime**|8|12|52%|  
|**uniqueidentifier**|16|20|43%|  
|**date**|3|7|69%|  
  
 **Precision-Dependent-Length データ型**  
  
|データ型|非スパース バイト数|スパース バイト数|NULL の比率|  
|---------------|---------------------|------------------|---------------------|  
|**datetime2(0)**|6|10|57%|  
|**datetime2(7)**|8|12|52%|  
|**time(0)**|3|7|69%|  
|**time(7)**|5|9|60%|  
|**datetimetoffset(0)**|8|12|52%|  
|**datetimetoffset (7)**|10|14|49%|  
|**decimal/numeric(1,s)**|5|9|60%|  
|**decimal/numeric(38,s)**|17|21|42%|  
|**vardecimal(p,s)**|控えめな推定値として **decimal** 型を使用してください。|||  
  
 **Data-Dependent-Length データ型**  
  
|データ型|非スパース バイト数|スパース バイト数|NULL の比率|  
|---------------|---------------------|------------------|---------------------|  
|**sql_variant**|基になるデータ型で異なります。|||  
|**varchar** または **char**|2*|4*|60%|  
|**nvarchar** または **nchar**|2*|4*+|60%|  
|**varbinary** または **binary**|2*|4*|60%|  
|**xml**|2*|4*|60%|  
|**hierarchyid**|2*|4*|60%|  
  
 * 長さは、型に含まれているデータの平均に 2 バイトまたは 4 バイトを加えた長さに等しくなります。  
  
## <a name="in-memory-overhead-required-for-updates-to-sparse-columns"></a>スパース列の更新に必要なインメモリ オーバーヘッド  
 スパース列を含むテーブルをデザインする場合は、行を更新するときにテーブル内の NULL 以外のスパース列ごとに追加の 2 バイトが必要になることに注意してください。 この追加のメモリ要件により、(このメモリ オーバーヘッドを含む) 合計行サイズが 8019 を超え、列を行外に出すことができないと、更新がエラー 576 で予期せずに失敗する可能性があります。  
  
 たとえば、bigint 型の 600 個のスパース列を持つテーブルがあるとします。 571 個の NULL 以外の列がある場合、ディスク上の合計サイズは 571 * 12 = 6852 バイトです。 追加の行のオーバーヘッドとスパース列ヘッダーを含めた場合、サイズは約 6895 バイトになります。 このページは、ディスク上で約 1124 バイトをまだ利用可能です。 これ場合、追加の列を正常に更新できるように思われます。 しかし、更新時は、"2\*(NULL 以外のスパース列の数)" で求められる追加のオーバーヘッドが発生します。 この例で追加のオーバーヘッド (2 \* 571 = 1142 バイト) を含めると、ディスク上の行サイズは約 8037 バイトに増えます。 このサイズは、最大許容サイズの 8019 バイトを超えています。 すべての列は固定長データ型であるため、行外に出すことはできません。 その結果、更新は 576 エラーで失敗します。  
  
## <a name="restrictions-for-using-sparse-columns"></a>スパース列の使用に関する制限  
 スパース列は、任意の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型にすることができ、他の列と同じように動作しますが、次の制限があります。  
  
-   スパース列は、NULL 値を許容する必要があり、ROWGUIDCOL または IDENTITY プロパティを持つことができません。 スパース列のデータ型を **text**、 **ntext**、 **image**、 **timestamp**、ユーザー定義データ型、 **geometry**、または **geography** にすることはできません。また、スパース列には FILESTREAM 属性を指定できません。  
  
-   スパース列には既定値を設定できません。  
  
-   スパース列はルールにバインドできません。  
  
-   計算列にスパース列を含めることはできますが、計算列を SPARSE とマークすることはできません。  
  
-   データ マスクは、スパース列で定義できますが、列セットの一部であるスパース列では定義できません。  
  
-   スパース列をクラスター化インデックスまたは一意の主キー インデックスに含めることはできません。 ただし、スパース列に定義された保存される計算列と保存されない計算列は、クラスター化キーに含めることができます。  
  
-   スパース列は、クラスター化インデックスまたはヒープのパーティション キーとして使用できません。 ただし、スパース列を非クラスター化インデックスのパーティション キーとして使用することはできます。  
  
-   テーブル変数およびテーブル値パラメーターで使用されるユーザー定義テーブル型に、スパース列を含めることはできません。  
  
-   スパース列は、データ圧縮と互換性がありません。 したがって、圧縮されたテーブルにスパース列を追加したり、スパース列を含むテーブルを圧縮したりすることはできません。  
  
-   スパースから非スパース、または非スパースからスパースに列を変更するには、列のストレージ形式を変更する必要があります。 SQL Server データベース エンジンでは、次の手順を使用してこの操作を実行します。  
  
    1.  新しいストレージ サイズおよびストレージ形式でテーブルに新しい列を追加します。  
  
    2.  テーブル内の行ごとに、古い列に格納されている値を更新して新しい列にコピーします。  
  
    3.  古い列をテーブル スキーマから削除します。  
  
    4.  古い列で使用されていた領域を再利用するために、テーブルを再構築するか (クラスター化インデックスが存在しない場合)、クラスター化インデックスを再構築します。  
  
    > [!NOTE]  
    >  許容されている最大行サイズを行内のデータのサイズが超える場合、手順 2. が失敗する場合があります。 このサイズには、古い列に格納されているデータのサイズと、新しい列に格納されている更新されたデータのサイズが含まれます。 この制限値は、スパース列を含まないテーブルの場合は 8,060 バイト、スパース列を含むテーブルの場合は 8,018 バイトです。 このエラーは、条件を満たすすべての列が行外にプッシュされている場合でも発生します。  
  
-   非スパース列をスパース列に変更すると、その列で NULL 以外の値に使用される領域が増えます。 行のサイズがその上限に近い場合は、操作が失敗することがあります。  
  
## <a name="sql-server-technologies-that-support-sparse-columns"></a>スパース列をサポートする SQL Server のテクノロジ  
 ここでは、次に示す [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のテクノロジでスパース列がどのようにサポートされるかを説明します。  
  
-   トランザクション レプリケーション  
  
     トランザクション レプリケーションでは、スパース列はサポートされますが、スパース列と併用できる列セットはサポートされません。 列セットの詳細については、「 [列セットの使用](../../relational-databases/tables/use-column-sets.md)」を参照してください。  
  
     SPARSE 属性のレプリケーションは、 [sp_addarticle](../../relational-databases/system-stored-procedures/sp-addarticle-transact-sql.md) または **の** [アーティクルのプロパティ] [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]ダイアログ ボックスで指定されるスキーマ オプションによって決まります。 以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、スパース列がサポートされません。 以前のバージョンにデータをレプリケートする必要がある場合は、SPARSE 属性をレプリケートしないように指定する必要があります。  
  
     テーブルがパブリッシュされる場合は、テーブルに新しいスパース列を追加したり、既存の列のスパース プロパティを変更したりできません。 このような操作が必要な場合は、パブリケーションを削除して再作成します。  
  
-   マージ レプリケーション  
  
     マージ レプリケーションでは、スパース列または列セットがサポートされません。  
  
-   変更の追跡  
  
     変更の追跡では、スパース列と列セットがサポートされます。 テーブルで列セットが更新されると、変更の追跡でその操作が行全体の更新として処理されます。 列セットの更新操作で更新された一連のスパース列を正確に特定するための詳細な変更追跡は行われません。 スパース列が DML ステートメントを通じて明示的に更新された場合は、その列に対する変更の追跡が通常どおりに機能し、変更された一連の列を正確に特定できます。  
  
-   変更データ キャプチャ  
  
     変更データ キャプチャでは、スパース列はサポートされますが、列セットはサポートされません。  
  
-   テーブルをコピーするとき、列のスパース プロパティは保持されません。  
  
## <a name="examples"></a>例  
 次の例では、ドキュメント テーブルに `DocID` 列と `Title`列のセットが共通で含まれています。 製造グループは、すべての製造ドキュメントに `ProductionSpecification` 列と `ProductionLocation` 列を必要とします。 マーケティング グループは、マーケティング ドキュメントに `MarketingSurveyGroup` 列を必要とします。 この例のコードでは、スパース列を使用するテーブルを作成し、そのテーブルに 2 つの行を挿入し、そのテーブルからデータを選択します。  
  
> [!NOTE]  
>  このテーブルは、簡単に表示して確認できるように、5 つの列のみで構成されています。 ANSI_NULL_DFLT_ON オプションが設定されている場合は、NULL 値を許容するようにスパース列を宣言しなくてもかまいません。  
  
```  
USE AdventureWorks2012;  
GO  
  
CREATE TABLE DocumentStore  
    (DocID int PRIMARY KEY,  
     Title varchar(200) NOT NULL,  
     ProductionSpecification varchar(20) SPARSE NULL,  
     ProductionLocation smallint SPARSE NULL,  
     MarketingSurveyGroup varchar(20) SPARSE NULL ) ;  
GO  
  
INSERT DocumentStore(DocID, Title, ProductionSpecification, ProductionLocation)  
VALUES (1, 'Tire Spec 1', 'AXZZ217', 27);  
GO  
  
INSERT DocumentStore(DocID, Title, MarketingSurveyGroup)  
VALUES (2, 'Survey 2142', 'Men 25 - 35');  
GO  
```  
  
 テーブルからすべての列を選択すると、通常の結果セットが返されます。  
  
```  
SELECT * FROM DocumentStore ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `DocID  Title        ProductionSpecification  ProductionLocation  MarketingSurveyGroup`  
  
 `1      Tire Spec 1  AXZZ217                  27                  NULL`  
  
 `2      Survey 2142  NULL                     NULL                Men 25-35`  
  
 製造部門にマーケティング データは必要ないため、次のクエリに示すように、必要な列のみを返す列セットを使用します。  
  
```  
SELECT DocID, Title, ProductionSpecification, ProductionLocation   
FROM DocumentStore   
WHERE ProductionSpecification IS NOT NULL ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `DocID  Title        ProductionSpecification  ProductionLocation`  
  
 `1      Tire Spec 1  AXZZ217                  27`  
  
## <a name="see-also"></a>関連項目  
 [列セットの使用](../../relational-databases/tables/use-column-sets.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [sys.columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)  
  
  
