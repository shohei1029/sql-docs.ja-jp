---
description: JDBC Driver for SQL Server でのデータベース ミラーリングの使用について、およびフェールオーバーが発生したときに考慮する必要があることについて説明します。
title: データベース ミラーリングの使用 (JDBC)
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 4ff59218-0d3b-4274-b647-9839c4955865
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 369c31309ab439bb3de5362c42c6563b99130b66
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104793042"
---
# <a name="using-database-mirroring-jdbc"></a>データベース ミラーリングの使用 (JDBC)

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

データベース ミラーリングは、主にデータベースの可用性とデータの冗長性を向上するためのソフトウェア ソリューションです。 [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] は、データベース ミラーリングを暗黙的にサポートするため、データベース用に構成されていれば、開発者がコードを記述したり、その他の操作を行ったりする必要はありません。

データベース ミラーリングは、データベースごとに実装され、スタンバイ サーバー上に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 運用データベースのコピーを保持します。 このサーバーは、データベース ミラーリング セッションの構成および状態に応じて、ホット スタンバイ サーバーかウォーム スタンバイ サーバーのいずれかになります。 ホット スタンバイ サーバーは、コミット済みのトランザクションを失うことなく、高速のフェールオーバーをサポートします。 ウォーム スタンバイ サーバーは、サービスの強制 (データ損失の可能性あり) をサポートします。

運用データベースは _プリンシパル_ データベースと呼ばれ、スタンバイ コピーは _ミラー_ データベースと呼ばれます。 プリンシパル データベースとミラー データベースは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の別々のインスタンス (サーバー インスタンス) 上にある必要があります。 可能であれば、別のコンピューターに配置します。

実稼働サーバー インスタンス (プリンシパル サーバー) は、スタンバイ サーバー (ミラー サーバー) と通信します。 プリンシパル サーバーとミラー サーバーは、データベース ミラーリング セッション内でパートナーとして機能します。 プリンシパル サーバーで障害が発生した場合、ミラー サーバーは "_フェールオーバー_" と呼ばれるプロセスを通じて、そのデータベースをプリンシパル データベースにできます。 たとえば、Partner_A と Partner_B がパートナー サーバーで、初期時点ではプリンシパル データベースがプリンシパル サーバーである Partner_A にあり、ミラー データベースがミラー サーバーである Partner_B にあるとします。 Partner_A がオフラインになった場合、Partner_B がフェールオーバーして現在のプリンシパル データベースになることができます。 Partner_A がミラー化セッションに再び参加すると、このサーバーがミラー サーバーになり、このサーバーのデータベースがミラー データベースになります。

Partner_A サーバーが破損して修復不可能な場合は、Partner_C サーバーをオンラインにして、プリンシパル サーバーとなった Partner_B のミラー サーバーとして機能させることができます。 ただし、このシナリオでは、データベース ミラーリング構成で使用される新しいサーバー名で接続文字列プロパティが更新されるように、クライアント アプリケーションにプログラミング ロジックを含める必要があります。 含めない場合、サーバーへの接続に失敗する可能性があります。

代替データベース ミラーリング構成は、さまざまなレベルのパフォーマンスとデータの安全性を提供し、さまざまな形態のフェールオーバーをサポートします。 詳細については、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] オンライン ブックの「データベース ミラーリングの概要」を参照してください。

## <a name="programming-considerations"></a>プログラミングの考慮事項

プリンシパル データベース サーバーに障害が発生した場合、クライアント アプリケーションの API 呼び出しの応答がエラーになり、データベースへの接続が失われたことが伝えられます。 これらのエラーが発生すると、データベースに対するコミットされていない変更はすべて失われ、現在のトランザクションはロールバックされます。 このシナリオでは、アプリケーションで接続を閉じて (または、データ ソース オブジェクトを解放して) から、再び開く必要があります。 接続時に、新しい接続はプリンシパル サーバーとなったミラー データベースに透過的にリダイレクトされます。クライアントが接続文字列またはデータ ソース オブジェクトを変更する必要はありません。

