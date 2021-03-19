---
title: Azure Synapse Analytics の SqlPackage
description: Azure Synapse Analytics のシナリオで SqlPackage を使用するためのヒント
ms.custom: tools|sos
ms.date: 03/10/2021
ms.prod: sql
ms.reviewer: llali; sstein
ms.prod_service: sql-tools
ms.topic: conceptual
author: dzsquared
ms.author: drskwier
ms.openlocfilehash: 500d946c036041481094292720744e7896d64622
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611022"
---
# <a name="sqlpackage-for-azure-synapse-analytics"></a>Azure Synapse Analytics の SqlPackage

この記事では、Azure Synapse Analytics データベースに焦点を当てた SqlPackage.exe の機能について説明します。

## <a name="extract"></a>Extract
Azure Blob Storage にスキーマを[抽出](sqlpackage-extract.md)するには、次のプロパティが必要です。
- /p:AzureStorageBlobEndpoint
- /p:AzureStorageContainer
- /p:AzureStorageKey

抽出のアクセスは、ストレージ アカウント キーを使用して承認されます。  コンテナー内のストレージ ルート パスを設定する追加のパラメーターは、省略可能です。
- /p:AzureStorageRootPath

このプロパティがない場合、パスの既定値は `servername/databasename/timestamp/` になります。

## <a name="publish"></a>発行
Azure Blob Storage の dacpac からスキーマを[公開](sqlpackage-publish.md)するには、次のプロパティが必要です。
- /p:AzureStorageBlobEndpoint
- /p:AzureStorageContainer
- /p:AzureStorageRootPath
- /p:AzureStorageKey or /p:AzureSharedAccessSignatureToken

公開のアクセスは、ストレージ アカウント キーまたは Shared Access Signature (SAS) トークンを使用して承認できます。

## <a name="next-steps"></a>次の手順
- [抽出](sqlpackage-extract.md)の詳細情報
- [公開](sqlpackage-publish.md)の詳細情報
- [Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction) の詳細情報
- [Azure ストレージ アカウント キー](/azure/storage/common/storage-account-keys-manage)の詳細