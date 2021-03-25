---
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 03/16/2021
ms.topic: include
author: dphansen
ms.author: davidph
ms.openlocfilehash: 70bf06deb43e861209e12b6481f3fd414cb9361f
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833779"
---
## <a name="install-language-extensions"></a>言語拡張をインストールする

> [!NOTE]
> SQL Server 2019 に [Machine Learning Services](../../sql-server-machine-learning-services.md) がインストールされている場合は、言語拡張機能用の **mssql-server-extensibility** パッケージが既にインストールされているため、この手順は省略できます。

次のコマンドを実行して、R カスタム ランタイムに使用される SUSE Linux Enterprise Server (SLES) に [SQL Server 言語拡張機能](../../../language-extensions/language-extensions-overview.md)をインストールします。

```bash
# Install as root or sudo
sudo zypper install mssql-server-extensibility
```

## <a name="install-r"></a>R のインストール

1. [Machine Learning Services](../../sql-server-machine-learning-services.md) がインストールされている場合は、R が既に `/opt/microsoft/ropen/3.5.2/lib64/R` にインストールされています。 このパスを R_HOME として引き続き使用する場合は、この手順を省略できます。

    R の別のランタイムを使用する場合は、新しいバージョンのインストールを続ける前に、まず `microsoft-r-open-mro` を削除する必要があります。

    ```bash
    sudo zypper remove microsoft-r-open-mro-3.4.4
    ```

1. SUSE Linux Enterprise Server (SLES) 用に [R (3.3 以降)](https://www.r-project.org/) をインストールします。 既定では、R は **/usr/lib64/R** にインストールされます。 このパスは **R_HOME** です。 R を別の場所にインストールする場合は、**R_HOME** としてそのパスを記録しておきます。

    次の手順に従って R をインストールしてください。

    ```bash
    sudo zypper ar -f http://download.opensuse.org/repositories/devel:/languages:/R:/patched/openSUSE_12.3/ R-patched
    sudo zypper --gpg-auto-import-keys ref
    sudo zypper install R-core-libs R-core R-core-doc R-patched
    ```

    このパッケージが不要な場合は、**R-tcltk-3.6.1** の警告を無視してもかまいません。

## <a name="install-gcc-c"></a>gcc-c++ をインストールする

SUSE Linux Enterprise Server (SLES) に **gcc-c++** をインストールします。 これは、後でインストールする **Rcpp** に使用されます。

```bash
sudo zypper install gcc-c++
```
