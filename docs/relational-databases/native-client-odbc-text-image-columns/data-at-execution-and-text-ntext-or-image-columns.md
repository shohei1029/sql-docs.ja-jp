---
description: 実行時データと text 型、ntext 型、または image 型の列
title: 実行時データと Text、ntext、Image
ms.custom: ''
ms.date: 03/06/2017
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
- data-at-execution
- ODBC data-at-execution
- image columns [ODBC]
ms.assetid: 67ffb1a6-f38d-4712-ba64-96bdd41ec2b2
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3d1cd9d264fe6c21c4b15b65b5250ea4f148c78f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755012"
---
# <a name="data-at-execution-and-text-ntext-or-image-columns"></a>実行時データと text 型、ntext 型、または image 型の列
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC 実行時データは、バインドされた列やパラメーターに対して、アプリケーションが大量のデータを操作できるようにする機能です。 非常に大きな **text** 型、 **ntext** 型、または **image** 型の列を取得する場合、アプリケーションは単純に大きなバッファーを割り当てて、その列をバッファーにバインドし、行をフェッチすることができない可能性があります。 非常に大きな **text** 型、 **ntext** 型、または **image** 型の列を更新する場合、アプリケーションは単純に大きなバッファーを割り当てて、それを SQL ステートメントのパラメーターマーカーにバインドした後、ステートメントを実行できない可能性があります。 このような場合、アプリケーションでは、実行時データオプションを使用して [SQLGetData](../../relational-databases/native-client-odbc-api/sqlgetdata.md) または [sqlputdata](../../relational-databases/native-client-odbc-api/sqlputdata.md) を使用する必要があります。  
  
## <a name="see-also"></a>参照  
 [text 列と image 列の管理](../../relational-databases/native-client-odbc-text-image-columns/managing-text-and-image-columns.md)  
  
  
