---
description: CREATE EXTERNAL FILE FORMAT (Transact-SQL)
title: CREATE EXTERNAL FILE FORMAT (Transact-SQL)
ms.custom: ''
ms.date: 02/23/2021
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE EXTERNAL FILE FORMAT
- CREATE_EXTERNAL_FILE_FORMAT
dev_langs:
- TSQL
helpviewer_keywords:
- External
- External, file format
- PolyBase, external file format
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017'
ms.openlocfilehash: 82bd61e91a36b2e8e71061be770dd70c64430270
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750192"
---
# <a name="create-external-file-format-transact-sql"></a>CREATE EXTERNAL FILE FORMAT (Transact-SQL)
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

Hadoop、Azure Blob Storage、Azure Data Lake Store に格納される外部データを定義する、または外部ストリームに関連付けられている入力および出力ストリーム用の外部ファイル形式オブジェクトを作成します。 外部ファイル形式の作成は、外部テーブルを作成するための前提条件です。 外部ファイル形式を作成することで、外部テーブルによって参照されるデータの実際のレイアウトを指定します。  
  
次のファイル形式がサポートされています。
  
- 区切りテキスト  
  
- Hive RCFile - Azure Synapse Analytics には適用されません。
  
- Hive ORC
  
- Parquet

- JSON - Azure SQL Edge にのみ適用されます。

外部テーブルの作成については、「[CREATE EXTERNAL TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-transact-sql.md)」をご覧ください。
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文
  
### <a name="delimited-text"></a>[区切りテキスト](#tab/delimited)
```syntaxsql
-- Create an external file format for DELIMITED (CSV/TSV) files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
        FORMAT_TYPE = DELIMITEDTEXT  
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    [ , DATA_COMPRESSION = {  
           'org.apache.hadoop.io.compress.GzipCodec'  
         | 'org.apache.hadoop.io.compress.DefaultCodec'  
        }  
     ]);

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter 
    | First_Row = integer -- ONLY AVAILABLE FOR AZURE SYNAPSE ANALYTICS
    | DATE_FORMAT = datetime_format  
    | USE_TYPE_DEFAULT = { TRUE | FALSE } 
    | Encoding = {'UTF8' | 'UTF16'} 
}
```
### <a name="rc"></a>[RC](#tab/rc)
```syntaxsql
--Create an external file format for RC files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = RCFILE,  
    SERDE_METHOD = {  
        'org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe'  
      | 'org.apache.hadoop.hive.serde2.columnar.ColumnarSerDe'  
    }  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec' ]);
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

### <a name="orc"></a>[ORC](#tab/orc)
```syntaxsql  
--Create an external file format for ORC file.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (
         FORMAT_TYPE = ORC  
     [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.DefaultCodec'      }  
    ]);  
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

