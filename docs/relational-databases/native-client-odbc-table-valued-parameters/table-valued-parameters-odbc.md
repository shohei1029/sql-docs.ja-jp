---
description: テーブル値パラメーター (ODBC)
title: Table-Valued パラメーター (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- table-valued parameters (ODBC)
- ODBC, table-valued parameters
ms.assetid: ef06cd13-18e2-4c65-8ede-c3955d820e54
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e7c561ef9b4ee7602bffe65f6a18c8a440970299
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755032"
---
# <a name="table-valued-parameters-odbc"></a>テーブル値パラメーター (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC のテーブル値パラメーターのサポートにより、クライアント アプリケーションは、1 回の呼び出しで複数の行をサーバーに送信することで、パラメーター化されたデータをサーバーに効率的に送信できます。  
  
 サーバー上のテーブル値パラメーターの詳細については、「 [データベースエンジン&#41;&#40;Table-Valued パラメーターの使用 ](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)」を参照してください。  
  
 ODBC でテーブル値パラメーターをサーバーに送信するには、次の 2 つの方法があります。  
  
-   SQLExecDirect または SQLExecute が呼び出されたときに、すべてのテーブル値パラメーターデータをメモリ内に配置できます。 テーブル値に複数の行がある場合、このデータを配列に格納します。  
  
-   アプリケーションでは、SQLExecDirect または SQLExecute が呼び出されたときに、テーブル値パラメーターの実行時データを指定できます。 この場合、テーブル値のデータの行をバッチ内で指定するか、必要なメモリ量を減らすために 1 つずつ指定することができます。  
  
 1 つ目の方法では、より多くのビジネス ロジックをストアド プロシージャにカプセル化できます。 たとえば、発注品目をテーブル値パラメーターとして渡す場合、注文入力のトランザクション全体を 1 つのストアド プロシージャにカプセル化することができます。 サーバーとのやり取りが 1 回で済むため、この方法は非常に効率的です。 また、異なるプロシージャを使用して、注文ヘッダーと発注品目を個別に処理することもできます。この場合、必要なコードが多くなり、クライアントとサーバー間のコントラクトが複雑になります。  
  
 2 つ目の方法は、膨大な量のデータを使用する一括操作のときに効率の良いメカニズムです。 この方法では、アプリケーションで最初にデータ行をメモリ内のバッファーに格納しなくても、データ行をサーバーにストリーム送信できます。  
  
 テーブル変数を作成するときに、制約と主キーを作成できます。 制約は、テーブル内のデータが特定の要件を満たしていることを確認するのに優れた方法です。  
  
## <a name="in-this-section"></a>このセクションの内容  
 [ODBC テーブル値パラメーターの使用](../../relational-databases/native-client-odbc-table-valued-parameters/uses-of-odbc-table-valued-parameters.md)  
 テーブル値パラメーターと ODBC の主なユーザー シナリオについて説明します。  
  
 [テーブル値パラメーター用の ODBC SQL 型](../../relational-databases/native-client-odbc-table-valued-parameters/odbc-sql-type-for-table-valued-parameters.md)  
 SQL_SS_TABLE 型について説明します。 この型は、テーブル値パラメーターをサポートする新しい ODBC SQL 型です。  
  
 [テーブル値パラメーターの記述子フィールド](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameter-descriptor-fields.md)  
 テーブル値パラメーターをサポートする記述子フィールドについて説明します。  
  
 [テーブル値パラメーターを構成する列の記述子フィールド](../../relational-databases/native-client-odbc-table-valued-parameters/descriptor-fields-for-table-valued-parameter-constituent-columns.md)  
 テーブル値パラメーターで意味を持つ記述子フィールドについて説明します。  
  
 [テーブル値パラメーターの診断レコードのフィールド](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameter-diagnostic-record-fields.md)  
 テーブル値パラメーターをサポートするために診断レコードに追加された 2 つの診断フィールドについて説明します。  
  
 [テーブル値パラメーターに影響を与えるステートメント属性](../../relational-databases/native-client-odbc-table-valued-parameters/statement-attributes-that-affect-table-valued-parameters.md)  
 テーブル値パラメーター列に対処できるようにする記述子の新しいヘッダー フィールドについて説明します。  
  
 [テーブル値パラメーターおよび列の値のバインドとデータ転送](../../relational-databases/native-client-odbc-table-valued-parameters/binding-and-data-transfer-of-table-valued-parameters-and-column-values.md)  
 パラメーター バインドと、テーブル値パラメーターをサーバーに渡す方法について説明します。  
  
 [準備されたステートメント用のテーブル値パラメーターのメタデータ](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameter-metadata-for-prepared-statements.md)  
 アプリケーションから、準備されたプロシージャ呼び出しのメタデータを取得する方法について説明します。  
  
 [テーブル値パラメーターの追加メタデータ](../../relational-databases/native-client-odbc-table-valued-parameters/additional-table-valued-parameter-metadata.md)  
 SQLProcedureColumns、SQLTables、および Sqltables を使用して、テーブル値パラメーターのメタデータを取得する方法について説明します。  
  
 [テーブル値パラメーターのデータ変換およびその他のエラーと警告](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameter-data-conversion-and-other-errors-and-warnings.md)  
 テーブル値パラメーターの列値に関するエラーを処理する方法について説明します。  
  
 [複数バージョン間の互換性](../../relational-databases/native-client-odbc-table-valued-parameters/cross-version-compatibility.md)  
 テーブル値パラメーターが、[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] より前のバージョンのクライアントまたはサーバーで使用されるときに発生する可能性がある競合について説明します。  
  
 [ODBC テーブル値パラメーター API の概要](../../relational-databases/native-client-odbc-table-valued-parameters/odbc-table-valued-parameter-api-summary.md)  
 テーブル値パラメーターをサポートする、ODBC 関数の一覧を示します。  

## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;ODBC&#41;](../../relational-databases/native-client/odbc/sql-server-native-client-odbc.md)   
 [テーブル値パラメーター &#40;SQL Server Native Client&#41;](../../relational-databases/native-client/features/table-valued-parameters-sql-server-native-client.md)  
  
