---
title: クライアントからサーバーへの変換 | Microsoft Docs
description: OLE DB Driver for SQL Server を使用して作成されたクライアント アプリケーションと SQL Server との間で実行される日付/時刻の変換について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- conversions [OLE DB], client to server
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 9ff13ec5b2df249d31aeb10a32761d119ed418d3
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748522"
---
# <a name="conversions-performed-from-client-to-server"></a>クライアントからサーバーへの変換
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  この記事では、OLE DB Driver for SQL Server を使用して作成されたクライアント アプリケーションと [!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] (以降) との間で実行される日付または時刻の変換について説明します。  
  
## <a name="conversions"></a>コンバージョン  
 この記事では、クライアントで行われる変換について説明します。 パラメーターに対して、サーバーで定義されているのとは異なる、秒の小数部の有効桁数をクライアントで指定した場合、サーバー側で変換すると処理に成功するのに、クライアント側で変換すると処理に失敗することがあります。 具体的には、クライアントでは、秒の小数部の切り捨てがエラーとして処理されますが、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では時刻値が最も近い秒単位の値に丸められます。  
  
 ICommandWithParameters::SetParameterInfo が呼び出されない場合、DBTYPE_DBTIMESTAMP バインドは **datetime2** の場合と同様に変換されます。  
  
|To -><br /><br /> ソース|DBDATE (date)|DBTIME (time)|DBTIME2 (time)|DBTIMESTAMP (smalldatetime)|DBTIMESTAMP (datetime)|DBTIMESTAMP (datetime2)|DBTIMESTAMPOFFSET (datetimeoffset)|STR|WSTR|SQLVARIANT<br /><br /> (sql_variant)|  
|----------------------|---------------------|---------------------|----------------------|-----------------------------------|------------------------------|-------------------------------|------------------------------------------|---------|----------|-------------------------------------|  
|DATE|1、2|1、3、4|4、12|1、12|1、12|1、12|1、5、12|1、12|1、12|1、12<br /><br /> datetime2(0)|  
|DBDATE|1|-|-|1、6|1、6|1、6|1、5、6|1、10|1、10|1<br /><br /> date|  
|DBTIME|-|1|1|1、7|1、7|1、7|1、5、7|1、10|1、10|1<br /><br /> Time(0)|  
|DBTIME2|-|1、3|1|1、7、10、14|1、7、10、15|1、7、10|1、5、7、10|1、10、11|1、10、11|1<br /><br /> Time(7)|  
|DBTIMESTAMP|1、2|1、3、4|1、4、10|1、10、14|1、10、15|1、10|1、5、10|1、10、11|1、10、11|1、10<br /><br /> datetime2(7)|  
|DBTIMESTAMPOFFSET|1、2、8|1、3、4、8|1、4、8、10|1、8、10、14|1、8、10、15|1、8、10|1、10|1、10、11|1、10、11|1、10<br /><br /> datetimeoffset(7)|  
|FILETIME|1、2|1、3、4|1、4、13|1、13|1、13|1、13|1、5、13|1、13|1、10|1、13<br /><br /> datetime2(3)|  
|BYTES|-|-|-|-|-|-|-|該当なし|該当なし|該当なし|  
|VARIANT|1|1|1|1、10|1、10|1、10|1、10|該当なし|該当なし|1、10|  
|SSVARIANT|1、16|1、16|1、16|1、10、16|1、10、16|1、10、16|1、10、16|該当なし|該当なし|1、16|  
|BSTR|1、9|1、9|1、9、10|1、9、10|1、9、10|1、9、10|1、9、10|該当なし|該当なし|該当なし|  
|STR|1、9|1、9|1、9、10|1、9、10|1、9、10|1、9、10|1、9、10|該当なし|該当なし|該当なし|  
|WSTR|1、9|1、9|1、9、10|1、9、10|1、9、10|1、9、10|1、9、10|該当なし|該当なし|該当なし|  
  
## <a name="key-to-symbols"></a>記号の説明  
  
