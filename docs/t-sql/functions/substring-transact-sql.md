---
title: SUBSTRING (Transact-SQL) | Microsoft Docs
description: SUBSTRING 関数の Transact-SQL リファレンス。 この関数からは、指定の文字、バイナリ、テキスト、イメージ型の式の一部が返されます。
ms.date: 10/21/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SUBSTRING
- SUBSTRING_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- portion of expression returned [SQL Server]
- part of expression returned [SQL Server]
- SUBSTRING function
- offsets [SQL Server]
- binary [SQL Server], returning part of
- expressions [SQL Server], part returned
- characters [SQL Server], returning part of
ms.assetid: a19c808f-aaf9-4a69-af59-b1a5fc3e5c4c
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6cfd3ffa82b9aaf333df0512f76860c726b1fd2f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753432"
---
# <a name="substring-transact-sql"></a>SUBSTRING (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で、文字、バイナリ、テキスト、またはイメージ型の式の一部を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
SUBSTRING ( expression ,start , length )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *式 (expression)*  
 **character**、**binary**、**text**、**ntext**、または **image** [式](../../t-sql/language-elements/expressions-transact-sql.md)です。  
  
 *start*  
 返された文字の開始位置を示す integer 式または **bigint** 式です。 (番号は 1 から開始し、これは式の最初の文字が 1 であることを意味します)。 *start* に 1 より小さい値を指定した場合は、*expression* に指定された文字の先頭から値が返されます。 この場合、返される文字数は、*start* + *length*-1 の合計値か、0 のどちらか大きい方になります。 *start* が値式の文字数を上回る場合は、長さがゼロの式が返されます。  
  
 *length*  
 *expression* で返す文字数を正の整数または **bigint** 式で指定します。 *length* が負の場合はエラーが生成され、ステートメントは終了します。 *start* と *length* の合計が *expression* の文字数を上回る場合は、*start* の先頭から値式全体が返されます。  
  
## <a name="return-types"></a>戻り値の型  
 *expression* が、サポートされている文字データ型の 1 つである場合は、文字データが返されます。 *expression* が、サポートされている **binary** データ型の 1 つである場合は、binary データが返されます。 返される文字列のデータ型は、指定した式のデータ型と同じです。ただし、次の表の場合は例外です。  
  
|指定した式|の戻り値の型 :|  
|--------------------------|-----------------|  
|**char**/**varchar**/**text**|**varchar**|  
|**nchar**/**nvarchar**/**ntext**|**nvarchar**|  
|**binary**/**varbinary**/**image**|**varbinary**|  
  
## <a name="remarks"></a>解説  
 値は、 *開始* と *長さ* の文字数で指定する必要があります **ntext**、**char**、または **varchar** データ型とのバイト **text**、**image**、**binary**、または **varbinary** データ型。  
  
 *式* する必要があります **varchar (max)** または **varbinary (max)** ときに、 *開始* または *長さ* 2,147, 483,647 を超える値が含まれています。  
  
## <a name="supplementary-characters-surrogate-pairs"></a>補助文字 (サロゲート ペア)  
 補助文字 (SC) の照合順序を使用する場合、*start* と *length* では、*expression* の各サロゲート ペアが 1 文字としてカウントされます。 詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-substring-with-a-character-string"></a>A. SUBSTRING に文字列を使用する  
 次の例では、文字列の一部分のみを返す方法を示しています。 `sys.databases` テーブルから、このクエリは最初の列でシステム データベース名、2 番目の列でデータベースの最初の文字、最後の列で 3 番目と 4 番目の文字を返します。  
  
```sql
SELECT name, SUBSTRING(name, 1, 1) AS Initial ,
SUBSTRING(name, 3, 2) AS ThirdAndFourthCharacters
FROM sys.databases  
WHERE database_id < 5;   
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  

|name |Initial |ThirdAndFourthCharacters|
|---|--|--|
|master    |m    |st |
|tempdb    |t    |mp |
|model    |m    |de |
|msdb    |m    |db |


  
 文字列定数 `abcdef` の 2 番目、3 番目、および 4 番目の文字を表示するには、次のようにします。  
  
```sql
SELECT x = SUBSTRING('abcdef', 2, 3);  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
x  
----------  
bcd  
  
(1 row(s) affected)
```  
  
### <a name="b-using-substring-with-text-ntext-and-image-data"></a>B. SUBSTRING に text、ntext、および image 型のデータを使用する  
  
> [!NOTE]  
>  次の例を実行するには、**pubs** データベースをインストールする必要があります。  
  
 次の例では、`pubs` データベースにある `pub_info` テーブルの **text** および **image** データ列から、それぞれ最初の 10 文字を返す方法を示します。 **text** としてデータが返される **varchar**, 、および **image** としてデータが返されます **varbinary** です。  
  
```sql
USE pubs;  
SELECT pub_id, SUBSTRING(logo, 1, 10) AS logo,   
   SUBSTRING(pr_info, 1, 10) AS pr_info  
