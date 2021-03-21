---
description: datetime データ型の C から SQL への変換
title: C から SQL への変換 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- conversions [ODBC], C to SQL
ms.assetid: 7ac098db-9147-4883-8da9-a58ab24a0d31
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 89c0627ff26cd5d12aa60b2a25f4ab52d9646695
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756112"
---
# <a name="datetime-data-type-conversions-from-c-to-sql"></a>datetime データ型の C から SQL への変換
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、C 型から日付型または時刻型に変換する際に考慮すべき問題を示し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 次の表で説明する変換は、クライアントで行われる変換に当てはまります。 サーバーで定義されているものとは異なるパラメーターの秒の小数部の有効桁数をクライアントが指定している場合、クライアントの変換は成功する可能性がありますが、 **Sqlexecute** または **sqlexecutedirect** が呼び出されると、サーバーからエラーが返されます。 特に、ODBC では、秒の小数部の切り捨てがエラーとして処理されます。一方、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **datetime2 (6)** から **datetime2 (2)** に進むと丸めが発生します。 datetime 列の値は 1/300 秒単位に丸められ、smalldatetime 列では、サーバーによって秒が 0 に設定されます。  
  
|   | SQL_TYPE_DATE | SQL_TYPE_TIME | SQL_SS_TIME2 | SQL_TYPE_TIMESTAMP | SQL_SS_TIMSTAMPOFFSET | SQL_CHAR | SQL_WCHAR |
| - | ------------- | ------------- | ------------ | ------------------ | --------------------- | -------- | --------- |
| **SQL_C_DATE** |1|-|-|1、6|1、5、6|1、13|1、13|  
| **SQL_C_TIME** |-|1|1|1、7|1、5、7|1、13|1、13|  
| **SQL_C_SS_TIME2** |-|1、3|1、10|1、7|1、5、7|1、13|1、13|  
| **SQL_C_BINARY(SQL_SS_TIME2_STRUCT)** |該当なし|該当なし|1、10、11|該当なし|該当なし|該当なし|該当なし|  
| **SQL_C_TYPE_TIMESTAMP** |1、2|1、3、4|1、4、10|1、10|1、5、10|1、13|1、13|  
| **SQL_C_SS_TIMESTAMPOFFSET** |1、2、8|1、3、4、8|1、4、8、10|1、8、10|1、10|1、13|1、13|  
| **SQL_C_BINARY(SQL_SS_TIMESTAMPOFFSET_STRUCT)** |該当なし|該当なし|該当なし|該当なし|1、10、11|該当なし|該当なし|  
| **SQL_C_CHAR/SQL_WCHAR (date)** |9|9|9|9、6|9、5、6|該当なし|該当なし|  
| **SQL_C_CHAR/SQL_WCHAR (time2)** |9|9、3|9、10|9、7、10|9、5、7、10|該当なし|該当なし|  
| **SQL_C_CHAR/SQL_WCHAR (datetime)** |9、2|9、3、4|9、4、10|9、10|9、5、10|該当なし|該当なし|  
| **SQL_C_CHAR/SQL_WCHAR (datetimeoffset)** |9、2、8|9、3、4、8|9、4、8、10|9、8、10|9、10|該当なし|該当なし|  
| **SQL_C_BINARY(SQL_DATE_STRUCT)** |1、11|該当なし|該当なし|該当なし|該当なし|該当なし|該当なし|  
| **SQL_C_BINARY(SQL_TIME_STRUCT)** |該当なし|該当なし|該当なし|該当なし|該当なし|該当なし|該当なし|  
| **SQL_C_BINARY(SQL_TIMESTAMP_STRUCT)** |該当なし|該当なし|該当なし|該当なし|該当なし|該当なし|該当なし|  
  
## <a name="key-to-symbols"></a>記号の説明  
  
-   **-**: 変換はサポートされていません。 "データ型の属性に関する制限に違反しました" というメッセージで SQLSTATE 07006 の診断レコードが生成されます。  
  
-   **1**: 指定されたデータが有効でない場合、"Datetime 形式が無効です" というメッセージで SQLSTATE 22007 の診断レコードが生成されます。  
  
-   **2**: 時間フィールドをゼロにする必要があります。または、"小数部の切り捨て" というメッセージで SQLSTATE 22008 の診断レコードが生成されます。  
  
