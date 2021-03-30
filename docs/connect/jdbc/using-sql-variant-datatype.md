---
description: JDBC ドライバーの sql_variant データ型について、およびそれを使用してテーブル値パラメーター (TVP) と一括コピーをサポートする方法について説明します。
title: Sql_variant データ型の使用
ms.custom: ''
ms.date: 03/26/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: ''
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: dff69a149a8acf50aafa02653a6a083354dfa993
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633295"
---
# <a name="using-sql_variant-data-type"></a>Sql_variant データ型の使用

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

バージョン 6.3.0 以降、JDBC ドライバーでは sql_variant データ型がサポートされています。 sql_variant は、テーブル値パラメーターや BulkCopy などの機能を使用する場合にもサポートされていますが、これにはいくつかの制限があります。 すべてのデータ型を sql_variant データ型に格納することはできません。 sl_variant でサポートされているデータ型の一覧については、「[sql_variant (Transact-SQL)](../../t-sql/data-types/sql-variant-transact-sql.md)」を参照してください。

## <a name="populating-and-retrieving-a-table"></a>テーブルの設定と取得

次のように、sql_variant 列を持つテーブルがあると仮定します。

```sql
CREATE TABLE sampleTable (col1 sql_variant)
```

ステートメントを使用して値を挿入するサンプル スクリプト:

```java
try (Statement stmt = connection.createStatement()){
    stmt.execute("insert into sampleTable values (1)");
}
```

準備されたステートメントを使用して値を挿入する:

```java
try (PreparedStatement preparedStatement = con.prepareStatement("insert into sampleTable values (?)")) {
    preparedStatement.setObject(1, 1);
    preparedStatement.execute();
}
```

渡されるデータの基になる型がわかっている場合は、それぞれのセッターを使用できます。 たとえば、整数値を挿入するときは、`preparedStatement.setInt()` を使用できます。

```java
try (PreparedStatement preparedStatement = con.prepareStatement("insert into table values (?)")) {
    preparedStatement.setInt (1, 1);
    preparedStatement.execute();
}
```

テーブルから値を読み取るために、それぞれのゲッターを使用できます。 たとえば、`getInt()` メソッドまたは `getString()` メソッドは、サーバーからの値がわかっている場合に使用できます。

```java
try (SQLServerResultSet resultSet = (SQLServerResultSet) stmt.executeQuery("select * from sampleTable ")) {
    resultSet.next();
    resultSet.getInt(1); //or rs.getString(1); or rs.getObject(1);
}
```

## <a name="using-stored-procedures-with-sql_variant"></a>sql_variant でストアド プロシージャを使用する

次のようなストアド プロシージャがあるとします。

```java
String sql = "CREATE PROCEDURE " + inputProc + " @p0 sql_variant OUTPUT AS SELECT TOP 1 @p0=col1 FROM sampleTable ";
```

出力パラメーターを登録する必要があります。

```java
try (CallableStatement callableStatement = con.prepareCall(" {call " + inputProc + " (?) }")) {
    callableStatement.registerOutParameter(1, microsoft.sql.Types.SQL_VARIANT);
    callableStatement.execute();
}
```

## <a name="limitations-of-sql_variant"></a>sql_variant の制限事項

- TVP を使用して、sql_variant に格納されている `datetime`/`smalldatetime`/`date` 値をテーブルに入力する場合、ResultSet で `getDateTime()`/`getSmallDateTime()`/`getDate()` を呼び出すと動作せず、次の例外がスローされます。

    `Java.lang.String cannot be cast to java.sql.Timestamp`

    回避策: 代わりに `getString()` または `getObject()` を使用します。

- TVP を使用してテーブルにデータを入力し、sql_variant で null 値を送信することはサポートされていません。 これを実行しようとすると、例外が発生します。

    `Inserting null value with column type sql_variant in TVP is not supported.`

## <a name="see-also"></a>関連項目

[JDBC ドライバーのデータ型について](understanding-the-jdbc-driver-data-types.md)
