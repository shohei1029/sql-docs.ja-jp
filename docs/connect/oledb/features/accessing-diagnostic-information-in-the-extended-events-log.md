---
title: 拡張イベント ログの診断情報へのアクセス | Microsoft Docs
description: OLE DB Driver for SQL Server のトレースと拡張イベント ログの診断情報へのアクセス
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: cf877daeda48af4bfc809d1dbf71c6bef604c96f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742702"
---
# <a name="accessing-diagnostic-information-in-the-extended-events-log"></a>拡張イベント ログの診断情報へのアクセス
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  [!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] 以降では、OLE DB Driver for SQL Server とデータ アクセスのトレース ([データ アクセスのトレース](/previous-versions/sql/sql-server-2008/cc765421(v=sql.100))) が更新され、接続リング バッファーからの接続エラーに関する診断情報と拡張イベント ログからのアプリケーションのパフォーマンス情報を簡単に取得できるようになりました。  
  
 拡張イベント ログを表示する方法については、「[イベント セッション データの表示](../../../relational-databases/extended-events/advanced-viewing-of-target-data-from-extended-events-in-sql-server.md)」を参照してください。 

  
> [!NOTE]  
>  この機能は、トラブルシューティングおよび診断用であるため、監査やセキュリティの用途には適さない場合があります。  
  
## <a name="remarks"></a>解説  
 接続操作では、OLE DB Driver for SQL Server はクライアント接続 ID を送信します。 接続に失敗した場合は、接続リング バッファー ([接続リング バッファーによる SQL Server 2008 での接続トラブルシューティング](/archive/blogs/sql_protocols/connectivity-troubleshooting-in-sql-server-2008-with-the-connectivity-ring-buffer)) にアクセスし、**ClientConnectionID** フィールドを見つけて、接続エラーに関する診断情報を取得することができます。 クライアント接続 ID は、エラーが発生した場合にのみリング バッファーに記録されます (ログイン前のパケットを送信する前に接続に失敗した場合、クライアント接続 ID は生成されません。)クライアント接続 ID は 16 バイトの GUID です。 また、**client_connection_id** 操作を拡張イベント セッションのイベントに追加すると、拡張イベントの出力先でクライアント接続 ID を検索することもできます。 詳しい診断サポートが必要な場合は、データ アクセスのトレースを有効にして、接続コマンドを再実行し、失敗した操作のデータ アクセスのトレースで **ClientConnectionID** フィールドを調べます。  
   
  
 OLE DB Driver for SQL Server からは、スレッド固有のアクティビティ ID も送信されます。 アクティビティ ID は、TRACK_CAUSAILITY オプションを有効にしてセッションを開始した場合、拡張イベント セッションでキャプチャされます。 アクティブな接続に関するパフォーマンスの問題については、クライアントのデータ アクセスのトレース (**ActivityID** フィールド) からアクティビティ ID を取得し、このアクティビティ ID を拡張イベント出力で検索することができます。 拡張イベントのアクティビティ ID は、16 バイトの GUID (クライアント接続 ID の GUID とは異なります) に 4 バイトのシーケンス番号が付加されたものです。 シーケンス番号は、スレッド内で要求の順序を表し、スレッドのバッチと RPC ステートメントの相対的順序を示します。 **ActivityID** は必要に応じて、データ アクセスのトレースが有効であり、データ アクセス トレース構成ワードの 18 番目のビットがオンである場合に、SQL バッチ ステートメントと RPC 要求に送信されます。  
  
 次のサンプルでは、[!INCLUDE[tsql](../../../includes/tsql-md.md)] を使用して拡張イベント セッションを開始します。このセッションは、リング バッファーに保存され、RPC およびバッチ操作でクライアントから送信されたアクティビティ ID を記録します。  
  
```  
create event session MySession on server   
add event connectivity_ring_buffer_recorded,   
add event sql_statement_starting (action (client_connection_id)),   
add event sql_statement_completed (action (client_connection_id)),   
add event rpc_starting (action (client_connection_id)),   
add event rpc_completed (action (client_connection_id))  
add target ring_buffer with (track_causality=on)  
  
```  
  
## <a name="control-file"></a>制御ファイル  
 OLE DB Driver for SQL Server 制御ファイル (ctrl.guid) の内容は次のとおりです。  
  
```  
{8B98D3F2-3CC6-0B9C-6651-9649CCE5C752}  0x630ff  0   MSDADIAG.ETW
{EE7FB59C-D3E8-9684-AEAC-B214EFD91B31}  0x630ff  0   MSOLEDBSQL.1  
```  
  