### <a name="parquet"></a>[Parquet](#tab/parquet)
```syntaxsql
--Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
         FORMAT_TYPE = PARQUET  
     [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);    
```
### <a name="json"></a>[JSON](#tab/json)
```syntaxsql
-- Create an external file format for JSON files.
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = JSON  
     [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      
      | 'org.apache.hadoop.io.compress.DefaultCodec'  }  
    ]);  
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

---
  
## <a name="arguments"></a>引数  
*file_format_name*  
外部ファイル形式の名前を指定します。
 
### <a name="format_type"></a>FORMAT_TYPE 
`FORMAT_TYPE = [ PARQUET | ORC | RCFILE | DELIMITEDTEXT]`

外部データの形式を指定します。
  
- PARQUET は、Parquet 形式を指定します。
  
- ORC  
  最適化行多桁式 (ORC) 形式を指定します。 このオプションでは、外部の Hadoop クラスター上で 0.11 以降のバージョンの Hive が必要です。 Hadoop では、ORC ファイル形式の方が RCFILE ファイル形式よりも圧縮とパフォーマンスに優れています。

- RCFILE (SERDE_METHOD = *SERDE_method* との組み合わせ): レコード単票形式ファイル形式 (RcFile)。 このオプションでは、Hive のシリアライザーとデシリアライザー (SerDe) メソッドを指定する必要があります。 この要件は、Hadoop の Hive/HiveQL を使用して RC ファイルに対してクエリを実行する場合と同じです。 SerDe メソッドは大文字と小文字の区別があることに注意してください。

  PolyBase をサポートする 2 つの SerDe メソッドを使用して RCFile を指定する例です。

  - FORMAT_TYPE = RCFILE, SERDE_METHOD = 'org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe'

  - FORMAT_TYPE = RCFILE, SERDE_METHOD = 'org.apache.hadoop.hive.serde2.columnar.ColumnarSerDe'

- DELIMITEDTEXT: フィールド ターミネータとも呼ばれる、列の区切り記号付きのテキスト形式を指定します。
   
- JSON: JSON 形式を指定します。 Azure SQL Edge にのみ適用されます。 

### <a name="data_compression"></a>DATA\_COMPRESSION
 `DATA_COMPRESSION = *data_compression_method*`  
 外部データのデータ圧縮方法を指定します。 DATA_COMPRESSION を指定しない場合の既定値は、圧縮されていないデータです。
正しく動作するためには、Gzip で圧縮されたファイルの拡張子が ".gz" である必要があります。
 
 #### <a name="delimited-text"></a>[区切りテキスト](#tab/delimited)
 DELIMITEDTEXT 形式の種類では、次の圧縮メソッドがサポートされています。
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

#### <a name="rc"></a>[RC](#tab/rc)
 RCFILE 形式の種類では、次の圧縮メソッドがサポートされています。
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'
  
#### <a name="orc"></a>[ORC](#tab/orc)
 ORC ファイル形式の種類では、次の圧縮メソッドがサポートされています。
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

#### <a name="parquet"></a>[Parquet](#tab/parquet)
 PARQUET ファイル形式の種類では、次の圧縮方法がサポートされています。
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

#### <a name="json"></a>[JSON](#tab/json)
 JSON ファイル形式の種類では、次の圧縮方法がサポートされています。
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'
---  
### <a name="delimited-text-format-options"></a>区切りテキスト形式のオプション

このセクションで説明する形式オプションは省略可能であり、区切りテキスト ファイルにのみ適用されます。

#### <a name="field_terminator"></a>FIELD_TERMINATOR
`FIELD_TERMINATOR = *field_terminator*`  
区切りテキスト ファイルにのみ適用されます。 フィールド ターミネータは、テキスト区切りファイルでの各フィールド (列) の終了を示す 1 つ以上の文字を指定します。 既定値はパイプ文字 ꞌ|ꞌ です。 サポートが保証されるよう、1 つまたは複数の Ascii 文字を使うことをお勧めします。
  
  
例 :  
  
-   FIELD_TERMINATOR = '|'  
  
-   FIELD_TERMINATOR = ' '  
  
-   FIELD_TERMINATOR = ꞌ\tꞌ  
  
-   FIELD_TERMINATOR = '~|~'  
  
#### <a name="string_delimiter"></a>STRING_DELIMITER
`STRING_DELIMITER = *string_delimiter*`

テキスト区切りのファイル内の文字列型のデータにはフィールド ターミネータを指定します。 文字列の区切り記号の長さは 1 文字または複数文字とし、単一引用符で囲みます。 既定値は空の文字列 "" です。 サポートが保証されるよう、1 つまたは複数の Ascii 文字を使うことをお勧めします。
 
  
 例 :  

-   STRING_DELIMITER = '"'

-   STRING_DELIMITER = '0x22' -- 二重引用符の十六進値
  
-   STRING_DELIMITER = '*'  
  
-   STRING_DELIMITER = ꞌ,ꞌ  
  
-   STRING_DELIMITER = '0x7E0x7E' -- 2 個のチルダ (~~)
 
#### <a name="first_row"></a>FIRST_ROW
 `FIRST_ROW = *First_row_int*`  
PolyBase の読み込みの間にすべてのファイルで最初に読み取る行番号を指定します。 このパラメーターで指定できる値は 1 ～ 15 です。 2 に設定すると、データが読み込まれるときに、すべてのファイルの最初の行 (ヘッダー行) がスキップされます。 行は、行ターミネータ (/r/n、/r、/n) の存在に基づいてスキップされます。 このオプションをエクスポートに使うと、行がデータに追加されて、データ損失なしにファイルを読み取ることができます。 2 より大きい値に設定する場合は、エクスポートされる最初の行は外部テーブルの列名です。

#### <a name="date_format"></a>DATE\_FORMAT
 `DATE_FORMAT = *datetime_format*`  
区切りテキスト ファイルに出現する可能性のあるすべての日付データと時刻データのカスタム形式を指定します。 ソース ファイルで既定の datetime 形式が使われている場合は、このオプションは必要ありません。 指定できるカスタム datetime 形式は、ファイルごとに 1 つだけです。 1 つのファイルに複数のカスタム datetime 形式を指定することはできません。 ただし、各形式が外部テーブル定義でそれぞれのデータ型の既定の形式になっている場合は、複数の datetime 形式を使うことができます。

> [!IMPORTANT]
> PolyBase では、カスタム日付形式はデータのインポートに対してのみ使われます。 外部ファイルへのデータの書き込みには、カスタム書式は使われません。

 DATE_FORMAT が指定されていないか、空の文字列の場合、PolyBase は次の既定の形式を使います。
  
-   DateTime: 'yyyy-MM-dd HH:mm:ss'  
  
-   SmallDateTime: 'yyyy-MM-dd HH:mm'  
  
-   Date: 'yyyy-MM-dd'  
  
-   DateTime2: 'yyyy-MM-dd HH:mm:ss'  
  
-   DateTimeOffset: 'yyyy-MM-dd HH:mm:ss'  
  
-   Time: 'HH:mm:ss'  

> [!IMPORTANT]
> カスタム `DATE_FORMAT` を指定すると、すべての既定の型の形式が上書きされます。 つまり、ファイル内のすべての日時、日付、時刻のセルで同じ日付形式を使用する必要があります。 オーバーライドされた `DATE_FORMAT` では、日付と時刻の値を異なる形式にすることはできません。

**日付形式の例** を次の表に示します。
  
テーブルに関する注意事項:  
  
-   年、月、日は、さまざまな形式と順序で指定できます。 表では、**ymd** 形式のみを示します。 月は、1 または 2 桁の数字、または 3 つの文字で指定できます。 日は、1 または 2 桁の数字で指定できます。 年は、2 または 4 桁の数字で指定できます。
  
-   ミリ秒 (fffffff) は、必須ではありません。
  
-   AM/PM (tt) は、必須ではありません。 既定値は AM です。
  
|日付型|例|説明|  
|---------------|-------------|-----------------|  
|DateTime|DATE_FORMAT = 'yyyy-MM-dd HH:mm:ss.fff'|年、月、日に加えて、この日付形式には、00-24 の時、00-59 の分、00-59 の秒、3 桁のミリ秒が含まれます。|  
|DateTime|DATE_FORMAT = 'yyyy-MM-dd hh:mm:ss.ffftt'|年、月、日に加えて、この日付形式には、00-12 の時、00-59 の分、00-59 の秒、3 桁のミリ秒、および AM/am/PM/pm が含まれます。 |  
|SmallDateTime|DATE_FORMAT =  'yyyy-MM-dd HH:mm'|年、月、日に加えて、この日付形式には、00-23 の時、00-59 の分が含まれます。|  
|SmallDateTime|DATE_FORMAT =  'yyyy-MM-dd hh:mmtt'|年、月、日に加えて、この日付形式には、00-11 の時、00-59 の分、AM/am/PM/pm が含まれ、秒は含まれません。|  
|Date|DATE_FORMAT =  'yyyy-MM-dd'|年、月、日。 時刻要素は含まれていません。|  
|Date|DATE_FORMAT = 'yyyy-MMM-dd'|年、月、日。 3 つの M を使用して月を指定する場合、入力値は 1 つです。つまり、1 月、2 月、3 月、4 月、6 月、7 月、8 月、9 月、10 月、11 月、または 12 月という文字列です。|  
|DateTime2|DATE_FORMAT = 'yyyy-MM-dd HH:mm:ss.fffffff'|年、月、日に加えて、この日付形式には、00-23 の時、00-59 の分、00-59 の秒、7 桁のミリ秒が含まれます。|  
|DateTime2|DATE_FORMAT = 'yyyy-MM-dd hh:mm:ss.ffffffftt'|年、月、日に加えて、この日付形式には、00-11 の時、00-59 の分、00-59 の秒、7 桁のミリ秒、および AM/am/PM/pm が含まれます。|  
|DateTimeOffset|DATE_FORMAT = 'yyyy-MM-dd HH:mm:ss.fffffff zzz'|年、月、日に加えて、この日付形式には、00-23 の時、00-59 の分、00-59 の秒、7 桁のミリ秒、および `{+&#124;-}HH:ss` として入力ファイルに格納するタイムゾーンのオフセットが含まれます。 たとえば、ロサンゼルス時間 (夏時間なし) は、UTC よりも 8 時間遅くなるので、入力ファイル内の値 -08:00 によってロサンゼルスのタイムゾーンが指定されます。|  
|DateTimeOffset|DATE_FORMAT = 'yyyy-MM-dd hh:mm:ss.ffffffftt zzz'|年、月、日に加えて、この日付形式には、00-11 の時、00-59 の分、00-59 の秒、7 桁のミリ秒、AM/am/PM/pm、およびタイムゾーンのオフセットが含まれます。 前の行の説明を参照してください。|  
|Time|DATE_FORMAT = 'HH:mm:ss'|日付の値はありません。00 から 23 時、00 から 59 分、00 から 59 秒のみです。|  
  
 ##### <a name="supported-date-and-time-formats"></a>サポートされていない日付と時刻の形式
 
 外部ファイル形式では、多数の日付と時刻の形式を記述できます。
  
|DATETIME|smalldatetime|date|datetime2|datetimeoffset|  
|--------------|-------------------|----------|---------------|--------------------|  
|[M[M]]M-[d]d-[yy]yy HH:mm:ss[.fff]|[M[M]]M-[d]d-[yy]yy HH:mm[:00]|[M[M]]M-[d]d-[yy]yy|[M[M]]M-[d]d-[yy]yy HH:mm:ss[.fffffff]|[M[M]]M-[d]d-[yy]yy HH:mm:ss[.fffffff] zzz|  
|[M[M]]M-[d]d-[yy]yy hh:mm:ss[.fff][tt]|[M[M]]M-[d]d-[yy]yy hh:mm[:00][tt]||[M[M]]M-[d]d-[yy]yy hh:mm:ss[.fffffff][tt]|[M[M]]M-[d]d-[yy]yy hh:mm:ss[.fffffff][tt] zzz|  
|[M[M]]M-[yy]yy-[d]d HH:mm:ss[.fff]|[M[M]]M-[yy]yy-[d]d HH:mm[:00]|[M[M]]M-[yy]yy-[d]d|[M[M]]M-[yy]yy-[d]d HH:mm:ss[.fffffff]|[M[M]]M-[yy]yy-[d]d HH:mm:ss[.fffffff] zzz|  
|[M[M]]M-[yy]yy-[d]d hh:mm:ss[.fff][tt]|[M[M]]M-[yy]yy-[d]d hh:mm[:00][tt]||[M[M]]M-[yy]yy-[d]d hh:mm:ss[.fffffff][tt]|[M[M]]M-[yy]yy-[d]d hh:mm:ss[.fffffff][tt] zzz|  
|[yy]yy-[M[M]]M-[d]d HH:mm:ss[.fff]|[yy]yy-[M[M]]M-[d]d HH:mm[:00]|[yy]yy-[M[M]]M-[d]d|[yy]yy-[M[M]]M-[d]d HH:mm:ss[.fffffff]|[yy]yy-[M[M]]M-[d]d HH:mm:ss[.fffffff]  zzz|  
|[yy]yy-[M[M]]M-[d]d hh:mm:ss[.fff][tt]|[yy]yy-[M[M]]M-[d]d hh:mm[:00][tt]||[yy]yy-[M[M]]M-[d]d hh:mm:ss[.fffffff][tt]|[yy]yy-[M[M]]M-[d]d hh:mm:ss[.fffffff][tt] zzz|  
|[yy]yy-[d]d-[M[M]]M HH:mm:ss[.fff]|[yy]yy-[d]d-[M[M]]M HH:mm[:00]|[yy]yy-[d]d-[M[M]]M|[yy]yy-[d]d-[M[M]]M HH:mm:ss[.fffffff]|[yy]yy-[d]d-[M[M]]M HH:mm:ss[.fffffff]  zzz|  
|[yy]yy-[d]d-[M[M]]M hh:mm:ss[.fff][tt]|[yy]yy-[d]d-[M[M]]M hh:mm[:00][tt]||[yy]yy-[d]d-[M[M]]M hh:mm:ss[.fffffff][tt]|[yy]yy-[d]d-[M[M]]M hh:mm:ss[.fffffff][tt] zzz|  
|[d]d-[M[M]]M-[yy]yy HH:mm:ss[.fff]|[d]d-[M[M]]M-[yy]yy HH:mm[:00]|[d]d-[M[M]]M-[yy]yy|[d]d-[M[M]]M-[yy]yy HH:mm:ss[.fffffff]|[d]d-[M[M]]M-[yy]yy HH:mm:ss[.fffffff] zzz|  
|[d]d-[M[M]]M-[yy]yy hh:mm:ss[.fff][tt]|[d]d-[M[M]]M-[yy]yy hh:mm[:00][tt]||[d]d-[M[M]]M-[yy]yy hh:mm:ss[.fffffff][tt]|[d]d-[M[M]]M-[yy]yy hh:mm:ss[.fffffff][tt] zzz|  
|[d]d-[yy]yy-[M[M]]M HH:mm:ss[.fff]|[d]d-[yy]yy-[M[M]]M HH:mm[:00]|[d]d-[yy]yy-[M[M]]M|[d]d-[yy]yy-[M[M]]M HH:mm:ss[.fffffff]|[d]d-[yy]yy-[M[M]]M HH:mm:ss[.fffffff]  zzz|  
|[d]d-[yy]yy-[M[M]]M hh:mm:ss[.fff][tt]|[d]d-[yy]yy-[M[M]]M hh:mm[:00][tt]||[d]d-[yy]yy-[M[M]]M hh:mm:ss[.fffffff][tt]|[d]d-[yy]yy-[M[M]]M hh:mm:ss[.fffffff][tt] zzz|  
  
 詳細:  
  
-   月、日、年の値を区切るには、"-"、"/"、"." を使うことができます。 わかりやすくするために、この表では、'-' の区切りのみを使用しています。
  
-   月をテキストとして指定するには、3 つ以上の文字を使います。 1 つまたは 2 つの文字で表された月は、数値として解釈されます。
  
-   時刻の値を区切るには、":" 記号を使います。
  
-   角かっこで囲まれた文字はオプションです。
  
-   文字 'tt' では [AM|PM|am|pm] が指定されます。 AM が既定値です。 'tt' を指定すると、時間の値 (hh) を 0 から 12 の範囲にする必要があります。
  
-   文字 'zzz' では、システムの現在のタイム ゾーンに対するオフセットが {+|-}HH:ss] の形式で指定されます。
 
