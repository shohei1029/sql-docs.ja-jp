---
title: Python と R の SQL ループバック接続
description: sp_execute_external_script から実行される Python または R スクリプトからデータの読み取りまたは書き込みを行うために、ループバック接続を使用して ODBC 経由で SQL Server に接続する方法について説明します。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 03/22/2021
ms.topic: how-to
author: Aniruddh25
ms.author: anmunde
ms.reviewer: dphansen
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15'
ms.openlocfilehash: 5de6c3e8c3ba65858cfc04ddcbd2bd45c5b84cd2
ms.sourcegitcommit: 17f05be5c08cf9a503a72b739da5ad8be15baea5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105103869"
---
# <a name="loopback-connection-to-sql-server-from-a-python-or-r-script"></a>Python または R スクリプトからの SQL Server へのループバック接続
[!INCLUDE [SQL Server 2019 and later, and SQL MI](../../includes/applies-to-version/sqlserver2019-asdbmi.md)]

[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) から実行される Python または R スクリプトからデータの読み取りまたは書き込みを行うために、ループバック接続と [Machine Learning Services](../sql-server-machine-learning-services.md) を使用して [ODBC](../../connect/odbc/microsoft-odbc-driver-for-sql-server.md) 経由で SQL Server に接続する方法について説明します。 この方法は、`sp_execute_external_script` の **InputDataSet** 引数と **OutputDataSet** 引数を使用できない場合に使用できます。

## <a name="connection-string"></a>接続文字列

ループバック接続を行うには、正しい接続文字列を使用する必要があります。 共通の必須引数として、[ODBC ドライバー](../../connect/odbc/microsoft-odbc-driver-for-sql-server.md)の名前、サーバーのアドレス、およびデータベースの名前を指定します。

### <a name="connection-string-on-windows"></a>Windows の接続文字列

SQL Server on Windows での認証の場合、Python または R スクリプトで、sp_execute_external_script を実行したのと同じユーザーとして認証するために、**Trusted_Connection** 接続文字列属性を使用できます。

Windows でのループバック接続文字列の例を次に示します。

``` 
"Driver=SQL Server;Server=.;Database=nameOfDatabase;Trusted_Connection=Yes;"
```

### <a name="connection-string-on-linux"></a>Linux の接続文字列

SQL Server on Linux での認証の場合、Python または R スクリプトで、`sp_execute_external_script` を実行したのと同じユーザーとして認証するために、ODBC ドライバーの **ClientCertificate** 属性と **ClientKey** 属性を使用する必要があります。 このために、[最新の ODBC ドライバー](../../connect/odbc/download-odbc-driver-for-sql-server.md) バージョン17.4.1.1 を使用する必要があります。

Linux でのループバック接続文字列の例を次に示します。

```
"Driver=ODBC Driver 17 for SQL Server;Server=fe80::8012:3df5:0:5db1%eth0;Database=nameOfDatabase;ClientCertificate=file:/var/opt/mssql-extensibility/data/baeaac72-60b3-4fae-acfd-c50eff5d34a2/sqlsatellitecert.pem;ClientKey=file:/var/opt/mssql-extensibility/data/baeaac72-60b3-4fae-acfd-c50eff5d34a2/sqlsatellitekey.pem;TrustServerCertificate=Yes;Trusted_Connection=no;Encrypt=Yes"
```

サーバー アドレス、クライアント証明書ファイルの場所、およびクライアント キー ファイルの場所は、どの `sp_execute_external_script` でも固有であり、Python の場合は API **rx_get_sql_loopback_connection_string()**、R の場合は API **rxGetSqlLoopbackConnectionString()** を使用して取得できます。

