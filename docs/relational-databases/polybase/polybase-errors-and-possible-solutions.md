---
title: PolyBase のエラーと考えられる解決策
description: PolyBase のエラーと推奨される解決策のリファレンス。
ms.date: 03/22/2021
ms.prod: sql
ms.technology: polybase
ms.topic: conceptual
dev_langs:
- TSQL
- XML
f1_keywords:
- PolyBase, monitoring
- PolyBase, performance monitoring
helpviewer_keywords:
- PolyBase, troubleshooting
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
monikerRange: '>= sql-server-linux-ver15 || >= sql-server-2016'
ms.openlocfilehash: 46f56288382c1b1928e6ad3081a8eea41e23bb5f
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833821"
---
# <a name="polybase-errors-and-possible-solutions"></a>PolyBase のエラーと考えられる解決策

[!INCLUDE[sqlserver](../../includes/applies-to-version/sqlserver.md)]

この記事では、PolyBase の一般的なエラー シナリオと解決策について説明します。

PolyBase の監視とトラブルシューティングの詳細については、「[PolyBase の監視とトラブルシューティング](polybase-troubleshooting.md)」を参照してください。

Windows と Linux での PolyBase のログ ファイルの一般的な場所については、「[PolyBase の監視とトラブルシューティング](polybase-troubleshooting.md#log-file-locations)」を参照してください。


## <a name="error-messages-and-possible-solutions"></a>エラー メッセージと考えられる解決策

### <a name="error-100001failed-to-generate-query-plan"></a>エラー: "100001;クエリ プランを生成できませんでした"

"クエリ プランを生成できませんでした" というエラーは、SQL Server データベース エンジンには累積的な更新プログラム 8 (15.0.4073) 以降の修正プログラムが適用されているが、PolyBase 機能が同じビルドに更新されていない場合に発生する可能性があります。 これは、PolyBase 機能を既存の SQL Server インスタンスに追加するときに発生する可能性があります。 詳細については、「[PolyBase error - 100001;Failed to generate query plan](https://techcommunity.microsoft.com/t5/sql-server-support/polybase-error-100001-failed-to-generate-query-plan/ba-p/2174693)」 (PolyBase エラー - 100001;クエリ プランを生成できませんでした) を参照してください。

常に、新しい機能を同じバージョン レベルにすることにより、PolyBase 機能のインストールを維持します。 必要に応じて、Service Pack (SP)、累積的な更新プログラム (CU)、一般配布リリース (GDR) をインストールします。 PolyBase のバージョンを確認するには、「[SQL Server とそのコンポーネントのバージョン、エディション、更新レベルを確認する](/troubleshoot/sql/general/determine-version-edition-update-level#polybase)」を参照してください。

### <a name="service-account-change"></a>サービス アカウントの変更

サンプルのエラー メッセージ:

> 107035;[DOMAIN\user] はグループ [PdwDataMovementAccess] のメンバーではないため、DMS の承認に失敗しました <BR>
> 107017;無効な DMS 制御ヘッダー:

このエラーは、PolyBase サービス アカウントが変更されたことが原因である可能性があります。 PolyBase エンジンと PolyBase Data Movement サービスのサービス アカウントを変更するには、PolyBase 機能をアンインストールし、再インストールします。


### <a name="data-movement-service-permissions-errors"></a>データ移動サービスのアクセス許可エラー

データ移動サービスでのアクセス許可に関する問題のトラブルシューティングと解決の詳細については、「[PolyBase サービス アカウントのアクセス許可と、それがないときに発生する一般的なエラー](https://techcommunity.microsoft.com/t5/sql-server-support/polybase-service-account-permissions-and-common-errors-observed/ba-p/2112711)」を参照してください。


### <a name="windows-authentication-failure"></a>Windows 認証エラー

Windows 認証でのエラーに関係のあるアクセス許可の問題のトラブルシューティングと解決の詳細については、「[PolyBase サービス アカウントのアクセス許可と、それがないときに発生する一般的なエラー](https://techcommunity.microsoft.com/t5/sql-server-support/polybase-service-account-permissions-and-common-errors-observed/ba-p/2112711)」を参照してください。


### <a name="cannot-execute-the-query-remote-query"></a>クエリ "Remote Query" を実行できない 

サンプルのエラー メッセージ:

> メッセージ 7320、レベル 16、状態 110、行 14<BR>
> リンク サーバー "(null)" の OLE DB プロバイダー "SQLNCLI11" に対するクエリ "Remote Query" を実行できません。 クエリは中止しました-- 外部ソースからの読み取り中に却下のしきい値の上限 (0 行) に達しました: 合計処理行数 1 行中、1 行が却下されました。
(/nation/sensors.ldjson.txt)Column ordinal: 0, Expected data type: INT, Offending value: {"id":"S2740036465E2B","time":"2016-02-26T16:59:02.9300000Z","temp":23.3,"hum":0.77,"wind":17,"press":1032,"loc":[-76.90914996169623,38.8929314364726]} (列変換エラー), エラー: データ型 NVARCHAR を INT に変換中にエラーが発生しました。

このエラーの派生が発生している可能性があることに注意してください。 最初に却下されたファイルの名前は、問題のあるデータ型または値と共に SQL Server Management Studio (SSMS) に表示されます。

**考えられる理由:**  
このエラーが発生する原因は、各ファイルのスキーマが異なるためです。 PolyBase の外部テーブル DDL でディレクトリがポイントされていると、そのディレクトリ内のすべてのファイルが再帰的に読み取られます。 列またはデータ型の不一致が発生した場合、このエラーが SSMS に表示される可能性があります。

**考えられる解決策:**  
各テーブルのデータが 1 つのファイルで構成されている場合は、外部ファイルのディレクトリを前に付けたファイル名を LOCATION セクションで使用します。 1 つのテーブルに複数のファイルがある場合は、各ファイルのセットを Azure Blob Storage 内の異なるディレクトリに配置します。 LOCATION で、特定のファイルではなくディレクトリを指定します。 この解決策は推奨されません。

**例:**  

```sqlsyntax
Create External Table foo
(col1 int)WITH (LOCATION='/bar/foobar.txt',DATA_SOURCE…); OR
Create External Table foo
(col1 int) WITH (LOCATION = '/bar/', DATA_SOURCE…);
```


### <a name="kerberos-support"></a>Kerberos のサポート

SQL Server は、サポートされている Hadoop クラスターにアクセスするように構成されています。 Kerberos のセキュリティは、Hadoop クラスターには適用されません。

外部テーブルから選択すると、次のエラーが返されます。

> メッセージ 105019、レベル 16、状態 1、行 55<BR>
> 内部エラー: 'Java exception raised on call to HdfsBridge_Connect: Error [Unable to instantiate LoginClass] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました<BR>
> メッセージ 7320、レベル 16、状態 110、行 55<BR>
> リンク サーバー "(null)" の OLE DB プロバイダー "SQLNCLI11" に対するクエリ "Remote Query" を実行できません。 内部エラー: 'Java exception raised on call to HdfsBridge_Connect: Error [Unable to instantiate LoginClass] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました

DWEngine サーバー ログの質問には、次のエラーが表示されます。

> [スレッド:16432] [EngineInstrumentation:EngineQueryErrorEvent] (エラー、高):<BR>
> 内部エラー: 'Java exception raised on call to HdfsBridge_Connect: Error [com.microsoft.polybase.client.KerberosSecureLogin] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました
Microsoft.SqlServer.DataWarehouse.Common.ErrorHandling.MppSqlException: 内部エラー: 'Java exception raised on call to HdfsBridge_Connect: Error [com.microsoft.polybase.client.KerberosSecureLogin] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました ---> Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException: Java exception raised on call to HdfsBridge_Connect: Error [com.microsoft.polybase.client.KerberosSecureLogin] occurred while accessing external file. (HdfsBridge_Connect の呼び出しで Java の例外が発生しました: 外部ファイルにアクセスしている間に、エラー [com.microsoft.polybase.client.KerberosSecureLogin] が発生しました。)

**考えられる理由:**  
Hadoop クラスターで Kerberos が有効になっていませんが、Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\Binn\Polybase\Hadoop\conf の下に既定で存在する core-site.xml、yarn-site.xml、または hdfs-site.xml で Kerberos セキュリティが有効になっています。 Linux でのファイルの既定の場所は /var/opt/mssql/binn/polybase/hadoop/conf/ です。

**考えられる解決策:**  
上記のファイルに記述されている Kerberos のセキュリティ情報をコメントにします。

PolyBase と Kerberos のトラブルシューティングの詳細については、「[PolyBase Kerberos の接続性のトラブルシューティング](polybase-troubleshoot-connectivity.md)」を参照してください。

### <a name="internal-query-processor-error"></a>内部クエリ プロセッサ エラー

外部テーブルのクエリを実行すると、次のエラーが返されます。

> メッセージ 8680、レベル 17、状態 5、行 118<BR>
> 内部クエリ プロセッサ エラー:リモート クエリ フェーズを処理中に、クエリ プロセッサで予期しないエラーが発生しました。

DWEngine のサーバー ログには、次のメッセージが含まれています。

> [Thread:5216] [ControlNodeMessenger:ErrorEvent] (エラー、高): ***** DMS System has disconnected nodes : (DMS システムによってノードが切断されました)<BR>
> [Thread:5216] [ControlNodeMessenger:ErrorEvent] (エラー、高): ***** DMS System has disconnected nodes : (DMS システムによってノードが切断されました)<BR>
> [Thread:5216] [ControlNodeMessenger:ErrorEvent] (エラー、高): ***** DMS System has disconnected nodes : (DMS システムによってノードが切断されました)<BR>

**考えられる理由:**  
このエラーの原因は、PolyBase の構成後に SQL Server が再起動されなかったことである可能性があります。

**考えられる解決策:**  
SQL Server を再起動してください。 DWEngine のサーバー ログを調べて、再起動後に DMS の切断がないことを確認します。


### <a name="user-needed-for-hdfs-access"></a>HDFS アクセスに必要なユーザー

**シナリオ:**  
SQL Server は、セキュリティで保護されていない Hadoop クラスターに接続されています (Kerberos が有効になっていません)。 PolyBase は、計算を Hadoop クラスターにプッシュするように構成されています。

**サンプル クエリ:**  

```sql
select count(*) from foo WITH (FORCE EXTERNALPUSHDOWN);
```

次のようなエラー メッセージが返されます。 

> メッセージ 105019、レベル 16、状態 1、行 1<BR>
> 内部エラー: 'Java exception raised on call to JobSubmitter_PollJobStatus: Error [java.net.ConnectException: Call From big1506sql2016/172.16.1.4 to 0.0.0.0:10020 failed on connection exception: java.net ConnectException: Connection refused: no further information; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused ] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました<BR>
> リンク サーバー "(null)" の OLE DB プロバイダー "SQLNCLI11" から、メッセージ "特定できないエラー" が返されました。<BR>
> メッセージ 7421、レベル 16、状態 2、行 1<BR>
> リンク サーバー "(null)" の OLE DB プロバイダー "SQLNCLI11" から行セットをフェッチできません。 .<BR>

Hadoop Yarn ログ エラー:
> Job setup failed : org.apache.hadoop.security.AccessControlException: Permission denied: user=pdw_user, access=WRITE, inode="/user":hdfs:hdfs:drwxr-xr-x at org.apache.hadoop.hdfs.server.namenode.FSPermissionChecker.checkFsPermission(FSPermissionChecker.java:265) at org.apache.hadoop.hdfs.server.namenode.FSPermissionChecker.check(FSPermissionChecker.java:251) at org.apache.hadoop.hdfs.server.namenode.FSPermissionChecker.check(FSPermissionChecker.java:232) org.apache.hadoop.hdfs.server.namenode.FSPermissionChecker.checkPermission(FSPermissionChecker.java:176) at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkPermission(FSNamesystem.java:5525)

**考えられる理由:**  
Kerberos を無効にすると、HDFS にアクセスして MapReduce ジョブを送信するためのユーザーとして pdw_user が PolyBase により使用されます。

**考えられる解決策:**  
Hadoop で pdw_user を作成し、MapReduce 処理の間に使用されるディレクトリに対する十分なアクセス許可を付与します。 また、pdw_user が /user/pdw_user HDFS ディレクトリの所有者であることも確認します。

ホーム ディレクトリを作成し、pdw_user にアクセス許可を割り当てる方法の例を次に示します。

```console
sudo -u hdfs hadoop fs -mkdir /user/pdw_user
sudo -u hdfs hadoop fs -chown pdw_user /user/pdw_user
```

その後、pdw_user に、/user/pdw_user ディレクトリでの読み取り、書き込み、実行のアクセス許可があることを確認します。 /tmp ディレクトリに 777 アクセス許可があることを確認します。

PolyBase と Kerberos のトラブルシューティングの詳細については、「[PolyBase Kerberos の接続性のトラブルシューティング](polybase-troubleshoot-connectivity.md)」を参照してください。

### <a name="java-memory-error-due-to-utf-8"></a>UTF-8 による Java メモリ エラー

**シナリオ:**  
SQL Server の PolyBase は、Hadoop クラスターまたは Azure Blob Storage を使用してセットアップされています。 Select クエリは、次のエラーで失敗します。

> メッセージ 106000、レベル 16、状態 1、行 1<BR>
> Java ヒープ スペース<BR>

**考えられる理由:**  
入力が正しくないと、Java のメモリ不足エラーが発生する可能性があります。 ファイルが UTF-8 形式ではない可能性があります。 DMS では、行区切り記号をデコードできず、Java のヒープ領域エラーが発生するため、ファイル全体を 1 つの行として読み取りが試みられます。

**考えられる解決策:**  
現在、PolyBase ではテキスト区切りファイルに UTF-8 形式が必要であるため、ファイルを UTF-8 形式に変換します。

### <a name="hadoop-connectivity-configuration"></a>Hadoop の接続の構成

Azure Blob Storage に接続するように SQL Server の PolyBase を構成すると、SQL Server で次のエラー メッセージが返されます。

> メッセージ 105019、レベル 16、状態 1、行 74<BR>
> 内部エラー: 'Java exception raised on call to HdfsBridge_Connect: Error [No FileSystem for scheme: wasbs] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました<BR>

**考えられる理由:**  
Hadoop の接続が、Azure Blob Storage にアクセスするための構成値に設定されていません。

**考えられる解決策:**  
Azure Blob Storage をサポートする値 (7 を推奨) に Hadoop の接続を設定して、SQL Server を再起動します。 接続の値とサポートされる種類の一覧については、「[PolyBase 接続構成](../../database-engine/configure-windows/polybase-connectivity-configuration-transact-sql.md#arguments)」を参照してください。


### <a name="create-table-as-select-error"></a>CREATE TABLE AS SELECT のエラー

**シナリオ:**  
SQL Server から PolyBase と CREATE EXTERNAL TABLE AS SELECT (CETAS) 構文を使用して Azure Blob Storage または Hadoop ファイル システムにデータをエクスポートしようとすると、次のエラー メッセージで失敗します。

> メッセージ 156、レベル 15、状態 1、行 177<BR>
> キーワード 'WITH' 付近に不適切な構文があります。<BR>
> メッセージ 319、レベル 15、状態 1、行 177<BR>
> キーワード 'with' 付近に不適切な構文があります。 このステートメントが共通テーブル式、xmlnamespaces 句、または変更追跡コンテキストの句の場合は、前のステートメントをセミコロンで終了してください。<BR>

**考えられる理由:**  
データを PolyBase 経由で Hadoop または Azure Blob Storage にエクスポートする場合、データだけがエクスポートされ、CREATE EXTERNAL TABLE コマンドで定義した列名 (メタデータ) はエクスポートされません。

**考えられる解決策:**  
最初に外部テーブルを作成した後、INSERT INTO SELECT を使用して、外部の場所にエクスポートします。 コード サンプルについては、「[PolyBase クエリのシナリオ](polybase-queries.md#export-data)」を参照してください。


### <a name="create-external-table-from-azure-blob-storage-fails"></a>Azure Blob Storage からの外部テーブルの作成が失敗する

**シナリオ:**  
SQL DW は、Azure Blob Storage からデータをインポートするようにセットアップされています。 外部テーブルを作成すると、次のメッセージで失敗します。

> メッセージ 105019、レベル 16、状態 1、行 34<BR>
> 内部エラー: 'Java exception raised on call to HdfsBridge_IsDirExist. Java exception message:com.microsoft.azure.storage.StorageException: Server failed to authenticate the request. Make sure the value of Authorization header is formed correctly including the signature.: Error [com.microsoft.azure.storage.StorageException: Server failed to authenticate the request. Make sure the value of Authorization header is formed correctly including the signature.] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました<BR>

**考えられる理由:**  
データベース スコープの資格情報の作成に使用された Azure ストレージ キーが正しくありませんでした。

**考えられる解決策:**  
関連するオブジェクト (データ ソース、ファイル形式など) をすべて削除してから、データベース スコープの資格情報を削除し、適切なストレージ キーを使用して再作成します。


### <a name="kerberos-configuration-capitalization"></a>Kerberos の構成での大文字の使用

**シナリオ:**  
SQL Server は、Kerberos が有効な Cloudera クラスターを使用してセットアップされています。 すべての構成を変更した後、SQL Server を再起動しました。 再起動後に PolyBase エンジンと PolyBase Data Movement サービスが実行されています。 次のエラー メッセージが返されます。

ジョブ トラッカーの場所を指定されずに構成されたデータ ソース:  
> org.apache.hadoop.fs.FileSystem: プロバイダー org.apache.hadoop.fs.viewfs.ViewFileSystem をインスタンス化できませんでした

ジョブ トラッカーの場所を指定されて構成されたデータ ソース:  
> Error [Can't get Kerberos realm] occurred while accessing external file (外部ファイルへのアクセス中にエラー [Kerberos 領域を取得できません] が発生しました)

**考えられる理由:**  
Coresite.xml で "hadoop.security.authentication" プロパティの値が kerberos と指定されています。

**考えられる解決策:**  
Coresite.xml の "hadoop.security.authentication" プロパティの値は、KERBEROS (すべて大文字) にする必要があります。 

PolyBase と Kerberos のトラブルシューティングの詳細については、「[PolyBase Kerberos の接続性のトラブルシューティング](polybase-troubleshoot-connectivity.md)」を参照してください。

### <a name="mapred-sitexml-missing-needed-values"></a>Mapred-site.xml に必要な値がない

**シナリオ:**  
SQL Server または APS は、サポートされている HDP クラスターでセットアップされています。 プッシュダウンを必要としないクエリは動作しますが、"FORCE PUSHDOWN" ヒントを使用すると、次のエラー メッセージで失敗します。

> メッセージ 7320、レベル 16、状態 110、行 35<BR>
> リンク サーバー "(null)" の OLE DB プロバイダー "SQLNCLI11" に対するクエリ "Remote Query" を実行できません。 内部エラー: 'Java exception raised on call to JobSubmitter_PollJobStatus: Error [org.apache.hadoop.ipc.RemoteException(java.lang.NullPointerException): java.lang.NullPointerException<BR>
> at org.apache.hadoop.mapreduce.v2.hs.HistoryClientService$HSClientProtocolHandler.getTaskAttemptCompletionEvents(HistoryClientService.java:277)<BR>
> at org.apache.hadoop.mapreduce.v2.api.impl.pb.service.MRClientProtocolPBServiceImpl.getTaskAttemptCompletionEvents(MRClientProtocolPBServiceImpl.java:173)<BR>
> at org.apache.hadoop.yarn.proto.MRClientProtocol$MRClientProtocolService$2.callBlockingMethod(MRClientProtocol.java:283)<BR>
> at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:619)<BR>
> at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:962)<BR>
> at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2127)<BR>
> at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2123)<BR>
> at java.security.AccessController.doPrivileged(Native Method)<BR>
> at javax.security.auth.Subject.doAs(Subject.java:415)<BR>
> at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1671)<BR>
> at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2121)<BR>
> ] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました<BR>

**考えられる理由:**  
Mapred-site.xml に、中間結果と終了結果を確認するために必要な値がありません。

**考えられる解決策:**  
次のプロパティを追加し、SQL Server の mapred-site.xml ファイルで Ambari について示されている正しい値を関連付けます。 

```xml
<property>
<name>yarn.app.mapreduce.am.staging-dir</name>
<value>/user</value>
</property>
<property>
<name>mapreduce.jobhistory.done-dir</name>
<value>/mr-history/done</value>
</property>
<property>
<name>mapreduce.jobhistory.intermediate-done-dir</name>
<value>/mr-history/tmp</value>
</property>
```

### <a name="configuring-access-by-hostname"></a>ホスト名によるアクセスの構成 

**シナリオ:**  
SQL Server は、サポートされている Hadoop クラスターにアクセスするようにセットアップされています。 外部テーブルを作成すると、次のいずれかのエラーが返されます。

> リンク サーバー "(null)" の OLE DB プロバイダー "SQLNCLI11" に対するクエリ "Remote Query" を実行できません。 110802;内部 DMS エラーが発生し、この操作は失敗しました。 詳細: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DmsSqlNativeException, Message: SqlNativeBufferReader.Run, error in OdbcExecuteQuery: SqlState: 42000, NativeError: 8680, 'Error calling: SQLExecDirect(this->GetHstmt(), (SQLWCHAR *)statementText, SQL_NTS), SQL return code: -1 | SQL Error Info: SrvrMsgState: 26, SrvrSeverity: 17,  Error <1>: ErrorMsg: [Microsoft][ODBC Driver 13 for SQL Server][SQL Server]クエリ プロセッサの内部エラー: リモート クエリ フェーズの処理中にクエリ プロセッサで予期しないエラーが発生しました。 | Error calling: pReadConn->ExecuteQuery(statementText, bufferFormat) | state: FFFF, number: 24, active connections: 8', Connection String: Driver={pdwodbc};APP=RCSmall-DmsNativeReader:WAD1D16HD2001\mpdwsvc (3600)-ODBC-PoolId1433;Trusted_Connection=yes;AutoTranslate=no;Server=\\.\pipe\sql\query<BR>

> [Thread:30544] [AbstractReaderWorker:ErrorEvent] (Error, High): QueryId QID2433 PlanId 6c3a4551-e54c-4c06-a5ed-a8733edac691 StepId 7:<BR>
> Could not obtain block: BP-1726738607-192.168.225.121-1443123675290:blk_1159687047_86196509 file=/user/hive/warehouse/u_data/000000_0<BR>
> Microsoft.SqlServer.DataWarehouse.Common.ErrorHandling.MppSqlException: Could not obtain block: BP-1726738607-192.168.225.121-1443123675290:blk_1159687047_86196509 file=/user/hive/warehouse/u_data/000000_0<BR>
> at Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsBridgeReadAccess.Read(MemoryBuffer buffer, Boolean& isDone)<BR>
> at Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DataReader.ExternalMoveBufferReader.Read()<BR>
> at Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.ExternalMoveReaderWorker.ReadAndSendData()<BR>
> at Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.ExternalMoveReaderWorker.Execute(Object status)<BR>

**考えられる理由:**  
このエラー メッセージは、データ ノードが IP アドレスではなくホスト名を使用することによってのみクラスターの外部からアクセスできる構成で、Hadoop クラスターがセットアップされている場合に、表示されることがあります。

**考えられる解決策:**  
クライアント (SQL Server) 側の hdfs-site.xml ファイルに以下を追加します。 この構成により、名前ノードは、内部 IP アドレスではなくホスト名でデータ ノードの URI を返すように強制されます。

```xml
<property>
<name>dfs.client.use.datanode.hostname</name>
<value>true</value>
</property>
```

### <a name="folder-organization-forces-excess-memory-overhead"></a>フォルダーの編成により過剰なメモリのオーバーヘッドが強制される

**シナリオ:**  
多数のファイル (ディレクトリ パスの下に再帰的に 30,000 万個を超えるファイル) が含まれるディレクトリに対する PolyBase クエリが SQL Server で実行されていて、次のいずれかのエラー メッセージが返されます。

> メッセージ 105019、レベル 16、状態 1、行 1<BR>
> 内部エラー: 'Java exception raised on call to HdfsBridge_GetFileNameByIndex. Java exception message: GC overhead limit exceeded: Error [GC overhead limit exceeded] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました<BR>

> メッセージ 105019、レベル 16、状態 1、行 1<BR>
> 内部エラー: 'Java exception raised on call to HdfsBridge_GetDirectoryFiles. Java exception message: Java heap space: Error [Java heap space] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました

**考えられる理由:**  
パスを処理するとき、PolyBase によりそのパスの下にあるすべてのファイルが列挙され、ファイルを表すために使用されるデータ構造に関連する固定のメモリ オーバーヘッドがあります。 ファイルの数が多いと、このオーバーヘッドが顕著になり、最終的に JVM で使用可能なすべてのメモリが消費される可能性があります。

**考えられる解決策:**  
データを複数のディレクトリに再配置して、各ディレクトリにファイルのサブセットが格納されるようにします。その後、クエリを複数に分割し、それぞれによって一度に元のパスの一部が処理され、テーブルが SQL Server テーブルとして具体化されるようにします (結合前)。

例: 外部テーブルのデータが次の場所にあるとします: Orders/file1.txt,...,file30K.txt。

レイアウトを変更して、データが Orders/*yyyy*/*mm*/*dd*/file1.txt という通常のファイル パーティション構造に配置されるようにします。
外部テーブルで月 (mm) や日 (dd) などの下位ディレクトリ パスを参照し、ファイルを SQL Server テーブルに個別にインポートした後、1 つのテーブルの一部として追加します。
最初に適切なディレクトリ構造を使用していたとしても、ステップ #2 に従うと、JVM メモリを使い切ることなく多数のファイルを処理できます。


### <a name="unexpected-characters-in-configuration-files"></a>構成ファイルに予期しない文字がある

**シナリオ:**  
yarn-site.xml、hdfs-site.xml、および他の構成ファイルの変更を伴う Hadoop クラスターを使用して、SQL Server または APS を設定します。 次の SQL Server エラー メッセージが表示されます。

> メッセージ 105019、レベル 16、状態 1、行 1<BR>
> Microsoft.SqlServer.DataWarehouse.Common.ErrorHandling.MppSqlException: 内部エラー: 'Java exception raised on call to HdfsBridge_Connect. Java exception message:com.sun.org.apache.xerces.internal.impl.io.MalformedByteSequenceException: Invalid byte 1 of 1-byte UTF-8 sequence.: Error [com.sun.org.apache.xerces.internal.impl.io.MalformedByteSequenceException: Invalid byte 1 of 1-byte UTF-8 sequence.] occurred while accessing external file.' のため、EXTERNAL TABLE のアクセスは失敗しました。 ---><BR>

**考えられる理由:**  
これは、Web サイトまたはチャット ウィンドウからテキストをコピーして構成ファイルに貼り付けた場合に、発生する可能性があります。 望ましくない文字または印刷できない文字が、構成ファイルに含まれている可能性があります。

**考えられる解決策:**  
別のテキスト エディター (メモ帳以外) でファイルを開き、これらの文字を探して削除します。 必要なサービスを再起動します。 


## <a name="see-also"></a>関連項目

[PolyBase の監視とトラブルシューティング](polybase-troubleshooting.md)  
[PolyBase Kerberos の接続性のトラブルシューティング](polybase-troubleshoot-connectivity.md)  