#### <a name="use_type_default"></a>USE_TYPE_DEFAULT 
 `USE_TYPE_DEFAULT = { TRUE | FALSE }`
 
 PolyBase によってテキスト ファイルからデータが取得されるときに、区切りテキスト ファイル内の不足値を処理する方法を指定します。
  
 TRUE  
 テキスト ファイルからデータを取得するときは、外部テーブルの定義内の対応する列のデータ型の既定値を使用して、各不足値を格納します。 たとえば、不足値を次の値に置き換えます。  
  
-   列が numeric 型の列として定義されている場合は 0 decimal 型の列はサポートされていないため、エラーになります。
  
-   列が string 型の列である場合は空の文字列 ""。
  
-   列が date 列の場合は 1900-01-01 です。
  
 **FALSE**  
 すべての不足値を NULL として格納します。 区切りテキスト ファイルで単語 NULL を使って格納されている NULL 値は、文字列 "NULL" としてインポートされます。
 
#### <a name="encoding"></a>ENCODING
   `Encoding = {'UTF8' | 'UTF16'}`
   
 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] と [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] (APS CU7.4) では、PolyBase は、UTF8 と UTF16 LE でエンコードされた区切りテキスト ファイルを読み取ることができます。 SQL Server の PolyBase では、UTF16 でエンコードされたファイルの読み取りはサポートされていません。

