---
description: SET ANSI_WARNINGS (Transact-SQL)
title: SET ANSI_WARNINGS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/15/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SET ANSI_WARNINGS
- ANSI_WARNINGS_TSQL
- ANSI_WARNINGS
- SET_ANSI_WARNINGS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- errors [SQL Server], ISO standard behavior
- warnings [SQL Server]
- SET ANSI_WARNINGS statement
- ANSI_WARNINGS option
ms.assetid: f82aaab0-334f-427b-89b0-de4af596b4fa
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9e0d90ea49db8084bef3b59c58c85de4def9a32f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104734180"
---
# <a name="set-ansi_warnings-transact-sql"></a>SET ANSI_WARNINGS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  複数のエラー条件に対して ISO 標準の動作をすることを指定します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文

### <a name="syntax-for-ssnoversion-mdmd-and-sssodfull-mdmd"></a>[!INCLUDE[ssnoversion-md.md](../../includes/ssnoversion-md.md)] および [!INCLUDE[sssodfull-md.md](../../includes/sssodfull-md.md)] の構文
```syntaxsql
SET ANSI_WARNINGS { ON | OFF }
```

### <a name="syntax-for-sssdw-mdmd-and-sspdw-mdmd"></a>[!INCLUDE[sssdw-md.md](../../includes/sssdw-md.md)] および [!INCLUDE[sspdw-md.md](../../includes/sspdw-md.md)] の構文
```syntaxsql
SET ANSI_WARNINGS ON
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>解説
 SET ANSI_WARNINGS は、次の条件に影響します。  
  
-   ON に設定した場合、SUM、AVG、MAX、MIN、STDEV、STDEVP、VAR、VARP、COUNT などの集計関数に NULL 値が含まれていると、警告メッセージが生成されます。 OFF の場合、警告メッセージは生成されません。  
  
-   ON に設定した場合、0 除算や演算オーバーフロー エラーが発生すると、ステートメントはロールバックされエラー メッセージが生成されます。 OFF に設定した場合、0 除算や演算オーバーフロー エラーが発生すると、NULL 値が返されます。 INSERT または UPDATE が **character**、Unicode、**binary** 型の列に対して実行され、新しい値の長さが列の最大サイズを超過すると、0 除算や演算オーバーフロー エラーが原因となって NULL 値が返されます。 SET ANSI_WARNINGS が ON の場合、INSERT または UPDATE は ISO 標準の指定に従って取り消されます。 文字型の列の末尾の空白文字とバイナリ列の末尾の NULL 値は無視されます。 OFF の場合、データは列のサイズに切り捨てられ、ステートメントは成功します。  
  
> [!NOTE]  
> **binary** データと **varbinary** データの型変換で切り捨てが発生した場合は、SET オプションの設定に関係なく、警告やエラーは発生しません。  
  
> [!NOTE]  
> ストアド プロシージャでパラメーターを引き渡す場合や、バッチ ステートメントで変数を宣言または設定する場合、またはユーザー定義関数においては、ANSI_WARNINGS は無視されます。 たとえば、変数を **char(3)** と定義し、これに 4 文字以上の値を設定すると、データが定義されたサイズに合わせて切り捨てられてから、INSERT または UPDATE ステートメントが成功します。  
  
sp_configure の user options オプションを使用すると、ANSI_WARNINGS の既定の設定をサーバーに対するすべての接続に適用できます。 詳細については、「 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)」を参照してください。  
  
計算列やインデックス付きビューのインデックスを作成または操作するときには、ANSI_WARNINGS を ON に設定する必要があります。 SET ANSI_WARNINGS が OFF の場合、計算列にインデックスが設定されているテーブルやインデックス付きビューに対して CREATE、UPDATE、INSERT、または DELETE ステートメントを実行すると失敗します。 インデックス付きビューおよび計算列上のインデックスに必要な SET オプション設定の詳細については、「[SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)」の「SET ステートメントの使用に関する留意事項」を参照してください。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] には、ANSI_WARNINGS データベース オプションが用意されています。 これは、SET ANSI_WARNINGS と同じです。 SET ANSI_WARNINGS が ON の場合、0 除算やデータベースの列のサイズを超える文字列、または同様のエラーが発生すると、エラーまたは警告が発生します。 SET ANSI_WARNINGS が OFF の場合、これらのエラーや警告は発生しません。 model データベースでは、SET ANSI_WARNINGS の既定の設定は OFF です。 指定しない場合は、ANSI_WARNINGS の設定が適用されます。 SET ANSI_WARNINGS を OFF に設定した場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) カタログ ビューの is_ansi_warnings_on 列の値が使用されます。  
  
> [!IMPORTANT]
> 分散クエリを実行する場合は、ANSI_WARNINGS を ON に設定してください。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバー、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB Provider for [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、および Microsoft JDBC Driver for [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] などのクライアントでは、接続フラグで ANSI_WARNINGS が自動的にオンに設定されます。 この構成は、ODBC データ ソースまたは ODBC 接続属性で定義され、接続前にアプリケーションで設定できます。 DB-Library アプリケーションからの接続に対しては、既定では SET ANSI_WARNINGS は OFF に設定されています。 詳細については、表形式データ ストリーム (TDS) プロトコル仕様の [LOGIN7](/openspecs/windows_protocols/ms-tds/773a62b6-ee89-4c02-9e5e-344882630aac) を参照してください。 

ANSI_DEFAULTS が ON の場合は、ANSI_WARNINGS は有効になります。  
  
ANSI_WARNINGS の設定は、解析時ではなく実行時に定義されます。  
  
SET ARITHABORT と SET ARITHIGNORE のいずれかが OFF でも、SET ANSI_WARNINGS が ON の場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で 0 除算やオーバーフロー エラーが検出されるとエラー メッセージが返されます。  
  
この設定の現在の設定を表示するには、次のクエリを実行します。  
  
```sql  
DECLARE @ANSI_WARN VARCHAR(3) = 'OFF';  
IF ( (8 & @@OPTIONS) = 8 ) SET @ANSI_WARN = 'ON';  
SELECT @ANSI_WARN AS ANSI_WARNINGS;  
```  
  
## <a name="permissions"></a>アクセス許可  
ロール **public** のメンバーシップが必要です。  
  
## <a name="examples"></a>例  
次の例では、SET ANSI_WARNINGS が ON の場合と OFF の場合に分けて、上の 3 つの状況を示しています。  
  
```sql  
CREATE TABLE T1   
(  
   a int,   
   b int NULL,   
   c varchar(20)  
);  
GO  
  
