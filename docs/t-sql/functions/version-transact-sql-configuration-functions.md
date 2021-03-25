---
description: '&#x40;&#x40;バージョン - Transact SQL 構成関数'
title: '@@VERSION (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 03/20/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- '@@VERSION'
- '@@VERSION_TSQL'
dev_langs:
- TSQL
helpviewer_keywords:
- '@@VERSION function'
- current SQL Server installation information
- versions [SQL Server], @@VERSION
- processors [SQL Server], types
ms.assetid: 385ba80e-7c28-41a5-9cdb-5648f3785983
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9e1534a67dc7d0009e0ab8ad624ac3dbc3322a23
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890728"
---
# <a name="x40x40version---transact-sql-configuration-functions"></a>&#x40;&#x40;バージョン - Transact SQL 構成関数
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在インストールされている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に関するシステム情報およびビルド情報を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  

> [!NOTE]  
> @@VERSION によって報告された製品バージョンが **Azure SQL Database** で正しくないという問題については認識しています。
> 
> Azure SQL Database で実行される SQL Server データベース エンジンのバージョンは常に、SQL Server のオンプレミスのバージョンより新しいため、最新のセキュリティ修正プログラムが含まれています。 これは、パッチ レベルが SQL Server のオンプレミスのバージョンと同じかそれより新しく、SQL Server で使用できる最新の機能が Azure SQL Database で使用できることを意味します。
>
> エンジンのエディションをプログラムで確認するには、SELECT SERVERPROPERTY('EngineEdition') を使用します。 このクエリでは、Azure SQL Database の場合は '5' が、Azure SQL Managed Instance の場合は '8' が返されます。
>
> この問題が解決されたら、ドキュメントを更新します。
## <a name="syntax"></a>構文  
  
```syntaxsql
@@VERSION  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型
 **nvarchar**  
  
## <a name="remarks"></a>解説  
 @@VERSION の結果は 1 つの nvarchar 文字列として表示されます。 を使用する [SERVERPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/serverproperty-transact-sql.md) 個々 のプロパティの値を取得します。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]では、次の情報が返されます。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のバージョン  
  
-   プロセッサ アーキテクチャ  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のビルド日  
  
-   著作権情報  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エディション  
  
-   オペレーティング システムのバージョン  
  
 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]では、次の情報が返されます。  
  
-   エディション - "Microsoft SQL Azure"  
  
-   製品レベル - "(RTM)"  
  
-   製品バージョン  
  
-   ビルド日  
  
-   著作権情報  
  
## <a name="examples"></a>例  
  
### <a name="a-return-the-current-version-of-ssnoversion"></a>A: 現在のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を返す  
 次の例では、現在のインストールに関するバージョン情報を返します。  
  
```sql
SELECT @@VERSION AS 'SQL Server Version';  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="b-return-the-current-version-of-ssdw"></a>B. 現在のバージョンの [!INCLUDE[ssDW](../../includes/ssdw-md.md)] を返す  
  
```sql
SELECT @@VERSION AS 'SQL Server PDW Version';  
```  
  
## <a name="see-also"></a>参照  
 [SERVERPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/serverproperty-transact-sql.md)  
  
  