-   **3**: 秒の小数部は0にする必要があります。指定しないと、"小数部の切り捨て" というメッセージで SQLSTATE 22008 の診断レコードが生成されます。  
  
-   **4**: 日付部分は無視されます。  
  
-   **5**: タイムゾーンは、クライアントのタイムゾーン設定に設定されます。  
  
-   **6**: 時刻は0に設定されます。  
  
-   **7**: 日付は現在の日付に設定されます。  
  
-   **8**: 時刻は、クライアントのタイムゾーンから UTC に変換されます。 この変換中にエラーが発生すると、"Datetime フィールド オーバーフロー" というメッセージで SQLSTATE 22008 の診断レコードが生成されます。  
  
-   **9**: 文字列が解析され、最初に検出された区切り文字と残りのコンポーネントの有無に応じて、日付、日時、datetimeoffset、または時刻の値に変換されます。 次に、上の表に示した規則のうち、この処理で検出された変換元の型についての規則に従って、文字列は変換先の型に変換されます。 データの解析中にエラーが検出された場合、"キャストした文字コードが正しくありません" というメッセージで SQLSTATE 22018 の診断レコードが生成されます。 datetime 型および smalldatetime 型のパラメーターでは、年がこれらの型でサポートされる範囲外の場合、"datetime 形式が無効です" というメッセージで SQLSTATE 22007 の診断レコードが生成されます。  
  
     datetimeoffset では、UTC への変換が必要なくても、値は UTC への変換後の範囲内に収まっている必要があります。 TDS とサーバーは datetimeoffset 値の時刻を常に UTC 用に正規化するので、クライアントは、時刻部分が、UTC への変換後にサポートされる範囲内に収まっていることを確認する必要があるためです。 値が、サポートされている UTC の範囲内に収まっていない場合、"datetime 形式が無効です" というメッセージで SQLSTATE 22007 の診断レコードが生成されます。  
  
-   **10**: データ損失を伴う切り捨てが発生した場合、"時刻の形式が無効です" というメッセージで SQLSTATE 22008 の診断レコードが生成されます。 サーバーが使用する UTC の範囲で表すことができる範囲の外に値がある場合にも、このエラーが発生します。  
  
-   **11**: データのバイト長が SQL 型で必要な構造体のサイズと等しくない場合、"数値が範囲を超えています" というメッセージで SQLSTATE 22003 の診断レコードが生成されます。  
  
-   **12**: データのバイト長が4または8の場合、生の TDS smalldatetime または datetime 形式でデータがサーバーに送信されます。 データのバイト長が SQL_TIMESTAMP_STRUCT のサイズと完全に一致する場合、データは、datetime2 用の TDS 形式に変換されます。  
  
-   **13**: データ損失を伴う切り捨てが発生した場合、"文字列データ、右側が切り捨てられました" というメッセージで SQLSTATE 22001 の診断レコードが生成されます。  
  
     秒の小数部の桁数 (小数点以下桁数) は、次の表に従って、変換先の列のサイズによって決まります。  
  
    |   | 暗黙の小数点以下桁数 | 暗黙の小数点以下桁数 |
    | - | ------------- | ------------- |
    | **Type** | 0 | 1.. 9 |  
    |**SQL_C_TYPE_TIMESTAMP** |19|21..29|  
  
     ただし、SQL_C_TYPE_TIMESTAMP では、データを損失することなく秒の小数部を 3 桁で表すことができる場合で、かつ、列のサイズが 23 以上である場合、ちょうど 3 桁になるように秒の小数部が生成されます。 この動作により、以前の ODBC ドライバーを使用して開発されたアプリケーションの下位互換性が保証されます。  
  
     テーブルの範囲よりサイズが大きい列の場合は、9 桁と見なされます。 この変換では、秒の小数点以下桁数が 9 桁まで許容されます。これは、ODBC で許容される最大桁数です。  
  
     列サイズ 0 は、ODBC では可変長文字型のサイズが無制限であることを意味します (SQL_C_TYPE_TIMESTAMP の 3 桁ルールが適用されなければ 9 桁)。 固定長文字型の列サイズ 0 を指定すると、エラーになります。  
  
-   **N/A**: 既存の [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 動作と以前の動作が維持されます。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;の日付と時刻の改善 ](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)  
  
  
