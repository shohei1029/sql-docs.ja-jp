---
description: SET ANSI_PADDING (Transact-SQL)
title: SET ANSI_PADDING (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/04/2017
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ANSI_PADDING_TSQL
- ANSI_PADDING
- SET_ANSI_PADDING_TSQL
- SET ANSI_PADDING
dev_langs:
- TSQL
helpviewer_keywords:
- data types [SQL Server], short values
- ANSI_PADDING option
- short values [SQL Server]
- SET ANSI_PADDING statement
- trailing blanks
ms.assetid: 92bd29a3-9beb-410e-b7e0-7bc1dc1ae6d0
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8e90a5092b02eaf3f646c1a12b22ad1601a01b63
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754312"
---
# <a name="set-ansi_padding-transact-sql"></a>SET ANSI_PADDING (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  列に定義されているサイズよりも短い値を列に格納する方法と、値の後に **char**、 **varchar**、 **binary**、 **varbinary** データ型で空白が続いている値を列に格納する方法を指定します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文
 
### <a name="syntax-for-ssnoversion-mdmd-and-sssodfull-mdmd"></a>[!INCLUDE[ssnoversion-md.md](../../includes/ssnoversion-md.md)] および [!INCLUDE[sssodfull-md.md](../../includes/sssodfull-md.md)] の構文 
```syntaxsql
SET ANSI_PADDING { ON | OFF }
```

### <a name="syntax-for-sssdw-mdmd-and-sspdw-mdmd"></a>[!INCLUDE[sssdw-md.md](../../includes/sssdw-md.md)] および [!INCLUDE[sspdw-md.md](../../includes/sspdw-md.md)] の構文
```syntaxsql
SET ANSI_PADDING ON
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>解説
 **char**、**varchar**、**binary**、および **varbinary** データ型で定義された列は、定義されたサイズを持ちます。  
  
 この設定は新しい列の定義にだけ影響します。 列が作成された後は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では列の作成時の設定に基づいて値が格納されます。 この設定を後で変更しても、既存の列には影響がありません。  
  
> [!NOTE]  
> ANSI_PADDING は常に ON になっている必要があります。  
  
 次の表は、**char**、**varchar**、**binary**、および **varbinary** データ型の列に値を挿入するときに、SET ANSI_PADDING の設定がどのように影響するかを示しています。  
  
|設定|char(*n*) NOT NULL または binary(*n*) NOT NULL|char(*n*) NULL または binary(*n*) NULL|varchar(*n*) または varbinary(*n*)|  
|-------------|----------------------------------------------------|--------------------------------------------|----------------------------------------|  
|ON|列の定義サイズになるように、**char** 型の列の場合は元の値の右側を空白で埋め、**binary** 型の列の場合は 0 で埋めます。|SET ANSI_PADDING が ON の場合は、**char(** _n_ **)** または **binary(** _n_ **)** NOT NULL と同じ規則に従います。|**varchar** 型の列に挿入された文字値の末尾にある空白は切り捨てられません。 **varbinary** 型の列に挿入されたバイナリ値の末尾にある 0 は切り捨てられません。 値は列の長さに合わせてパディングされません。|  
|OFF|列の定義サイズになるように、**char** 型の列の場合は元の値の右側を空白で埋め、**binary** 型の列の場合は 0 で埋めます。|SET ANSI_PADDING が OFF の場合は、**varchar** または **varbinary** と同じ規則に従います。|**varchar** 型の列に挿入された文字値の末尾にある空白は切り捨てられます。 **varbinary** 型の列に挿入されたバイナリ値の末尾にある 0 は切り捨てられます。|  
  
> [!NOTE]  
> 埋め込みが行われる場合、**char** 型の列は空白で埋められ、**binary** 型の列は 0 で埋められます。 切り捨てられる場合は、**char** 型の列では末尾の空白が切り捨てられ、**binary** 型の列では末尾の 0 が切り捨てられます。  
  
計算列やインデックス付きビューのインデックスを作成または変更するときには、ANSI_PADDING を ON に設定する必要があります。 インデックス付きビューおよび計算列上のインデックスに必要な SET オプション設定の詳細については、「[SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)」の「SET ステートメントの使用に関する留意事項」を参照してください。  
  
既定では、SET ANSI_PADDING は ON に設定されています。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーおよび [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB Provider for [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、接続時に自動的に ANSI_PADDING が ON に設定されます。 この構成は、ODBC データ ソースまたは ODBC 接続属性で定義でき、接続前にアプリケーションで設定される OLE DB 接続プロパティでも定義できます。 DB-Library アプリケーションからの接続に対しては、既定で SET ANSI_PADDING は OFF に設定されています。  
  
 SET ANSI_PADDING 設定は、**nchar**、**nvarchar**、**ntext**、**text**、**image**、**varbinary(max)**、**varchar(max)**、および **nvarchar(max)** データ型には影響しません。 これらの値では、常に SET ANSI_PADDING ON の動作が示されます。 つまり、末尾の空白と 0 は切り捨てられません。  
  
ANSI_DEFAULTS が ON の場合は、ANSI_PADDING が有効になります。  
  
ANSI_PADDING の設定は、解析時ではなく実行時に定義されます。  
  
この設定の現在の設定を表示するには、次のクエリを実行します。  
  
```sql  
DECLARE @ANSI_PADDING VARCHAR(3) = 'OFF';  
IF ( (16 & @@OPTIONS) = 16 ) SET @ANSI_PADDING = 'ON';  
SELECT @ANSI_PADDING AS ANSI_PADDING;  
```  
  
## <a name="permissions"></a>アクセス許可  
ロール **public** のメンバーシップが必要です。  
  
## <a name="examples"></a>例  
次の例では、この設定が各データ型にどのように影響するかを示しています。  

ANSI_PADDING を ON に設定し、テストします。

```sql  
PRINT 'Testing with ANSI_PADDING ON'  
SET ANSI_PADDING ON;  
GO  
  
CREATE TABLE t1 (  
   charcol CHAR(16) NULL,   
   varcharcol VARCHAR(16) NULL,   
   varbinarycol VARBINARY(8)  
);  
GO  
INSERT INTO t1 VALUES ('No blanks', 'No blanks', 0x00ee);  
INSERT INTO t1 VALUES ('Trailing blank ', 'Trailing blank ', 0x00ee00);  
  
SELECT 'CHAR' = '>' + charcol + '\<', 'VARCHAR'='>' + varcharcol + '\<',  
   varbinarycol  
FROM t1;  
GO  
```

次に ANSI_PADDING を OFF に設定し、テストします。

```sql
PRINT 'Testing with ANSI_PADDING OFF';  
SET ANSI_PADDING OFF;  
GO  
  
CREATE TABLE t2 (  
   charcol CHAR(16) NULL,   
   varcharcol VARCHAR(16) NULL,   
   varbinarycol VARBINARY(8)  
);  
GO  
INSERT INTO t2 VALUES ('No blanks', 'No blanks', 0x00ee);  
INSERT INTO t2 VALUES ('Trailing blank ', 'Trailing blank ', 0x00ee00);  
  
SELECT 'CHAR' = '>' + charcol + '\<', 'VARCHAR'='>' + varcharcol + '<',  
   varbinarycol  
FROM t2;  
GO  
  
DROP TABLE t1;  
DROP TABLE t2;  
```  
  
## <a name="see-also"></a>参照  
 [SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SESSIONPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/sessionproperty-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [SET ANSI_DEFAULTS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-defaults-transact-sql.md)  
  
  
