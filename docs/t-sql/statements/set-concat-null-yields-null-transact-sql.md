---
description: SET CONCAT_NULL_YIELDS_NULL (Transact-SQL)
title: SET CONCAT_NULL_YIELDS_NULL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CONCAT_NULL_YIELDS_NULL_TSQL
- SET CONCAT_NULL_YIELDS_NULL
- CONCAT_NULL_YIELDS_NULL
- SET_CONCAT_NULL_YIELDS_NULL_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CONCAT_NULL_YIELDS_NULL option
- null values [SQL Server], concatenation results
- concatenation [SQL Server]
- SET CONCAT_NULL_YIELDS_NULL statement
ms.assetid: 3091b71c-6518-4eb4-88ab-acae49102bc5
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 703e3a34b5d704cef56f15931e491e6424d4b98e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754462"
---
# <a name="set-concat_null_yields_null-transact-sql"></a>SET CONCAT_NULL_YIELDS_NULL (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  連結の結果が NULL として取り扱われるのか、空文字列として取り扱われるのかを制御します。  
  
> [!IMPORTANT]  
>  今後のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、CONCAT_NULL_YIELDS_NULL が常に ON になり、このオプションを明示的に OFF に設定するすべてのアプリケーションでエラーが発生します。 新規の開発作業ではこの機能を使用しないようにし、現在この機能を使用しているアプリケーションは修正することを検討してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文

### <a name="syntax-for-ssnoversion-mdmd-and-sssodfull-mdmd"></a>[!INCLUDE[ssnoversion-md.md](../../includes/ssnoversion-md.md)] および [!INCLUDE[sssodfull-md.md](../../includes/sssodfull-md.md)] の構文

```syntaxsql
    
SET CONCAT_NULL_YIELDS_NULL { ON | OFF }   
```  

### <a name="syntax-for-sssdw-mdmd-and-sspdw-mdmd"></a>[!INCLUDE[sssdw-md.md](../../includes/sssdw-md.md)] および [!INCLUDE[sspdw-md.md](../../includes/sspdw-md.md)] の構文

```syntaxsql
  
SET CONCAT_NULL_YIELDS_NULL ON    
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>解説
 SET CONCAT_NULL_YIELDS_NULL が ON の場合、NULL 値を文字列と連結すると、結果は NULL になります。 たとえば、`SELECT 'abc' + NULL` の結果は `NULL` になります。 SET CONCAT_NULL_YIELDS_NULL が OFF の場合、NULL 値を文字列と連結すると、結果は元の文字列になり、NULL 値は空文字列として扱われます。 たとえば、`SELECT 'abc' + NULL` の結果は `abc` になります。  
  
 SET CONCAT_NULL_YIELDS_NULL を指定しなかった場合は、**CONCAT_NULL_YIELDS_NULL** データベース オプションの設定が適用されます。  
  
> [!NOTE]  
>  SET CONCAT_NULL_YIELDS_NULL は、ALTER DATABASE の CONCAT_NULL_YIELDS_NULL 設定と同じ設定です。  
  
 SET CONCAT_NULL_YIELDS_NULL の設定は、解析時ではなく実行時に設定されます。  

インデックス付きビュー、計算列のインデックス、フィルター選択されたインデックス、または空間インデックスを作成または変更する場合は、SET CONCAT_NULL_YIELDS_NULL を **ON** に設定する必要があります。 SET CONCAT_NULL_YIELDS_NULL が **OFF** の場合、計算列にインデックスが設定されているテーブル、フィルター選択されたインデックス、空間インデックス、またはインデックス付きビューに対する CREATE、UPDATE、INSERT、および DELETE ステートメントはすべて失敗します。 インデックス付きビューおよび計算列上のインデックスに必要な SET オプション設定の詳細については、「[SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)」の「SET ステートメントの使用に関する留意事項」を参照してください。
  
 CONCAT_NULL_YIELDS_NULL を OFF に設定した場合、複数のサーバーにまたがって文字列を連結することはできません。  
  
 この設定の現在の設定を表示するには、次のクエリを実行します。  
  
```sql
DECLARE @CONCAT_SETTING VARCHAR(3) = 'OFF';  
IF ( (4096 & @@OPTIONS) = 4096 ) SET @CONCAT_SETTING = 'ON';  
SELECT @CONCAT_SETTING AS CONCAT_NULL_YIELDS_NULL; 
```  
  
## <a name="examples"></a>例  
 次の例では、両方の `SET CONCAT_NULL_YIELDS_NULL` 設定を示します。  
  
```sql
PRINT 'Setting CONCAT_NULL_YIELDS_NULL ON';  
GO  
-- SET CONCAT_NULL_YIELDS_NULL ON and testing.  
SET CONCAT_NULL_YIELDS_NULL ON;  
GO  
SELECT 'abc' + NULL ;  
GO  
  
-- SET CONCAT_NULL_YIELDS_NULL OFF and testing.  
SET CONCAT_NULL_YIELDS_NULL OFF;  
GO  
SELECT 'abc' + NULL;   
GO  
```  
  
## <a name="see-also"></a>参照  
 [SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SESSIONPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/sessionproperty-transact-sql.md)  
  
  
