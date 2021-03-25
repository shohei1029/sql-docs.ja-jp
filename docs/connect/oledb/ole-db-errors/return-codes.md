---
title: リターンコード (OLE DB ドライバー)
description: OLE DB Driver for SQL Server のメンバー関数のリターン コードと、成功以外の結果に関する詳細情報を取得する方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB error handling, return codes
- OLE DB Driver for SQL Server, errors
- failed function [OLE DB]
- successful function [OLE DB]
- S_FALSE
- IS_ERROR macro
- DB_S_ERRORSOCCURRED return
- return codes [OLE DB]
- S_OK
- FAILED macro
- errors [OLE DB], return codes
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: b4da3606117023854ac668ba4b90fcaae0b1a986
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104741852"
---
# <a name="return-codes"></a>リターン コード

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

基本的なレベルでは、メンバー関数は成功するか失敗するかのどちらかです。 ただし、関数が成功した場合も、その成功はアプリケーション開発者の意図とは異なる場合があります。
  
OLE DB のリターン コードの詳細については、「[リターン コード (OLE DB)](/previous-versions/windows/desktop/ms725451(v=vs.85))」を参照してください。  
  
OLE DB Driver for SQL Server のメンバー関数が S_OK を返したときは、その関数は成功しています。  
  
OLE DB Driver for SQL Server のメンバー関数が S_OK を返さない場合は、OLE/COM HRESULT をアンパックする FAILED マクロと IS_ERROR マクロにより、関数全体が成功したか失敗したかを判断できます。  
  
FAILED または IS_ERROR が TRUE を返した場合、OLE DB Driver for SQL Server のコンシューマーは、メンバー関数の実行が失敗したことを確認できます。 FAILED または IS_ERROR が FALSE を返し、HRESULT が S_OK ではない場合、OLE DB Driver for SQL Server のコンシューマーは、部分的に関数が成功していることを確認できます。 コンシューマーは OLE DB Driver for SQL Server のエラー インターフェイスから返される、関数の成功に関する詳細情報を取得できます。 また、関数が明らかに失敗している (FAILED マクロが TRUE を返した) 場合、OLE DB Driver for SQL Server のエラー インターフェイスから拡張エラー情報を取得できます。  
  
OLE DB Driver for SQL Server のコンシューマーは、HRESULT から返される、DB_S_ERRORSOCCURRED により成功が示される情報を取得する場合があります。 通常、DB_S_ERRORSOCCURRED を返すメンバー関数では、コンシューマーに状態値を提供するパラメーターが 1 つ以上定義されています。 コンシューマーは状態値パラメーターに返される情報以外のエラー情報を取得できない場合があるので、状態値が提供された場合にこの値を取得できるアプリケーション ロジックを実装することをお勧めします。  
  
OLE DB Driver for SQL Server メンバーからは、成功コード S_FALSE は返されません。 OLE DB Driver for SQL Server のメンバー関数はすべて、成功を示す場合、常に S_OK を返します。  
  
## <a name="see-also"></a>参照

[エラー](errors.md)  
