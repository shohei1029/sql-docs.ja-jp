---
title: 機能強化された日付型と時刻型向けの一括コピーの変更 (OLE DB) | Microsoft Docs
description: OLE DB Driver for SQL Server で一括コピー機能をサポートするための日付/時刻の機能強化について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB, bulk copy operations
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: d4ceeea79de149408cb57a92c340de97ca22125f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748802"
---
# <a name="bulk-copy-changes-for-enhanced-date-and-time-types-ole-db"></a>機能強化された日付型と時刻型向けの一括コピーの変更 (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  この記事では、OLE DB Driver for SQL Server で一括コピー機能をサポートするための日付と時刻の機能強化について説明します。  
  
## <a name="format-files"></a>フォーマット ファイル  
 フォーマット ファイルを対話形式で作成する場合に、日付型と時刻型の指定に使用する入力、および対応するホスト ファイル データ型名を次の表に示します。  
  
|ファイル ストレージ型|ホスト ファイル データ型|次のプロンプトへの応答:"フィールド <field_name> [\<default>]: のファイル ストレージ型を入力してください"|  
|-----------------------|-------------------------|-----------------------------------------------------------------------------------------------------|  
|Datetime|SQLDATETIME|d|  
|Smalldatetime|SQLDATETIM4|D|  
|Date|SQLDATE|de|  
|Time|SQLTIME|te|  
|Datetime2|SQLDATETIME2|d2|  
|Datetimeoffset|SQLDATETIMEOFFSET|do|  
  
 XML フォーマット ファイルの XSD への追加内容を次に示します。  
  
```  
<xs:complexType name="SQLDATETIME2">  
    <xs:complexContent>  
        <xs:extension base="bl:Fixed"/>  
    </xs:complexContent>  
</xs:complexType>  
<xs:complexType name="SQLDATETIMEOFFSET">  
    <xs:complexContent>  
        <xs:extension base="bl:Fixed"/>  
    </xs:complexContent>  
</xs:complexType>  
<xs:complexType name="SQLDATE">  
    <xs:complexContent>  
        <xs:extension base="bl:Fixed"/>  
    </xs:complexContent>  
</xs:complexType>  
<xs:complexType name="SQLTIME">  
    <xs:complexContent>  
        <xs:extension base="bl:Fixed"/>  
    </xs:complexContent>  
</xs:complexType>  
```  
  
## <a name="character-data-files"></a>文字データ ファイル  
 文字データ ファイルでは、日付と時刻の値は「[OLE DB の日付/時刻の強化に対するデータ型のサポート](../../oledb/ole-db-date-time/data-type-support-for-ole-db-date-and-time-improvements.md)」の「データ フォーマット: 文字列とリテラル」セクションにある説明のとおりに表現されます。  
  
 ネイティブ データ ファイルでは、4 つの新しい型の日付と時刻の値は、小数点以下桁数が 7 桁の TDS 表現として表されます (これが [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] によってサポートされている最大であり、bcp データ ファイルはこれらの列の小数点以下桁数を格納しないためです)。 既存の **datetime** 型および **smalldatetime** 型や、それらの TDS (表形式のデータ ストリーム) 表現のストレージに変更はありません。  
  
 OLE DB の場合、さまざまなストレージ型のストレージ サイズは次のとおりです。  
  
|ファイル ストレージ型|ストレージ サイズ (バイト単位)|  
|-----------------------|---------------------------|  
|DATETIME|8|  
|smalldatetime|4|  
|date|3|  
|time|6|  
|datetime2|9|  
|datetimeoffset|11|  
 
  
## <a name="bcp-types-in-msoledbsqlh"></a>msoledbsql.h の BCP 型  
 msoledbsql.h には次の型が定義されています。 これらの型は OLE DB で IBCPSession::BCPColFmt の *eUserDataType* パラメーターと共に渡されます。  
  
|ファイル ストレージ型|ホスト ファイル データ型|IBCPSession::BCPColFmt と共に使用するための msoledbsql.h での型|値|  
|-----------------------|-------------------------|-----------------------------------------------------------|-----------|  
|Datetime|SQLDATETIME|BCP_TYPE_SQLDATETIME|0x3d|  
|Smalldatetime|SQLDATETIM4|BCP_TYPE_SQLDATETIM4|0x3a|  
|Date|SQLDATE|BCP_TYPE_SQLDATE|0x28|  
|Time|SQLTIME|BCP_TYPE_SQLTIME|0x29|  
|Datetime2|SQLDATETIME2|BCP_TYPE_SQLDATETIME2|0x2a|  
|Datetimeoffset|SQLDATETIMEOFFSET|BCP_TYPE_SQLDATETIMEOFFSET|0x2b|  
  
## <a name="bcp-data-type-conversions"></a>BCP データ型変換  
 次の表に、変換情報を示します。  
  
 **OLE DB に関するメモ** 次の変換は、IBCPSession によって実行されます。 IRowsetFastLoad では、「[クライアントからサーバーへの変換](../../oledb/ole-db-date-time/conversions-performed-from-client-to-server.md)」に定義されているとおり、OLE DB 変換が使用されます。 次に示すクライアントでの変換が実行されると、datetime 値は 1/300 秒単位に丸められ、smalldatetime 値の秒は 0 に設定されます。 datetime の丸め処理は、時間と分に適用されますが、日付には適用されません。  
  
