---
title: ストアド プロシージャの呼び出し (OLE DB)
description: パラメーター値の渡し方など、OLE DB Driver for SQL Server でストアド プロシージャを呼び出す方法について説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- calling stored procedures
- RPC escape sequence
- OLE DB, stored procedures
- parameters [OLE DB Driver for SQL Server], OLE DB
- ODBC CALL escape sequence
- stored procedures [OLE DB], calling
- OLE DB Driver for SQL Server, stored procedures
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 601e2c07e7b2abb188e75ace02d1856945e0d689
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633301"
---
# <a name="calling-a-stored-procedure-ole-db"></a>ストアド プロシージャの呼び出し (OLE DB)

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

ストアド プロシージャは、0 個以上のパラメーターを受け取ることができます。 また、値を返すこともできます。 OLE DB Driver for SQL Server を使用しているときは、ストアド プロシージャのパラメーターは次の方法で渡すことができます。  
  
- データ値をハードコーディングする。
- パラメーター マーカー (?) を使用してパラメーターを指定し、プログラム変数をパラメーター マーカーにバインドしてから、データ値をプログラム変数に格納する。

> [!NOTE]
> OLE DB で名前付きパラメーターを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ストアド プロシージャを呼び出す場合は、パラメーター名の先頭に '\@' 文字を付ける必要があります。 これは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 固有の制限です。 OLE DB Driver for SQL Server では、この制限が MDAC よりも厳密に適用されます。
  
パラメーターをサポートするには、コマンド オブジェクトで **ICommandWithParameters** インターフェイスを公開します。 パラメーターを使用するには、コンシューマーは、まず、**ICommandWithParameters::SetParameterInfo** メソッドを呼び出して、プロバイダーにパラメーターを示します (または、必要に応じて、**GetParameterInfo** メソッドを呼び出す呼び出し元ステートメントを準備します)。 次に、バッファーの構造を指定するアクセサーを作成し、このバッファーにパラメーター値を格納します。 最後に、アクセサーのハンドルとバッファーへのポインターを **Execute** に渡します。 その後 **Execute** を呼び出す場合、コンシューマーはバッファーに新しいパラメーター値を格納し、アクセサー ハンドルとバッファー ポインターを指定して **Execute** を呼び出します。

パラメーターを使用して一時ストアド プロシージャを呼び出すコマンドでは、まず、**ICommandWithParameters::SetParameterInfo** を呼び出してパラメーター情報を定義する必要があります。 コマンドを正常に準備できるようにするには、この呼び出しを事前に実行する必要があります。 このことが求められるのは、一時ストアド プロシージャの内部名がクライアントで使用される外部名と異なるためです。 MSOLEDBSQL では、システム テーブルに対して、一時ストアド プロシージャのパラメーター情報の特定を目的としたクエリを実行することはできません。

次の項目は、パラメーターのバインド プロセスの手順を示します。

1. DBPARAMBINDINFO 構造体の配列にパラメーター情報を格納します。パラメーター情報には、パラメーター名、パラメーターのデータ型を表すプロバイダー固有の名前、標準のデータ型名などが含まれます。 配列内の構造体 1 つが、1 つのパラメーターを表します。 その後、この配列は **SetParameterInfo** メソッドに渡されます。
2. **ICommandWithParameters::SetParameterInfo** メソッドを呼び出して、パラメーターをプロバイダーに示します。 **SetParameterInfo** では、各パラメーターのネイティブ データ型を指定します。 **SetParameterInfo** 引数は次のとおりです。
    - 型情報を設定するパラメーターの数
    - 型情報を設定するパラメーターの序数の配列
    - DBPARAMBINDINFO 構造体の配列
3. **IAccessor::CreateAccessor** コマンドを使用して、パラメーター アクセサーを作成します。 このアクセサーにより、バッファーの構造を指定し、パラメーター値をバッファーに格納します。 **CreateAccessor** コマンドでは、バインドのセットからアクセサーを作成します。 このバインドは、コンシューマーが DBBINDING 構造体の配列を使用して記述します。 各バインドでは、1 つのパラメーターをコンシューマーのバッファーに関連付けます。バインドには、次のような情報が含まれます。
    - バインドが適用されるパラメーターの序数
    - バインドの対象 (データ値、データ値の長さと状態)
    - これらの各情報に関するバッファー内のオフセット
    - コンシューマーのバッファー内にあるデータ値の長さと型

    アクセサーは、アクセサーのハンドルにより識別されます。このハンドルは HACCESSOR 型です。 このハンドルは、**CreateAccessor** メソッドから返されます。 コンシューマーはアクセサーを使い終わるたびに、**ReleaseAccessor** メソッドを呼び出して、アクセサーが保持しているメモリを解放する必要があります。  
  
    コンシューマーが **ICommand::Execute** などのメソッドを呼び出す場合、アクセサーへのハンドルとバッファー自体へのポインターを渡します。 プロバイダーはこのアクセサーを使用して、バッファー内にあるデータを送信する方法を決定します。
