---
title: OLE DB Driver での ADO の使用
description: OLE DB Driver で ADO を使用する方法について説明します。これには複数のアクティブな結果セット、クエリ通知、ユーザー定義型、または xml データ型などの新機能が含まれます。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB Driver for SQL Server, ADO
- data access [OLE DB Driver for SQL Server], ADO
- ADO [OLE DB Driver for SQL Server]
- MSOLEDBSQL, ADO
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: c40b9c2541c724f448dd636d5f7217da223a6f92
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742812"
---
# <a name="using-ado-with-ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server での ADO の使用
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  ADO (ActiveX Data Object) を使用する既存のアプリケーションで、MARS (複数のアクティブな結果セット)、クエリ通知、UDT (ユーザー定義型)、新しい **xml** データ型など、[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] で導入された新機能を利用するには、データ アクセス プロバイダーとして OLE DB Driver for SQL Server を使用する必要があります。  
  
 ADO から [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の最新バージョンの新機能を使用できるように、OLE DB Driver for SQL Server を機能強化し、OLE DB の中核となる機能を拡張しました。 ADO アプリケーションはこのような機能強化により、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の新しい機能、および [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] で導入された 2 つのデータ型 (**xml** と **udt**) を使用できるようになります。 また、この機能強化では、**varchar**、**nvarchar**、**varbinary** の各データ型に対する機能も強化されています。 OLE DB Driver for SQL Server では、ADO アプリケーションで使用するために、SSPROP_INIT_DATATYPECOMPATIBILITY 初期化プロパティが DBPROPSET_SQLSERVERDBINIT プロパティ セットに追加されています。このため、新しいデータ型は ADO との互換性を確保する方法で公開されます。 さらに、OLE DB Driver for SQL Server では、接続文字列に設定される、**DataTypeCompatibility** という新しい接続文字列キーワードも定義されます。  

> [!NOTE]  
>  既存の ADO アプリケーションは、SQLOLEDB プロバイダーを使用して、XML、UDT、および大きな値のテキストやバイナリのフィールド値にアクセスして更新できます。 サイズの大きな値をとる新しいデータ型 **varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** はそれぞれ、**adLongVarChar**、**adLongVarWChar**、**adLongVarBinary** という ADO 型として返されます。 XML 列は **adLongVarChar** として返され、UDT 列は **adVarBinary** として返されます。 ただし、SQLOLEDB ではなく OLE DB Driver for SQL Server (MSOLEDBSQL) を使用する場合は、新しいデータ型が ADO データ型に正しくマップされるように、**DataTypeCompatibility** キーワードを "80" に設定する必要があります。  

## <a name="enabling-ole-db-driver-for-sql-server-from-ado"></a>ADO からの OLE DB Driver for SQL Server の有効化  
 OLE DB Driver for SQL Server を使用できるようにするには、ADO アプリケーションで、接続文字列に次のキーワードを実装する必要があります。  

-   `Provider=MSOLEDBSQL`  

-   `DataTypeCompatibility=80`  

 OLE DB Driver for SQL Server でサポートされている ADO 接続文字列キーワードについて詳しくは、「[OLE DB Driver for SQL Server での接続文字列キーワードの使用](using-connection-string-keywords-with-oledb-driver-for-sql-server.md)」をご覧ください。  

 次に、MARS 機能の有効化など、OLE DB Driver for SQL Server を使用した操作で利用できる ADO 接続文字列を作成する例を示します。  

```  
Dim con As New ADODB.Connection  

con.ConnectionString = "Provider=MSOLEDBSQL;" _  
         & "Server=(local);" _  
         & "Database=AdventureWorks;" _   
         & "Integrated Security=SSPI;" _  
         & "DataTypeCompatibility=80;" _  
         & "MARS Connection=True;"  
con.Open  
```  

## <a name="examples"></a>例  
 以下のセクションでは、OLE DB Driver for SQL Server と ADO を併用する方法の例を示します。  

### <a name="retrieving-xml-column-data"></a>XML 列データの取得  
 この例では、レコードセットを使用し、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] **AdventureWorks** サンプル データベースの XML 列からデータを取得および表示します。  

