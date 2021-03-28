---
description: sendTimeAsDatetime 接続オプションを使用して、java.sql.Time 値のサーバーへの送信方法を構成する方法について説明します。
title: java.sql.Time の値をサーバーに送信する方法の構成
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 07eb00dd-621a-46f9-a5a5-8cab4d6058b5
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 41e53b1e0feb18e3d3394a0c726f80df8743fd61
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890625"
---
# <a name="configuring-how-javasqltime-values-are-sent-to-the-server"></a>java.sql.Time の値をサーバーに送信する方法の構成

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

パラメーターを設定するために java.sql.Time オブジェクトまたは java.sql.Types.TIME JDBC 型を使用する場合、サーバーに java.sql.Time 値をどのように ( の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **time** 型として、または **datetime** 型として) 送信するかを構成することができます。

次のいずれかのメソッドを使用するケースが、このシナリオに該当します。

- [SQLServerCallableStatement.registerOutParameter(int, int)](reference/registeroutparameter-method-int-int.md)
- [SQLServerCallableStatement.registerOutParameter(int, int, int)](reference/registeroutparameter-method-int-int-int.md)
- [SQLServerCallableStatement.setTime](reference/settime-method-sqlservercallablestatement.md)
- [SQLServerPreparedStatement.setTime](reference/settime-method-sqlserverpreparedstatement.md)
- [SQLServerCallableStatement.setObject](reference/setobject-method-sqlservercallablestatement.md)
- [SQLServerPreparedStatement.setObject](reference/setobject-method-sqlserverpreparedstatement.md)

## <a name="sendtimeasdatetime"></a>SendTimeAsDatetime

java.sql.Time 値の送信方法を構成するには **sendTimeAsDatetime** 接続プロパティを使用します。 詳細については、「[接続プロパティの設定](setting-the-connection-properties.md)」を参照してください。

**sendTimeAsDatetime** 接続プロパティの値は、[SQLServerDataSource.setSendTimeAsDatetime](reference/setsendtimeasdatetime-method-sqlserverdatasource.md) を使用してプログラムから変更できます。

[!INCLUDE[ssKatmai](../../includes/sskatmai_md.md)] より前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、**time** データ型がサポートされていないため、java.sql.Time を使用するアプリケーションでは通常、java.sql.Time 値が **datetime** または **smalldatetime** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型として格納されます。

**Smalldatetime** 値を使用するときに **datetime** データ型と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]データ型を使用する場合は、 **sendTimeAsDatetime** 接続プロパティを **true** に設定する必要があります。 java.sql.Time 値を操作するときに **time** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型を使用する場合は、**sendTimeAsDatetime** 接続プロパティを **false** に設定する必要があります。

日付と時刻の両方を格納できるパラメーターに java.sql.Time 値を送信すると、java.sql.Time 値の送信方法によって、既定の日付は異なります。**datetime** 値として送信された場合は 1/1/1970 に、**time** 値として送信された場合は 1/1/1900 になります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にデータを送信する際のデータ変換の詳細については、「[日時データの使用](/previous-versions/sql/sql-server-2008-r2/ms180878(v=sql.105))」を参照してください。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] JDBC Driver 3.0 では、**sendTimeAsDatetime** は既定で true となります。 今後のリリースでは、**sendTimeAsDatetime** 接続プロパティの既定値が false になる予定です。

アプリケーションの動作が **sendTimeAsDatetime** 接続プロパティの既定値に左右されないようにするための対策としては、次のような方法があります。

- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **time** データ型を使用する場合は java.sql.Time を使用する。
- **datetime**、**smalldatetime**、および **datetime2**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型を操作する場合は、java.sql.Timestamp を使用する。

暗号化された列では time から datetime への変換がサポートされないため、暗号化された列では SendTimeAsDatetime が false である必要があります。 Microsoft JDBC Driver 6.0 for SQL Server 以降では、SQLServerConnection クラスには、sendTimeAsDatetime プロパティの値を設定および取得するための次の 2 つのメソッドがあります。

```java
  public boolean getSendTimeAsDatetime()
  public void setSendTimeAsDatetime(boolean sendTimeAsDateTimeValue)
```

## <a name="see-also"></a>関連項目

[JDBC ドライバーのデータ型について](understanding-the-jdbc-driver-data-types.md)
