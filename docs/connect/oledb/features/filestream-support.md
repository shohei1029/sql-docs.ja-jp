---
title: FILESTREAM のサポート | Microsoft Docs
description: SQL Server では、強化された FILESTREAM 機能がサポートされています。この機能を使用すると、SQL Server またはファイル システムで大きなバイナリ値の格納やアクセスが可能になります。
ms.custom: ''
ms.date: 09/13/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- FILESTREAM [SQL Server], OLE DB Driver for SQL Server
- OLE DB Driver for SQL Server [FILESTREAM support]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 3453ff4d5fff8d2c32cdfaf2f1641bbbc19d6fc0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742452"
---
# <a name="filestream-support-in-ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server の FILESTREAM のサポート
[!INCLUDE[sql windows only](../../../includes/applies-to-version/sql-windows-only.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

[!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] から、強化された FILESTREAM 機能が OLE DB Driver for SQL Server でサポートされるようになりました。 サンプルについては、「[FILESTREAM と OLE DB](../../oledb/ole-db-how-to/filestream/filestream-and-ole-db.md)」を参照してください。  

FILESTREAM を使用すると、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] を経由するか、Windows ファイル システムに直接アクセスすることで、大きなバイナリ値の格納やアクセスが可能になります。 大きなバイナリ値とは、2 ギガバイト (GB) よりも大きい値です。 強化された FILESTREAM のサポートの詳細については、「[FILESTREAM &#40;SQL Server&#41;](../../../relational-databases/blob/filestream-sql-server.md)」を参照してください。  
  
データベース接続を開くと、 **\@\@TEXTSIZE** が既定で -1 (無制限) に設定されます。  
  
Windows ファイル システムの API を使用して、FILESTREAM 列にアクセスし、更新することもできます。  
  
詳細については、「[OpenSqlFilestream による FILESTREAM データへのアクセス](../../../relational-databases/blob/access-filestream-data-with-opensqlfilestream.md)」を参照してください。  
  
## <a name="querying-for-filestream-columns"></a>FILESTREAM 列のクエリ  
OLE DB のスキーマ行セットでは、列が FILESTREAM 列かどうかは報告されません。 また、OLE DB の ITableDefinition を使用して FILESTREAM 列を作成することはできません。    
  
FILESTREAM 列を作成する場合や、既存の FILESTREAM 列を検出する場合は、[sys.columns](../../../relational-databases/system-catalog-views/sys-columns-transact-sql.md) カタログ ビューの **is_filestream** 列を使用できます。  
  
以下に例を示します。  
  
```sql  
-- Create a table with a FILESTREAM column.  
CREATE TABLE Bob_01 (GuidCol1 uniqueidentifier ROWGUIDCOL NOT NULL UNIQUE DEFAULT NEWID(), IntCol2 int, varbinaryCol3 varbinary(max) FILESTREAM);  
  
-- Find FILESTREAM columns.  
SELECT name FROM sys.columns WHERE is_filestream=1;  
  
-- Determine whether a column is a FILESTREAM column.  
SELECT is_filestream FROM sys.columns WHERE name = 'varbinaryCol3' AND object_id IN (SELECT object_id FROM sys.tables WHERE name='Bob_01');  
```  
  
## <a name="down-level-compatibility"></a>下位互換性  
クライアントが OLE DB Driver for SQL Server を使用してコンパイルされていて、アプリケーションが [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] 以降) に接続している場合、**varbinary(max)** の動作は [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] で [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client によって導入された動作と互換性を持つようになります。 返されるデータの最大サイズが 2 GB に制限されます。 戻り値が 2 GB より大きい場合は切り捨てが行われ、"文字列データの右側が切り捨てられました" という警告が返されます。 
  
データ型の互換性が 80 に設定されている場合は、クライアントの動作で下位クライアントとの互換性が維持されます。  
  
[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] より前にリリースされた SQLOLEDB などのプロバイダーを使用しているクライアントでは、**varbinary(max)** が image にマップされます。  
  
## <a name="comments"></a>説明
- アプリケーションで 2 GB より大きい **varbinary(max)** 値を送受信するには、パラメーターと結果のバインドで **DBTYPE_IUNKNOWN** を使用します。 パラメーターの場合、プロバイダーは ISequentialStream および ISequentialStream を返す結果に対して IUnknown::QueryInterface を呼び出す必要があります。  

-  OLE DB の場合は、ISequentialStream 値に関連するチェックが緩和されます。 *wType* が **DBBINDING** 構造体内で **DBTYPE_IUNKNOWN** である場合、*dwPart* から **DBPART_LENGTH** を省略するか、データの長さ (データ バッファー内のオフセット *obLength*) を ~0 に設定することにより、長さのチェックを無効にすることができます。 この場合、プロバイダーは値の長さをチェックせず、ストリームで利用可能なすべてのデータを要求し、返します。 この変更は、[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] (以降の) サーバーに接続する場合に限り、すべてのラージ オブジェクト (LOB) 型と XML に適用されます。 これにより、既存のアプリケーションや下位レベルのサーバーとの一貫性や下位互換性を維持しつつ、より柔軟な開発が可能になります。  この変更は、データを転送するすべてのインターフェイス (主に IRowset:: GetData、ICommand:: Execute、および IRowsetFastLoad::InsertRow) に影響します。
 

## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)  
  
  
