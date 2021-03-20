---
description: sys.dm_exec_xml_handles (Transact-sql)
title: sys.dm_exec_xml_handles (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_exec_xml_handles
- dm_exec_xml_handles_TSQL
- sys.dm_exec_xml_handles_TSQL
- sys.dm_exec_xml_handles
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_xml_handles dynamic management function
ms.assetid: a873ce0f-6955-417a-96a1-b2ef11a83633
author: pmasl
ms.author: pelopes
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 5a0169418710234983772a8623efdd0d9ff337cf
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750222"
---
# <a name="sysdm_exec_xml_handles-transact-sql"></a>sys.dm_exec_xml_handles (Transact-sql)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-asdw-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-asdw-xxx-md.md)]

  **Sp_xml_preparedocument** によって開かれているアクティブハンドルに関する情報を返します。  
  
## <a name="syntax"></a>構文  
  
```  
  
dm_exec_xml_handles (session_id | 0 )  
```  
  
## <a name="arguments"></a>引数  
 *session_id* |0  
 セッションの ID。 *Session_id* が指定されている場合、この関数は、指定されたセッションの XML ハンドルに関する情報を返します。  
  
 0 を指定した場合、この関数ではすべてのセッションのすべての XML ハンドルに関する情報が返されます。  
  
## <a name="table-returned"></a>返されるテーブル  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**session_id**|**int**|この XML ドキュメントハンドルを保持しているセッションのセッション ID。|  
|**document_id**|**int**|**Sp_xml_preparedocument** によって返される XML ドキュメントハンドル ID。|  
|**namespace_document_id**|**int**|**Sp_xml_preparedocument** に3番目のパラメーターとして渡された、関連付けられた名前空間ドキュメントに使用される内部ハンドル ID。 名前空間ドキュメントがない場合は NULL になります。|  
|**sql_handle**|**varbinary(64)**|ハンドルが定義されている SQL コードのテキストへのハンドル。|  
|**statement_start_offset**|**int**|現在実行中のバッチまたはストアドプロシージャに含まれる、 **sp_xml_preparedocument** の呼び出しが発生するまでの文字数。 **Sql_handle**、 **statement_end_offset**、および **sys.dm_exec_sql_text** の動的管理関数と共に使用して、要求に対して現在実行中のステートメントを取得できます。|  
|**statement_end_offset**|**int**|現在実行中のバッチまたはストアドプロシージャに含まれる、 **sp_xml_preparedocument** の呼び出しが発生するまでの文字数。 **Sql_handle**、 **statement_start_offset**、および **sys.dm_exec_sql_text** の動的管理関数と共に使用して、要求に対して現在実行中のステートメントを取得できます。|  
|**creation_time**|**datetime**|**Sp_xml_preparedocument** が呼び出されたときのタイムスタンプ。|  
|**original_document_size_bytes**|**bigint**|未解析の XML ドキュメントのサイズ (バイト単位)。|  
|**original_namespace_document_size_bytes**|**bigint**|未解析の XML 名前空間ドキュメントのサイズ (バイト単位)。 名前空間ドキュメントがない場合は NULL になります。|  
|**num_openxml_calls**|**bigint**|このドキュメントハンドルを持つ OPENXML 呼び出しの数。|  
|**row_count**|**bigint**|このドキュメントハンドルに対する以前のすべての OPENXML 呼び出しによって返された行の数。|  
|**dormant_duration_ms**|**bigint**|最後の OPENXML 呼び出しからのミリ秒。 OPENXML が呼び出されていない場合、は **sp_xml_preparedocumen** t 呼び出しからのミリ秒を返します。|  
  
## <a name="remarks"></a>解説  
 **Sp_xml_preparedocument** の呼び出しを実行した sql テキストを取得するために使用される **sql_handles** の有効期間は、クエリの実行に使用されるキャッシュされたプランになります。 クエリ テキストがキャッシュ内で使用できない場合は、関数の結果に示された情報を使用してデータを取得することはできません。 これは、多数の大きなバッチを実行している場合に発生する可能性があります。  
  
## <a name="permissions"></a>権限  
 呼び出し元が所有していないすべてのセッションまたはセッション Id を表示するには、サーバーに対する VIEW SERVER STATE 権限が必要です。 呼び出し元は、自分の現在のセッション ID のデータを常に表示できます。      
  
## <a name="examples"></a>例  
 次の例では、アクティブ ハンドルをすべて選択します。  
  
```  
SELECT * FROM sys.dm_exec_xml_handles(0);  
```  
  
## <a name="see-also"></a>参照  
 <br>[動的管理ビューおよび関数 (Transact-sql)](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)
 <br>[実行関連の動的管理ビューおよび関数 (Transact-sql)](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)
 <br>[sp_xml_preparedocument (Transact-sql)](../system-stored-procedures/sp-xml-preparedocument-transact-sql.md)
 <br>[sp_xml_removedocument (Transact-sql)](../system-stored-procedures/sp-xml-removedocument-transact-sql.md)


 
  
  
