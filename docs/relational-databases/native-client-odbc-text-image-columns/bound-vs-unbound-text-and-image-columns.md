---
description: バインドされた text、image 型の列とバインドされない text、image 型の列
title: バインドされたテキストと画像の列 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- text columns [ODBC]
- SQL Server Native Client ODBC driver, image columns
- SQL Server Native Client ODBC driver, text columns
- data types [ODBC], image
- data types [ODBC], text
- columns [ODBC]
- ODBC data types, image columns
- ODBC data types, text columns
- image columns [ODBC]
ms.assetid: ffd3442e-d880-46e9-b848-2365a09a2406
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ede2c58f6b5a1d3710aa6d4a06c0ffe61761f7d7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755042"
---
# <a name="bound-vs-unbound-text-and-image-columns"></a>バインドされた text、image 型の列とバインドされない text、image 型の列
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  サーバーカーソルを使用する場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーは、 **sqlfetch** の実行時に、バインドされていない **text**、 **ntext**、または **image** 型の列のデータを転送しないように最適化されています。 **Text** 型、 **ntext** 型、または **image** 型のデータは、アプリケーションが列に対して [SQLGetData](../../relational-databases/native-client-odbc-api/sqlgetdata.md)を発行するまで、実際にはサーバーから取得されません。  
  
 ユーザーがカーソル内を上下にスクロールしている間に、 **text**、 **ntext**、または **image** データが表示されないように、多くのアプリケーションを記述できます。 ユーザーが行を選択して詳細を取得すると、アプリケーションは **SQLGetData** を呼び出して、 **text**、 **ntext**、または **image** データを取得できます。 これにより、ユーザーが選択していない行の **text** 型、 **ntext** 型、または **image** 型のデータが転送されるのを防ぐことができるため、非常に大量のデータの転送を防ぐことができます。  
  
## <a name="see-also"></a>参照  
 [Text 列と Image 列の管理](../../relational-databases/native-client-odbc-text-image-columns/managing-text-and-image-columns.md)   
 [カーソル動作](../../relational-databases/native-client-odbc-cursors/cursor-behaviors.md)  
  
  
