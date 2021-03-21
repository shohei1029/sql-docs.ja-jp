---
title: SQL Server 2005 Native Client からのアプリケーションの更新 | Microsoft Docs
description: SQL Server 2005 (9.x) の SQL Server Native Client 以降における OLE DB Driver for SQL Server の破壊的変更について説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB Driver for SQL Server, updating applications
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 112f0d28120adee56cc3b5e2f222bd4b23d725b2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751222"
---
# <a name="updating-applications-from-sql-server-2005-native-client"></a>SQL Server 2005 Native Client からのアプリケーションの更新
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  このトピックでは、[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 以降の OLE DB Driver for SQL Server における破壊的変更について説明します。  

 Microsoft Data Access Components (MDAC) を OLE DB Driver for SQL Server にアップグレードした場合も、一部の動作で違いが生じます。 詳細については、「[MDAC から OLE DB Driver for SQL Server へのアプリケーションの更新](../../oledb/applications/updating-an-application-to-oledb-driver-for-sql-server-from-mdac.md)」を参照してください。  

 [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] に付属する [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 9.0。 [!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] に付属する [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 10.0。  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に付属する [!INCLUDE[ssKilimanjaro](../../../includes/sskilimanjaro-md.md)] Native Client 10.5。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] および [!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] に付属する [!INCLUDE[ssSQL14](../../../includes/sssql14-md.md)] Native Client 11.0。  

|[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] Native Client と比較して、OLE DB Driver for SQL Server で変更された動作|説明|  
|------------------------------------------------------------------------------------|-----------------|  
|OLE DB によって定義された有効桁数までしか埋め込まれない|変換後のデータがサーバーに送信される変換の場合、OLE DB Driver for SQL Server では、データの末尾の 0 は **datetime** 値の最大長までしか埋め込まれません。 SQL Server Native Client 9.0 では、9 桁まで埋め込まれていました。|  
|ICommandWithParameter::SetParameterInfo の DBTYPE_DBTIMESTAMP が検証される。|OLE DB Driver for SQL Server では、ICommandWithParameter::SetParameterInfo の *bScale* を DBTYPE_DBTIMESTAMP の秒の小数部の有効桁数に設定するための OLE DB 要件が実装されています。|  
|**sp_columns** ストアド プロシージャによって IS_NULLABLE 列に **"NO "** ではなく **"NO"** が返される。|OLE DB Driver for SQL Server では、**sp_columns** ストアド プロシージャによって IS_NULLABLE 列に **"NO "** ではなく **"NO"** が返されるようになりました。|  
|日付が範囲外の場合に別のエラーが返される|**datetime** 型では、範囲外の日付に対して OLE DB Driver for SQL Server によって返されるエラー番号が、前のバージョンで返されていたエラー番号と変わります。<br /><br /> 具体的には、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 9.0 では、文字列から **datetime** への変換で年の値が範囲外の場合は 22007 が返されていました。OLE DB Driver for SQL Server では、日付が **datetime2** でサポートされる範囲内でも、**datetime** または **smalldatetime** でサポートされる範囲外の場合、22008 が返されます。|  
|丸め処理によって日が変わる場合に **datetime** 値では秒の小数部が丸められずに切り捨てられる。|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 10.0 より前のバージョンでは、サーバーに送信される **datetime** 値は、クライアントによって 1/300 秒単位に丸められていました。 OLE DB Driver for SQL Server では、丸め処理によって日が変わる場合、秒の小数部が切り捨てられます。|  
|**datetime** 値の秒数が切り捨てられる可能性がある。|OLE DB Driver for SQL Server でビルドしたアプリケーションが [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 2005 サーバーに接続する場合、型識別子を DBTYPE_DBTIMESTAMP (OLE DB) または SQL_TIMESTAMP (ODBC) に設定し、小数点以下桁数を 0 に設定して datetime 列にバインドすると、サーバーに送信されるデータの時刻部分の秒および秒の小数部が切り捨てられます。<br /><br /> 次に例を示します。<br /><br /> 入力データ: 1994-08-21 21:21:36.000<br /><br /> 挿入されるデータ: 1994-08-21 21:21:00.000|  
|DBTYPE_DBTIME から DBTYPE_DATE への OLE DB データ変換で日が変更されなくなる|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 10.0 より前のバージョンでは、DBTYPE_DATE の時刻部分が午前 0 時から 0.5 秒以内の場合、OLE DB の変換コードによって日が変更されました。 OLE DB Driver for SQL Server では日付は変更されません (秒の小数部は丸められずに切り捨てられます)。|  
|IBCPSession::BCColFmt の変換の変更。|OLE DB Driver for SQL Server で IBCPSession::BCOColFmt を使用して SQLDATETIME または SQLDATETIME を文字列型に変更すると、小数部の値がエクスポートされます。 たとえば、SQLDATETIME 型を SQLNVARCHARMAX 型に変換すると、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 10.0 より前のバージョンでは、<br /> 1989-02-01 00:00:00 が返されます。<br />OLE DB Driver for SQL Server では、 <br />1989-02-01 00:00:00.0000000 が返されます。|  
|BCP API を使用するカスタム アプリケーションで警告が表示される|指定されたフィールド長をデータ長が上回る場合、どの型の場合でも BCP API によって警告メッセージが生成されます。 以前のバージョンでは、この警告は、すべての型ではなく文字型についてのみ表示されました。|  
|日付型または時刻型としてバインドされた **sql_variant** に空の文字列を挿入するとエラーが生成される。|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 9.0 では、日付型または時刻型としてバインドされた **sql_variant** に空の文字列を挿入してもエラーは生成されませんでした。 OLE DB Driver for SQL Server では、この場合に正しくエラーが生成されます。|  
|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] は、トリガーの実行時に異なる結果を返す場合がある|[!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] で導入された変更により、**NOCOUNT OFF** が有効なときに、トリガーを実行させたステートメントからアプリケーションに返される結果が異なる可能性があります。 このような場合、アプリケーションでエラーが発生することがあります。 エラーを解決するには、トリガーに **NOCOUNT ON** を設定します。|  

## <a name="see-also"></a>参照   
 [OLE DB Driver for SQL Server](../../oledb/oledb-driver-for-sql-server.md)