SET NOCOUNT ON;  
  
INSERT INTO T1   
VALUES (1, NULL, '')   
      ,(1, 0, '')  
      ,(2, 1, '')  
      ,(2, 2, '');  
  
SET NOCOUNT OFF;  
GO  
```

次に ANSI_WARNINGS を ON に設定し、テストします。

```sql
PRINT '**** Setting ANSI_WARNINGS ON';  
GO  
  
SET ANSI_WARNINGS ON;  
GO  
  
PRINT 'Testing NULL in aggregate';  
GO  
SELECT a, SUM(b)   
FROM T1   
GROUP BY a;  
GO  
  
PRINT 'Testing String Overflow in INSERT';  
GO  
INSERT INTO T1   
VALUES (3, 3, 'Text string longer than 20 characters');  
GO  
  
PRINT 'Testing Divide by zero';  
GO  
SELECT a / b AS ab   
FROM T1;  
GO  
```

次に ANSI_WARNINGS を OFF に設定し、テストします。

```sql
PRINT '**** Setting ANSI_WARNINGS OFF';  
GO  
SET ANSI_WARNINGS OFF;  
GO  
  
PRINT 'Testing NULL in aggregate';  
GO  
SELECT a, SUM(b)   
FROM T1   
GROUP BY a;  
GO  
  
PRINT 'Testing String Overflow in INSERT';  
GO  
INSERT INTO T1   
VALUES (4, 4, 'Text string longer than 20 characters');  
GO  
SELECT a, b, c   
FROM T1  
WHERE a = 4;  
GO  
  
PRINT 'Testing Divide by zero';  
GO  
SELECT a / b AS ab   
FROM T1;  
GO  
  
DROP TABLE T1;  
```  
  
## <a name="see-also"></a>参照  
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET ANSI_DEFAULTS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-defaults-transact-sql.md)   
 [SESSIONPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/sessionproperty-transact-sql.md)  
  
