---
title: 分散トランザクションを作成する |Microsoft Docs
description: アプリケーションでは、MSDTC を使用して、SQL Server の複数のインスタンスにわたってトランザクションを拡張または分散できます。 .NET クラスは、トランザクションを配布することもできます。
ms.custom: ''
ms.date: 05/13/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- MS DTC, performing distributed transactions
- SQL Server Native Client ODBC driver, transactions
- distributed transactions [ODBC]
- transactions [ODBC]
- ODBC, transactions
ms.assetid: 2c17fba0-7a3c-453c-91b7-f801e7b39ccb
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e87efff1462cef968a6fdce7038c65325bf7681c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755702"
---
# <a name="create-a-distributed-transaction"></a>分散トランザクションの作成

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

<!--
The following includes .md file is Empty, as of long before 2019/May/13.
/includes/snac-deprecated.md
-->


分散トランザクションは、さまざまな方法でさまざまな Microsoft SQL システムに対して作成できます。

## <a name="odbc-driver-calls-the-msdtc-for-sql-server-on-premises"></a>ODBC ドライバーは SQL Server オンプレミスの MSDTC を呼び出します。

Microsoft 分散トランザクションコーディネーター (MSDTC) を使用すると、アプリケーションはの2つ以上のインスタンスに対してトランザクションを拡張または _配信_ でき [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ます。 分散トランザクションは、2つのインスタンスが別々のコンピューターでホストされている場合でも機能します。

MSDTC はオンプレミス Microsoft SQL Server 用にインストールされていますが、Microsoft の Azure SQL Database クラウドサービスでは使用できません。

MSDTC は、C++ プログラムが分散トランザクションを管理するときに、SQL Server Native Client driver for Open Database Connectivity (ODBC) によって呼び出されます。 Native Client ODBC ドライバーには、Open Group Distributed Transaction Processing (DTP) XA 標準に準拠しているトランザクションマネージャーがあります。 このコンプライアンスは MSDTC によって要求されます。 通常、すべてのトランザクション管理コマンドは、この Native Client ODBC ドライバーを介して送信されます。 順序は次のとおりです。

1. C++ Native Client ODBC アプリケーションは、自動コミットモードがオフになっている状態で [SQLSetConnectAttr](../../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md)を呼び出すことによってトランザクションを開始します。

2. アプリケーションは、コンピューター A 上の SQL Server X の一部のデータを更新します。

3. アプリケーションは、コンピューター B 上の SQL Server Y の一部のデータを更新します。
    - SQL Server Y で更新が失敗した場合、両方の SQL Server インスタンスのすべてのコミットされていない更新がロールバックされます。

4. 最後に、SQL_COMMIT または SQL_ROLLBACK オプションを使用して、 [SQLEndTran _(1)_](../../../relational-databases/native-client-odbc-api/sqlendtran.md)を呼び出すことによってトランザクションを終了します。

_(1)_ ODBC を使用せずに MSDTC を呼び出すことができます。 このような場合、MSDTC はトランザクションマネージャーになり、アプリケーションは **SQLEndTran** を使用しなくなります。

### <a name="only-one-distributed-transaction"></a>1つの分散トランザクションのみ

C++ Native Client ODBC アプリケーションが分散トランザクションに参加しているとします。 次に、アプリケーションが2番目の分散トランザクションに参加します。 この場合、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーは元の分散トランザクションを残し、新しい分散トランザクションに参加します。

詳細については、「 [DTC プログラマーズリファレンス](/previous-versions/windows/desktop/ms686108(v=vs.85))」を参照してください。

## <a name="c-alternative-for-sql-database-in-the-cloud"></a>クラウドでの SQL Database の代替 C#

MSDTC は Azure SQL Database または Azure Synapse Analytics ではサポートされていません。

ただし、C# プログラムで .NET クラスの [TransactionScope](/dotnet/api/system.transactions.transactionscope)を使用することによって、SQL Database に対して分散トランザクションを作成できます。

### <a name="other-programming-languages"></a>その他のプログラミング言語

次の他のプログラミング言語では、SQL Database サービスを使用した分散トランザクションのサポートが提供されない場合があります。

- ODBC ドライバーを使用するネイティブ C++
- Transact-sql を使用したリンクサーバー
- JDBC ドライバー

## <a name="see-also"></a>関連項目

[トランザクションの実行 (ODBC)](performing-transactions-in-odbc.md)