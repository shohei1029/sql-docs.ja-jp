---
description: アプリケーション クエリで使用できるさまざまな種類のカーソルと、それらを使用するタイミングについて説明します。
title: カーソルの種類について
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 4f4d3db7-4f76-450d-ab63-141237a4f034
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 1eed3881d9c7162b370b17a5dacb1aa4ea87f956
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104793050"
---
# <a name="understanding-cursor-types"></a>カーソルの種類について

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

リレーショナル データベースで操作を実行する場合、行の完全なセットが操作の対象になります。 SELECT ステートメントでは、WHERE 句で指定した条件を満たすすべての行のセットが返されます。 このステートメントが返す行の完全なセットを結果セットと呼びます。 アプリケーションでは、必ずしも、結果セット全体をひとまとめに使用して作業することが効率的であるとは限りません。 そのため、このようなアプリケーションでは、一度に 1 行または少数の行のブロックを使用するためのメカニズムが必要になります。 カーソルはそのメカニズムを提供する結果セットの拡張機能です。

カーソルは、次の項目を実行することで、結果セットの処理を拡張します。

- 結果セット内の特定の行に位置付けることができます。
- 結果セット内の現在位置から 1 行または 1 ブロックの行を取得します。
- 結果セット内の現在位置の行データを修正できます。
- 結果セット内のデータベース データに対して他のユーザーが行った変更をさまざまなレベルで表示できます。

> [!NOTE]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のカーソルの種類の詳細については、「[カーソルの種類](../../relational-databases/cursors.md#type-of-cursors)」を参照してください。

JDBC の仕様では、他のジョブによって行われた変更を認識する、または認識しない順方向専用およびスクロール可能なカーソルや、読み取り専用または更新可能なカーソルがサポートされています。 この機能は、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] の [SQLServerResultSet](reference/sqlserverresultset-class.md) クラスによって提供されます。

## <a name="remarks"></a>解説

JDBC ドライバーでは、次の種類のカーソルがサポートされています。

