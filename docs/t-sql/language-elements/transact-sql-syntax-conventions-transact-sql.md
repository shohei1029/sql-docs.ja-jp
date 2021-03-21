---
description: Transact-SQL 構文表記規則 (Transact-SQL)
title: Transact-SQL 構文表記規則 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/09/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- sql13.TSQLExpandPortal.f1
dev_langs:
- TSQL
helpviewer_keywords:
- conventions [SQL Server]
- Applies to section in Transact-SQL topics
- code example conventions [SQL Server]
- objects [SQL Server], names
- code [SQL Server], conventions
- multipart names [SQL Server]
- Transact-SQL syntax conventions
- syntax conventions [SQL Server]
- code [SQL Server]
- Transact-SQL
- naming conventions [SQL Server]
- syntax [SQL Server], Transact-SQL
ms.assetid: 35fbcf7f-8b55-46cd-a957-9b8c7b311241
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6a9bcfd31f5038a52fa4a155ad4026ab4061e4be
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747872"
---
# <a name="transact-sql-syntax-conventions-transact-sql"></a>Transact-SQL 構文表記規則 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

次の表は、[!INCLUDE[tsql](../../includes/tsql-md.md)] リファレンスの構文で使用している表記規則について示しています。  
  
|表記|使用目的|  
|----------------|--------------|  
|UPPERCASE|[!INCLUDE[tsql](../../includes/tsql-md.md)] キーワードを示します。|  
|_斜体_|ユーザーが指定する [!INCLUDE[tsql](../../includes/tsql-md.md)] 構文のパラメーターを示します。|  
|**太字**|データベース名、テーブル名、列名、インデックス名、ストアド プロシージャ、ユーティリティ、データ型名、およびテキストを、記載されているとおりに入力します。|  
|下線|下線が引かれている値を含む句がステートメントから省略されているときに適用される既定値を示します。|  
|&#124; (垂直線)|角かっこ、または中かっこで囲まれた構文項目を区切ります。 使用できる項目は 1 つだけです。|  
|`[ ]` (角かっこ)|省略可能な構文項目。 角かっこは入力しません。|  
|{} (中かっこ)|必須の構文項目を示します。 中かっこは入力しません。|  
|[ **,** ..._n_]|先行する項目を _n_ 回繰り返せることを示します。 出現箇所は、コンマで区切られます。|  
|[..._n_]|先行する項目を _n_ 回繰り返せることを示します。 項目は空白で区切ります。|  
|;|[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントの終端記号を示します。 セミコロンは、このバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のほとんどのステートメントでは必須ではありませんが、将来のバージョンでは必須になる予定です。|  
|\<label> ::=|構文のブロックの名前を示します。 この表記規則を使用して、1 つのステートメント内の複数の箇所で使用できる長い構文の一部、または構文の 1 単位について、グループ化してラベルを付けます。 構文のブロックを使用できる箇所は、シェブロンで囲まれたラベル (\<label>) で示します。<br /><br /> セットとは、式のコレクションです (たとえば \<grouping set>)。リストとは、セットのコレクションです (たとえば \<composite element list>)。|  
  
## <a name="multipart-names"></a>マルチパート名  
特に指定のない限り、データベース オブジェクトの名前に対するすべての [!INCLUDE[tsql](../../includes/tsql-md.md)] 参照は、次のような 4 部構成の名前の形式をとります。  
  
_server\_name_.[_database\_name_].[_schema\_name_]._object\_name_  
  
| _database\_name_.[_schema\_name_]._object\_name_  
 
| _schema\_name_._object\_name_  
  
| _object\_name_  
  
_server\_name_  
リンク サーバー名またはリモート サーバー名を示します。  
  
_database\_name_  
オブジェクトが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のローカル インスタンスに存在するときは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースの名前を指定します。 オブジェクトがリンク サーバーに存在するとき、*database_name* には OLE DB カタログを指定します。  
  
_schema\_name_  
オブジェクトが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースに存在する場合は、そのオブジェクトを含むスキーマの名前を示します。 オブジェクトがリンク サーバーに存在するとき、*schema_name* には OLE DB スキーマ名を指定します。  
  
_object\_name_  
オブジェクトの名前を示します。  
  
特定のオブジェクトを参照する場合、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] にオブジェクトを識別させるために、必ずしもサーバー、データベース、所有者を指定する必要はありません。 ただし、オブジェクトが見つからない場合は、エラーが返されます。  
  