接続が最初に確立されると、プリンシパル サーバーはフェールオーバー パートナーの ID を、フェールオーバー発生時に使用されるクライアントに送信します。 アプリケーションが障害の発生したプリンシパル サーバーと最初の接続を確立しようとするとき、フェールオーバー パートナーの ID はクライアントに通知されていません。 クライアントがこのシナリオに対処できるように、failoverPartner 接続文字列プロパティ、およびオプションで [setFailoverPartner](../../connect/jdbc/reference/setfailoverpartner-method-sqlserverdatasource.md) データ ソース メソッドを使用することで、クライアントはフェールオーバー パートナーの ID を独自に指定できます。 クライアント プロパティはこのシナリオでのみ使用されます。プリンシパル サーバーが利用可能な場合は使用されません。

> [!NOTE]
> 接続文字列またはデータ ソース オブジェクトで failoverPartner が指定されている場合は、databaseName プロパティも設定する必要があります。これが設定されていないと、例外がスローされます。 failoverPartner および databaseName が明示的に指定されていないと、プリンシパル データベース サーバーに障害が発生した場合に、アプリケーションがフェールオーバーを実行しません。 つまり、透過的なリダイレクトは、failoverPartner および databaseName が明示的に指定された接続に対してのみ機能します。 failoverPartner およびその他の接続文字列プロパティの詳細については、「[接続プロパティの設定](../../connect/jdbc/setting-the-connection-properties.md)」を参照してください。

クライアントで指定されたフェールオーバー パートナー サーバーが、指定されたデータベースのフェールオーバー パートナーの役割を担うサーバーを参照しておらず、かつ参照先のサーバー/データベースがミラー化機構に属している場合、接続はサーバーによって拒否されます。 [SQLServerDataSource](../../connect/jdbc/reference/sqlserverdatasource-class.md) クラスは [getFailoverPartner](../../connect/jdbc/reference/getfailoverpartner-method-sqlserverdatasource.md) メソッドを提供しますが、このメソッドは接続文字列または setFailoverPartner メソッドで指定されたフェールオーバー パートナーの名前のみを返します。 現在使用されている実際のフェールオーバー パートナーの名前を取得するには、次の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを使用します。

```sql
SELECT m.mirroring_role_DESC, m.mirroring_state_DESC,
m.mirroring_partner_instance FROM sys.databases as db,
sys.database_mirroring AS m WHERE db.name = 'MirroringDBName'
AND db.database_id = m.database_id
```

> [!NOTE]
> ミラーリング データベースの名前を使用するには、このステートメントを変更する必要があります。

接続の最初の試行が失敗した場合に備えて、接続文字列を更新するか、または再試行の戦略を立てるために、パートナー情報をキャッシュすることを検討してください。

## <a name="example"></a>例

次の例では、最初にプリンシパル サーバーへの接続が試行されます。 接続に失敗して例外がスローされた場合、ミラー サーバーへの接続が試行されます。ミラー サーバーは新しいプリンシパル サーバーに昇格している可能性があります。 接続文字列での failoverPartner プロパティの使用に注意してください。

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class ClientFailover {
    public static void main(String[] args) {

        String connectionUrl = "jdbc:sqlserver://serverA:1433;"
                + "databaseName=AdventureWorks;integratedSecurity=true;"
                + "failoverPartner=serverB";

        // Establish the connection to the principal server.
        try (Connection con = DriverManager.getConnection(connectionUrl);
                Statement stmt = con.createStatement();) {
            System.out.println("Connected to the principal server.");

            // Note that if a failover of serverA occurs here, then an
            // exception will be thrown and the failover partner will
            // be used in the first catch block below.

            // Execute a SQL statement that inserts some data.

            // Note that the following statement assumes that the
            // TestTable table has been created in the AdventureWorks
            // sample database.
            stmt.executeUpdate("INSERT INTO TestTable (Col2, Col3) VALUES ('a', 10)");
        }
        catch (SQLException se) {
            System.out.println("Connection to principal server failed, " + "trying the mirror server.");
            // The connection to the principal server failed,
            // try the mirror server which may now be the new
            // principal server.
            try (Connection con = DriverManager.getConnection(connectionUrl);
                    Statement stmt = con.createStatement();) {
                System.out.println("Connected to the new principal server.");
                stmt.executeUpdate("INSERT INTO TestTable (Col2, Col3) VALUES ('a', 10)");
            }
            // Handle any errors that may have occurred.
            catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## <a name="see-also"></a>関連項目

[JDBC ドライバーによる SQL Server への接続](../../connect/jdbc/connecting-to-sql-server-with-the-jdbc-driver.md)
