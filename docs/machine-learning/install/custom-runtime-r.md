---
title: R カスタム ランタイムをインストールする
description: 言語拡張機能を使用して SQL Server 用の R カスタム ランタイムをインストールする方法について説明します。 Python カスタム ランタイムは機械学習スクリプトを実行できます。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 03/16/2021
ms.topic: how-to
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
zone_pivot_groups: sqlml-platforms
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15'
ms.openlocfilehash: 5130a45eafbc9fa7b5fb684686fe9e0ea15c649b
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833765"
---
# <a name="install-an-r-custom-runtime-for-sql-server"></a>SQL Server 用の R カスタム ランタイムをインストールする

[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

SQL Server で外部 R スクリプトを実行するための R カスタム ランタイムをインストールする方法について説明します。

+ Windows
+ Ubuntu Linux
+ Red Hat Enterprise Linux (RHEL)
+ SUSE Linux Enterprise Server (SLES) バージョン 12

カスタム ランタイムは、機械学習スクリプトを実行することができ、[SQL Server 言語拡張機能](../../language-extensions/language-extensions-overview.md)を使用します。

[SQL Server Machine Learning Services](../sql-server-machine-learning-services.md) と共にインストールされる既定のランタイム バージョンではなく、独自のバージョンの R ランタイムを SQL Server と共に使用します。

::: zone pivot="platform-windows"
[!INCLUDE [R custom runtime - Windows](includes/custom-runtime-r-windows.md)]
::: zone-end

::: zone pivot="platform-linux-ubuntu"
[!INCLUDE [R custom runtime - Linux - Prerequisites](includes/custom-runtime-r-linux-prerequisites.md)]

[!INCLUDE [R custom runtime - Linux - Ubuntu specific steps](includes/custom-runtime-r-linux-ubuntu.md)]

[!INCLUDE [R custom runtime on Linux - Common steps](includes/custom-runtime-r-linux-common.md)]
::: zone-end

::: zone pivot="platform-linux-rhel"
[!INCLUDE [R custom runtime - Linux - Prerequisites](includes/custom-runtime-r-linux-prerequisites.md)]

[!INCLUDE [R custom runtime - Linux - RHEL specific steps](includes/custom-runtime-r-linux-rhel.md)]

[!INCLUDE [R custom runtime on Linux - Common steps](includes/custom-runtime-r-linux-common.md)]
::: zone-end

::: zone pivot="platform-linux-sles"
[!INCLUDE [R custom runtime - Linux - Prerequisites](includes/custom-runtime-r-linux-prerequisites.md)]

[!INCLUDE [R custom runtime - Linux - SLES specific steps](includes/custom-runtime-r-linux-sles.md)]

[!INCLUDE [R custom runtime on Linux - Common steps](includes/custom-runtime-r-linux-common.md)]
::: zone-end

## <a name="enable-external-script"></a>外部スクリプトを有効にする

Python 外部スクリプトは、ストアド プロシージャ [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) を使用して実行できます。

外部スクリプトを有効にするには、[Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md) を使用して次のステートメントを実行します。

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;  
```

## <a name="verify-installation"></a>インストールの確認

R カスタム ランタイムのインストールと機能を確認するには、次の SQL スクリプトを使用します。

```sql
EXEC sp_execute_external_script
    @language =N'myR',
    @script=N'
print(R.home());
print(file.path(R.home("bin"), "R"));
print(R.version);
print("Hello RExtension!");'
```

::: zone pivot="platform-linux-rhel"
[!INCLUDE [R custom runtime - Linux - RHEL known issues](includes/custom-runtime-r-linux-known-issues-rhel.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

+ [SQL Server 用の Python カスタム ランタイムをインストールする](custom-runtime-python.md)
+ [SQL Server の機能拡張フレームワーク](../concepts/extensibility-framework.md)
+ [言語拡張機能の概要](../../language-extensions/language-extensions-overview.md)