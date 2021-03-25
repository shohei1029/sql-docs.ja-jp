---
description: JDBC ドライバーでのクエリ実行からの結果 (複数の結果セットを含む) を完全に処理することについて説明します。
title: 結果の解析
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: v-daenge
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: ''
author: rene-ye
ms.author: v-reye
manager: kenvh
ms.openlocfilehash: 1605fd65414a81acc0912d774a24ff4ae10a1c9c
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104793021"
---
# <a name="parsing-the-results"></a>結果の解析

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

この記事では、ユーザーがクエリから返される結果を完全に処理することを SQL Server がどのように想定するかについて説明します。

## <a name="update-counts-and-result-sets"></a>更新数と結果セット

このセクションでは、SQL Server から返される最も一般的な 2 つの結果、つまり更新数と結果セットについて説明します。 一般に、ユーザーがクエリを実行すると、これらの結果のいずれかが返されます。 ユーザーは、結果を処理するときに両方を処理することが想定されます。

次のコード例は、ユーザーがサーバーからのすべての結果を反復処理する方法を示しています。

```java
try (Connection connection = DriverManager.getConnection(URL); Statement statement = connection.createStatement()) {
    boolean resultsAvailable = statement.execute(USER_SQL);
    int updateCount = -2;
    while (true) {
        updateCount = statement.getUpdateCount();
        if (!resultsAvailable && updateCount == -1)
            break;
        if (resultsAvailable) {
            // handle ResultSet
        } else {
            // handle Update Count
        }
        resultsAvailable = statement.getMoreResults();
    }
}
```

## <a name="exceptions"></a>例外

エラーまたは情報メッセージを生成するステートメントを実行すると、SQL Server の応答が異なる場合があります。 たとえば、実行プランを生成できない場合は、`execute()` ですぐにエラー メッセージがスローされます。 エラーがステートメントの処理に関連している場合、アプリケーションで結果セットを解析して例外を確認する必要があります。

SQL Server が実行プランを生成できない場合、例外はただちにスローされます。

```java
String SQL = "SELECT * FROM nonexistentTable;";
try (Statement statement = connection.createStatement();) {
    statement.execute(SQL);
} catch (SQLException e) {
    e.printStackTrace();
}
```

SQL Server が結果セットにエラー メッセージを返す場合、その結果セットを処理して例外を取得する必要があります。

```java
String SQL = "SELECT 1/0;";
try (Statement statement = connection.createStatement();) {
    boolean hasResult = statement.execute(SQL);
    if (hasResult) {
        try (ResultSet rs = statement.getResultSet()) {
            // Exception is thrown on next().
            while (rs.next()) {}
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

ステートメント実行によって複数の結果セットが生成される場合、例外を含む結果セットに到達するまで、各結果セットを処理する必要があります。

```java
String SQL = "SELECT 1; SELECT * FROM nonexistentTable;";
try (Statement statement = connection.createStatement();) {
    // Does not throw an exception on execute().
    boolean hasResult = statement.execute(SQL);
    while (hasResult) {
        try (ResultSet rs = statement.getResultSet()) {
            while (rs.next()) {
                System.out.println(rs.getString(1));
            }
        }
        // Moves the next result set that generates the exception.
        hasResult = statement.getMoreResults();
    }
} catch (SQLException e) {
    e.printStackTrace();
}
```

たとえば、`String SQL = "SELECT * FROM nonexistentTable; SELECT 1;";` は、`execute()` ですぐに例外をスローし、`SELECT 1` は一切実行されません。

SQL Server からのエラーの重大度が `0` から `9` までの場合、情報メッセージと見なされ、`SQLWarning`として返されます。

```java
String SQL = "RAISERROR ('WarningLevel5', 5, 2);";
try (Statement statement = connection.createStatement();) {
    boolean hasResult = statement.execute(SQL);
    SQLWarning warning = statement.getWarnings();
    System.out.println(warning);
}
```

## <a name="see-also"></a>関連項目

[JDBC ドライバーの概要](overview-of-the-jdbc-driver.md)