|結果セット<br /><br /> (カーソル) の種類|SQL Server のカーソルの種類|特性|select<br /><br /> メソッド|応答<br /><br /> Buffering|説明|
|------------------------------------|----------------------------|---------------------|-----------------------|----------------------------|-----------------|
|TYPE_FORWARD_ONLY (CONCUR_READ_ONLY)|該当なし|順方向専用、読み取り専用|直接|フル|アプリケーションで結果セットに対して 1 回の (順方向の) パススルーを行う必要があります。 このパスは既定の動作です。TYPE_SS_DIRECT_FORWARD_ONLY カーソルと同じように動作します。 ステートメントの実行時には、サーバーからメモリに結果セット全体が読み込まれます。|
|TYPE_FORWARD_ONLY (CONCUR_READ_ONLY)|該当なし|順方向専用、読み取り専用|直接|adaptive|アプリケーションで結果セットに対して 1 回の (順方向の) パススルーを行う必要があります。 TYPE_SS_DIRECT_FORWARD_ONLY カーソルと同じように動作します。 アプリケーションの要求に応じてサーバーから行が読み取られるため、クライアント側のメモリの使用が最小限に抑えられます。|
|TYPE_FORWARD_ONLY (CONCUR_READ_ONLY)|高速順方向|順方向専用、読み取り専用|カーソル (cursor)|該当なし|アプリケーションでサーバー カーソルを使用して結果セットに対する 1 回の (順方向の) パススルーを行う必要があります。 TYPE_SS_SERVER_CURSOR_FORWARD_ONLY カーソルと同じように動作します。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。|
|TYPE_FORWARD_ONLY (CONCUR_UPDATABLE)|動的 (順方向専用)|順方向専用、更新可能|該当なし|該当なし|アプリケーションで 1 行以上の行を更新するには、結果セットに対して 1 回の (順方向の) パススルーを行う必要があります。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。<br /><br /> 既定では、アプリケーションで [SQLServerResultSet](reference/sqlserverresultset-class.md) オブジェクトの [setFetchSize](reference/setfetchsize-method-sqlserverresultset.md) メソッドが呼び出されるとフェッチ サイズが固定されます。<br /><br /> **注:** JDBC ドライバーには、ステートメントの実行結果を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] から (一度に取得するのではなく) 必要に応じて取得できるアダプティブ バッファリングの機能が備わっています。 たとえば、アプリケーションからメモリに収まりきらないほどの大きなデータを取得する必要があっても、アダプティブ バッファリングが作用することで、クライアント アプリケーションがそのような値をストリームとして取得できるようになります。 ドライバーの既定の動作は "**adaptive**" です。 ただし、順方向専用の更新可能な結果セットに対するアダプティブ バッファリングを使用するには、アプリケーションは、[SQLServerStatement](reference/sqlserverstatement-class.md) オブジェクトの [setResponseBuffering](reference/setresponsebuffering-method-sqlserverstatement.md) メソッドに、**String** 値 "**adaptive**" を指定して、このメソッドを明示的に呼び出す必要があります。 コード例については、「[大きなデータを更新するサンプル](updating-large-data-sample.md)」を参照してください。|
|TYPE_SCROLL_INSENSITIVE|静的|スクロール可能、更新不可。<br /><br /> 外部の行の更新、挿入、および削除は参照できません。|該当なし|該当なし|アプリケーションで、データベース スナップショットが必要になります。 結果セットは更新できません。 CONCUR_READ_ONLY のみがサポートされます。  それ以外のコンカレンシータイプは、このカーソル タイプと組み合わせて使用すると例外が発生します。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。|
|TYPE_SCROLL_SENSITIVE<br /><br /> (CONCUR_READ_ONLY)|Keyset|スクロール可能、読み取り専用。 外部の行の更新を参照できます。削除はデータの欠落によって表されます。<br /><br /> 外部の行の挿入は参照できません。|該当なし|該当なし|アプリケーションで、既存の行についてのみ、変更されたデータを調べる必要があります。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。|
|TYPE_SCROLL_SENSITIVE<br /><br /> (CONCUR_UPDATABLE、CONCUR_SS_SCROLL_LOCKS、CONCUR_SS_OPTIMISTIC_CC、CONCUR_SS_OPTIMISTIC_CCVAL)|Keyset|スクロール可能、更新可能。<br /><br /> 外部および内部の行の更新を参照できます。削除はデータの欠落によって表されます。挿入は参照できません。|該当なし|該当なし|アプリケーションから ResultSet オブジェクトを使用して既存の行のデータを変更できます。 また、ResultSet オブジェクト以外から、他のアプリケーションによって行われた行に対する変更も参照できます。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。|
|TYPE_SS_DIRECT_FORWARD_ONLY|該当なし|順方向専用、読み取り専用|該当なし|full または adaptive|整数値 2003 です。 全体がバッファーされた、読み取り専用のクライアント側カーソルを使用できます。 サーバー カーソルは作成されません。<br /><br /> コンカレンシーの種類としては CONCUR_READ_ONLY のみがサポートされます。 それ以外の同時実行タイプは、このカーソル タイプと組み合わせて使用すると例外が発生します。|
|TYPE_SS_SERVER_CURSOR_FORWARD_ONLY|高速順方向|順方向専用|該当なし|該当なし|整数値 2004 です。 サーバー カーソルを使用して、すべてのデータに高速にアクセスできます。 コンカレンシーの種類が CONCUR_UPDATABLE である場合にのみ更新できます。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。<br /><br /> この場合にアダプティブ バッファリングを使用するには、アプリケーションで明示的に **String** 値 "**adaptive**" を指定して [SQLServerStatement](reference/sqlserverstatement-class.md) オブジェクトの [setResponseBuffering](reference/setresponsebuffering-method-sqlserverstatement.md) メソッドを呼び出す必要があります。 コード例については、「[大きなデータを更新するサンプル](updating-large-data-sample.md)」を参照してください。|
|TYPE_SS_SCROLL_STATIC|静的|他のユーザーによる更新は反映されません。|該当なし|該当なし|整数値 1004 です。 アプリケーションで、データベース スナップショットが必要になります。 このオプションは JDBC TYPE_SCROLL_INSENSITIVE に対する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有のシノニムです。コンカレンシー設定の動作は同じです。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。|
|TYPE_SS_SCROLL_KEYSET<br /><br /> (CONCUR_READ_ONLY)|Keyset|スクロール可能、読み取り専用。 外部の行の更新を参照できます。削除はデータの欠落によって表されます。<br /><br /> 外部の行の挿入は参照できません。|該当なし|該当なし|整数値 1005 です。 アプリケーションで、既存の行についてのみ、変更されたデータを調べる必要があります。 このオプションは JDBC TYPE_SCROLL_SENSITIVE に対する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有のシノニムです。コンカレンシー設定の動作は同じです。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。|
|TYPE_SS_SCROLL_KEYSET<br /><br /> (CONCUR_UPDATABLE、CONCUR_SS_SCROLL_LOCKS、CONCUR_SS_OPTIMISTIC_CC、CONCUR_SS_OPTIMISTIC_CCVAL)|Keyset|スクロール可能、更新可能。<br /><br /> 外部および内部の行の更新を参照できます。削除はデータの欠落によって表されます。挿入は参照できません。|該当なし|該当なし|整数値 1005 です。 アプリケーションは、既存の行について変更済みデータを参照したり、データを変更したりする必要があります。 このオプションは JDBC TYPE_SCROLL_SENSITIVE に対する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有のシノニムです。コンカレンシー設定の動作は同じです。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。|
|TYPE_SS_SCROLL_DYNAMIC<br /><br /> (CONCUR_READ_ONLY)|動的|スクロール可能、読み取り専用。<br /><br /> 外部の行の更新と挿入を参照できます。削除は現在のフェッチ バッファー内の一時的なデータ欠落として表されます。|該当なし|該当なし|整数値 1006 です。 アプリケーションで、既存の行について、変更されたデータを調べる必要があります。また、カーソルの有効期間内に挿入および削除された行を調べる必要があります。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。|
|TYPE_SS_SCROLL_DYNAMIC<br /><br /> (CONCUR_UPDATABLE、CONCUR_SS_SCROLL_LOCKS、CONCUR_SS_OPTIMISTIC_CC、CONCUR_SS_OPTIMISTIC_CCVAL)|動的|スクロール可能、更新可能。<br /><br /> 外部および内部の行の更新と挿入を参照できます。削除は現在のフェッチ バッファー内の一時的なデータ欠落として表されます。|該当なし|該当なし|整数値 1006 です。 アプリケーションから ResultSet オブジェクトを使用して、既存の行のデータを変更したり、行の挿入または削除を行ったりすることができます。 また、ResultSet オブジェクト以外から、他のアプリケーションによって行われた行に対する変更、挿入、および削除も参照できます。<br /><br /> 行は、フェッチ サイズで指定されたブロック単位でサーバーから取得されます。|