## <a name="permissions"></a>アクセス許可  
 ALTER ANY EXTERNAL FILE FORMAT アクセス許可が必須です。
  
## <a name="general-remarks"></a>全般的な解説
 外部ファイルの形式は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] ではデータベース スコープです。 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ではサーバー スコープです。  
  
 形式オプションはすべてオプションであり、区切りテキスト ファイルにのみ適用されます。  
  
 いずれかの圧縮形式でデータが格納されていると、PolyBase は最初にデータの圧縮を解除してから、データ レコードを返します。
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項
  
 区切りテキスト ファイルの行区切り記号は、Hadoop の LineRecordReader でサポートされている必要があります。 つまり、"\r"、"'\n"、"\r\n" のいずれかでなければなりません。 これらの区切り記号はユーザー構成可能ではありません。
  
 サポートされる SerDe メソッドと RCFile の組み合わせ、およびサポートされるデータ圧縮方法は、この記事で前に示しました。 すべての組み合わせがサポートされているわけではありません。
  
 PolyBase の同時実行クエリの最大数は、32 です。 32 の同時実行クエリを実行した場合、各クエリでは、外部ファイルの場所から最大で 33,000 個のファイルを読み取ることができます。 ルート フォルダーと各サブフォルダーもファイルとしてカウントされます。 同時実行クエリの数が 32 未満である場合、外部ファイルの場所には 33,000 個を超えるファイルを含めることができます。
  
 外部テーブル内のファイル数には制限があるため、外部ファイルの場所のルートおよびサブフォルダーに格納するファイルの数は 30,000 個未満とすることをお勧めします。 また、ルート ディレクトリの下に保持するサブフォルダーの数は少なくすることをお勧めします。 参照されているファイルが多すぎる場合、Java 仮想マシンのメモリ不足例外が発生する可能性があります。
  
  PolyBase で Hadoop または Azure Blob Storage にデータをエクスポートするときは、データだけがエクスポートされて、CREATE EXTERNAL TABLE コマンドで定義されている列名 (メタデータ) はエクスポートされません。

