---
description: COLLATE (Transact-SQL)
title: COLLATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/30/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- COLLATE
- COLLATE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- collations [SQL Server], COLLATE clause
- COLLATE clause
ms.assetid: 76763ac8-3e0d-4bbb-aa53-f5e7da021daa
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5cda69577b36d735688475f8229de994d6db3869
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750282"
---
# <a name="collate-transact-sql"></a>COLLATE (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

データベースまたはテーブル列の照合順序、または文字列式に適用されたときの照合順序のキャスト操作を定義します。 照合順序名には、Windows 照合順序名または SQL 照合順序名を指定できます。 データベースの作成時に指定しない場合は、データベースに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの既定の照合順序が割り当てられます。 テーブル列の作成時に指定しない場合、データベースの既定の照合順序に列が割り当てられます。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql
COLLATE { <collation_name> | database_default }
<collation_name> :: =
    { Windows_collation_name } | { SQL_collation_name }
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

*collation_name* 式、列定義、またはデータベース定義に適用する照合順序の名前です。 *collat​​ion_name* には、指定された *Windows_collat​​ion_name* または *SQL_collat​​ion_name* のみを指定できます。 *collation_name* はリテラル値である必要があります。 *collation_name* を変数または式で表すことはできません。

*Windows_collation_name* は、「[Windows 照合順序名](../../t-sql/statements/windows-collation-name-transact-sql.md)」の照合順序名です。

*SQL_collation_name* は、「[SQL Server 照合順序名](../../t-sql/statements/sql-server-collation-name-transact-sql.md)」の照合順序名です。

**database_default** COLLATE 句によって、現在のデータベースの照合順序が継承されます。

## <a name="remarks"></a>解説

COLLATE 句は、さまざまなレベルで指定できます。 これらには、次のものが含まれます。

1. データベースの作成または変更

    `CREATE DATABASE` または `ALTER DATABASE` ステートメントの COLLATE 句を使用して、データベースの既定の照合順序を指定できます。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を使用してデータベースを作成するときに、照合順序も指定できます。 照合順序を指定しない場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスの既定の照合順序がデータベースに指定されます。

    > [!NOTE]
    > Unicode 専用の Windows 照合順序は、COLLATE 句で **nchar**、**nvarchar**、および **ntext** の各データ型を列レベルおよび式レベルのデータに適用する場合にのみ使用できます。COLLATE 句でデータベースまたはサーバー インスタンスの照合順序を定義または変更するために使用することはできません。

2. テーブル列の作成または変更

    `CREATE TABLE` または `ALTER TABLE` ステートメントの COLLATE 句を使用して、文字型の各列に対して照合順序を指定できます。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を使用してテーブルを作成するときに、照合順序も指定できます。 照合順序を指定しない場合、列には、データベースの既定の照合順序が指定されます。

    また、COLLATE 句の `database_default` オプションを使用して、一時テーブルの列で、**tempdb** の代わりに、接続に対する現在のユーザー データベースの既定の照合順序が使用されるように指定することもできます。

3. 式の照合順序のキャスト

    COLLATE 句を使用して、文字式を特定の照合順序に適用できます。 文字リテラルと変数には、現在のデータベースの既定の照合順序が指定されます。 列参照には、列の既定の照合順序が指定されます。

識別子の照合順序は、識別子が定義されているレベルによって異なります。 ログイン名やデータベース名など、インスタンスレベルのオブジェクトの識別子には、インスタンスの既定の照合順序が指定されます。 テーブル名、ビュー名、列名など、データベース内のオブジェクトの識別子には、データベースの既定の照合順序が指定されます。 たとえば、大文字と小文字においてのみ名前が異なる 2 つのテーブルを作成する場合、大文字と小文字が区別される照合順序が指定されたデータベースでは作成できますが、大文字と小文字が区別されない照合順序が指定されたデータベースでは作成できません。 詳細については、「[データベース識別子](../../relational-databases/databases/database-identifiers.md)」を参照してください。

接続コンテキストが 1 つのデータベースに関連付けられたときに変数、GOTO ラベル、一時ストアド プロシージャおよび一時テーブルを作成し、コンテキストを別のデータベースに切り替えたときに、それらを参照することができます。 変数、GOTO ラベル、一時ストアド プロシージャ、および一時テーブルの各識別子は、サーバー インスタンスの既定の照合順序に従います。