## <a name="cursor-positioning"></a>カーソルの行の指定

TYPE_FORWARD_ONLY、TYPE_SS_DIRECT_FORWARD_ONLY、および TYPE_SS_SERVER_CURSOR_FORWARD_ONLY カーソルは、行を指定するメソッドとしては [next](../../connect/jdbc/reference/next-method-sqlserverresultset.md) メソッドだけをサポートしています。

TYPE_SS_SCROLL_DYNAMIC カーソルは、[absolute](reference/absolute-method-sqlserverresultset.md) メソッドおよび [getRow](reference/getrow-method-sqlserverresultset.md) メソッドをサポートしていません。 absolute メソッドについては、動的カーソルに対して [first](reference/first-method-sqlserverresultset.md) メソッドおよび [relative](reference/relative-method-sqlserverresultset.md) メソッドを組み合わせて呼び出すことで、同等の結果を得ることができます。

getRow メソッドは、TYPE_FORWARD_ONLY、TYPE_SS_DIRECT_FORWARD_ONLY、TYPE_SS_SERVER_CURSOR_FORWARD_ONLY、TYPE_SS_SCROLL_KEYSET、および TYPE_SS_SCROLL_STATIC カーソルのみでサポートされています。 getRow メソッドは、どの順方向専用カーソルについても、そのカーソルを使用してそれまでに読み取られた行数が返されます。

