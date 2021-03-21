---
title: データベース エンジン エラーについて | Microsoft Docs
description: SQL Server データベース エンジンで発生するエラーの属性と、すべてのシステムおよびユーザー定義エラー メッセージを sys.messages からアクセスする方法について説明します。
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
helpviewer_keywords:
- errors [SQL Server], about errors
- errors [SQL Server], Database Engine
- errors [SQL Server]
- Database Engine [SQL Server], errors
ms.assetid: ddaca9d3-956f-46a5-8cd3-a7a15ec75878
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c836216ffa3acd6bdb24c660114980c1f3d2c259
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737702"
---
# <a name="understanding-database-engine-errors"></a>データベース エンジン エラーについて
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  次の表で、[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] によって発生したエラーの属性について説明します。  
  
|属性|説明|  
|---------------|-----------------|  
|エラー番号|エラー メッセージには、それぞれ一意なエラー番号が付いています。|  
|エラー メッセージ文字列|エラー メッセージには、エラーの原因についての診断情報が含まれています。 多くのエラー メッセージには、エラーが発生したオブジェクト名などの情報を挿入するための置換変数が用意されています。|  
|重大度|重大度レベルは、エラーの重大度を示します。 1、2 など重大度レベルが低いエラーは情報メッセージ、または低レベル警告です。 重大度レベルが高いエラーは、できるだけ早く対処することが推奨される問題であることを示します。 重大度レベルの詳細については、「 [データベース エンジン エラーの重大度](../../relational-databases/errors-events/database-engine-error-severities.md)」を参照してください。|  
|State|エラー メッセージが [!INCLUDE[ssDE](../../includes/ssde-md.md)]のコード内の複数の場所で生成される場合があります。 たとえば、複数の異なる条件に対して 1105 エラーが生成されることがあります。 エラーを生成する特定の条件はそれぞれ一意の状態コードを割り当てます。<br /><br /> [!INCLUDE[msCoName](../../includes/msconame-md.md)] のサポート技術情報など既知の問題に関する情報が含まれたデータベースを参照する場合は、状態番号を使用して、記録された問題と検出されたエラーが同じかどうかを判断することができます。 たとえば、サポート技術情報の記事が状態 2 の 1105 エラーについて説明しており、受け取った 1105 エラー メッセージが状態 3 の場合、このエラーは記事で報告されているエラーとは原因が異なる可能性が高いと考えられます。<br /><br /> [!INCLUDE[msCoName](../../includes/msconame-md.md)] のサポート エンジニアがこのエラーの状態コードを使用して、エラー コードが生成されたソース コード内の場所を探すこともできます。 この情報が、問題に対する他の診断方法のヒントになる場合もあります。|  
|プロシージャ名|エラーが発生したストアド プロシージャまたはトリガーの名前です。|  
|行番号|バッチ、ストアド プロシージャ、トリガー、または関数内のどのステートメントでエラーが発生したかを示します。|  
  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] のインスタンス内のすべてのシステム エラー メッセージおよびユーザー定義のエラー メッセージは、 **sys.messages** カタログ ビューに含まれています。 RAISERROR ステートメントを使用すると、アプリケーションにユーザー定義エラーを返すことができます。  
  
 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] **SQLClient** 名前空間、ActiveX Data Objects (ADO)、OLE DB、Open Database Connectivity (ODBC) などのすべてのデータベース API で、基本的なエラー属性が報告されます。 この情報には、エラー番号およびメッセージ文字列が含まれています。 ただし、すべての API で他のすべてのエラー属性が報告されるわけではありません。  
  
 TRY...CATCH 構造の TRY ブロックの適用範囲内で発生したエラーの詳細は、関連付けられた CATCH ブロックの適用範囲内で ERROR_LINE、ERROR_MESSAGE、ERROR_NUMBER、ERROR_PROCEDURE、ERROR_SEVERITY、ERROR_STATE などの関数を使用して [!INCLUDE[tsql](../../includes/tsql-md.md)] コードで取得することができます。 詳細については、「 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)」を参照してください。  
  
## <a name="examples"></a>例  
 次の例では、 `sys.messages` カタログ ビューにクエリを実行し、英語テキスト ( [!INCLUDE[ssDE](../../includes/ssde-md.md)] ) が含まれた`1033`内のシステム エラー メッセージおよびユーザー定義のエラー メッセージがすべて記載された一覧を返します。  
  
```  
SELECT  
    message_id,  
    language_id,  
    severity,  
    is_event_logged,  
    text  
  FROM sys.messages  
  WHERE language_id = 1033;  
```  
  
 詳細については、「 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)   
 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md)   
 [@@ERROR &#40;Transact-SQL&#41;](../../t-sql/functions/error-transact-sql.md)   
 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)   
 [ERROR_LINE &#40;Transact-SQL&#41;](../../t-sql/functions/error-line-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_NUMBER &#40;Transact-SQL&#41;](../../t-sql/functions/error-number-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)  
  
  