## <a name="mof-file"></a>MOF ファイル  
 OLE DB Driver for SQL Server mof ファイルの内容は次のとおりです。  
  
```  
#pragma classflags("forceupdate")  
#pragma namespace ("\\\\.\\Root\\WMI")  
  
/////////////////////////////////////////////////////////////////////////////  
//  
//  MSDADIAG.ETW  
  
[  
 dynamic: ToInstance,  
 Description("MSDADIAG.ETW"),  
 Guid("{8B98D3F2-3CC6-0B9C-6651-9649CCE5C752}"),  
 locale("MS\\0x409")  
]  
class Bid2Etw_MSDADIAG_ETW : EventTrace  
{  
};  
  
[  
 dynamic: ToInstance,  
 Description("MSDADIAG.ETW"),  
 Guid("{8B98D3F3-3CC6-0B9C-6651-9649CCE5C752}"),  
 DisplayName("msdadiag"),  
 locale("MS\\0x409")  
]  
class Bid2Etw_MSDADIAG_ETW_Trace : Bid2Etw_MSDADIAG_ETW  
{  
};  
  
[  
 dynamic: ToInstance,  
 Description("MSDADIAG.ETW formatted output (A)"),  
 EventType(17),  
 EventTypeName("TextA"),  
 locale("MS\\0x409")  
]  
class Bid2Etw_MSDADIAG_ETW_Trace_TextA : Bid2Etw_MSDADIAG_ETW_Trace  
{  
    [  
     WmiDataId(1),  
     Description("Module ID"),  
     read  
    ]  
    uint32 ModID;  
  
    [  
     WmiDataId(2),  
     Description("Text StringA"),  
     extension("RString"),  
     read  
    ]  
    object msgStr;  
};  
  
[  
 dynamic: ToInstance,  
 Description("MSDADIAG.ETW formatted output (W)"),  
 EventType(18),  
 EventTypeName("TextW"),  
 locale("MS\\0x409")  
]  
class Bid2Etw_MSDADIAG_ETW_Trace_TextW : Bid2Etw_MSDADIAG_ETW_Trace  
{  
    [  
     WmiDataId(1),  
     Description("Module ID"),  
     read  
    ]  
    uint32 ModID;  
  
    [  
     WmiDataId(2),  
     Description("Text StringW"),  
     extension("RWString"),  
     read  
    ]  
    object msgStr;  
};  
  
/////////////////////////////////////////////////////////////////////////////  
//  
//  MSOLEDBSQL.1  
  
[  
 dynamic: ToInstance,  
 Description("MSOLEDBSQL.1"),  
 Guid("{EE7FB59C-D3E8-9684-AEAC-B214EFD91B31}"),  
 locale("MS\\0x409")  
]  
class Bid2Etw_MSOLEDBSQL_1 : EventTrace  
{  
};  
  
[  
 dynamic: ToInstance,  
 Description("MSOLEDBSQL.1"),  
 Guid("{EE7FB59D-D3E8-9684-AEAC-B214EFD91B31}"),  
 DisplayName("MSOLEDBSQL.1"),  
 locale("MS\\0x409")  
]  
class Bid2Etw_MSOLEDBSQL_1_Trace : Bid2Etw_MSOLEDBSQL_1  
{  
};  
  
[  
 dynamic: ToInstance,  
 Description("MSOLEDBSQL.1 formatted output (A)"),  
 EventType(17),  
 EventTypeName("TextA"),  
 locale("MS\\0x409")  
]  
class Bid2Etw_MSOLEDBSQL_1_Trace_TextA : Bid2Etw_MSOLEDBSQL_1_Trace  
{  
    [  
     WmiDataId(1),  
     Description("Module ID"),  
     read  
    ]  
    uint32 ModID;  
  
    [  
     WmiDataId(2),  
     Description("Text StringA"),  
     extension("RString"),  
     read  
    ]  
    object msgStr;  
};  
  
[  
 dynamic: ToInstance,  
 Description("MSOLEDBSQL.1 formatted output (W)"),  
 EventType(18),  
 EventTypeName("TextW"),  
 locale("MS\\0x409")  
]  
class Bid2Etw_MSOLEDBSQL_1_Trace_TextW : Bid2Etw_MSOLEDBSQL_1_Trace  
{  
    [  
     WmiDataId(1),  
     Description("Module ID"),  
     read  
    ]  
    uint32 ModID;  
  
    [  
     WmiDataId(2),  
     Description("Text StringW"),  
     extension("RWString"),  
     read  
    ]  
    object msgStr;  
};  
```  
  
## <a name="see-also"></a>参照  
 [エラーの処理](../../oledb/ole-db-errors/errors.md)  
  
