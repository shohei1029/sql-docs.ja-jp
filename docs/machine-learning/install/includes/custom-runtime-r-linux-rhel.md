---
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 03/18/2021
ms.topic: include
author: dphansen
ms.author: davidph
ms.openlocfilehash: f6ec3bbcc720fe472cc7146d59c30f0f3fc65ab0
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833757"
---
+ RExtension には、GLIBCXX_3.4.20 が必要です。 Red Hat Enterprise Linux (RHEL) に **libstdc++.so.6** のバージョンをインストールすることで、これが提供されることを確認してください。

## <a name="install-language-extensions"></a>言語拡張をインストールする

> [!NOTE]
> SQL Server 2019 に [Machine Learning Services](../../sql-server-machine-learning-services.md) がインストールされている場合は、言語拡張機能用の **mssql-server-extensibility** パッケージが既にインストールされているため、この手順は省略できます。

次のコマンドを実行して、R カスタム ランタイムに使用される Red Hat Enterprise Linux (RHEL) に [SQL Server 言語拡張機能](../../../language-extensions/language-extensions-overview.md)をインストールします。

```bash
# Install as root or sudo
sudo yum install mssql-server-extensibility
```

## <a name="install-r"></a>R のインストール

1. [Machine Learning Services](../../sql-server-machine-learning-services.md) がインストールされている場合は、R が既に `/opt/microsoft/ropen/3.5.2/lib64/R` にインストールされています。 このパスを R_HOME として引き続き使用する場合は、この手順を省略できます。

    R の別のランタイムを使用する場合は、新しいバージョンのインストールを続ける前に、まず `microsoft-r-open-mro` を削除する必要があります。

    ```bash
    sudo yum erase microsoft-r-open-mro-3.5.2
    ```

1. Red Hat Enterprise Linux (RHEL) 用の [R (3.3 以降)](https://www.r-project.org/) をインストールします。 既定では、R は **/usr/lib64/R** にインストールされます。 このパスは **R_HOME** です。 R を別の場所にインストールする場合は、**R_HOME** としてそのパスを記録しておきます。

    ```bash
    sudo yum install -y R
    ```
