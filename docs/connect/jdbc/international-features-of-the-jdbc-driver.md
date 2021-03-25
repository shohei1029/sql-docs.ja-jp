---
description: JDBC ドライバーの国際化機能とアプリケーションをローカライズする方法について学びます。
title: JDBC ドライバーの国際化機能
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: bbb74a1d-9278-401f-9530-7b5f45aa79de
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 159c1e35761876d786ad0a36c78eaa8ce8f3e847
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673426"
---
# <a name="international-features-of-the-jdbc-driver"></a>JDBC ドライバーの国際化機能

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] の国際対応機能には、次の項目があります。
  
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と同じ言語により完全にローカライズされたサービスをサポートします。  
- ロケールに依存する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データに対する Java 言語変換をサポートします。  
- オペレーティング システムに関係なく、世界中の言語に対応  
- 世界中のドメイン名に対応 (Microsoft JDBC Driver 6.0 for SQL Server より)  
  
## <a name="handling-of-character-data"></a>文字データの処理

Java の文字データは、既定で Unicode として処理されます。Java **String** オブジェクトは Unicode 文字データを表します。 JDBC ドライバーにおいてこの規則の唯一の例外となるのは、ASCII ストリームの getter メソッドと setter メソッドです。暗黙の仮定で既知のコード ページの 1 つ (ASCII) によるバイト ストリームが使用されるため、これは特殊なケースです。  
  
さらに、JDBC ドライバーには、**sendStringParametersAsUnicode** 接続文字列プロパティが用意されています。 このプロパティを使用して、文字データに対して準備されたパラメーターを Unicode ではなく ASCII またはマルチバイト文字セット (MBCS) で送信するように指定できます。 **sendStringParametersAsUnicode** 接続文字列プロパティについて詳しくは、「[接続プロパティの設定](setting-the-connection-properties.md)」をご覧ください。  
  
### <a name="driver-incoming-conversions"></a>ドライバーの受信変換

サーバーから受信する Unicode テキスト データは、変換する必要はありません。 データは Unicode として直接渡されます。 サーバーから受信する Unicode 以外のデータは、データベースまたは列レベルでデータのコード ページから Unicode に変換されます。 JDBC ドライバーは、Java 仮想マシン (JVM) 変換ルーチンを使用してこのような変換を実行します。 変換は、あらゆる型の文字列および文字のストリームの getter メソッドで実行されます。  
  
JVM がデータベースのデータに対して適切なコード ページをサポートしていない場合、JDBC ドライバーは、"XXX コード ページは Java 環境ではサポートされていません" という例外をスローします。 この問題を回避するには、その JVM でサポートする必要のある国際的な文字を完全にインストールする必要があります。
  
### <a name="driver-outgoing-conversions"></a>ドライバーの送信変換

ドライバーからサーバーへ送信される文字データは、ASCII または Unicode です。 たとえば、[SQLServerPreparedStatement](../../connect/jdbc/reference/sqlserverpreparedstatement-class.md) クラスと [SQLServerCallableStatement](../../connect/jdbc/reference/sqlservercallablestatement-class.md) クラスの setNString、setNCharacterStream、setNClob メソッドのような、JDBC 4.0 の新しい National Character メソッドでは、常にパラメーター値が Unicode 形式でサーバーに送信されます。  
  
一方で、[SQLServerPreparedStatement](reference/sqlserverpreparedstatement-class.md) クラスと [SQLServerCallableStatement](reference/sqlservercallablestatement-class.md) クラスの setString、setCharacterStream、setClob メソッドのような非 National Character の API メソッドでは、**sendStringParametersAsUnicode** プロパティが既定値である "true" に設定されているときにのみ、値がサーバーに Unicode で送信されます。  
  
## <a name="non-unicode-parameters"></a>Unicode 以外のパラメーター

Unicode 以外のパラメーターの **CHAR**、**VARCHAR** または **LONGVARCHAR** 型で最適なパフォーマンスを得るには、**sendStringParametersAsUnicode** 接続文字列プロパティを "false" に設定し、National Character メソッド以外のメソッドを使用します。  
  
## <a name="formatting-issues"></a>書式設定に関する問題

日付、時刻、および通貨の場合、ローカライズされたデータのすべての書式設定は、Locale オブジェクトや、**Date**、**Calendar**、**Number** データ型用のさまざまな書式設定メソッドを使用して、Java 言語レベルで実行されます。 JDBC ドライバーがロケールに依存するデータをローカライズされた書式で渡す必要があることはまれですが、このような場合は既定の JVM ロケールを使用して適切なフォーマッタが使用されます。  
  
## <a name="collation-support"></a>照合順序のサポート

JDBC Driver 3.0 では、[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] と [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] でサポートされるすべての照合順序に加え、新しい照合順序 ([!INCLUDE[ssKatmai](../../includes/sskatmai_md.md)] で導入された新バージョンの Windows 照合順序名) がサポートされます。  
  
照合順序の詳細については、「[照合順序と Unicode のサポート](/previous-versions/sql/sql-server-2008-r2/ms143503(v=sql.105))」と「[Windows 照合順序名 (Transact-SQL)](../../t-sql/statements/windows-collation-name-transact-sql.md)」を参照してください。  
  
## <a name="using-international-domain-names-idn"></a>国際ドメイン名 (IDN) の使用

SQL Server 用 JDBC Driver 6.0 は国際ドメイン名 (IDN) の使用に対応しており、接続で要求されるとき、Unicode serverName を ASCII 互換エンコーディング (Punycode) に変換できます。  IDN がドメイン名システム (DNS) に Punycode 形式 (仕様は RFC 3490 で既定) で ASCII 文字列として保存されている場合、serverNameAsACE プロパティを「true」に設定することで Unicode サーバー名を変換できます。  そのように保存されていなければ、DNS サービスが Unicode 文字を使用できるように構成されている場合、serverNameAsACE プロパティを既定の「false」に設定します。  以前のバージョンの JDBC ドライバーの場合、[Java の IDN.toASCII](https://docs.oracle.com/javase/8/docs/api/java/net/IDN.html) メソッドを使って serverName を Punycode に変換してから、そのプロパティを接続のために設定することもできます。  
  
> [!NOTE]  
> Windows プラットフォーム以外のために記述されたリゾルバー ソフトウェアの大半はインターネット DSN 標準に基づき、多くの場合、IDN に Punycode 形式を使用します。一方で、プライベート ネットワークの Windows ベースのDNS サーバーはサーバーごとに UTF-8 文字を使用できるように構成できます。  詳細については、「[Unicode 文字のサポート](/previous-versions/windows/it-pro/windows-server-2003/cc738403(v=ws.10))」を参照してください。  
  
## <a name="see-also"></a>関連項目

[JDBC ドライバーの概要](overview-of-the-jdbc-driver.md)  