> [!NOTE]  
> 名前解決のエラーを回避するために、スキーマ スコープ オブジェクトを指定するときは、常にスキーマ名を指定することをお勧めします。  
  
中間のノードを省略するには、ピリオドを使用してそれらの位置を示します。 次の表に、オブジェクト名の有効な形式を示します。  
  
|オブジェクト参照形式|説明|  
|-----------------------------|-----------------|  
|_server_._database_._schema_._object_|4 部構成の名前です。|  
|_server_._database_.._object_|スキーマ名を省略しています。|  
|_server_.._schema_._object_|データベース名を省略しています。|  
|_server_..._object_|データベースとスキーマ名を省略しています。|  
|_database_._schema_._object_|サーバー名を省略しています。|  
|_database_.._object_|サーバーとスキーマ名を省略しています。|  
|_schema_._object_|サーバーとデータベース名を省略しています。|  
|_object_|サーバー、データベース、およびスキーマ名を省略しています。|  
  
## <a name="code-example-conventions"></a>コード例の規則  
特に断りのない限り、[!INCLUDE[tsql](../../includes/tsql-md.md)] リファレンスで提供されている例は、[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を使用し、次のオプションを既定の設定にしてテストされています。  
  
-   ANSI_NULLS  
-   ANSI_NULL_DFLT_ON  
-   ANSI_PADDING  
-   ANSI_WARNINGS  
-   CONCAT_NULL_YIELDS_NULL  
-   QUOTED_IDENTIFIER  
  
[!INCLUDE[tsql](../../includes/tsql-md.md)] リファレンスのほとんどのコード例は、大文字と小文字を区別する並べ替え順を使用するサーバーでテストされています。 通常、テスト サーバーでは、ANSI/ISO 1252 コード ページが使用されています。  
  
多くのコード例では、Unicode 文字列定数に **N** という文字をプレフィックスとして加えています。プレフィックス **N** がない場合、文字列はデータベースの既定のコード ページに変換されます。 この既定のコード ページでは、一部の文字が認識されない場合があります。  
  
## <a name="applies-to-references"></a>"適用対象" リファレンス  
[!INCLUDE[tsql](../../includes/tsql-md.md)] リファレンスには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降)、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]、[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] に関連する記事が含まれています。   

各記事の先頭近くに、記事の主題をサポートする製品を示すセクションがあります。 製品が省略されている場合、記事で説明されている機能は、その製品では使用できません。 たとえば、可用性グループは [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] で導入されました。 **CREATE AVAILABILITY GROUP** の記事は、[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]、[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] に適用されないため、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) が適用対象と記載されています。  
  
場合によっては、記事の一般的な主題を製品で使用できますが、すべての引数がサポートされるわけではありません。 たとえば、包含データベース ユーザーは [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] で導入されました。 **CREATE USER** ステートメントはすべての [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 製品で使用できますが、**WITH PASSWORD** 構文は以前のバージョンでは使用できません。 記事の本文の適切な引数の説明に、「**適用対象**」セクションが追加で挿入されます。  
  
## <a name="see-also"></a>参照  
[TRANSACT-SQL リファレンス &#40;データベース エンジン&#41;](../language-reference.md)    
[予約済みキーワード &#40;Transact-SQL&#41;](../../t-sql/language-elements/reserved-keywords-transact-sql.md)      
[Transact-SQL の設計の問題](/previous-versions/visualstudio/visual-studio-2010/dd193411(v=vs.100))    
[Transact-SQL の名前付けの問題](/previous-versions/visualstudio/visual-studio-2010/dd193246(v=vs.100))        
[Transact-SQL のパフォーマンスの問題](/previous-versions/visualstudio/visual-studio-2010/dd172117(v=vs.100))