> [!NOTE]
> アプリケーションでサポートされていない、カーソルの行を指定する呼び出しを行った場合、または getRow メソッドに対してサポートされていない呼び出しを行った場合は、"要求された操作は、このカーソルの種類ではサポートされていません" という意味のメッセージで例外がスローされます。

削除された行を参照できるのは、TYPE_SS_SCROLL_KEYSET および等価である TYPE_SCROLL_SENSITIVE カーソルの場合のみです。 削除された行にカーソルが置かれている場合、列の値を得ることはできず、[rowDeleted](reference/rowdeleted-method-sqlserverresultset.md) メソッドによって "true" が返されます。 get\<Type> メソッドを呼び出すと、"削除された行から値を取得することができません" というメッセージで例外がスローされます。 削除された行を更新することはできません。 削除された行に対して update\<Type> メソッドの呼び出しを試みると、"削除された行を更新することはできません" というメッセージで例外がスローされます。 TYPE_SS_SCROLL_DYNAMIC カーソルも、現在のフェッチ バッファーからカーソルが移動するまでは同じ動作です。

順方向カーソルと動的カーソルでは、フェッチ バッファー内でカーソルにアクセスできる間だけ、削除された行を同様の方法で参照することができます。 順方向カーソルの場合、この動作は非常に単純です。 動的カーソルの場合は、フェッチ サイズが 1 より大きい場合に複雑になります。 アプリケーションでは、フェッチ バッファーによって定義された範囲内でカーソルを前後に移動できますが、更新が行われた元のフェッチ バッファーが移動した場合には、削除された行が参照できなくなります。 アプリケーションで、一時的に削除された行を動的カーソルを使用して参照しない場合は、FETCH RELATIVE (0) を使用する必要があります。

TYPE_SS_SCROLL_KEYSET または TYPE_SCROLL_SENSITIVE カーソル行のキー値がカーソルによって更新されると、更新された行がカーソルの選択基準を満たすかどうかにかかわらず、結果セット内でその行の元の位置が維持されます。 行がカーソルの外部で更新されると、削除された行がその行の元の位置に現れます。ただし、その行がカーソル内に現れるのは、新しいキー値を持ちカーソル内にもともと存在していた別の行が、その時点で削除されている場合だけです。

動的カーソルの場合は、更新された行は、フェッチ バッファーで定義された範囲が移動するまでは、フェッチ バッファー内での位置が維持されます。 更新された行はその後結果セット内の異なる場所に再度現れるか、または完全に削除されます。 結果セット内での一時的な不整合を回避する必要のあるアプリケーションでは、フェッチ サイズを 1 にする必要があります (既定では、CONCUR_SS_SCROLL_LOCKS のコンカレンシーの場合は 8 行、その他のコンカレンシーの場合は 128 行です)。

## <a name="cursor-conversion"></a>カーソルの変換

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、要求されたものと異なる種類のカーソルを実装することもできます。これは暗黙のカーソル変換 (またはカーソルの下位変換) と呼ばれます。

[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] では、ResultSet.TYPE_SCROLL_SENSITIVE および ResultSet.CONCUR_UPDATABLE の結果セットを使用してデータを更新すると、"カーソルは読み取り専用です" というメッセージで例外がスローされます。 この例外が発生するのは、[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] ではその結果セットに対して暗黙のカーソル変換が行われ、要求した更新可能なカーソルが返されないからです。

この問題を回避するには次の 2 つの方法があります。

- 基になるテーブルに主キーがあることを確認する。
- ステートメントを作成する際に、ResultSet.TYPE_SCROLL_SENSITIVE の代わりに [SQLServerResultSet.TYPE_SS_SCROLL_DYNAMIC](reference/type-ss-scroll-dynamic-field-sqlserverresultset.md) を使用する。

## <a name="cursor-updating"></a>カーソルの更新

