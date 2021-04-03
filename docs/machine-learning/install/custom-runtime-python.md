---
title: Python カスタム ランタイムをインストールする
description: 言語拡張機能を使用して SQL Server 用の Python カスタム ランタイムをインストールする方法について説明します。 Python カスタム ランタイムは機械学習スクリプトを実行できます。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 02/08/2021
ms.topic: how-to
author: dphansen
ms.author: davidph
ms.custom: contperf-fy21q3
zone_pivot_groups: sqlml-platforms
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15'
ms.openlocfilehash: 22ba1ff16d22582be39e7b48a297e9dc65f9f554
ms.sourcegitcommit: e4b71e5d432a29b6c76ea457b00aa0abd4b6c77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106273474"
---
# <a name="install-a-python-custom-runtime-for-sql-server"></a>SQL Server 用の Python カスタム ランタイムをインストールする
[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

SQL Server で外部 Python スクリプトを実行するための Python カスタム ランタイムをインストールする方法について説明します。

+ Windows
+ Ubuntu Linux
+ Red Hat Enterprise Linux (RHEL)
+ SUSE Linux Enterprise Server (SLES)

カスタム ランタイムは、機械学習スクリプトを実行することができ、[SQL Server 言語拡張機能](../../language-extensions/language-extensions-overview.md)を使用します。

[SQL Server Machine Learning Services](../sql-server-machine-learning-services.md) と共にインストールされる既定のランタイム バージョンではなく、独自のバージョンの Python ランタイムを SQL Server と共に使用します。

::: zone pivot="platform-windows"
[!INCLUDE [Python custom runtime - Windows](includes/custom-runtime-python-windows.md)]
::: zone-end

::: zone pivot="platform-linux-ubuntu"
[!INCLUDE [Python custom runtime - Linux - Prerequisites](includes/custom-runtime-python-linux-prerequisites.md)]

[!INCLUDE [Python custom runtime - Linux - Ubuntu specific steps](includes/custom-runtime-python-linux-ubuntu.md)]

[!INCLUDE [Python custom runtime on Linux - Common steps](includes/custom-runtime-python-linux-common.md)]
::: zone-end

::: zone pivot="platform-linux-rhel"
[!INCLUDE [Python custom runtime - Linux - Prerequisites](includes/custom-runtime-python-linux-prerequisites.md)]

[!INCLUDE [Python custom runtime - Linux - RHEL specific steps](includes/custom-runtime-python-linux-rhel.md)]

[!INCLUDE [Python custom runtime on Linux - Common steps](includes/custom-runtime-python-linux-common.md)]
::: zone-end

::: zone pivot="platform-linux-sles"
[!INCLUDE [Python custom runtime - Linux - Prerequisites](includes/custom-runtime-python-linux-prerequisites.md)]

[!INCLUDE [Python custom runtime - Linux - SLES specific steps](includes/custom-runtime-python-linux-sles.md)]

[!INCLUDE [Python custom runtime on Linux - Common steps](includes/custom-runtime-python-linux-common.md)]
::: zone-end

## <a name="enable-external-script"></a>外部スクリプトを有効にする

Python 外部スクリプトは、ストアド プロシージャ [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) を使用して実行できます。

外部スクリプトを有効にするには、[Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md) を使用して次のステートメントを実行します。

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;  
```

## <a name="verify-installation"></a>インストールの確認

Python カスタム ランタイムのインストールと機能を確認するには、次の SQL スクリプトを使用します。

```sql
EXEC sp_execute_external_script
@language =N'myPython',
@script=N'
import sys
print(sys.path)
print(sys.version)
print(sys.executable)'
```

## <a name="next-steps"></a>次のステップ

+ [SQL Server 用の R カスタム ランタイムをインストールする](custom-runtime-r.md)
+ [SQL Server の機能拡張フレームワーク](../concepts/extensibility-framework.md)
+ [言語拡張機能の概要](../../language-extensions/language-extensions-overview.md)