## <a name="locking"></a>ロック  
 EXTERNAL FILE FORMAT オブジェクトに対して共有ロックを取得します。
  
## <a name="performance"></a>パフォーマンス
 常に圧縮したファイルを使用すると、外部データソースと SQL Server の間で転送するデータ量が少なくなると同時に、データの圧縮および圧縮解除のために CPU の使用率が高くなるというデメリットもあります。
  
 Gzip で圧縮されたテキスト ファイルは分割可能ではありません。 Gzip で圧縮されたテキスト ファイルのパフォーマンスを向上させるには、複数のファイルを生成し、そのすべてを外部データ ソースの同じディレクトリに格納することをお勧めします。 このようなファイル構造にすると、PolyBase は、複数のリーダー プロセスと圧縮解除プロセスを使って、より速くデータを圧縮解除できます。 圧縮されたファイルの理想的な数は、計算ノードあたりのデータ リーダー プロセスの最大数です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] では、データ リーダー プロセスの最大数は、ノードあたり 20 個の [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] Gen2 を除き、ノードあたり 8 個です。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] では、ノードごとのデータ リーダー プロセスの最大数は SLO によって変化します。 詳細については、「[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] の読み込みパターンと戦略](https://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-create-a-delimitedtext-external-file-format"></a>A. DELIMITEDTEXT 外部ファイル形式を作成する  
 この例では、テキスト区切りファイル用に *textdelimited1* という名前の外部ファイル形式を作成します。 FORMAT\_OPTIONS のオプション リストでは、ファイルのフィールドをパイプ文字 "|" を使って区切ることが指定されています。 テキスト ファイルはまた、Gzip コーデックを使用して圧縮されます。 DATA\_COMPRESSION を指定しないと、テキスト ファイルは圧縮されません。
  
 区切りテキスト ファイルの場合、データ圧縮方法は、既定のコーデック "org.apache.hadoop.io.compress.DefaultCodec" または Gzip コーデック "org.apache.hadoop.io.compress.GzipCodec" です。
  
```sql  
CREATE EXTERNAL FILE FORMAT textdelimited1  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,  
    FORMAT_OPTIONS (  
        FIELD_TERMINATOR = '|',  
        DATE_FORMAT = 'MM/dd/yyyy' ),  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
);  
```  
  
### <a name="b-create-an-rcfile-external-file-format"></a>B. RCFile 外部ファイル形式を作成する  
 この例では、シリアル化/逆シリアル化方法として org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe を使う RCFile の外部ファイル形式を作成します。 また、データ圧縮方法として既定のコーデックを使用するように指定しています。 DATA_COMPRESSION を指定しない場合の既定の設定は非圧縮です。
  
```sql  
CREATE EXTERNAL FILE FORMAT rcfile1  
WITH (  
    FORMAT_TYPE = RCFILE,  
    SERDE_METHOD = 'org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe',  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'  
);  
```  
  
### <a name="c-create-an-orc-external-file-format"></a>C. ORC 外部ファイル形式を作成する  
 この例では、org.apache.io.compress.SnappyCodec データ圧縮方法でデータを圧縮する ORC ファイルの外部ファイル形式を作成します。 DATA_COMPRESSION を指定しない場合の既定の設定は非圧縮です。
  
```sql 
CREATE EXTERNAL FILE FORMAT orcfile1  
WITH (  
    FORMAT_TYPE = ORC,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);  
```  
  
### <a name="d-create-a-parquet-external-file-format"></a>D. PARQUET 外部ファイル形式を作成する  
 この例では、org.apache.io.compress.SnappyCodec データ圧縮方法でデータを圧縮する Parquet ファイルの外部ファイル形式を作成します。 DATA_COMPRESSION を指定しない場合の既定の設定は非圧縮です。  
  
```sql  
CREATE EXTERNAL FILE FORMAT parquetfile1  
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);  
```  
### <a name="e-create-a-delimited-text-file-skipping-header-row-azure-synapse-analytics-only"></a>E. 区切りテキスト ファイルのスキップ ヘッダー行を作成する (Azure Synapse Analytics のみ)
 この例では、ヘッダー行が 1 つの CSV ファイルの外部ファイル形式を作成します。 
  
```sql  
CREATE EXTERNAL FILE FORMAT skipHeader_CSV
WITH (FORMAT_TYPE = DELIMITEDTEXT,
      FORMAT_OPTIONS(
          FIELD_TERMINATOR = ',',
          STRING_DELIMITER = '"',
          FIRST_ROW = 2, 
          USE_TYPE_DEFAULT = True)
)
```   
### <a name="f-create-a-json-external-file-format"></a>F. JSON 外部ファイル形式を作成する  
 この例では、org.apache.io.compress.SnappyCodec データ圧縮方法でデータを圧縮する JSON ファイルの外部ファイル形式を作成します。 DATA_COMPRESSION を指定しない場合の既定の設定は非圧縮です。 この例は Azure SQL Edge に適用されます。他の SQL 製品では現在サポートされていません。 
  
```sql  
CREATE EXTERNAL FILE FORMAT jsonFileFormat  
WITH (  
    FORMAT_TYPE = JSON,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);  
```  

## <a name="see-also"></a>参照
 [CREATE EXTERNAL DATA SOURCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-data-source-transact-sql.md)   
 [CREATE EXTERNAL TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-transact-sql.md)   
 [CREATE EXTERNAL TABLE AS SELECT &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-as-select-transact-sql.md)   
 [CREATE TABLE AS SELECT &#40;Azure Synapse Analytics&#41;](../../t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md)   
 [sys.external_file_formats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-external-file-formats-transact-sql.md)  
