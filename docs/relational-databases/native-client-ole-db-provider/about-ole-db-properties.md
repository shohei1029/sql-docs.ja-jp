---
description: SQL Server Native Client OLE DB のプロパティについて
title: OLE DB プロパティについて | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- OLE DB, properties
- SQL Server Native Client OLE DB provider, properties
- properties [OLE DB]
- property values [SQL Server Native Client]
ms.assetid: 0b36a61e-b542-400d-a3d2-e6f643caf2c6
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c4037f4fdc78594c5a9ae9ebb0a23dd4de9cc4b5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750382"
---
# <a name="about-sql-server-native-client-ole-db-properties"></a>SQL Server Native Client OLE DB のプロパティについて
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  コンシューマーは、プロパティ値を設定することで、特定のオブジェクトの動作を要求します。 たとえば、プロパティを使用して、行セットによって公開されるインターフェイスを指定します。 コンシューマーは、プロパティ値を取得して、行セット、セッション、データ ソース オブジェクトなど、オブジェクトの機能を判断します。  
  
 各プロパティには、値、データ型、説明、および読み取り/書き込み属性があります。また、行セット プロパティの場合は、列単位で適用できるかどうかを示すインジケーターがあります。  
  
 プロパティは GUID およびプロパティ ID を表す整数によって識別されます。 プロパティ セットは、同じ GUID を共有するすべてのプロパティのセットです。 定義済みの OLE DB プロパティセットに加えて、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは、プロバイダー固有のプロパティセットとプロパティを実装します。 各プロパティは、1 つ以上のプロパティ グループに属しています。 プロパティ グループは、特定のオブジェクトに適用されるすべてのプロパティをグループ化したものです。 プロパティ グループには、初期化プロパティ グループ、データ ソース プロパティ グループ、セッション プロパティ グループ、行セット プロパティ グループ、テーブル プロパティ グループ、列プロパティ グループなどがあります。 これらの各プロパティ グループに、プロパティが含まれています。  
  
 プロパティ値を設定するには、次の手順を実行します。  
  
1.  値を設定するプロパティを決定します。  
  
2.  目的のプロパティを含むプロパティ セットを決定します。  
  
3.  目的のプロパティ セットごとに 1 つ、DBPROPSET 構造体の配列を割り当てます。  
  
4.  プロパティ セットごとに DBPROP 構造体の配列を割り当てます。 各配列の要素数は、そのプロパティ セットに属するプロパティ (手順 1. で特定したプロパティ) の個数です。  
  
5.  プロパティごとに DBPROP 構造体に値を設定します。  
  
6.  プロパティ セットごとに DBPROPSET 構造体に情報 (プロパティ セット GUID、要素数、および対応する DBPROP 配列を指すポインター) を設定します。  
  
7.  要素数と DBPROPSET 構造体の配列を渡してメソッドを呼び出し、プロパティを設定します。  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client OLE DB プロバイダーアプリケーションの作成](../../relational-databases/native-client-ole-db-provider/creating-a-sql-server-native-client-ole-db-provider-application.md)   
 [プロパティ [OLE DB]](/previous-versions/windows/desktop/ms722734(v=vs.85))  
  
