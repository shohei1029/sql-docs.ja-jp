---
description: 動的 SQL がアプリケーションのパフォーマンスにどのように影響するか、および ODBC での準備されたステートメントがより高速なソリューションであるかどうかについて説明します。
title: ODBC での動的 SQL パフォーマンス
ms.custom: ''
ms.date: 04/02/2020
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- SQL [ODBC], embedded SQL
- SQL statements [ODBC], dynamic SQL
- SQL statements [ODBC], embedded SQL
- dynamic SQL [ODBC]
- SQL [ODBC], dynamic SQL
- embedded SQL [ODBC]
ms.assetid: 0bfb9ab7-9c15-4433-93bc-bad8b6c9d287
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: aa411d4381042c14230fe18421a9d211d868074e
ms.sourcegitcommit: f1a571b6ce02a39c385ad32508ceff23475ed9f5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106377378"
---
# <a name="dynamic-sql-performance-in-odbc"></a>ODBC での動的 SQL パフォーマンス

静的 SQL は多くの状況で適切に動作しますが、データアクセスを事前に特定できないアプリケーションのクラスもあります。 たとえば、スプレッドシートによってユーザーがクエリを入力できるようにした場合、スプレッドシートからデータを取得するために DBMS に送信されるとします。 このクエリの内容は、スプレッドシートプログラムが記述されている場合、プログラマにとってはわかりません。

## <a name="dynamic-execution"></a>動的実行

この問題を解決するために、スプレッドシートには動的 SQL と呼ばれる embedded SQL の形式が使用されています。 プログラムにハードコーディングされている静的 SQL ステートメントとは異なり、動的 SQL ステートメントは実行時に作成し、文字列ホスト変数に配置できます。 その後、処理のために DBMS に送信されます。 DBMS は動的 SQL ステートメントの実行時にアクセスプランを生成する必要があるため、動的 SQL は通常、静的 SQL よりも遅くなります。 動的 sql ステートメントを含むプログラムがコンパイルされると、静的 SQL のように、動的 SQL ステートメントはプログラムから削除されません。 代わりに、ステートメントを DBMS に渡す関数呼び出しに置き換えられています。同じプログラム内の静的 SQL ステートメントは、通常どおりに処理されます。

動的 SQL ステートメントを実行する最も簡単な方法は、EXECUTE IMMEDIATE ステートメントを使用することです。 このステートメントは、コンパイルと実行のために SQL ステートメントを DBMS に渡します。

EXECUTE IMMEDIATE ステートメントの欠点の1つは、ステートメントを実行するたびに、 [SQL ステートメントを処理する5つ](processing-a-sql-statement.md) の各手順を実行する必要があることです。 多くのステートメントが動的に実行され、それらのステートメントが類似している場合は無駄になるので、このプロセスに伴うオーバーヘッドが大きくなる可能性があります。

## <a name="prepared-execution"></a>準備実行

このような状況に対処するために、動的 SQL は、次の手順を使用する、準備された実行と呼ばれる最適化された形式の実行を提供します。

1. このプログラムは、EXECUTE IMMEDIATE ステートメントと同様に、バッファー内に SQL ステートメントを構築します。 ホスト変数の代わりに、ステートメントテキスト内の任意の場所で疑問符 (?) を使用して、定数の値が後で指定されることを示すことができます。 疑問符はパラメーターマーカーとして呼び出されます。

2. プログラムは PREPARE ステートメントを使用して SQL ステートメントを DBMS に渡します。このステートメントは、DBMS がステートメントを解析、検証、最適化し、そのステートメントの実行プランを生成するよう要求します。 その後、プログラムは EXECUTE ステートメント (EXECUTE IMMEDIATE ステートメントではありません) を使用して PREPARE ステートメントを後で実行します。 このメソッドは、SQL データ領域または SQLDA と呼ばれる特殊なデータ構造を使用して、ステートメントのパラメーター値を渡します。

3. プログラムでは、EXECUTE ステートメントを繰り返し使用して、動的ステートメントを実行するたびに異なるパラメーター値を指定できます。

準備された実行は、静的 SQL と同じではありません。 静的 SQL では、コンパイル時に SQL ステートメントを処理する最初の4つの手順が実行されます。 準備実行では、これらの手順は実行時に行われますが、一度だけ実行されます。 プランの実行は、EXECUTE が呼び出された場合にのみ行われます。 この動作により、動的 SQL のアーキテクチャに特有のパフォーマンス上の欠点がいくつか排除されます。

## <a name="see-also"></a>関連項目

[EXECUTE (Transact-SQL)](../../t-sql/language-elements/execute-transact-sql.md)  
[sp_executesql (Transact-sql)](../../relational-databases/system-stored-procedures/sp-executesql-transact-sql.md)  