COLLATE 句は、**char**、**varchar**、**text**、**nchar**、**nvarchar**、および **ntext** データ型にのみ適用できます。

COLLATE は *collate_name* を使用して、式、列定義、データベース定義に適用される [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 照合順序または Windows 照合順序のいずれかの名前を参照します。 *collation_name* に指定できるのは、指定された *Windows_collation_name* または *SQL_collation_name* だけで、パラメーターにはリテラル値を含める必要があります。 *collation_name* を変数または式で表すことはできません。

照合順序は、通常、照合順序名によって識別します。ただし、セットアップ時は例外です。 セットアップ時には、Windows 照合順序にルート照合順序指定子 (照合ロケール) を指定してから、大文字と小文字の区別やアクセントの区別に関する並べ替えオプションを指定します。

システム関数の [fn_helpcollations](../../relational-databases/system-functions/sys-fn-helpcollations-transact-sql.md) を実行すると、Windows 照合順序および SQL Server 照合順序のすべての有効な照合順序名の一覧を取得できます。

```sql
SELECT name, description
FROM fn_helpcollations();
```

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、動作しているオペレーティング システムがサポートしているコード ページのみをサポートすることができます。 照合順序に依存するアクションを実行する場合、参照されるオブジェクトが使用する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 照合順序は、コンピューター上で実行されているオペレーティング システムがサポートしているコード ページを使用する必要があります。 このようなアクションには、次のものがあります。

- データベースの作成または変更時に、データベースの既定の照合順序を指定する。
- テーブルの作成または変更時に、列の照合順序を指定する。
- データベースの復元または接続を行う場合、データベースの既定の照合順序、およびデータベース内の **char** 型、**varchar** 型、および **text** 型の任意の列またはパラメーターの照合順序は、オペレーティング システムでサポートされている必要があります。

> [!NOTE]
> コード ページ変換は **char** および **varchar** データ型に対してはサポートされていますが、**text** データ型に対してはサポートされていません。 コード ページ変換時のデータ損失はレポートされません。
>
> 指定した照合順序、または参照先のオブジェクトで使用される照合順序で、Windows でサポートされていないコード ページが使用されていると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でエラーが表示されます。

## <a name="examples"></a>例

### <a name="a-specifying-collation-during-a-select"></a>A. SELECT 時に照合順序を指定する

次の例では、単純なテーブルを作成し、4 つの行を挿入します。 次に、テーブルからデータを選択するときに 2 つの照合順序を適用して、`Chiapas` が異なる方法で格納されることを示します。

```sql
CREATE TABLE Locations
(Place varchar(15) NOT NULL);
GO
INSERT Locations(Place) VALUES ('Chiapas'),('Colima')
                             , ('Cinco Rios'), ('California');
GO
--Apply an typical collation
SELECT Place FROM Locations
ORDER BY Place
COLLATE Latin1_General_CS_AS_KS_WS ASC;
GO
-- Apply a Spanish collation
SELECT Place FROM Locations
ORDER BY Place
COLLATE Traditional_Spanish_ci_ai ASC;
GO
```

最初のクエリからの結果を次に示します。

```output
Place
-------------
California
Chiapas
Cinco Rios
Colima
```

2 番目のクエリからの結果を次に示します。

```output
Place
-------------
California
Cinco Rios
Colima
Chiapas
```

### <a name="b-additional-examples"></a>B. 追加の例

**COLLATE** を使用するその他の例については、「[CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md#examples)」の例「**G. データベースを作成し、照合順序名とオプションを指定する**」と「[ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md#alter_column)」の例「**V. 列の照合順序を変更する**」を参照してください。

## <a name="see-also"></a>関連項目

- [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)
- [照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)
- [照合順序の優先順位](../../t-sql/statements/collation-precedence-transact-sql.md)
- [定数](../../t-sql/data-types/constants-transact-sql.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)
- [DECLARE @local_variable](../../t-sql/language-elements/declare-local-variable-transact-sql.md)
- [テーブル データ型](../../t-sql/data-types/table-transact-sql.md)
