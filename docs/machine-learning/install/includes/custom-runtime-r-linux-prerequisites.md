---
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 03/16/2021
ms.topic: include
author: dphansen
ms.author: davidph
ms.openlocfilehash: f14691931190da9f72d3d54a3f1b4280b679c6e3
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833745"
---
## <a name="prerequisites"></a>前提条件

R カスタム ランタイムをインストールする前に、次のものをインストールします。

+ Linux 用 SQL Server 2019 をインストールします。 SQL Server は、Red Hat Enterprise Linux (RHEL)、SUSE Linux Enterprise Server (SLES) バージョン 12、および Ubuntu にインストールできます。 詳細については、[SQL Server on Linux のインストール ガイダンス](../../../linux/sql-server-linux-setup.md)を参照してください。

+ SQL Server 2019 の累積的な更新プログラム (CU) 3 以降にアップグレードします。 次の手順に従います。
    1. 累積的な更新プログラムのリポジトリを構成します。 詳細については、「[SQL Server on Linux のインストールとアップグレードを行うためのリポジトリを構成する](../../../linux/sql-server-linux-change-repo.md)」を参照してください。

    1. **mssql-server** パッケージを、最新の累積更新プログラムに更新します。 詳細については、[「SQL Server on Linux のインストール ガイド」の「SQL Server の更新またはアップグレード」](../../../linux/sql-server-linux-setup.md#upgrade)を参照してください。