|Symbol|意味|  
|------------|-------------|  
|-|変換はサポートされていません。 IAccessor::CreateAccessor の呼び出し時にバインドが検証される場合、DBBINDSTATUS_UPSUPPORTEDCONVERSION が *rgStatus* で返されます。 アクセサー検証が遅延する場合は、DBSTATUS_E_BADACCESSOR が設定されます。|  
|該当なし|適用不可。|  
|1|指定されたデータが有効でない場合、DBSTATUS_E_CANTCONVERTVALUE が設定されます。 入力データが検証されてから変換が適用されるので、コンポーネントは後続の変換で無視されることがあっても、変換を成功させるには有効である必要があります。|  
|2|時刻フィールドは無視されます。|  
|3|秒の小数部は 0 である必要があります。そうでなければ、DBSTATUS_E_DATAOVERFLOW が設定されます。|  
|4|日付部分は無視されます。|  
|5|タイム ゾーンには、クライアントのタイム ゾーン設定が設定されます。|  
|6|時刻は 0 に設定されます。|  
|7|日付は現在の日付に設定されます。|  
|8|時刻は UTC に変換されます。 この変換中にエラーが発生した場合、DBSTATUS_E_CANTCONVERTVALUE が設定されます。|  
|9|文字列は ISO リテラルとして解析され、対象の型に変換されます。 これが失敗すると、文字列は OLE 日付リテラル (時刻要素も含む) として解析され、OLE Date (DBTYPE_DATE) から対象の型に変換されます。<br /><br /> ターゲットの型が DBTIMESTAMP、**smalldatetime**、**datetime**、**datetime2** のいずれかの場合、文字列は日付リテラル、時刻リテラル、または **datetime2** リテラルの構文か、OLE で認識される構文に準拠している必要があります。 文字列が日付リテラルの場合、すべての時刻部分が 0 に設定されます。 文字列が時刻リテラルの場合、日付は現在の日付に設定されます。<br /><br /> その他の対象の型の場合、文字列は対象の型のリテラルの構文に準拠している必要があります。|  
|10|データ損失を伴って秒の小数部が切り捨てられる場合、DBSTATUS_E_DATAOVERFLOW が設定されます。 文字列変換では、文字列が ISO 構文に準拠している場合のみオーバーフロー チェックが有効になります。 文字列が OLE 日付リテラルの場合、秒の小数部は丸められます。<br /><br /> DBTIMESTAMP (datetime) から smalldatetime への変換の場合、OLE DB Driver for SQL Server では DBSTATUS_E_DATAOVERFLOW エラーは返されず、秒の値は暗黙に切り捨てられます。|  
|11|秒の小数点以下桁数は、次の表に従って、変換先の列のサイズによって決まります。 テーブルの範囲よりサイズが大きい列の場合は、9 桁と見なされます。 この変換では、秒の小数点以下桁数が 9 桁まで許容されます。これは、OLE DB で許容される最大桁数です。<br /><br /> ただし、変換元の型が DBTIMESTAMP で、秒の小数部が 0 の場合、秒の小数部または小数点は生成されません。 この動作により、以前の OLE DB プロバイダーを使用して開発されたアプリケーションの下位互換性が保証されます。<br /><br /> 列サイズが 0 より小さい場合は、OLE DB ではサイズが無制限であることを意味します (DBTIMESTAMP の 3 桁ルールが適用されなければ 9 桁)。|  
|12|[!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] より前のバージョンの DBTYPE_DATE に対する変換セマンティクスが保持されます。 秒の小数部は 0 に切り捨てられます。|  
|13|[!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] より前のバージョンの DBTYPE_FILETIME に対する変換セマンティクスが保持されます。 Windows FileTimeToSystemTime API を使用する場合、1 秒未満の秒の有効桁数は 1 ミリ秒に制限されます。|  
|14|[!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] より前のバージョンの **smalldatetime** に対する変換セマンティクスが保持されます。 秒は 0 に設定されます。|  
|15|[!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] より前のバージョンの **datetime** に対する変換セマンティクスが保持されます。 秒は、1/300 秒単位に丸められます。|  
|16|SSVARIANT クライアントの構造体に埋め込まれた (特定の型の) 値の変換動作は、SSVARIANT クライアントの構造体に埋め込まれていない場合の同一の値および型の動作と同じです。|  
  
| 種類 | 長さ (文字数) | スケール |  
| ---- | ----------------- | ----- |  
|DBTIME2|8、10..18|0、1..9|  
|DBTIMESTAMP|19、21..29|0、1..9|  
|DBTIMESTAMPOFFSET|26、28..36|0、1..9|  
  
## <a name="see-also"></a>参照  
 [バインドと変換 &#40;OLE DB&#41;](../../oledb/ole-db-date-time/conversions-ole-db.md)  
  
  