4. DBPARAMS 構造体にデータを格納します。 入力パラメーター値の取得と出力パラメーター値の書き込みに使われるコンシューマーの変数が、実行時に DBPARAMS 構造体の **ICommand::Execute** に渡されます。 DBPARAMS 構造体には、次の 3 つの要素が含まれます。
    - アクセサー ハンドルで指定されているバインドに従ってプロバイダーが入力パラメーター データを取得し、出力パラメーター データを返すための、バッファーへのポインター
    - バッファー内のパラメーター セットの数
    - 手順 3. で作成したアクセサー ハンドル
5. **ICommand::Execute** を使用してコマンドを実行します。

## <a name="methods-of-calling-a-stored-procedure"></a>ストアド プロシージャを呼び出す方法

[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] でストアド プロシージャを実行する場合、OLE DB Driver for SQL Server では次がサポートされます。

- ODBC CALL エスケープ シーケンス。
- リモート プロシージャ コール (RPC) エスケープ シーケンス
- [!INCLUDE[tsql](../../../includes/tsql-md.md)] EXECUTE ステートメント

### <a name="odbc-call-escape-sequence"></a>ODBC CALL エスケープ シーケンス  

パラメーター情報を把握している場合は、**ICommandWithParameters::SetParameterInfo** メソッドを呼び出して、パラメーターをプロバイダーに示します。 それ以外の場合は、ストアド プロシージャの呼び出しに ODBC CALL 構文を使用すると、プロバイダーはヘルパー関数を呼び出してストアド プロシージャのパラメーター情報を取得します。

パラメーター情報 (パラメーターのメタデータ) を把握していない場合は、ODBC CALL 構文の使用をお勧めします。

ODBC CALL エスケープ シーケンスを使用してプロシージャを呼び出す場合の一般的な構文は、次のとおりです。

{ **[?=]** **call**_procedure\_name_[ **(** [*parameter*][ **,** [_parameter_]]... **)** ]}

次に例を示します。

```sql
{call SalesByCategory('Produce', '1995')}
```

### <a name="rpc-escape-sequence"></a>RPC エスケープ シーケンス

RPC エスケープ シーケンスは、ストアド プロシージャを呼び出す ODBC CALL 構文と似ています。 プロシージャを何度も呼び出す場合、ストアド プロシージャを呼び出す 3 つの方法のうち、最適なパフォーマンスを発揮するのは、RPC エスケープ シーケンスです。

RPC エスケープ シーケンスを使用してストアド プロシージャを実行する場合、パラメーター情報を特定するためにプロバイダーによってヘルパー関数が呼び出されることはありません (ODBC CALL 構文を使用する場合と同様に)。 RPC 構文は ODBC CALL 構文よりも簡単です。そのためコマンドが高速に解析され、パフォーマンスが向上します。 この場合は、**ICommandWithParameters::SetParameterInfo** を実行してパラメーター情報を提供する必要があります。

RPC エスケープ シーケンスを使用する場合は、戻り値が必要です。 ストアド プロシージャから値が返されない場合、サーバーからは既定で 0 が返されます。 また、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ストアド プロシージャ上でカーソルを開くこともできません。 ストアド プロシージャは暗黙的に準備され、**ICommandPrepare::Prepare** の呼び出しは失敗します。 RPC 呼び出しを準備できないため、列のメタデータに対してクエリを実行することはできません。IColumnsInfo::GetColumnInfo と IColumnsRowset::GetColumnsRowset によって DB_E_NOTPREPARED が返されます。

パラメーターのメタデータを把握できている場合は、ストアド プロシージャの実行方法として RPC エスケープ シーケンスの使用をお勧めします。

この SQL は、ストアド プロシージャを呼び出すための RPC エスケープ シーケンスの例を示しています。

```sql
{rpc SalesByCategory}
```

RPC エスケープ シーケンスを示すサンプル アプリケーションについては、[ストアド プロシージャの実行 &#40;RPC 構文を使用&#41; とリターン コードおよび出力パラメーターの処理 &#40;OLE DB&#41;](../ole-db-how-to/results/execute-stored-procedure-with-rpc-and-process-output.md) に関するページを参照してください。

### <a name="transact-sql-execute-statement"></a>Transact-SQL EXECUTE ステートメント

ストアド プロシージャを呼び出す方法としては、[EXECUTE](../../../t-sql/language-elements/execute-transact-sql.md) ステートメントよりも、ODBC CALL エスケープ シーケンスや RPC エスケープ シーケンスをお勧めします。 OLE DB Driver for SQL Server では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の RPC メカニズムを使用して、コマンド処理が最適化されます。 この RPC プロトコルでは、サーバー側で実行されるパラメーター処理やステートメントの解析作業の多くを排除することで、パフォーマンスを向上させています。

次の SQL は、[!INCLUDE[tsql](../../../includes/tsql-md.md)] **EXECUTE** ステートメントの例です。

```sql
EXECUTE SalesByCategory 'Produce', '1995'
```

## <a name="see-also"></a>関連項目

[ストアド プロシージャ](stored-procedures.md)
