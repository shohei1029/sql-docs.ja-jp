---
title: 結果の処理 (OLE DB ドライバー)
description: OLE DB Driver for SQL Server のコンシューマーでコマンドまたはプロバイダーによって生成された行セット内のデータを取得してアクセスする方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB Driver for SQL Server, results processing
- OLE DB, processing results
- rowsets [SQL Server], results processing
- results [OLE DB Driver for SQL Server]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 99c6875a48ee85f13a5197689e8f74361469a3fc
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104746342"
---
# <a name="processing-results"></a>結果の処理
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  コマンドの実行、またはプロバイダーからの行セット オブジェクトの直接生成のいずれかによって行セット オブジェクトを作成する場合、コンシューマーはその行セット内のデータにアクセスして、データを取得する必要があります。  
  
 行セットは、OLE DB Driver for SQL Server が表形式でデータを公開できるようにするための重要な機能を持つオブジェクトです。 概念的には、行セットは行の集まりを表し、各行には列データが格納されています。 行セット オブジェクトでは、**IRowset** (行セットから順番に行をフェッチするメソッドを含みます)、**IAccessor** (コンシューマーのプログラム変数に表形式のデータをバインドする方法を指定する一連の列バインドの定義を許可します)、**IColumnsInfo** (行セット内の列に関する情報を提供します)、**IRowsetInfo** (行セットに関する情報を提供します) などのインターフェイスが公開されます。  
  
 コンシューマーは、**IRowset::GetData** メソッドを呼び出して、データ行を行セットからバッファーに取得できます。 **GetData** を呼び出す前に、DBBINDING 構造体のセットを使用してバッファーを記述します。 各バインドは、行セット内の列をコンシューマーのバッファーに格納する方法を記述するもので、次の情報が含まれます。  
  
-   バインドが適用される列 (パラメーター) の序数  
  
-   バインドされる内容 (データ値、データの長さ、バインドの状態など) に関する情報  
  
-   バッファー内の各部分へのオフセットに関する情報  
  
-   コンシューマーのバッファー内でのデータ値の長さと型  
  
 プロバイダーはデータを取得するときに、各バインドの情報を使用してコンシューマーのバッファーからデータを取得する位置と方法を決定します。 また、コンシューマーのバッファーにデータを設定するときに、各バインドの情報を使用してコンシューマーのバッファー内にあるデータを返す位置と方法を決定します。  
  
 DBBINDING 構造体を指定したら、アクセサーを作成 (**IAccessor::CreateAccessor**) します。 バインドの集まりであるアクセサーは、コンシューマーのバッファー内のデータを取得または設定するときに使用します。  
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server のアプリケーションの作成](../../oledb/ole-db-driver/creating-a-oledb-driver-for-sql-server-application.md)   
 [OLE DB の使用法に関するトピック](../../oledb/ole-db-how-to/ole-db-how-to-topics.md)  
  
  
