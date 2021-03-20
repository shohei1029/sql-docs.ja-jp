---
description: SET ANSI_DEFAULTS (Transact-SQL)
title: SET ANSI_DEFAULTS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/16/2020
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SET ANSI_DEFAULTS
- ANSI_DEFAULTS
- SET_ANSI_DEFAULTS_TSQL
- ANSI_DEFAULTS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- ANSI_DEFAULTS option
- SET ANSI_DEFAULTS statement
ms.assetid: bd721d97-6e23-488b-8c8c-c0453d5b3b86
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 552fb5a2de6220f46b2fb2b0ff9aa036b0c10a92
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756002"
---
# <a name="set-ansi_defaults-transact-sql"></a>SET ANSI_DEFAULTS (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  一部の ISO 標準動作を集合的に指定する、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 設定のグループを制御します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  

## <a name="syntax"></a>構文

### <a name="syntax-for-ssnoversion-mdmd-and-sssodfull-mdmd"></a>[!INCLUDE[ssnoversion-md.md](../../includes/ssnoversion-md.md)] および [!INCLUDE[sssodfull-md.md](../../includes/sssodfull-md.md)] の構文
```syntaxsql
SET ANSI_DEFAULTS { ON | OFF }
```

### <a name="syntax-for-sssdw-mdmd-and-sspdw-mdmd"></a>[!INCLUDE[sssdw-md.md](../../includes/sssdw-md.md)] および [!INCLUDE[sspdw-md.md](../../includes/sspdw-md.md)] の構文
```syntaxsql
SET ANSI_DEFAULTS ON
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>解説
ANSI_DEFAULTS はサーバー側の設定であり、すべてのクライアント接続の動作を有効にすることができます。 クライアントでは、通常、接続またはセッションの初期化時に設定を要求します。 サーバー設定は、ユーザーが変更するものではありません。   
ユーザーがクライアントの動作を変更するには、`SQL_COPT_SS_PRESERVE_CURSORS` などのクライアント固有のメソッドを使用する必要があります。 詳細については、「[SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md)」を参照してください。
  
有効 (ON) に設定すると、次の ISO 設定が有効になります。  
  
:::row:::
    :::column:::
        [SET ANSI_NULLS]
    :::column-end:::
    :::column:::
        [SET CURSOR_CLOSE_ON_COMMIT]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [SET ANSI_NULL_DFLT_ON]
    :::column-end:::
    :::column:::
        [SET IMPLICIT_TRANSACTIONS]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [SET ANSI_PADDING]
    :::column-end:::
    :::column:::
        [SET QUOTED_IDENTIFIER]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [SET ANSI_WARNINGS]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

&nbsp;

また、ユーザーの作業セッション時や、トリガーまたはストアド プロシージャの実行時は、これらの ISO 標準 SET オプションは、クエリ処理環境を定義します。 ただし、これらの SET オプションには ISO 標準に準拠するために必要なオプションがすべて含まれているわけではありません。  
  
計算列およびインデックス付きビューにおいてインデックスを操作する場合は、これらの 4 つの既定値 (`ANSI_NULLS`、`ANSI_PADDING`、`ANSI_WARNINGS`、および `QUOTED_IDENTIFIER`) を ON に設定する必要があります。 これは、計算列とインデックス付きビューにおいてインデックスを作成および変更するときに、指定された値に設定する必要がある 7 つの SET オプションの中の 4 つのオプションです。 その他の SET オプションは、`ARITHABORT` (ON)、`CONCAT_NULL_YIELDS_NULL` (ON)、および `NUMERIC_ROUNDABORT` (OFF) です。 インデックス付きビューおよび計算列上のインデックスに必要な SET オプション設定の詳細については、「[SET ステートメントの使用に関する留意事項](../../t-sql/statements/set-statements-transact-sql.md#considerations-when-you-use-the-set-statements)」を参照してください。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーおよび [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB Provider for [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、接続時に自動的に ANSI_DEFAULTS が ON に設定されます。 次に、このドライバーとプロバイダーによって、CURSOR_CLOSE_ON_COMMIT と IMPLICIT_TRANSACTIONS が OFF に設定されます。 `CURSOR_CLOSE_ON_COMMIT` および `IMPLICIT_TRANSACTIONS` の OFF 設定は、ODBC データ ソースまたは ODBC 接続属性で、あるいは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続する前にアプリケーションに設定される OLE DB 接続プロパティで構成できます。 DB-Library アプリケーションからの接続に対しては、`ANSI_DEFAULTS` は既定で OFF に設定されています。  
  
SET ANSI_DEFAULTS を実行すると、QUOTED_IDENTIFIER は解析時に設定され、次のオプションは実行時に設定されます。  
  
:::row:::
    :::column:::
        [SET ANSI_NULLS]
    :::column-end:::
    :::column:::
        [SET ANSI_WARNINGS]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [SET ANSI_NULL_DFLT_ON]
    :::column-end:::
    :::column:::
        [SET CURSOR_CLOSE_ON_COMMIT]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [SET ANSI_PADDING]
    :::column-end:::
    :::column:::
        [SET IMPLICIT_TRANSACTIONS]
    :::column-end:::
:::row-end:::

## <a name="permissions"></a>アクセス許可  
ロール **public** のメンバーシップが必要です。  
  
## <a name="examples"></a>例  
次の例では、ANSI_DEFAULTS を ON に設定し、`DBCC USEROPTIONS` ステートメントを使用して、影響を受ける設定を表示します。  
  
```sql  
-- SET ANSI_DEFAULTS ON.  
SET ANSI_DEFAULTS ON;  
GO  

-- Display the current settings.  
DBCC USEROPTIONS;  
GO 

-- SET ANSI_DEFAULTS OFF.  
SET ANSI_DEFAULTS OFF;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [DBCC USEROPTIONS &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-useroptions-transact-sql.md)   
 [SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET ANSI_NULL_DFLT_ON &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-null-dflt-on-transact-sql.md)   
 [SET ANSI_NULLS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-nulls-transact-sql.md)   
 [SET ANSI_PADDING &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-padding-transact-sql.md)   
 [SET ANSI_WARNINGS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-warnings-transact-sql.md)   
 [SET CURSOR_CLOSE_ON_COMMIT &#40;Transact-SQL&#41;](../../t-sql/statements/set-cursor-close-on-commit-transact-sql.md)   
 [SET IMPLICIT_TRANSACTIONS &#40;Transact-SQL&#41;](../../t-sql/statements/set-implicit-transactions-transact-sql.md)   
 [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)  
  
  
