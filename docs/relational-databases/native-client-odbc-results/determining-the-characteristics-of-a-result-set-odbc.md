---
description: 結果セットの特性の決定 (ODBC)
title: 結果セットの特性 (ODBC)
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- result sets [ODBC], characteristics
- SQL Server Native Client ODBC driver, result sets
- ODBC applications, result sets
- SQLDescribeCol function
- metadata [ODBC]
- SQLColAttribute function
- SQLNumResultCols function
ms.assetid: 90be414c-04b3-46c0-906b-ae7537989b7d
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 56dc41dc4f92af9e7be22a148b29f6540f517237
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752812"
---
# <a name="determining-the-characteristics-of-a-result-set-odbc"></a>結果セットの特性の決定 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  メタデータは、他のデータを説明するデータです。 たとえば、結果セットのメタデータは、結果セットに含まれる列数、これらの列のデータ型、名前、有効桁数、NULL 値の許容属性など、結果セットの特性を説明します。  
  
 ODBC は、ODBC のカタログ API 関数を使用してアプリケーションにメタデータを渡します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT odbc ドライバーでは、対応するカタログプロシージャへの呼び出しとして、多くの ODBC API カタログ関数が実装されて [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] います。  
  
 アプリケーションは、ほとんどの結果セット操作にメタデータを必要とします。 たとえば、列のデータ型を使用して、列にバインドされている変数の種類を判断します。 または、文字型の列のバイト長を使用して、その列のデータの表示に必要な領域サイズを判断します。 アプリケーションが列のメタデータを判断する方法は、アプリケーションの種類によって異なります。  
  
 業種特有のアプリケーションでは、通常、事前定義されたテーブルを使用して、これらのテーブルに対して事前に定義された操作を実行します。 このようなアプリケーション用の結果セットのメタデータは、アプリケーションが作成される前に既に定義され、開発者が制御するので、アプリケーションにハードコーディングできます。 たとえば、データ ソース内で受注 ID 列が 4 バイト integer 型で定義されている場合、アプリケーションは常に 4 バイト integer をこの列にバインドできます。 メタデータがアプリケーション内でハードコーディングされている場合、アプリケーションが使用するテーブルへの変更は、通常、アプリケーション コードに対する変更になります。  
  
 汎用アプリケーション、特にアドホック クエリをサポートするアプリケーションでは、アプリケーションが作成する結果セットのメタデータは、通常実行時まで特定できません。  
  
 アプリケーションは次の関数を呼び出すことで、結果セットの特性を判断できます。  
  
-   要求から返される列の数を確認するには、 [Sqlnumresultcols](../../relational-databases/native-client-odbc-api/sqlnumresultcols.md)を指定します。  
  
-   [Sqlcolattribute](../../relational-databases/native-client-odbc-api/sqlcolattribute.md) または [SQLDescribeCol](../../relational-databases/native-client-odbc-api/sqldescribecol.md) を実行して、結果セット内の列を記述します。  
  
 適切にデザインされたアプリケーションを作成する場合、結果セットの状態が不明であることが前提となります。またアプリケーションでは、これらの関数により返される情報を使用して結果セットの列をバインドします。 アプリケーションは、ステートメントが準備または実行されると、いつでもこれらの関数を呼び出すことができます。 ただし、最適なパフォーマンスを得るには、ステートメントの実行後に、アプリケーションで **Sqlcolattribute**、 **SQLDescribeCol**、および **sqlnumresultcols** を呼び出す必要があります。  
  
 メタデータを取得するための呼び出しを複数同時に実行できます。 ODBC カタログ API 実装の基盤であるシステム カタログ プロシージャは、ODBC ドライバーが静的サーバー カーソルを使用しているときでも ODBC ドライバーから呼び出すことができます。 このため、アプリケーションは ODBC カタログ関数の複数の呼び出しを同時に処理できます。  
  
 アプリケーションが特定のメタデータのセットを複数回使用する場合、関数からの情報を最初に取得した時点でプライベート変数に格納すると、メタデータの利点を活用することができます。 このような操作を行うと、同じ情報を取得するために、ドライバーとサーバー間のラウンドトリップが必要になる ODBC カタログ関数の呼び出しを後で実行する必要がなくなります。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;結果の処理 ](../../relational-databases/native-client-odbc-results/processing-results-odbc.md)  
  
  
