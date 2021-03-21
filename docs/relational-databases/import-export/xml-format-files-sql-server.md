---
title: XML フォーマット ファイル (SQL Server) | Microsoft Docs
description: SQL Server 2019 には、SQL Server のテーブルにデータを一括インポートする目的で使用する XML フォーマット ファイルを記述するための構文を定義した XML スキーマが用意されています。
ms.custom: ''
ms.date: 01/11/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- format files [SQL Server], XML format files
- bulk importing [SQL Server], format files
- XML format files [SQL Server]
ms.assetid: 69024aad-eeea-4187-8fea-b49bc2359849
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 432f1c263e132a2883d70792146b56724452ab77
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104736222"
---
# <a name="xml-format-files-sql-server"></a>XML フォーマット ファイル (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] には、 *のテーブルにデータを一括インポートする目的で使用する* XML フォーマット ファイル [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を記述するための構文を定義した XML スキーマが用意されています。 このスキーマは XML Schema Definition Language (XSDL) で定義されています。XML フォーマット ファイルはこのスキーマに準拠している必要があります。 XML フォーマット ファイルは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ツールが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client と共にインストールされている場合のみサポートされます。  
  
 XML フォーマット ファイルは、**bcp** コマンド、BULK INSERT ステートメント、または INSERT ...SELECT \* FROM OPENROWSET(BULK...) ステートメントのいずれかを使用して実行します。 **bcp** コマンドを使用して、あるテーブルに対する XML フォーマット ファイルを自動的に生成できます。詳細については、「 [bcp Utility](../../tools/bcp-utility.md)」を参照してください。  
  
> [!NOTE]  
>  一括エクスポートおよび一括インポート用に 2 種類のフォーマット ファイルがサポートされています。 *XML 以外のフォーマット ファイル* と *XML フォーマット ファイル* です。 XML フォーマット ファイルは XML 以外のフォーマット ファイルに比べ、柔軟かつ強力です。 XML 以外のフォーマット ファイルの詳細については、「 [XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)」を参照してください。  
  
 **このトピックの内容**  
  
