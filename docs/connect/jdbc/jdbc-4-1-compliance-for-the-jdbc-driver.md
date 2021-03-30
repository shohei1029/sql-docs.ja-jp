---
description: JDBC Driver for SQL Server が JDBC 4.1 仕様にどのように準拠しているかについて説明します。
title: JDBC ドライバーの JDBC 4.1 への準拠
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: f087fd40-8451-478e-b465-43112c711515
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 60fef7e2a6340e8d450732b814035ff8e92714c8
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633197"
---
# <a name="jdbc-41-compliance-for-the-jdbc-driver"></a>JDBC ドライバーの JDBC 4.1 への準拠

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

> [!NOTE]
> Microsoft JDBC Driver 4.2 for SQL Server より前のバージョンのドライバーは、Java Database Connectivity API 4.0 仕様に準拠しています。 このセクションは、4.2 リリースより前のバージョンには適用されません。

Java Database Connectivity API 4.1 仕様は Microsoft JDBC Driver 4.2 for SQL Server によってサポートされ、以下の API メソッドが実装されています。

## <a name="sqlserverconnection-class"></a>SQLServerConnection クラス

|新しいメソッド|説明|JDBC ドライバーの実装|
|----------------|-----------------|--------------------------------|
|void abort(Executor executor)|SQL Server への開いている接続を終了します。|java.sql.Connection インターフェイスの説明に従って実装されています。 詳細については、[java.sql.Connection](https://docs.oracle.com/javase/7/docs/api/java/sql/Connection.html) に関するページをご覧ください。|
|void setSchema(String schema)|現在の接続のスキーマを設定します。|SQL Server では、現在のセッションのスキーマの設定はサポートされていません。 このメソッドが呼び出された場合、ドライバーは警告を表示せずに、警告メッセージをログに記録します。 詳細については、[java.sql.Connection](https://docs.oracle.com/javase/7/docs/api/java/sql/Connection.html) に関するページをご覧ください。|
|String getSchema()|現在の接続のスキーマ名を返します。|SQL Server では、現在の接続のスキーマの設定がサポートされていないので、ドライバーから代わりにユーザーの既定のスキーマが返されます。 詳細については、[java.sql.Connection](https://docs.oracle.com/javase/7/docs/api/java/sql/Connection.html) に関するページをご覧ください。|

## <a name="sqlserverdatabasemetadata-class"></a>SQLServerDatabaseMetaData クラス

|新しいメソッド|説明|JDBC ドライバーの実装|
|----------------|-----------------|--------------------------------|
|boolean generatedKeyAlwaysReturned()|生成されたキーの取得をドライバーがサポートしているので true を返します|java.sql で説明されているように実装されています。 DatabaseMetaData インターフェイス。 詳細については、[java.sql.DatabaseMetaData](https://docs.oracle.com/javase/7/docs/api/java/sql/DatabaseMetaData.html) に関するページをご覧ください。|
|ResultSet getPseudoColumns(String catalog, String schemaPattern, String tableNamePattern, String columnNamePattern)|擬似/非表示の列の説明を取得します。|SQL Server には擬似列の正式な概念がないので、空の結果セットを返します。 詳細については、[java.sql.DatabaseMetaData](https://docs.oracle.com/javase/7/docs/api/java/sql/DatabaseMetaData.html) に関するページをご覧ください。|

## <a name="sqlserverstatement-class"></a>SQLServerStatement クラス

|新しいメソッド|説明|JDBC ドライバーの実装|
|----------------|-----------------|--------------------------------|
|void closeOnCompletion()|すべての依存結果セットが閉じられたときに、このステートメントが閉じられることを指定します。|Java.sql.Statement インターフェイスの説明に従って実装されています。 詳細については、[java.sql.Statement](https://docs.oracle.com/javase/7/docs/api/java/sql/Statement.html) に関するページを参照してください。|
|boolean isCloseOnCompletion()|すべての依存結果セットが閉じられたときに、このステートメントが閉じられるかどうかを示す値を返します。|Java.sql.Statement インターフェイスの説明に従って実装されています。 詳細については、[java.sql.Statement](https://docs.oracle.com/javase/7/docs/api/java/sql/Statement.html) に関するページを参照してください。|

 Java Database Connectivity API 4.1 仕様は Microsoft JDBC Driver 4.2 for SQL Server によってサポートされ、以下の機能が実装されています。

|新機能|説明|
|-----------------|-----------------|
|新しいエスケープ関数<br /><br /> 制限された戻り行エスケープ|部分的にサポートされています。<br /><br /> エスケープ構文: LIMIT \<rows> [OFFSET <row_offset>](using-sql-escape-sequences.md)。|

Java Database Connectivity API 4.1 仕様は Microsoft JDBC Driver 4.2 for SQL Server によってサポートされ、以下のようにデータ型がマッピングされています。

|データ型のマップ|説明|
|------------------------|-----------------|
|PreparedStatement.setObject() と PreparedStatement.setNull() メソッドで、新しいデータ型マッピングがサポートされるようになりました。|1.Java から JDBC 型への新しいマッピング<br /><br /> (a) java.math.BigInteger から JDBC BIGINT へ<br /><br /> (b) java.util.Date および java.util.Calendar から JDBC TIMESTAMP へ<br /><br /> 2.新しいデータ型変換:<br /><br /> (a) java.math.BigInteger から CHAR、VARCHAR、LONGVARCHAR、および BIGINT へ<br /><br /> (b) java.util.Date および java.util.Calendar から CHAR、VARCHAR、LONGVARCHAR、DATE、TIME、および TIMESTAMP へ<br /><br /> 詳細については、JDBC 4.1 仕様を参照してください。|
