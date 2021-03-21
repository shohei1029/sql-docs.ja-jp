---
title: OLE DB Driver for SQL Server のシステム要件
description: OLE DB Driver for SQL Server で SQL Server のデータ アクセス機能 (MARS など) を使用するために必要なソフトウェアの前提条件について説明します。
ms.custom: ''
ms.date: 03/18/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- system requirements [OLE DB Driver for SQL Server]
- data access [OLE DB Driver for SQL Server], system requirements
- OLE DB Driver for SQL Server, system requirements
- MSOLEDBSQL, system requirements
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: aa46b5a4db292b5ac4ee5df71aa18abeb74c5559
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104744872"
---
# <a name="system-requirements-for-ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server のシステム要件

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../includes/driver_oledb_download.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ アクセス機能 (MARS など) を使用するには、次のソフトウェアがインストールされている必要があります。  

* クライアント上の OLE DB Driver for SQL Server  
* [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンス (サーバー)

> [!NOTE]  
> このソフトウェアは、必ず管理者特権でログオンしてからインストールしてください。  

## <a name="operating-system-requirements"></a>オペレーティング システムの要件  

OLE DB Driver for SQL Server をサポートするオペレーティング システムの一覧については、「[OLE DB Driver for SQL Server のサポート ポリシー](../oledb/applications/support-policies-for-oledb-driver-for-sql-server.md)」を参照してください。  

## <a name="azure-active-directory-authentication-requirements"></a>Azure Active Directory 認証の要件  

18.3 ***より前*** のバージョンの OLE DB driver for SQL Server で Azure Active Directory 認証方法を使用する場合は、[SQL Server 用 Active Directory 認証ライブラリ](https://go.microsoft.com/fwlink/?LinkID=513072)が確実にインストールされていることを確認します (バージョン 18.3 には、インストーラー パッケージの一部として依存関係が含まれています)。ADAL は、他の認証方法や OLE DB 操作には必要ありません。 詳細については、次を参照してください。[Azure Active Directory の使用](features/using-azure-active-directory.md)。

## <a name="sql-server-requirements"></a>SQL Server 要件  

OLE DB Driver for SQL Server を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースのデータにアクセスするには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスがインストールされている必要があります。  

[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] は、MDAC、Windows Data Access Components、および OLE DB Driver for SQL Server のすべてのバージョンからの接続をサポートします。 古いクライアント バージョンで [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続する場合、クライアントで認識されないサーバーのデータ型は、クライアント バージョンと互換する型にマップされます。 詳細については、「[クライアント バージョンのデータ型の互換性](#data-type-compatibility-for-client-versions)」をご覧ください。  

## <a name="cross-language-requirements"></a>言語間の要件  

OLE DB Driver for SQL Server の英語版は、サポートされているオペレーティング システムのすべてのローカライズ版でサポートされています。 OLE DB Driver for SQL Server のローカライズ版は、ローカライズされた OLE DB Driver for SQL Server バージョンと同じ言語でローカライズされたオペレーティング システムでサポートされています。 また、対応する言語設定がインストールされていれば、サポートされているオペレーティング システムの英語版でも利用できます。  

アップグレードの要件を次に示します。  

* OLE DB Driver for SQL Server の英語版は、OLE DB Driver for SQL Server の任意のローカライズ版にアップグレードできます。  
* OLE DB Driver for SQL Server のローカライズ版は、同じ言語の OLE DB Driver for SQL Server のローカライズ版にアップグレードできます。  
* OLE DB Driver for SQL Server のローカライズ版は、英語版の OLE DB Driver for SQL Server にアップグレードできます。  
* OLE DB Driver for SQL Server のローカライズ版は、別のローカライズ言語でローカライズされた OLE DB Driver for SQL Server にアップグレードすることはできません。  

## <a name="data-type-compatibility-for-client-versions"></a>クライアント バージョンのデータ型の互換性  

以下の表に示すように、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] および OLE DB Driver for SQL Server は新しいデータ型を、下位クライアントと互換する古いデータ型にマップします。  

OLE DB および ADO アプリケーションでは、OLE DB Driver for SQL Server で **DataTypeCompatibility** 接続文字列キーワードを使用して、古いデータ型を操作できます。 **DataTypeCompatibility=80** の場合、OLE DB クライアントは TDS (表形式データ ストリーム) バージョンではなく、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] TDS バージョンを使用して接続します。 つまり、[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降のデータ型の場合、OLE DB Driver for SQL Server ではなく、サーバーによって下位変換が実行されます。 またこの場合は、接続で使用可能な機能が、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 機能セットに制限されます。 新しいデータ型または機能を使用しようとすると、API 呼び出し時にすぐに検出されます。無効な要求はサーバーに渡されず、呼び出し元のアプリケーションにエラーが返されます。  

IDBInfo::GetKeywords からは、接続のサーバー バージョンに対応し、**DataTypeCompatibility** の影響を受けないキーワードの一覧が常に返されます。  

|データ型|SQL Server Native Client<br /><br />SQL Server 2005|SQL Server Native Client 11.0<br /><br /> [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|OLE DB Driver for SQL Server|Windows Data Access Components、MDAC、<br /><br /> DataTypeCompatibility=80 を使用する OLE DB Driver for SQL Server OLE DB アプリケーション|  
|---------------|--------------------------------------------------|-------------------------------------------------------------|-------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|  
|CLR UDT (\<= 8 Kb)|udt|udt|udt|Varbinary|  
|varbinary(max)|varbinary|varbinary|varbinary|Image|  
|varchar(max)|varchar|varchar|varchar|Text|  
|nvarchar(max)|nvarchar|nvarchar|nvarchar|Ntext|  
|xml|xml|xml|xml|Ntext|  
|CLR UDT (> 8 Kb)|varbinary|udt|udt|Image|  
|date|varchar|date|date|Varchar|  
|datetime2|varchar|datetime2|datetime2|Varchar|  
|datetimeoffset|varchar|datetimeoffset|datetimeoffset|Varchar|  
|time|varchar|time|time|Varchar|  

## <a name="see-also"></a>関連項目  

[OLE DB Driver for SQL Server](../oledb/oledb-driver-for-sql-server.md)  
[OLE DB Driver for SQL Server のインストール](../oledb/applications/installing-oledb-driver-for-sql-server.md)  