```  
Dim con As New ADODB.Connection  
Dim rst As New ADODB.Recordset  
Dim sXMLResult As String  

con.ConnectionString = "Provider=MSOLEDBSQL;" _  
         & "Server=(local);" _  
         & "Database=AdventureWorks;" _   
         & "Integrated Security=SSPI;" _   
         & "DataTypeCompatibility=80;"  

con.Open  

' Get the xml data as a recordset.  
Set rst.ActiveConnection = con  
rst.Source = "SELECT AdditionalContactInfo FROM Person.Contact " _  
   & "WHERE AdditionalContactInfo IS NOT NULL"  
rst.Open  

' Display the data in the recordset.  
While (Not rst.EOF)  
   sXMLResult = rst.Fields("AdditionalContactInfo").Value  
   Debug.Print (sXMLResult)  
   rst.MoveNext  
End While  

con.Close  
Set con = Nothing  
```  

> [!NOTE]  
>  レコードセットのフィルター選択は、XML 列でサポートされません。 これを使用すると、エラーが返されます。  

### <a name="retrieving-udt-column-data"></a>UDT 列データの取得  
 この例では、**Command** オブジェクトを使用して、UDT を返す SQL クエリを実行します。その後、UDT データを更新し、新しいデータをデータベースに挿入します。 ここでは、**Point** UDT が既にデータベースに登録されていることを前提としています。  

```  
Dim con As New ADODB.Connection  
Dim cmd As New ADODB.Command  
Dim rst As New ADODB.Recordset  
Dim strOldUDT As String  
Dim strNewUDT As String  
Dim aryTempUDT() As String  
Dim strTempID As String  
Dim i As Integer  

con.ConnectionString = "Provider=MSOLEDBSQL;" _  
         & "Server=(local);" _  
         & "Database=AdventureWorks;" _   
         & "Integrated Security=SSPI;" _  
         & "DataTypeCompatibility=80;"  

con.Open  

' Get the UDT value.  
Set cmd.ActiveConnection = con  
cmd.CommandText = "SELECT ID, Pnt FROM dbo.Points.ToString()"  
Set rst = cmd.Execute  
strTempID = rst.Fields(0).Value  
strOldUDT = rst.Fields(1).Value  

' Do something with the UDT by adding i to each point.  
arytempUDT = Split(strOldUDT, ",")  
i = 3  
strNewUDT = LTrim(Str(Int(aryTempUDT(0)) + i)) + "," + _  
   LTrim(Str(Int(aryTempUDT(1)) + i))  

' Insert the new value back into the database.  
cmd.CommandText = "UPDATE dbo.Points SET Pnt = '" + strNewUDT + _  
   "' WHERE ID = '" + strTempID + "'"  
cmd.Execute  

con.Close  
Set con = Nothing  
```  

### <a name="enabling-and-using-mars"></a>MARS の有効化と使用  
 この例では、OLE DB Driver for SQL Server で MARS を有効にするように接続文字列を構築し、その後、同じ接続を使用して実行する 2 つのレコードセット オブジェクトを作成します。  

```  
Dim con As New ADODB.Connection  

con.ConnectionString = "Provider=MSOLEDBSQL;" _  
         & "Server=(local);" _  
         & "Database=AdventureWorks;" _   
         & "Integrated Security=SSPI;" _  
         & "DataTypeCompatibility=80;" _  
         & "MARS Connection=True;"  
con.Open  

Dim recordset1 As New ADODB.Recordset  
Dim recordset2 As New ADODB.Recordset  

Dim recordsaffected As Integer  
Set recordset1 =  con.Execute("SELECT * FROM Table1", recordsaffected, adCmdText)  
Set recordset2 =  con.Execute("SELECT * FROM Table2", recordsaffected, adCmdText)  

con.Close  
Set con = Nothing  
```  

 以前のバージョンの OLE DB プロバイダーでは、アクティブな結果セットを 1 つの接続ごとに 1 つしか開くことができなかったので、このコードにより 2 回目の実行時に暗黙の接続が作成されました。 暗黙の接続が OLE DB 接続プールにプールされなかったので、これが原因でオーバーヘッドが増加することになります。 OLE DB Driver for SQL Server で公開された MARS 機能を使用すると、1 つの接続で複数のアクティブな結果を取得できます。  

## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server を使用したアプリケーションの構築](building-applications-with-oledb-driver-for-sql-server.md)  