接続文字列の属性について詳しくは、Microsoft ODBC Driver for SQL Server の「[DSN と接続文字列のキーワードと属性](../../connect/odbc/dsn-connection-string-attribute.md#new-connection-string-keywords-and-connection-attributes)」を参照してください。

### <a name="connection-string-on-azure-sql-managed-instance"></a>Azure SQL Managed Instance での接続文字列
Azure SQL Managed Instance の接続文字列を生成するには、次のセクションの例を参照してください。 **ODBC Driver 11 for SQL Server** をループバック接続用の ODBC ドライバーとして使用します。

## <a name="generate-connection-string-with-revoscalepy-for-python"></a>Python の revoscalepy を使用して接続文字列を生成する

Python スクリプトでループバック接続用の正しい接続文字列を生成するために、[revoscalepy](../python/ref-py-revoscalepy.md) の API **rx_get_sql_loopback_connection_string ()** を使用できます。

これは、次の引数を受け取ります。

| 引数 | 説明 |
|-|-|
| name_of_database | 接続する先のデータベースの名前 |
| odbc_driver | ODBC ドライバーの名前 |

### <a name="examples"></a>例

SQL Server on Windows の例:

```sql
EXECUTE sp_execute_external_script
@language = N'Python',
@script = N'
from revoscalepy import rx_get_sql_loopback_connection_string, RxSqlServerData, rx_data_step
loopback_connection_string = rx_get_sql_loopback_connection_string(odbc_driver="SQL Server", name_of_database="DBName")
print("Connection String:{0}".format(loopback_connection_string))
data_set = RxSqlServerData(sql_query = "select col1, col2 from tableName",
                           connection_string = loopback_connection_string)
OutputDataSet = rx_data_step(data_set)
'
WITH RESULT SETS ((col1 int, col2 int))
GO
```

SQL Server on Linux の例:

```sql
EXECUTE sp_execute_external_script
@language = N'Python',
@script = N'
from revoscalepy import rx_get_sql_loopback_connection_string, RxSqlServerData, rx_data_step
loopback_connection_string = rx_get_sql_loopback_connection_string(odbc_driver="ODBC Driver 17 for SQL Server",
                                                                   name_of_database="DBName")
print("Loopback Connection String:{0}".format(loopback_connection_string))
data_set = RxSqlServerData(sql_query = "select col1, col2 from tableName",
                           connection_string = loopback_connection_string)
OutputDataSet = rx_data_step(data_set)
'
WITH RESULT SETS ((col1 int, col2 int))
GO
```

Azure SQL Managed Instance の例:

```sql
EXECUTE sp_execute_external_script
@language = N'Python',
@script = N'
from revoscalepy import rx_get_sql_loopback_connection_string, RxSqlServerData, rx_data_step
loopback_connection_string = rx_get_sql_loopback_connection_string(odbc_driver="ODBC Driver 11 for SQL Server", name_of_database="DBName")
print("Connection String:{0}".format(loopback_connection_string))
data_set = RxSqlServerData(sql_query = "select col1, col2 from tableName",
                           connection_string = loopback_connection_string)
OutputDataSet = rx_data_step(data_set)
'
WITH RESULT SETS ((col1 int, col2 int))
GO
```

## <a name="generate-connection-string-with-revoscaler-for-r"></a>R の RevoScaleR を使用して接続文字列を生成する

R スクリプトでループバック接続用の正しい接続文字列を生成するために、[RevoScaleR](../r/ref-r-revoscaler.md) の API **rxGetSqlLoopbackConnectionString()** を使用できます。

これは、次の引数を受け取ります。

| 引数 | 説明 |
|-|-|
| nameOfDatabase | 接続する先のデータベースの名前 |
| odbcDriver | ODBC ドライバーの名前 |

### <a name="examples"></a>例

SQL Server on Windows の例:

```sql
EXECUTE sp_execute_external_script
@language = N'R',
@script = N'
    loopbackConnectionString <- rxGetSqlLoopbackConnectionString(nameOfDatabase="DBName", odbcDriver ="SQL Server")
    print(paste("Connection String:", loopbackConnectionString))
    dataSet <- RxSqlServerData(sqlQuery = "select col1, col2 from tableName",
                               connectionString = loopbackConnectionString)
    OutputDataSet <- rxDataStep(dataSet)
'
WITH RESULT SETS ((col1 int, col2 int))
GO
```

SQL Server on Linux の例:

```sql
EXECUTE sp_execute_external_script
@language = N'R',
@script = N'
    loopbackConnectionString <-  rxGetSqlLoopbackConnectionString(nameOfDatabase="DBName", 
                                                                  odbcDriver ="ODBC Driver 17 for SQL Server")
    print(paste("Connection String:", loopbackConnectionString))
    dataSet <- RxSqlServerData(sqlQuery = "select col1, col2 from tableName", 
                               connectionString = loopbackConnectionString)
    OutputDataSet <- rxDataStep(dataSet)
'
WITH RESULT SETS ((col1 int, col2 int))
GO
```

Azure SQL Managed Instance の例:

```sql
EXECUTE sp_execute_external_script
@language = N'R',
@script = N'
    loopbackConnectionString <- rxGetSqlLoopbackConnectionString(nameOfDatabase="DBName", odbcDriver ="ODBC Driver 11 for SQL Server")
    print(paste("Connection String:", loopbackConnectionString))
    dataSet <- RxSqlServerData(sqlQuery = "select col1, col2 from tableName",
                               connectionString = loopbackConnectionString)
    OutputDataSet <- rxDataStep(dataSet)
'
WITH RESULT SETS ((col1 int, col2 int))
GO
```

## <a name="next-steps"></a>次のステップ

+ [Microsoft ODBC Driver for SQL Server](../../connect/odbc/microsoft-odbc-driver-for-sql-server.md)
+ [revoscalepy](../python/ref-py-revoscalepy.md)
+ [RevoScaleR](../r/ref-r-revoscaler.md)
