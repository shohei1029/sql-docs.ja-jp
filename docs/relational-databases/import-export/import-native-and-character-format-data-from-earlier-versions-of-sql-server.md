---
title: 以前のバージョンの SQL Server からのネイティブ形式データおよび文字形式データのインポート
description: SQL Server 2019 では、bcp を使用し、他のバージョンの SQL Server からネイティブ形式データおよび文字形式データを -V スイッチと修飾子を指定してインポートすることができます。
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- earlier versions [SQL Server], import and export data formats
- -V switch
- data formats [SQL Server], earlier versions
- previous versions [SQL Server], import and export data formats
ms.assetid: e644696f-9017-428e-a5b3-d445d1c630b3
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: 18bd8c7b114329f5acacacd2c2230bf75ecfb6b5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750202"
---
# <a name="import-native-and-character-format-data-from-earlier-versions-of-sql-server"></a>以前のバージョンの SQL Server からのネイティブ形式データおよび文字形式データのインポート
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE [sssql14-md](../../includes/sssql14-md.md)] 以降では、**bcp** を使用すると、[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)]、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]、[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]、[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、または [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] からネイティブ形式データおよび文字形式データを **-V** スイッチを指定してインポートすることができます。 **-V** スイッチを使用すると、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] は指定された以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のデータ型を使用し、データ ファイル形式はその以前のバージョンのものと同じになります。  
  
データ ファイルに以前の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バージョンを指定するには、 **-V** スイッチと次のいずれかの修飾子を使用します。  
  
|SQL Server のバージョン|Qualifier|  
|------------------------|---------------|  
|[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)]|**-V80**|  
|[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]|**-V90**|  
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]|**-V100**|  
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]|**-V 110**|  
  
## <a name="interpretation-of-data-types"></a>データ型について  
 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降のバージョンでは、いくつかの新しいデータ型がサポートされるようになりました。 以前の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バージョンに新しいデータ型をインポートする場合は、古い **bcp** クライアントで読み取ることが可能な形式でそのデータ型を格納する必要があります。 次の表では、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]との互換性を維持するために、新しいデータ型がどのように変換されるかをまとめています。  
  
|SQL Server 2005 の新しいデータ型|バージョン 6 *x* の互換性のあるデータ型|バージョン 70 の互換性のあるデータ型|バージョン 80 の互換性のあるデータ型|  
|---------------------------------------|-------------------------------------------|-----------------------------------------|-----------------------------------------|  
|**bigint**|**decimal**|**decimal**|*|  
|**sql_variant**|**text**|**nvarchar (4000)**|*|  
|**varchar(max)**|**text**|**text**|**text**|  
|**nvarchar(max)**|**ntext**|**ntext**|**ntext**|  
|**varbinary(max)**|**image**|**image**|**image**|  
|XML|**ntext**|**ntext**|**ntext**|  
|UDT**|**image**|**image**|**image**|  
  
 \* この型はネイティブでサポートされています。  
  
 ** UDT はユーザー定義型を示します。  
  
## <a name="exporting-using--v-80"></a>-V 80 を使用したエクスポート  
 **-V80** スイッチを使用してデータを一括エクスポートする場合、**nvarchar(max)** 、**varchar(max)** 、**varbinary(max)** 、型のデータ、XML データ、およびネイティブ モードの UDT データは、**以降のバージョンの既定である 8 バイトのプレフィックスではなく、** text **、** image **、および** ntext[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 型のデータと同様に、4 バイトのプレフィックス付きで格納されます。  
  
## <a name="copying-date-values"></a>日付値のコピー  
 **bcp** は ODBC 一括コピー API を使用します。 したがって、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、 **bcp** に日付値をインポートするには、ODBC の日付形式 (*yyyy-mm-dd hh:mm:ss*[ *.f...* ]) を使用します。  
  
 **bcp** コマンドでは、 **datetime** 型と **smalldatetime** 型の値に使用される ODBC の既定の形式を使用して、文字形式のデータ ファイルがエクスポートされます。 たとえば、日付 **が含まれた** datetime `12 Aug 1998` 型の列は、文字列 `1998-08-12 00:00:00.000`としてデータ ファイルに一括コピーされます。  
  
> [!IMPORTANT]  
>  **bcp** を使用してデータを **smalldatetime** フィールドにインポートする場合は、秒の値が 00.000 になっていることを確認してください。それ以外の場合、この操作は失敗します。 **smalldatetime** データ型には、最も近い "分" までの値のみが保持されます。 この場合、BULK INSERT および INSERT ... SELECT * FROM OPENROWSET(BULK...) は失敗しませんが、秒の値は切り捨てられます。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 関連タスク  
 **一括インポートまたは一括エクスポートのデータ形式を使用するには**  
  
-   [文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)  
  
-   [ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)  
  
-   [Unicode 文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)  
  
-   [Unicode ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)  
  
## <a name="see-also"></a>参照  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [SQL Server データベース エンジンの旧バージョンとの互換性](../../database-engine/discontinued-database-engine-functionality-in-sql-server.md)   
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
  