|To --><br /><br /> ソース|date|time|smalldatetime|DATETIME|datetime2|datetimeoffset|char|wchar|  
|------------------------|----------|----------|-------------------|--------------|---------------|--------------------|----------|-----------|  
|Date|1|-|1、6|1、6|1、6|1、5、6|1、3|1、3|  
|Time|該当なし|1、10|1、7、10|1、7、10|1、7、10|1、5、7、10|1、3|1、3|  
|Smalldatetime|1、2|1、4、10|1|1|1、10|1、5、10|1、11|1、11|  
|Datetime|1、2|1、4、10|1、12|1|1、10|1、5、10|1、11|1、11|  
|Datetime2|1、2|1、4、10|1、12|1、10|1、10|1、5、10|1、3|1、3|  
|Datetimeoffset|1、2、8|1、4、8、10|1、8、10|1、8、10|1、8、10|1、10|1、3|1、3|  
|Char/wchar (date)|9|-|9、6、12|9、6、12|9、6|9、5、6|該当なし|該当なし|  
|Char/wchar (time)|-|9、10|9、7、10、12|9、7、10、12|9、7、10|9、5、7、10|該当なし|該当なし|  
|Char/wchar (datetime)|9、2|9、4、10|9、10、12|9、10、12|9、10|9、5、10|該当なし|該当なし|  
|Char/wchar (datetimeoffset)|9、2、8|9、4、8、10|9、8、10、12|9、8、10、12|9、8、10|9、10|該当なし|該当なし|  
  
#### <a name="key-to-symbols"></a>記号の説明  
  
|Symbol|意味|  
|------------|-------------|  
|-|変換はサポートされていません。<br />|  
|1|指定されたデータが有効ではない場合、エラーが通知されます。 datetimeoffset 値の場合は、UTC への変換が必要なくても、時刻部分は UTC への変換後の範囲内に収まっている必要があります。 TDS とサーバーは datetimeoffset 値の時刻を常に UTC 用に正規化するためです。 したがって、クライアントは、時刻部分が、UTC への変換後にサポートされる範囲内に収まっていることを確認する必要があります。|  
|2|時刻部分は無視されます。|  
|3|データの損失を伴う切り捨てが行われると、エラーが通知されます。 datetime2 に関しては、次の表に示すように、秒の小数点以下桁数 (スケール) は変換先の列のサイズによって決まります。 テーブルの範囲よりサイズが大きい列の場合は、9 桁と見なされます。 この変換では、秒の小数点以下桁数が 9 桁まで許容されます。これは、OLE DB で許容される最大桁数です。<br /><br /> **種類:** DBTIME2<br /><br /> **暗黙の小数点以下桁数 0** 8<br /><br /> **暗黙の小数点以下桁数 1..9** 1..9<br /><br /> <br /><br /> **種類:** DBTIMESTAMP<br /><br /> **暗黙の小数点以下桁数 0:** 19<br /><br /> **暗黙の小数点以下桁数 1..9:** 21..29<br /><br /> <br /><br /> **種類:** DBTIMESTAMPOFFSET<br /><br /> **暗黙の小数点以下桁数 0:** 26<br /><br /> **暗黙の小数点以下桁数 1..9:** 28..36|  
|4|日付部分は無視されます。|  
|5|タイム ゾーンは UTC (00:00 など) に設定されます。|  
|6|時刻は 0 に設定されます。|  
|7|日付は 1900-01-01 に設定されます。|  
|8|タイム ゾーン オフセットは無視されます。|  
|9|文字列は解析され、最初に検出された句読点、および残りの部分があるかどうかに応じて、date 値、datetime 値、datetimeoffset 値、time 値のいずれかに変換されます。 次に、この記事の最後にある表に示した規則のうち、この処理で検出された変換元の型についての規則に従って、文字列は変換先の型に変換されます。 指定したデータを解析するとエラーが発生する場合、データを構成するいずれかの部分が許容範囲の外にある場合、または、リテラル型から変換先の型への変換が存在しない場合は、エラーが通知されます。 datetime パラメーターと smalldatetime パラメーターについては、年がこれらの型でサポートされる範囲外になる場合、エラーが通知されます。<br /><br /> datetimeoffset では、UTC への変換が必要なくても、値は UTC への変換後の範囲内に収まっている必要があります。 TDS とサーバーは datetimeoffset 値の時刻を常に UTC 用に正規化するので、クライアントは、時刻部分が、UTC への変換後にサポートされる範囲内に収まっていることを確認する必要があるためです。 サポートされている UTC 範囲内に値がない場合、エラーが通知されます。|  
|10|クライアントからサーバーへの変換の場合は、データの損失を伴う切り捨てが行われると、エラーが通知されます。 サーバーが使用する UTC の範囲で表すことができる範囲の外に値がある場合にも、このエラーが発生します。 サーバーからクライアントへの変換で、秒または秒の小数部の切り捨てが行われた場合は、警告のみが発生します。|  
|11|クライアントからサーバーへの変換の場合は、データの損失を伴う切り捨てが行われると、エラーが通知されます。|
|12|秒は 0 に設定され、秒の小数部は破棄されます。 切り捨てエラーは発生しません。|  
|該当なし|既存の [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] 以前のバージョンの動作が維持されます。|  
  
## <a name="see-also"></a>参照     
 [日付と時刻の強化機能 &#40;OLE DB&#41;](../../oledb/ole-db-date-time/date-and-time-improvements-ole-db.md)  
  
  
