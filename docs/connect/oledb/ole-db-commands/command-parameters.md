---
title: コマンド パラメーター (OLE DB ドライバー) | Microsoft Docs
description: SQL ステートメントおよび procedure-call コマンドに対して OLE DB Driver for SQL Server がサポートしている型など、コマンド パラメーターについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- parameters [OLE DB Driver for SQL Server]
- OLE DB Driver for SQL Server, parameters
- OLE DB Driver for SQL Server, commands
- parameters [OLE DB Driver for SQL Server], OLE DB
- commands [OLE DB]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: a36e123a61cc31e80eb667aab51082870f7970de
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737842"
---
# <a name="command-parameters"></a>コマンド パラメーター
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  コマンド テキスト内のパラメーターは、疑問符文字でマークされます。 たとえば、次の SQL ステートメントでは 1 つの入力パラメーターがマークされています。  
  
```  
{call SalesByCategory('Produce', ?)}  
```  
  
 ネットワーク トラフィックを削減してパフォーマンスを向上するために、OLE DB Driver for SQL Server では、コマンドの実行前に **ICommandWithParameters::GetParameterInfo** または **ICommandPrepare::Prepare** が呼び出されない限り、パラメーター情報の自動抽出は行いません。 つまり、OLE DB Driver for SQL Server では次のことは自動で行われません。  
  
-   **ICommandWithParameters::SetParameterInfo** で指定されたデータ型の正当性を確認すること。  
  
-   アクセサー バインド情報で指定された DBTYPE から、そのパラメーターに対する適切な [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型にマップすること。  
  
 アプリケーションで [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型のパラメーターと互換性のないデータ型を指定すると、上記のいずれかが原因で、エラーが発生したり、有効桁数が失われる可能性があります。  
  
 このようなことが起きないようにするには、アプリケーションで次の条件を満たす必要があります。  
  
-   **ICommandWithParameters::SetParameterInfo** をハードコーディングしている場合、*pwszDataSourceType* をパラメーターの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型と一致させます。  
  
-   アクセサーをハードコーディングしている場合、パラメーターにバインドされている DBTYPE 値の型を、パラメーターの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型と同じにします。  
  
-   **ICommandWithParameters::GetParameterInfo** を呼び出すようにアプリケーションをコーディングし、プロバイダーでパラメーターの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型を動的に取得できるようにします。 これにより、ネットワーク上でサーバーとの余分なやり取りが増えることに注意してください。  
  
> [!NOTE]  
>  SQL Native Client OLE DB プロバイダーでは、FROM 句が含まれている [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] UPDATE ステートメントや DELETE ステートメント、パラメーターを含むサブクエリに依存する SQL ステートメント、比較の両方の式、LIKE 述部、および定量化された述語内にパラメーター マーカーを含む SQL ステートメント、またはパラメーターのいずれかが、関数に対するパラメーターになっているクエリの場合は、**ICommandWithParameters::GetParameterInfo** を呼び出すことはできません。 また、SQL ステートメントをバッチ処理する場合、バッチ内の最初のステートメントの後にあるステートメント内のパラメーター マーカーに対して、**ICommandWithParameters::GetParameterInfo** を呼び出すことはできません。 [!INCLUDE[tsql](../../../includes/tsql-md.md)] コマンド内ではコメント (/* \*/) を使用できません。  
  
 OLE DB Driver for SQL Server では、SQL ステートメント コマンドで入力パラメーターを使用できます。 OLE DB Driver for SQL Server では、プロシージャ呼び出しコマンドで入力パラメーター、出力パラメーター、入出力パラメーターを使用できます。 出力パラメーターの値は、実行時 (行セットが返されない場合のみ)、または返されたすべての行セットがアプリケーションによって使用されたときにアプリケーションに返されます。 返される値が有効であることを保証するには、**IMultipleResults** を使用して行セットを強制的に使用します。  
  
 ストアド プロシージャ パラメーターの名前を DBPARAMBINDINFO 構造体で指定する必要はありません。 OLE DB Driver for SQL Server でパラメーター名を無視し、**ICommandWithParameters::SetParameterInfo** の *rgParamOrdinals* メンバーで指定された序数だけを使用する必要があることを示すには、*pwszName* メンバーの値に NULL を使用します。 コマンド テキストに名前付きのパラメーターと名前のないパラメーターの両方が含まれている場合、どの名前付きパラメーターよりも前に、名前のないパラメーターをすべて指定する必要があります。  
  
 ストアド プロシージャ パラメーターの名前が指定された場合、OLE DB Driver for SQL Server ではその名前が妥当かどうかがチェックされます。 OLE DB Driver for SQL Server でコンシューマーから不適切なパラメーター名を受け取ると、エラーが返されます。  
  
> [!NOTE]  
>  OLE DB Driver for SQL Server には [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] XML と UDT (ユーザー定義型) のサポートを公開するために、新しい [ISSCommandWithParameters](../../oledb/ole-db-interfaces/isscommandwithparameters-ole-db.md) インターフェイスが実装されています。  
  
## <a name="see-also"></a>参照  
 [コマンド](../../oledb/ole-db-commands/commands.md)  
  
  