FROM pub_info  
WHERE pub_id = '1756';  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 pub_id logo    pr_info
------ ---------------------- ----------
1756   0x474946383961E3002500 This is sa

(1 row(s) affected)
```  
  
 次の例では、**text** データと **ntext** データの両方に対する SUBSTRING の効果を示します。 この例では最初に、`npub_info` という名前の `pubs` データベースに新しいテーブルを作成します。 次に、`pr_info` 列の最初の 80 文字から `npub_info` テーブルの `pub_info.pr_info` 列を作成し、最初の文字として `ü` を追加します。 最後に、`INNER JOIN` を使って、**text** および **ntext** の両方のパブリッシャー情報列から、すべてのパブリッシャー ID 番号と `SUBSTRING` を取得します。  
  
```sql
IF EXISTS (SELECT table_name FROM INFORMATION_SCHEMA.TABLES   
      WHERE table_name = 'npub_info')  
   DROP TABLE npub_info;  
GO  
-- Create npub_info table in pubs database. Borrowed from instpubs.sql.  
USE pubs;  
GO  
CREATE TABLE npub_info  
(  
 pub_id CHAR(4) NOT NULL  
    REFERENCES publishers(pub_id)  
    CONSTRAINT UPKCL_npubinfo PRIMARY KEY CLUSTERED,  
pr_info ntext NULL  
);  
  
GO  
  
-- Fill the pr_info column in npub_info with international data.  
RAISERROR('Now at the inserts to pub_info...',0,1);  
  
GO  
  
INSERT npub_info VALUES('0736', N'üThis is sample text data for New Moon Books, publisher 0736 in the pubs database')  
,('0877', N'üThis is sample text data for Binnet & Hardley, publisher 0877 in the pubs databa')  
,('1389', N'üThis is sample text data for Algodata Infosystems, publisher 1389 in the pubs da')  
,('9952', N'üThis is sample text data for Scootney Books, publisher 9952 in the pubs database')  
,('1622', N'üThis is sample text data for Five Lakes Publishing, publisher 1622 in the pubs d')  
,('1756', N'üThis is sample text data for Ramona Publishers, publisher 1756 in the pubs datab')  
,('9901', N'üThis is sample text data for GGG&G, publisher 9901 in the pubs database. GGG&G i')  
,('9999', N'üThis is sample text data for Lucerne Publishing, publisher 9999 in the pubs data');  
GO  
-- Join between npub_info and pub_info on pub_id.  
SELECT pr.pub_id, SUBSTRING(pr.pr_info, 1, 35) AS pr_info,  
   SUBSTRING(npr.pr_info, 1, 35) AS npr_info  
FROM pub_info pr INNER JOIN npub_info npr  
   ON pr.pub_id = npr.pub_id  
ORDER BY pr.pub_id ASC;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-using-substring-with-a-character-string"></a>C. SUBSTRING に文字列を使用する  
 次の例では、文字列の一部分のみを返す方法を示しています。 このクエリでは、`dbo.DimEmployee` テーブルから、最初の列に姓を、2 番目の列には名のイニシャルのみを返します。  
  
```sql
-- Uses AdventureWorks  
  
SELECT LastName, SUBSTRING(FirstName, 1, 1) AS Initial  
FROM dbo.DimEmployee  
WHERE LastName LIKE 'Bar%'  
ORDER BY LastName;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
LastName             Initial
-------------------- -------
Barbariol            A
Barber               D
Barreto de Mattos    P
```  
  
 次の例では、文字列定数 `abcdef` の 2 番目、3 番目、4 番目の文字を返す方法を示します。  
  
```sql
USE ssawPDW;  
  
SELECT TOP 1 SUBSTRING('abcdef', 2, 3) AS x FROM dbo.DimCustomer;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
x
-----
bcd
```  
  
## <a name="see-also"></a>参照  
 [LEFT &#40;Transact-SQL&#41;](../../t-sql/functions/left-transact-sql.md)  
 [LTRIM &#40;Transact-SQL&#41;](../../t-sql/functions/ltrim-transact-sql.md)  
 [RIGHT &#40;Transact-SQL&#41;](../../t-sql/functions/right-transact-sql.md)  
 [RTRIM &#40;Transact-SQL&#41;](../../t-sql/functions/rtrim-transact-sql.md)  
 [STRING_SPLIT &#40;Transact-SQL&#41;](../../t-sql/functions/string-split-transact-sql.md)  
 [TRIM &#40;Transact-SQL&#41;](../../t-sql/functions/trim-transact-sql.md)  
 [文字列関数 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)  
  
  