-   [XML フォーマット ファイルの利点](#BenefitsOfXmlFFs)  
  
-   [XML フォーマット ファイルの構造](#StructureOfXmlFFs)  
  
-   [XML フォーマット ファイルのスキーマ構文](#SchemaSyntax)  
  
-   [XML フォーマット ファイルのサンプル](#SampleXmlFFs)  
  
-   [関連タスク](#RelatedTasks)  
  
-   [関連コンテンツ](#RelatedContent)  
  
##  <a name="benefits-of-xml-format-files"></a><a name="BenefitsOfXmlFFs"></a> XML フォーマット ファイルの利点  
  
-   XML フォーマット ファイルは、自己記述型を特徴とし、見やすく簡単に作成、拡張することができます。 人間に読みやすいように書かれているので、一括操作でのデータの解釈方法を容易に理解できます。  
  
-   XML フォーマット ファイルには、ターゲット列のデータ型が格納されています。  XML エンコーディングでは、データ ファイルのデータ型およびデータ要素だけでなく、データ要素とテーブル列の間のマッピングを明確に記述できます。  
  
     これにより、データ ファイルでのデータの表記方法と、ファイル内の各フィールドに関連付けられるデータ型を切り離すことができます。 たとえば、データ ファイルにデータの文字表記が含まれている場合、対応する SQL 列の型が失われます。  
  
-   XML フォーマット ファイルがあることで、データ ファイルから、単一のラージ オブジェクト (LOB) データ型を格納するフィールドを読み込むことができます。  
  
-   XML フォーマット ファイルは機能を拡張できますが、以前のバージョンとの互換性も保たれます。 さらに XML エンコーディングは明確なので、特定のデータ ファイルに対して複数のフォーマット ファイルを容易に作成できます。 この特性は、データ フィールドの全体または一部を、さまざまなテーブルまたはビューの列にマップする必要がある場合に役立ちます。  
  
-   XML の構文は、操作の方向とは関係がありません。つまり、一括エクスポートでも一括インポートでも構文は同じです。  
  
-   XML フォーマット ファイルを使用すると、テーブルまたは非パーティション ビューにデータの一括インポートを行ったり、データの一括エクスポートを行ったりすることができます。  
  
-   OPENROWSET(BULK...) 関数では、対象テーブルの指定は省略できます。 これは、この関数が XML フォーマット ファイルを使用してデータ ファイルからデータを読み取るためです。  
  
    > [!NOTE]  
    >  対象テーブル列を使用して型変換を行う **bcp** コマンドと BULK INSERT ステートメントでは、対象テーブルが必要となります。  
  
##  <a name="structure-of-xml-format-files"></a><a name="StructureOfXmlFFs"></a> XML フォーマット ファイルの構造  
 XML 以外のフォーマット ファイルと同様に、XML フォーマット ファイルでもデータ ファイル内のデータ フィールドの形式および構造を定義し、定義したデータ フィールドを 1 つのマップ先テーブル内の列にマップします。  
  
 XML フォーマット ファイルには、\<RECORD> と \<ROW> という 2 つの主要なコンポーネントがあります。  
  
-   \<RECORD> にはデータ ファイルに保存するデータをそのまま記述します。  
  
     各 \<RECORD> 要素は、1 つ以上の \<FIELD> 要素のセットを格納します。 それらの要素はデータ ファイル内のフィールドに対応します。 基本構文は次のとおりです。  
  
     \<RECORD>  
  
     \<FIELD .../> [ ...*n* ]  
  
     \</RECORD>  
  
     各 \<FIELD> 要素には、特定のデータ フィールドの内容を記述します。 個々のフィールドは、テーブル内の 1 つの列にのみマップできます。 すべてのフィールドを列にマップする必要はありません。  
  
     データ ファイルのフィールドは、固定/可変長にすることも、任意の文字で区切ることもできます。 *フィールドの値* は、半角文字 (1 バイト表現を使用)、全角文字 (Unicode の 2 バイト表現を使用)、ネイティブ データベース形式、またはファイル名で表現できます。 フィールドの値をファイル名で表現する場合、対象になるテーブルの BLOB 列の値を含むファイルをそのファイル名で指すようにします。  
  
-   \<ROW> には、データ ファイルから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルにデータをインポートするときのデータ行の構成方法を記述します。  
  
     \<ROW> 要素は、\<COLUMN> 要素のセットを格納します。 それらの要素はテーブル列に対応します。 基本構文は次のとおりです。  
  
     \<ROW>  
  
     \<COLUMN .../> [ ...*n* ]  
  
     \</ROW>  
  
     個々の \<COLUMN> 要素は、データ ファイル内の 1 つのフィールドにのみマップできます。 \<ROW> 要素内の \<COLUMN> 要素の順序により、一括操作で返される順序が決定されます。 XML フォーマット ファイルでは、一括インポート操作の対象になるテーブルの列とのリレーションシップがない各 \<COLUMN> 要素にローカル名が割り当てられます。  
  
##  <a name="schema-syntax-for-xml-format-files"></a><a name="SchemaSyntax"></a> XML フォーマット ファイルのスキーマ構文  
 このセクションでは、XML フォーマット ファイルに対する XML スキーマの要素および属性について概要を説明します。 フォーマット ファイルの構文は、操作の方向とは関係がありません。つまり、一括エクスポートでも一括インポートでも構文は同じです。 また、一括インポートで \<ROW> 要素と \<COLUMN> 要素を使用する方法、および要素の xsi:type 値をデータ セットに格納する方法についても説明します。  
  
 構文が実際の XML フォーマット ファイルとどのように対応しているかについては、このトピックの「 [XML フォーマット ファイルのサンプル](#SampleXmlFFs)」を参照してください。  
  
> [!NOTE]  
>  フォーマット ファイルを変更して、フィールドの数や順序がテーブル列とは異なるデータ ファイルから一括インポートできます。 詳細については、「 [データのインポートまたはエクスポート用のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)」を参照してください。  
  
 **このセクションの内容**  
  
-   [XML スキーマの基本構文](#BasicSyntax)  
  
-   [一括インポートで \<ROW> 要素を使用する方法](#HowUsesROW)  
  
-   [一括インポートで \<COLUMN> 要素を使用する方法](#HowUsesColumn)  
  
-   [xsi:type 値のデータセットへの格納](#PutXsiTypeValueIntoDataSet)  
  
###  <a name="basic-syntax-of-the-xml-schema"></a><a name="BasicSyntax"></a> XML スキーマの基本構文  
 この基本構文のステートメントでは、要素 (\<BCPFORMAT>、\<RECORD>、\<FIELD>、\<ROW>、\<COLUMN>) とその基本属性のみを示します。  
  
 \<BCPFORMAT ...>  
  
 \<RECORD>  
  
 \<FIELD ID = "*fieldID*" xsi:type = "*fieldType*" [...]  
  
 />  
  
 \</RECORD>  
  
 \<ROW>  
  
 \<COLUMN SOURCE = "*fieldID*" NAME = "*columnName*" xsi:type = "*columnType*" [...]  
  
 />  
  
 \</ROW>  
  
 \</BCPFORMAT>  
  
> [!NOTE]  
>  \<FIELD> 要素または \<COLUMN> 要素の xsi:type の値と関連付けられている他の属性については、このトピックの後半で説明します。  
  
 **このセクションの内容**  
  
-   [Schema 要素](#SchemaElements)  
  
-   [\<FIELD> 要素の属性](#AttrOfFieldElement) (および [\<FIELD> 要素の Xsi:type 値](#XsiTypeValuesOfFIELD))  
  
-   [\<COLUMN> 要素の属性](#AttrOfColumnElement) (および [\<COLUMN> 要素の Xsi:type 値](#XsiTypeValuesOfCOLUMN))  
  
####  <a name="schema-elements"></a><a name="SchemaElements"></a> Schema 要素  
 ここでは、XML スキーマで XML フォーマット ファイル用に定義している各要素の目的を説明します。 各要素の属性については、このトピックの後半で説明します。  
  
 \<BCPFORMAT>  
 特定のデータ ファイルのレコード構造とテーブル内の対応するテーブル行の列を定義するフォーマット ファイル要素です。  
  
 \<RECORD .../>  
 1 つ以上の \<FIELD> 要素を含む複合要素を定義します。 フォーマット ファイルでフィールドが宣言される順序は、データ ファイルでフィールドが表示される順序と同じです。  
  
 \<FIELD .../>  
 データを含むデータ ファイル内のフィールドを定義します。  
  
 この要素の属性については、このトピックの「[\<FIELD> 要素の属性](#AttrOfFieldElement)」で説明します。  
  
 \<ROW .../>  
 1 つ以上の \<COLUMN> 要素を含む複合要素を定義します。 \<COLUMN> 要素の順序は、RECORD 定義の \<FIELD> 要素の順序とは関係ありません。 結果行セットの列の順序は、フォーマット ファイルの \<COLUMN> 要素の順序で決まります。 データ フィールドは、対応する \<COLUMN> 要素が \<COLUMN> 要素内で宣言されている順序で読み込まれます。  
  
 詳細については、このトピックの「[一括インポートで \<ROW> 要素を使用する方法](#HowUsesROW)」を参照してください。  
  
 \<COLUMN>  
 列を要素 (\<COLUMN>) として定義します。 各 \<COLUMN> 要素は、\<FIELD> 要素に対応しています (この要素の ID は、\<COLUMN> 要素の SOURCE 属性で指定されます)。  
  
 この要素の属性については、このトピックの「[\<COLUMN> 要素の属性](#AttrOfColumnElement)」で説明します。 また、このトピックの「[一括インポートで \<COLUMN> 要素を使用する方法](#HowUsesColumn)」を参照してください。  
  
 \</BCPFORMAT>  
 フォーマット ファイルを終了するために必要なフォーマット ファイル要素です。  
  
####  <a name="attributes-of-the-field-element"></a><a name="AttrOfFieldElement"></a> \<FIELD> 要素の属性  
 ここでは、次のスキーマ構文に示す \<FIELD> 要素の属性について説明します。  
  
 <FIELD  
  
 ID **="** _fieldID_ **"**  
  
 xsi **:** type **="** _fieldType_ **"**  
  
 [ LENGTH **="** _n_ **"** ]  
  
 [ PREFIX_LENGTH **="** _p_ **"** ]  
  
 [ MAX_LENGTH **="** _m_ **"** ]  
  
 [ COLLATION **="** _collationName_ **"** ]  
  
 [ TERMINATOR **="** _terminator_ **"** ]  
  
 />  
  
 それぞれの \<FIELD> 要素は、他の要素から独立しています。 フィールドは、次の属性を使用して表現されます。  
  
|FIELD 要素の属性|説明|省略可能 /<br /><br /> 必須|  
|---------------------|-----------------|------------------------------|  
|ID **="** _fieldID_ **"**|データ ファイル内のフィールドの論理名を指定します。 フィールドの ID は、フィールドを参照する際に使用するキーになります。<br /><br /> \<FIELD ID**="**_fieldID_**"**/> は \<COLUMN SOURCE**="**_fieldID_**"**/> にマップされます。|必須|  
|xsi:type **="** _fieldType_ **"**|要素のインスタンスの種類を特定する XML コンストラクトです (これは属性のように使用します)。 *fieldType* の値により、要素のインスタンスで必要なオプションの属性 (下記参照) が決まります。|必須 (データ型により異なる)|  
|LENGTH **="** _n_ **"**|固定長データ型のインスタンスの長さを定義します。<br /><br /> *n* の値は、正の整数にする必要があります。|省略可能 (xsi:type 値で必要な場合は必須)。|  
|PREFIX_LENGTH **="** _p_ **"**|バイナリ データ表現のプレフィックス長を定義します。 PREFIX_LENGTH 値の *p* は、1、2、4、または 8 のいずれかにする必要があります。|省略可能 (xsi:type 値で必要な場合は必須)。|  
|MAX_LENGTH **="** _m_ **"**|指定したフィールドに格納できる最大バイト数を定義します。 対象のテーブルがない場合、列の最大長を決めることはできません。 MAX_LENGTH 属性では、出力先の文字列型の列の最大長を制限し、列の値に割り当てる領域を制限しています。 この属性は、SELECT FROM 句で OPENROWSET 関数の BULK オプションを使用している場合に特に有益です。<br /><br /> *m* の値は、正の整数にする必要があります。 既定では、 **char** 列の最大長は 8,000 文字で、 **nchar** 列の最大長は 4,000 文字です。|省略可能|  
|COLLATION **="** _collationName_ **"**|COLLATION は、文字列型のフィールドでのみ使用できる属性です。 SQL 照合順序名の一覧については、「[SQL Server の照合順序名 &#40;Transact-SQL&#41;](../../t-sql/statements/sql-server-collation-name-transact-sql.md)」を参照してください。|省略可能|  
|TERMINATOR **= "** _terminator_ **"**|データ フィールドのターミネータを指定します。 ターミネータには、任意の文字を使用できます。 ただし、ターミネータには、データに含まれていない一意な文字を使用する必要があります。<br /><br /> 既定では、フィールド ターミネータはタブ文字 (\t) です。 段落記号を表すには、\r\n を使用します。|この属性が必要な文字型データの xsi:type でのみ使用されます。|  
  
#####  <a name="xsitype-values-of-the-field-element"></a><a name="XsiTypeValuesOfFIELD"></a> \<FIELD> 要素の Xsi:type 値  
 xsi:type 値は、要素のインスタンスのデータ型を特定する XML コンストラクトです (これは属性のように使用します)。 詳細については、このトピックの「xsi:type 値のデータセットへの格納」を参照してください。  
  
 \<FIELD> 要素の xsi:type 値では、次のデータ型がサポートされています。  
  
|\<FIELD> の xsi:type 値|必要な XML<br /><br /> 属性|データ型に関する省略可能な XML<br /><br /> 属性|  
|-------------------------------|---------------------------------------------------|---------------------------------------------------|  
|**NativeFixed**|**LENGTH**|[なし] :|  
|**NativePrefix**|**PREFIX_LENGTH**|MAX_LENGTH|  
|**CharFixed**|**LENGTH**|COLLATION|  
|**NCharFixed**|**LENGTH**|COLLATION|  
|**CharPrefix**|**PREFIX_LENGTH**|MAX_LENGTH、COLLATION|  
|**NCharPrefix**|**PREFIX_LENGTH**|MAX_LENGTH、COLLATION|  
|**CharTerm**|**TERMINATOR**|MAX_LENGTH、COLLATION|  
|**NCharTerm**|**TERMINATOR**|MAX_LENGTH、COLLATION|  
  
 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型について詳しくは、「[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)」をご覧ください。  
  
####  <a name="attributes-of-the-column-element"></a><a name="AttrOfColumnElement"></a> \<COLUMN> 要素の属性  
 ここでは、次のスキーマ構文に示す \<COLUMN> 要素の属性について説明します。  
  
 <COLUMN  
  
 SOURCE = "*fieldID*"  
  
 NAME = "*columnName*"  
  
 xsi:type = "*columnType*"  
  
 [ LENGTH = "*n*" ]  
  
 [ PRECISION = "*n*" ]  
  
 [ SCALE = "*value*" ]  
  
 [ NULLABLE = { "YES"  
  
 "NO" } ]  
  
 />  
  
 フィールドは、次の属性を使用して、対象となるテーブルにマップされます。  
  
|COLUMN 要素の属性|説明|省略可能 /<br /><br /> 必須|  
|----------------------|-----------------|------------------------------|  
|SOURCE **="** _fieldID_ **"**|列にマップされているフィールドの ID を指定します。<br /><br /> \<COLUMN SOURCE**="**_fieldID_**"**/> は \<FIELD ID**="**_fieldID_**"**/> にマップされます。|必須|  
|NAME = "*columnName*"|フォーマット ファイルで表している行セットの列の名前を指定します。 この列名は、結果セット内で列名を特定する際に使用されるので、対象のテーブルで使用されている列名に対応する必要はありません。|必須|  
|xsi **:** type **="** _ColumnType_ **"**|要素のインスタンスのデータ型を特定する XML コンストラクトです (これは属性のように使用します)。 *ColumnType* の値により、要素のインスタンスで必要なオプションの属性 (下記参照) が決まります。<br /><br /> 注:*ColumnType* に設定できる値と関連する属性値は、「[&lt;COLUMN&gt; 要素の xsi:type 値](#XsiTypeValuesOfCOLUMN)」セクションの \<COLUMN> 要素の表に示します。|省略可能|  
|LENGTH **="** _n_ **"**|固定長データ型のインスタンスの長さを定義します。 LENGTH 属性は、xsi:type が文字列データ型の場合にのみ使用します。<br /><br /> *n* の値は、正の整数にする必要があります。|省略可能 (xsi:type が文字列データ型の場合にのみ使用可能)|  
|PRECISION **="** _n_ **"**|数値全体の桁数を示します。 たとえば、数字 123.45 の有効桁数は 5 桁です。<br /><br /> この値は、正の整数にする必要があります。|省略可能 (xsi:type が可変数値のデータ型の場合にのみ使用可能)|  
|SCALE **="** _int_ **"**|数値の中で小数点より右側の桁数を示します。 たとえば、数字 123.45 の小数点以下桁数は 2 桁です。<br /><br /> この値は、整数にする必要があります。|省略可能 (xsi:type が可変数値のデータ型の場合にのみ使用可能)|  
|NULLABLE **=** { **"** YES **"**<br /><br /> **"** NO **"** }|列で NULL 値を使用できるかどうかを示します。 この属性は、FIELDS 要素から完全に独立しています。 ただし、列が NULLABLE ではない場合に、フィールドで NULL が指定された (何も値が指定されない) 場合、実行時エラーが発生します。<br /><br /> NULLABLE 属性は、単純な SELECT FROM OPENROWSET(BULK...) ステートメントを実行する場合にのみ使用されます。|省略可能 (任意のデータ型で使用可能)|  
  
#####  <a name="xsitype-values-of-the-column-element"></a><a name="XsiTypeValuesOfCOLUMN"></a> \<COLUMN> 要素の Xsi:type 値  
 xsi:type 値は、要素のインスタンスのデータ型を特定する XML コンストラクトです (これは属性のように使用します)。 詳細については、このトピックの「xsi:type 値のデータセットへの格納」を参照してください。  
  
 次の表に示すように、\<COLUMN> 要素ではネイティブな SQL データ型がサポートされています。  
  
|データ型|\<COLUMN> データ型|必要な XML<br /><br /> 属性|データ型に関する省略可能な XML<br /><br /> 属性|  
|-------------------|---------------------------|---------------------------------------------------|---------------------------------------------------|  
|固定|**SQLBIT**、 **SQLTINYINT**、 **SQLSMALLINT**、 **SQLINT**、 **SQLBIGINT**、 **SQLFLT4**、 **SQLFLT8**、 **SQLDATETIME**、 **SQLDATETIM4**、 **SQLDATETIM8**、 **SQLMONEY**、 **SQLMONEY4**、 **SQLVARIANT**、および **SQLUNIQUEID**|[なし] :|NULLABLE|  
|可変数値|**SQLDECIMAL** および **SQLNUMERIC**|[なし] :|NULLABLE、PRECISION、SCALE|  
|LOB|**SQLIMAGE**、 **CharLOB**、 **SQLTEXT**、および **SQLUDT**|[なし] :|NULLABLE|  
|CLOB|**SQLNTEXT**|[なし] :|NULLABLE|  
|バイナリ文字列|**SQLBINARY** および **SQLVARYBIN**|[なし] :|NULLABLE、LENGTH|  
|文字列|**SQLCHAR**、 **SQLVARYCHAR**、 **SQLNCHAR**、および **SQLNVARCHAR**|[なし] :|NULLABLE、LENGTH|  
  
> [!IMPORTANT]  
>  SQLXML データを一括エクスポートまたは一括インポートするには、フォーマット ファイルで次のいずれかのデータ型を使用します。SQLCHAR または SQLVARYCHAR (データはクライアント コード ページまたは照合順序で暗黙的に指定されるコード ページで送られます)、SQLNCHAR または SQLNVARCHAR (データは Unicode として送られます)、SQLBINARY または SQLVARYBIN (データは変換なしで送られます)。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型の詳細については、「[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)」を参照してください。  
  
###  <a name="how-bulk-import-uses-the-row-element"></a><a name="HowUsesROW"></a> 一括インポートで \<ROW> 要素を使用する方法  
 \<ROW> 要素は、一部のコンテキストでは無視されます。 \<ROW> 要素が、一括インポート操作に影響を与えるかどうかは、一括インポート操作がどのように実行されるかによって異なります。  
  
-   **bcp** コマンド  
  
     データがインポート先のテーブルに読み込まれるときに、**bcp** コマンドでは \<ROW> コンポーネントは無視されます。 代わりに、 **bcp** コマンドでは、インポート先テーブルの列の型に基づいてデータが読み込まれます。  
  
-   [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメント (BULK INSERT および OPENROWSET の一括行セット プロバイダー)  
  
     テーブルにデータを一括インポートする際、[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントでは、\<ROW> コンポーネントを使用して入力行セットが生成されます。 また、[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントでは、\<ROW> で指定されている列の型とインポート先のテーブルの対応する列に基づいて、適切な型変換が実行されます。 フォーマット ファイルで指定されている列の型とインポート先のテーブルの列の型が一致しない場合、追加の型変換が実行されます。 この追加の型変換によって、 **bcp** コマンドと比べたときの BULK INSERT または OPENROWSET の一括行セット プロバイダーの動作に矛盾が生じる (精度が低下する) ことがあります。  
  
     \<ROW> 要素の情報に基づいて行が構築されるので、追加の情報は必要ありません。 このため、SELECT ステートメント (SELECT \* FROM OPENROWSET(BULK *datafile* FORMATFILE=*xmlformatfile*) を使用して行セットを生成することができます。  
  
    > [!NOTE]  
    >  OPENROWSET BULK 句では、フォーマット ファイルが必要です (フィールドのデータ型から列のデータ型に変換する処理には、XML フォーマット ファイルが必要であることに注意してください)。  
  
###  <a name="how-bulk-import-uses-the-column-element"></a><a name="HowUsesColumn"></a> 一括インポートで \<COLUMN> 要素を使用する方法  
 データをテーブルに一括インポートする際、フォーマット ファイル内の \<COLUMN> 要素により、次のことが指定され、データ ファイルのフィールドがテーブルの列にマップされます。  
  
-   データ ファイルの行内における各フィールドの位置。  
  
-   列の型 (この型はフィールドのデータ型を適切な列のデータ型に変換する際に使用します)。  
  
 フィールドに列がマップされていない場合、フィールドは生成された行にコピーされません。 この動作により、データ ファイルでは、(異なるテーブルの) 異なる列を持つ行を生成することができます。  
  
 同様に、データをテーブルから一括エクスポートする際、フォーマット ファイルの各 \<COLUMN> 要素により、エクスポート元のテーブル行の列が出力先データ ファイルの対応するフィールドにマップされます。  
  
###  <a name="putting-the-xsitype-value-into-a-data-set"></a><a name="PutXsiTypeValueIntoDataSet"></a> xsi:type 値のデータセットへの格納  
 XML ドキュメントが XML Schema Definition (XSD) 言語で検証されると、xsi:type 値はデータセットに格納されません。 ただし、次のコードに示すように、XML フォーマット ファイルを XML ドキュメント (たとえば、 `myDoc`) に読み込んで、xsi:type の情報をデータセットに格納することができます。  
  
```cs
...;  
myDoc.LoadXml(xmlFormat);  
XmlNodeList ColumnList = myDoc.GetElementsByTagName("COLUMN");  
for(int i=0;i<ColumnList.Count;i++)  
{  
   Console.Write("COLUMN: xsi:type=" +ColumnList[i].Attributes["type",  
      "http://www.w3.org/2001/XMLSchema-instance"].Value+"\n");  
}  
```  
  
##  <a name="sample-xml-format-files"></a><a name="SampleXmlFFs"></a> XML フォーマット ファイルのサンプル  
 ここでは、 [!INCLUDE[ssSampleDBCoShort](../../includes/sssampledbcoshort-md.md)] の例を基に、さまざまな状況で XML フォーマット ファイルを使用するときの情報について説明します。  
  
> [!NOTE]  
>  下記の例で示すデータ ファイルでは、 `<tab>` がデータ ファイル内のタブ文字、 `<return>` が復帰を表します。  
  
 以下、XML フォーマット ファイルを使用する際の主な局面について例を使用して説明します。  
  
-   [文字データ フィールドの順序とテーブル列の順序が同じ場合](#OrderCharFieldsSameAsCols)  
  
-   [データ フィールドの順序とテーブル列の順序が異なる場合](#OrderFieldsAndColsDifferently)  
  
-   [データ フィールドをスキップする場合](#OmitField)  
  
-   [フィールドを異なる型の列にマッピングする場合](#MapXSItype)  
  
-   [XML データをテーブルにマッピングする場合](#MapXMLDataToTbl)  
  
-   [固定長フィールドまたは固定幅フィールドをインポートする場合](#ImportFixedFields)  
  
-   [その他の例](#AdditionalExamples)  
  
> [!NOTE]  
>  フォーマット ファイルの作成方法については、「 [フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)」を参照してください。  
  
###  <a name="a-ordering-character-data-fields-the-same-as-table-columns"></a><a name="OrderCharFieldsSameAsCols"></a> A. 文字データ フィールドの順序とテーブル列の順序が同じ場合  
 次の例は、文字データのフィールドを 3 つ含んだデータ ファイルを記述する XML フォーマット ファイルを示しています。 フォーマット ファイルによって、このデータ ファイルを 3 列のテーブルにマッピングします。 データ フィールドは、テーブルの列と一対一に対応します。  
  
 **テーブル (行):** Person (Age int, FirstName varchar(20), LastName varchar(30))  
  
 **データ ファイル (レコード):** Age\<tab>Firstname\<tab>Lastname\<return>  
  
 次の XML フォーマット ファイルはデータ ファイルのデータをテーブルに読み込みます。  
  
 `<RECORD>` 要素では、3 つのフィールドすべてのデータ値が文字データとして表現されています。 各フィールドの `TERMINATOR` 属性は、データ値の後のターミネータを示します。  
  
 データ フィールドは、テーブルの列と一対一に対応します。 `<ROW>` 要素で、列 `Age` を 1 番目のフィールドに、列 `FirstName` を 2 番目のフィールドに、列 `LastName` を 3 番目のフィールドにマッピングします。  
  
```xml
<?xml version="1.0"?>  
<BCPFORMAT   
xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format"   
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
  <RECORD>  
    <FIELD ID="1" xsi:type="CharTerm" TERMINATOR="\t"   
      MAX_LENGTH="12"/>   
    <FIELD ID="2" xsi:type="CharTerm" TERMINATOR="\t"   
      MAX_LENGTH="20" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
    <FIELD ID="3" xsi:type="CharTerm" TERMINATOR="\r\n"   
      MAX_LENGTH="30"   
      COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
  </RECORD>  
  <ROW>  
    <COLUMN SOURCE="1" NAME="age" xsi:type="SQLINT"/>  
    <COLUMN SOURCE="2" NAME="firstname" xsi:type="SQLVARYCHAR"/>  
    <COLUMN SOURCE="3" NAME="lastname" xsi:type="SQLVARYCHAR"/>  
  </ROW>  
</BCPFORMAT>  
```  
  
> [!NOTE]  
>  [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] を使用した同様の例については、「[フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)」を参照してください。  
  
###  <a name="b-ordering-data-fields-and-table-columns-differently"></a><a name="OrderFieldsAndColsDifferently"></a> B. データ フィールドの順序とテーブル列の順序が異なる場合  
 次の例は、文字データのフィールドを 3 つ含んだデータ ファイルを記述する XML フォーマット ファイルを示しています。 フォーマット ファイルによって、データ ファイルを 3 列のテーブルにマッピングします。ここでは、データ ファイルのフィールドの順序とマッピング先のテーブルの列の順序は異なるものとします。  
  
 **テーブル (行):** Person (Age int, FirstName varchar(20), LastName varchar(30))  
  
 **データ ファイル** (レコード): Age\<tab>Lastname\<tab>Firstname\<return>  
  
 `<RECORD>` 要素では、3 つのフィールドすべてのデータ値が文字データとして表現されています。  
  
 `<ROW>` 要素で、列 `Age` を 1 番目のフィールドに、列 `FirstName` を 3 番目のフィールドに、列 `LastName` を 2 番目のフィールドにマッピングします。  
  
```xml
<?xml version="1.0"?>  
<BCPFORMAT   
xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format"   
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
  <RECORD>  
    <FIELD ID="1" xsi:type="CharTerm" TERMINATOR="\t"   
      MAX_LENGTH="12"/>  
    <FIELD ID="2" xsi:type="CharTerm" TERMINATOR="\t" MAX_LENGTH="20"   
      COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
    <FIELD ID="3" xsi:type="CharTerm" TERMINATOR="\r\n"   
      MAX_LENGTH="30" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
  </RECORD>  
  <ROW>  
    <COLUMN SOURCE="1" NAME="age" xsi:type="SQLINT"/>  
    <COLUMN SOURCE="3" NAME="firstname" xsi:type="SQLVARYCHAR"/>  
    <COLUMN SOURCE="2" NAME="lastname" xsi:type="SQLVARYCHAR"/>  
  </ROW>  
</BCPFORMAT>  
```  
  
> [!NOTE]  
>  [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] を使用した同様の例については、「[フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)」を参照してください。  
  
###  <a name="c-omitting-a-data-field"></a><a name="OmitField"></a> C. データ フィールドをスキップする場合  
 次の例は、文字データのフィールドを 4 つ含んだデータ ファイルを記述する XML フォーマット ファイルを示しています。 フォーマット ファイルによって、このデータ ファイルを 3 列のテーブルにマッピングします。 2 番目のデータ フィールドは対応するテーブル列がありません。  
  
 **テーブル (行):** Person (Age int, FirstName Varchar(20), LastName Varchar(30))  
  
 **データ ファイル (レコード):** Age\<tab>employeeID\<tab>Firstname\<tab>Lastname\<return>  
  
 `<RECORD>` 要素では、4 つのフィールドすべてのデータ値が文字データとして表現されています。 各フィールドの TERMINATOR 属性は、データ値の後のターミネータを示します。  
  
 `<ROW>` 要素で、列 `Age` を 1 番目のフィールドに、列 `FirstName` を 3 番目のフィールドに、列 `LastName` を 4 番目のフィールドにマッピングします。  
  
```xml
<?xml version = "1.0"?>  
<BCPFORMAT   
xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format"   
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
  <RECORD>  
    <FIELD ID="1" xsi:type="CharTerm" TERMINATOR="\t"   
      MAX_LENGTH="12"/>  
    <FIELD ID="2" xsi:type="CharTerm" TERMINATOR="\t"   
      MAX_LENGTH="10"   
      COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
    <FIELD ID="3" xsi:type="CharTerm" TERMINATOR="\t"   
      MAX_LENGTH="20"   
      COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
    <FIELD ID="4" xsi:type="CharTerm" TERMINATOR="\r\n"   
      MAX_LENGTH="30"   
      COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
  </RECORD>  
  <ROW>  
    <COLUMN SOURCE="1" NAME="age" xsi:type="SQLINT"/>  
    <COLUMN SOURCE="3" NAME="firstname" xsi:type="SQLVARYCHAR"/>  
    <COLUMN SOURCE="4" NAME="lastname" xsi:type="SQLVARYCHAR"/>  
  </ROW>  
</BCPFORMAT>  
```  
  
> [!NOTE]  
>  [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] を使用した同様の例については、「[フォーマット ファイルを使用したデータ フィールドのスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)」を参照してください。  
  
###  <a name="d-mapping-field-xsitype-to-column-xsitype"></a><a name="MapXSItype"></a> D. \<FIELD> xsi: type を \<COLUMN> xsi: type にマッピングする場合  
 次の例では、さまざまな型のフィールド、および列への各フィールドのマッピングを示します。  
  
```xml
<?xml version = "1.0"?>  
<BCPFORMAT  
xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format"   
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
   <RECORD>  
      <FIELD xsi:type="CharTerm" ID="C1" TERMINATOR="\t"   
            MAX_LENGTH="4"/>  
      <FIELD xsi:type="CharFixed" ID="C2" LENGTH="10"   
         COLLATION="SQL_LATIN1_GENERAL_CP1_CI_AS"/>  
      <FIELD xsi:type="CharPrefix" ID="C3" PREFIX_LENGTH="2"   
         MAX_LENGTH="32" COLLATION="SQL_LATIN1_GENERAL_CP1_CI_AS"/>  
      <FIELD xsi:type="NCharTerm" ID="C4" TERMINATOR="\t"   
         MAX_LENGTH="4"/>  
      <FIELD xsi:type="NCharFixed" ID="C5" LENGTH="10"   
         COLLATION="SQL_LATIN1_GENERAL_CP1_CI_AS"/>  
      <FIELD xsi:type="NCharPrefix" ID="C6" PREFIX_LENGTH="2"   
         MAX_LENGTH="32" COLLATION="SQL_LATIN1_GENERAL_CP1_CI_AS"/>  
      <FIELD xsi:type="NativeFixed" ID="C7" LENGTH="4"/>  
   </RECORD>  
   <ROW>  
      <COLUMN SOURCE="C1" NAME="Age" xsi:type="SQLTINYINT"/>  
      <COLUMN SOURCE="C2" NAME="FirstName" xsi:type="SQLVARYCHAR"   
      LENGTH="16" NULLABLE="NO"/>  
      <COLUMN SOURCE="C3" NAME="LastName" />  
      <COLUMN SOURCE="C4" NAME="Salary" xsi:type="SQLMONEY"/>  
      <COLUMN SOURCE="C5" NAME="Picture" xsi:type="SQLIMAGE"/>  
      <COLUMN SOURCE="C6" NAME="Bio" xsi:type="SQLTEXT"/>  
      <COLUMN SOURCE="C7" NAME="Interest"xsi:type="SQLDECIMAL"   
      PRECISION="5" SCALE="3"/>  
   </ROW>  
</BCPFORMAT>  
```  
  
###  <a name="e-mapping-xml-data-to-a-table"></a><a name="MapXMLDataToTbl"></a> E. XML データをテーブルにマッピングする場合  
 次の例では、2 列の空テーブル (`t_xml`) を作成し、1 番目の列を `int` データ型に、2 番目の列を `xml` データ型にマッピングしています。  
  
```sql
CREATE TABLE t_xml (c1 int, c2 xml)  
```  
  
 次の XML フォーマット ファイルを使用すると、テーブル `t_xml`にデータ ファイルが読み込まれます。  
  
```xml
<?xml version="1.0"?>  
<BCPFORMAT xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format"   
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
 <RECORD>  
  <FIELD ID="1" xsi:type="NativePrefix" PREFIX_LENGTH="1"/>  
  <FIELD ID="2" xsi:type="NCharPrefix" PREFIX_LENGTH="8"/>  
 </RECORD>  
 <ROW>  
  <COLUMN SOURCE="1" NAME="c1" xsi:type="SQLINT"/>  
  <COLUMN SOURCE="2" NAME="c2" xsi:type="SQLNCHAR"/>  
 </ROW>  
</BCPFORMAT>  
```  
  
###  <a name="f-importing-fixed-length-or-fixed-width-fields"></a><a name="ImportFixedFields"></a> F. 固定長フィールドまたは固定幅フィールドをインポートする場合  
 次の例では、長さと幅がそれぞれ `10` 文字または `6` 文字に固定されたフィールドについて説明します。 これらのフィールドの長さと幅は `LENGTH="10"` および `LENGTH="6"`としてそれぞれ表現されています。 データ ファイルのすべての行の末尾には、復帰と改行の組み合わせ ({CR}{LF}) が記述されます。フォーマット ファイルでは `TERMINATOR="\r\n"`として表現されています。  
  
```xml
<?xml version="1.0"?>  
<BCPFORMAT  
       xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
  <RECORD>  
    <FIELD ID="1" xsi:type="CharFixed" LENGTH="10"/>  
    <FIELD ID="2" xsi:type="CharFixed" LENGTH="6"/>  
    <FIELD ID="3" xsi:type="CharTerm" TERMINATOR="\r\n"/>  
  </RECORD>  
  <ROW>  
    <COLUMN SOURCE="1" NAME="C1" xsi:type="SQLINT" />  
    <COLUMN SOURCE="2" NAME="C2" xsi:type="SQLINT" />  
  </ROW>  
</BCPFORMAT>  
```  
  
###  <a name="additional-examples"></a><a name="AdditionalExamples"></a> その他の例  
 XML 以外のフォーマット ファイルと XML フォーマット ファイルに関するその他の例については、次のトピックを参照してください。  
  
-   [フォーマット ファイルを使用したテーブル列のスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)  
  
-   [フォーマット ファイルを使用したデータ フィールドのスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)  
  
-   [フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 関連タスク  
  
-   [フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)  
  
-   [データの一括インポートでのフォーマット ファイルの使用 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)  
  
-   [フォーマット ファイルを使用したテーブル列のスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)  
  
-   [フォーマット ファイルを使用したデータ フィールドのスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)  
  
-   [フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 関連コンテンツ  
 [なし] :  
  
## <a name="see-also"></a>参照  
 [データの一括インポートと一括エクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/bulk-import-and-export-of-data-sql-server.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)   
 [データのインポートまたはエクスポート用のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)  
  
  