直接の更新は、カーソルの種類およびコンカレンシーで更新が可能なカーソルについてサポートされています。 カーソルの位置が結果セットの更新可能な行でない場合 (get\<Type> メソッドの呼び出しが成功していない場合) は、update\<Type> メソッドを呼び出すと "ResultSet に現在の行がありません" というメッセージで例外がスローされます。 JDBC の仕様には、CONCUR_READ_ONLY であるカーソルの列に対して更新メソッドが呼び出されると、例外が発生すると記載されています。 更新または削除の競合のようなオプティミスティック同時実行制御の競合があった場合など、行が更新可能でない場合は、[insertRow](reference/insertrow-method-sqlserverresultset.md)、[updateRow](reference/updaterow-method-sqlserverresultset.md)、または [deleteRow](reference/deleterow-method-sqlserverresultset.md) が呼び出されるまで、例外は発生しません。

update\<Type> の呼び出し後は、updateRow または [cancelRowUpdates](reference/cancelrowupdates-method-sqlserverresultset.md) が呼び出されるまで、影響を受けた列に get\<Type> でアクセスすることはできません。 この動作により、サーバーによって返された型とは異なる型を使用して列が更新されるという問題と、その後の getter 呼び出しでクライアント側の型変換が生じ不正確な結果になるという問題が回避されます。 get\<Type> を呼び出すと、"updateRow() または cancelRowUpdates() が呼び出されるまで、更新された列にアクセスすることはできません" というメッセージで例外がスローされます。

> [!NOTE]
> 更新された列がない場合に updateRow メソッドが呼び出されると、JDBC ドライバーでは "updateRow() が呼び出されましたが、列が更新されていません" という意味のメッセージで例外がスローされます。

[moveToInsertRow](reference/movetoinsertrow-method-sqlserverresultset.md) が呼び出された後で、get\<Type>、update\<Type>、insertRow、カーソルの行を指定するメソッド ([moveToCurrentRow](reference/movetocurrentrow-method-sqlserverresultset.md) を含む) 以外のメソッドが結果セットに対して呼び出された場合は、例外がスローされます。 moveToInsertRow メソッドによって結果セットが挿入モードになり、カーソルの行を指定するメソッドによって挿入モードが終了します。 相対的なカーソル行を指定する呼び出しでは、moveToInsertRow が呼び出される前の位置を基準にカーソルが移動します。 カーソルの行を指定する呼び出しの後は、その結果得られたカーソルの位置が新しいカーソル位置になります。

挿入モードで実行されたカーソルの行を指定する呼び出しが失敗した場合、その呼び出し後のカーソル位置は、moveToInsetRow が呼び出される前の元のカーソル位置になります。 insertRow が失敗すると、カーソルは挿入行に残り、カーソルは挿入モードのままになります。

挿入行の列は、最初は初期化されていない状態です。 update\<Type> メソッドを呼び出すことで、列の状態が初期化済みに設定されます。 初期化されていない列に対して get\<Type> メソッドを呼び出すと、例外がスローされます。 insertRow メソッドを呼び出すと、挿入行のすべての列が初期化されていない状態に戻ります。

insertRow メソッドが呼び出されたときに初期化されていない列があった場合、その列の既定値が挿入されます。 既定値がなく、列が null を許容する場合は、null が挿入されます。 既定値がなく、列が null を許容しない場合は、サーバーがエラーを返し、例外がスローされます。

> [!NOTE]
> getRow メソッドを挿入モードで呼び出すと、0 が返されます。
>
> JDBC ドライバーでは、位置を指定した更新または削除はできません。 JDBC の仕様によると、[setCursorName](reference/setcursorname-method-sqlserverstatement.md) メソッドを使用しても効果はなく、[getCursorName](reference/getcursorname-method-sqlserverresultset.md) メソッドを呼び出すと例外がスローされます。
>
> 読み取り専用および静的カーソルは更新できません。
>
> SQL Server では、サーバー カーソルの使用が 1 つの結果セットに制限されています。 バッチまたはストアド プロシージャに複数のステートメントが含まれる場合は、順方向専用かつ読み取り専用のクライアント カーソルを使用する必要があります。

## <a name="see-also"></a>関連項目

[JDBC ドライバーによる結果セットの管理](managing-result-sets-with-the-jdbc-driver.md)  
