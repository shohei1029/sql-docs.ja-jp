---
title: OLE DB Driver for SQL Server のアプリケーションの作成
description: OLE DB Driver for SQL Server アプリケーションを作成するために必要な手順とその他のリソースについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB Driver for SQL Server, application creation
- applications [OLE DB Driver for SQL Server]
- OLE DB, creating applications
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: fdf209a8bdb34dc5f22a60ee190a118d8a608995
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104793080"
---
# <a name="creating-an-ole-db-driver-for-sql-server-application"></a>OLE DB Driver for SQL Server のアプリケーションの作成

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server アプリケーションは次の手順で作成します。

1. データ ソースへの接続の確立。
2. コマンドの実行。
3. 結果の処理。

> [!NOTE]
> 可能な場合は、Windows 認証を使用します。 Windows 認証が使用できない場合は、実行時に資格情報を入力するようユーザーに求めます。 資格情報をファイルに保存するのは避けてください。 資格情報を保存する必要がある場合は、[Win32 CryptoAPI](/windows/win32/seccng/cng-portal) を使用して暗号化してください。

## <a name="in-this-section"></a>このセクションの内容

- [データ ソースへの接続の確立](establishing-a-connection-to-a-data-source.md)
- [コマンドの実行](executing-a-command.md)
- [結果の処理](processing-results.md)
- [OLE DB プロパティについて](about-ole-db-properties.md)
- [OLE DB Driver for SQL Server での OLE DB を使用した OUTPUT 句の使用](using-the-output-clause-with-ole-db-in-oledb-driver-for-sql-server.md)

## <a name="see-also"></a>関連項目

[OLE DB Driver for SQL Server のプログラミング](../ole-db/oledb-driver-for-sql-server-programming.md)
