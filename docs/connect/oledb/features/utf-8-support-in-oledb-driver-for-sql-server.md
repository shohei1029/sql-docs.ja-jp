---
title: OLE DB Driver for SQL Server の UTF-8 のサポート
description: UTF-8 サーバー エンコードと UTF-8 クライアント エンコードの OLE DB Driver for SQL Server サポートについて学びます。
ms.custom: ''
ms.date: 05/25/2020
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
ms.reviewer: v-kaywon
ms.author: v-daenge
author: David-Engel
ms.openlocfilehash: 52e66fe97869046d182f816a846b1b0121949385
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673923"
---
# <a name="utf-8-support-in-ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server の UTF-8 のサポート

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

Microsoft OLE DB Driver for SQL Server (version 18.2.1) によって、UTF-8 サーバーのエンコードのサポートが追加されます。 SQL Server の UTF-8 のサポートについては、以下を参照してください。

- [照合順序と Unicode のサポート](../../../relational-databases/collations/collation-and-unicode-support.md)
- [UTF-8 のサポート](../../../relational-databases/collations/collation-and-unicode-support.md#utf8)

ドライバーの version 18.4.0 によって、UTF-8 クライアントのエンコードに対するサポートが追加されます (Windows 10 の [地域の設定] で [世界各国の言語のサポートに Unicode UTF-8 を使用する] チェックボックスをオンにします)。

> [!NOTE]  
> Microsoft OLE DB Driver for SQL Server では [GetACP](/windows/win32/api/winnls/nf-winnls-getacp) 関数を使用して、DBTYPE_STR 入力バッファーのエンコードを決定します。
>
> GetACP によって UTF-8 のエンコードが返されるシナリオ (Windows 10 の [地域の設定] で [世界各国の言語のサポートに Unicode UTF-8 を使用する] チェックボックスをオンにします) は、version 18.4 以降でサポートされています。 以前のバージョンでは、バッファーで Unicode データを格納する必要がある場合は、バッファー データ型を *DBTYPE_WSTR* (UTF-16 エンコード) に設定する必要があります。

## <a name="data-insertion-into-a-utf-8-encoded-char-or-varchar-column"></a>UTF-8 でエンコードされた CHAR または VARCHAR 列へのデータの挿入

挿入用の入力パラメーター バッファーが作成されるとき、そのバッファーは、[DBBINDING 構造体](/previous-versions/windows/desktop/ms716845(v=vs.85))の配列を使用して記述されます。 各 DBBINDING 構造体では単一のパラメーターがコンシューマーのバッファーに関連付けられ、データ値の長さや型などの情報が含まれます。 CHAR 型の入力パラメーター バッファーでは、DBBINDING 構造体の *wType* を DBTYPE_STR に設定する必要があります。 WCHAR 型の入力パラメーター バッファーでは、DBBINDING 構造体の *wType* を DBTYPE_WSTR に設定する必要があります。

パラメーターを指定してコマンドを実行するとき、ドライバーによってパラメーターのデータ型情報が作成されます。 入力バッファーの型とパラメーターのデータ型が一致している場合、ドライバーで変換は行われません。 それ以外の場合は、ドライバーによって、入力パラメーター バッファーがパラメーターのデータ型に変換されます。 パラメーターのデータ型は、[icommandwithparameters::setparameterinfo](/previous-versions/windows/desktop/ms725393(v=vs.85)) を呼び出すことでユーザーが明示的に設定できます。 情報が提供されていない場合、ドライバーでは、(a) ステートメントが準備されたときにサーバーから列のメタデータを取得するか、(b) 入力パラメーターのデータ型から既定の変換を試みることによって、パラメーターのデータ型情報を取得します。

入力パラメーター バッファーは、入力バッファーのデータ型とパラメーターのデータ型に応じて、ドライバーまたはサーバーによって、サーバーの列の照合順序に変換される場合があります。 この変換時に、入力バッファー内のすべての文字をクライアントのコード ページまたはデータベースの照合順序のコード ページで表現できない場合は、データ損失が発生する可能性があります。 次の表に、UTF-8 対応列にデータを挿入する際の変換プロセスを示します。

|バッファーのデータ型|パラメーター データ型|変換|ユーザーの予防措置|
|---             |---                |---       |---            |
|DBTYPE_STR|DBTYPE_STR|クライアントのコード ページからデータベースの照合順序のコード ページへのサーバーの変換; データベースの照合順序のコード ページから列の照合順序のコード ページへのサーバーの変換。|入力データのすべての文字を、クライアントのコード ページとデータベースの照合順序のコード ページで表現できることを確認します。 たとえば、ポーランド語の文字を挿入するには、クライアントのコード ページを 1250 (ANSI 中央ヨーロッパ) に設定し、データベースの照合順序で、照合順序指定子として Polish を使用する (例: Polish_100_CI_AS_SC) か、UTF-8 対応にすることができます。|
|DBTYPE_STR|DBTYPE_WSTR|クライアントのコード ページから UTF-16 エンコードへのドライバーの変換; UTF-16 エンコードから列の照合順序のコード ページへのサーバーの変換。|入力データのすべての文字を、クライアントのコード ページで表現できることを確認します。 たとえば、ポーランド語の文字を挿入するには、クライアントのコード ページを 1250 (ANSI 中央ヨーロッパ) に設定できます。|
|DBTYPE_WSTR|DBTYPE_STR|UTF-16 エンコードからデータベースの照合順序のコード ページへのドライバーの変換; データベースの照合順序のコード ページから列の照合順序のコード ページへのサーバーの変換。|入力データのすべての文字を、データベースの照合順序のコード ページで表現できることを確認します。 たとえば、ポーランド語の文字を挿入するには、データベースの照合順序のコード ページで、照合順序指定子として Polish を使用する (例: Polish_100_CI_AS_SC) か、UTF-8 対応にすることができます。|
|DBTYPE_WSTR|DBTYPE_WSTR|UTF-16 から列の照合順序のコード ページへのサーバーの変換。|[なし] :|

## <a name="data-retrieval-from-a-utf-8-encoded-char-or-varchar-column"></a>UTF-8 でエンコードされた CHAR または VARCHAR 列からのデータ取得

データ取得用のバッファーが作成されるとき、そのバッファーは、[DBBINDING 構造体](/previous-versions/windows/desktop/ms716845(v=vs.85))の配列を使用して記述されます。 各 DBBINDING 構造体には、取得された行の単一の列が関連付けられます。 列データを CHAR として取得するには、DBBINDING 構造体の *wType* を DBTYPE_STR に設定します。 列データを WCHAR として取得するには、DBBINDING 構造体の *wType* を DBTYPE_WSTR に設定します。

結果のバッファーの型インジケーターが DBTYPE_STR の場合は、ドライバーによって UTF-8 エンコード データがクライアントのエンコードに変換されます。 ユーザーは、UTF-8 列からのデータをクライアントのエンコードで表現できることを確認する必要があります。そうでない場合は、データ損失が発生する可能性があります。

結果のバッファーの型インジケーターが DBTYPE_WSTR の場合は、ドライバーによって UTF-8 エンコード データが UTF-16 エンコードに変換されます。

## <a name="communication-with-servers-that-dont-support-utf-8"></a>UTF-8 をサポートしていないサーバーとの通信

Microsoft OLE DB Driver for SQL Server を使用すると、サーバーが認識できる方法で、データがサーバーに公開されるようになります。 UTF-8 が有効になっているクライアントからデータを挿入する場合、ドライバーは、UTF-8 でエンコードされた文字列を、データベースの照合順序のコード ページに変換してからサーバーに送信します。

> [!NOTE]  
> [ISequentialStream](/previous-versions/windows/desktop/ms718035(v=vs.85)) インターフェイスを使用して UTF-8 でエンコードされたデータをレガシ テキスト列に挿入することは、UTF-8 をサポートするサーバーのみに制限されます。 詳細については、「[BLOB と OLE オブジェクト](../ole-db-blobs/blobs-and-ole-objects.md)」を参照してください。

## <a name="see-also"></a>参照  

[OLE DB Driver for SQL Server の機能](oledb-driver-for-sql-server-features.md)  
[OLE DB Driver for SQL Server の UTF-16 のサポート](utf-16-support-in-oledb-driver-for-sql-server.md)  
