---
description: sp_sequence_get_range (Transact-SQL)
title: sp_sequence_get_range (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/08/2015
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_sequence_get_range
- sp_sequence_get_range_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sequence number object, sp_sequence_get_range procedure
- sp_sequence_get_range
ms.assetid: 8ca6b0c6-8d9c-4eee-b02f-51ddffab4492
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 23ca83a62a194bb30bbf7a9b1432e42525592f3c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104744642"
---
# <a name="sp_sequence_get_range-transact-sql"></a>sp_sequence_get_range (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  シーケンスオブジェクトからシーケンス値の範囲を返します。 シーケンスオブジェクトは、要求された値の数を生成して発行し、その範囲に関連するメタデータをアプリケーションに提供します。  
  
 シーケンス番号の詳細については、「 [シーケンス番号](../../relational-databases/sequence-numbers/sequence-numbers.md)」を参照してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_sequence_get_range [ @sequence_name = ] N'<sequence>'   
     , [ @range_size = ] range_size  
     , [ @range_first_value = ] range_first_value OUTPUT   
    [, [ @range_last_value = ] range_last_value OUTPUT ]  
    [, [ @range_cycle_count = ] range_cycle_count OUTPUT ]  
    [, [ @sequence_increment = ] sequence_increment OUTPUT ]  
    [, [ @sequence_min_value = ] sequence_min_value OUTPUT ]  
    [, [ @sequence_max_value = ] sequence_max_value OUTPUT ]  
    [ ; ]  
```  
  
## <a name="arguments"></a>引数  
`[ @sequence_name = ] N'sequence'` シーケンスオブジェクトの名前。 スキーマは省略可能です。 *sequence_name* は **nvarchar (776)** です。  
  
`[ @range_size = ] range_size` シーケンスからフェッチする値の数。 **\@ range_size** は **bigint** です。  
  
`[ @range_first_value = ] range_first_value` 出力パラメーターは、要求された範囲の計算に使用されるシーケンスオブジェクトの最初 (最小値または最大値) の値を返します。 **\@ range_first_value** は、要求で使用されているシーケンスオブジェクトと同じ基本型で **sql_variant** ます。  
  
`[ @range_last_value = ] range_last_value` 省略可能な出力パラメーターは、要求された範囲の最後の値を返します。 **\@ range_last_value** は、要求で使用されているシーケンスオブジェクトと同じ基本型で **sql_variant** ます。  
  
`[ @range_cycle_count = ] range_cycle_count` 省略可能な出力パラメーターは、要求された範囲を返すためにシーケンスオブジェクトが循環した回数を返します。 **\@ range_cycle_count** は **int** です。  
  
`[ @sequence_increment = ] sequence_increment` 省略可能な出力パラメーターは、要求された範囲の計算に使用されるシーケンスオブジェクトのインクリメントを返します。 **\@ sequence_increment** は、要求で使用されているシーケンスオブジェクトと同じ基本型で **sql_variant** ます。  
  
`[ @sequence_min_value = ] sequence_min_value` 省略可能な出力パラメーターは、シーケンスオブジェクトの最小値を返します。 **\@ sequence_min_value** は、要求で使用されているシーケンスオブジェクトと同じ基本型で **sql_variant** ます。  
  
`[ @sequence_max_value = ] sequence_max_value` 省略可能な出力パラメーターは、シーケンスオブジェクトの最大値を返します。 **\@ sequence_max_value** は、要求で使用されているシーケンスオブジェクトと同じ基本型で **sql_variant** ます。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="remarks"></a>解説  
 sys で sp_sequence_get_rangeis します。 スキーマとは sys.sp_sequence_get_range として参照できます。  
  
### <a name="cycling-sequences"></a>シーケンスの循環  
 必要に応じて、シーケンスオブジェクトは、要求された範囲にサービスを実行するための適切な回数を繰り返します。 循環した回数は、`@range_cycle_count` パラメーターを通じて、呼び出し元に返されます。  
  
> [!NOTE]  
>  循環する場合、シーケンスオブジェクトは、シーケンスオブジェクトの開始値からではなく、昇順のシーケンスの最小値および降順のシーケンスの最大値から再起動されます。  
  
### <a name="non-cycling-sequences"></a>循環しないシーケンス  
 要求された範囲の値の数がシーケンスオブジェクトで使用可能な残りの値より大きい場合、要求された範囲はシーケンスオブジェクトから差し引かれず、次のエラー11732が返されます。  
  
 `The requested range for sequence object '%.*ls' exceeds the maximum or minimum limit. Retry with a smaller range.`  
  
## <a name="permissions"></a>権限  
 シーケンスオブジェクトまたはシーケンスオブジェクトのスキーマに対する UPDATE 権限が必要です。  
  
## <a name="examples"></a>例  
 次の例では、テスト RangeSeq という名前のシーケンスオブジェクトを使用します。 次のステートメントを使用して、テスト RangeSeq シーケンスを作成します。  
  
```  
CREATE SCHEMA Test ;  
GO  
  
CREATE SEQUENCE Test.RangeSeq  
    AS int   
    START WITH 1  
    INCREMENT BY 1  
    MINVALUE 1  
    MAXVALUE 25  
    CYCLE  
    CACHE 10  
;  
```  
  
### <a name="a-retrieving-a-range-of-sequence-values"></a>A. シーケンス値の範囲を取得する  
 次のステートメントは、テスト RangeSeq シーケンスオブジェクトから4つのシーケンス番号を取得し、最初の番号をユーザーに返します。  
  
```  
DECLARE @range_first_value_output sql_variant ;  
  
EXEC sys.sp_sequence_get_range  
@sequence_name = N'Test.RangeSeq'  
, @range_size = 4  
, @range_first_value = @range_first_value_output OUTPUT ;  
  
SELECT @range_first_value_output AS FirstNumber ;  
  
```  
  
### <a name="b-returning-all-output-parameters"></a>B. すべての出力パラメーターを返す  
 次の例では、sp_sequence_get_range プロシージャからのすべての出力値を返します。  
  
```  
DECLARE    
  @FirstSeqNum sql_variant  
, @LastSeqNum sql_variant  
, @CycleCount int  
, @SeqIncr sql_variant  
, @SeqMinVal sql_variant  
, @SeqMaxVal sql_variant ;  
  
EXEC sys.sp_sequence_get_range  
@sequence_name = N'Test.RangeSeq'  
, @range_size = 5  
, @range_first_value = @FirstSeqNum OUTPUT   
, @range_last_value = @LastSeqNum OUTPUT   
, @range_cycle_count = @CycleCount OUTPUT  
, @sequence_increment = @SeqIncr OUTPUT  
, @sequence_min_value = @SeqMinVal OUTPUT  
, @sequence_max_value = @SeqMaxVal OUTPUT ;  
  
-- The following statement returns the output values  
SELECT  
  @FirstSeqNum AS FirstVal  
, @LastSeqNum AS LastVal  
, @CycleCount AS CycleCount  
, @SeqIncr AS SeqIncrement  
, @SeqMinVal AS MinSeq  
, @SeqMaxVal AS MaxSeq ;  
  
```  
  
 引数 `@range_size` を 75 などの大きな数に変更すると、シーケンス オブジェクトを循環します。 シーケンス オブジェクトを循環したかどうか、およびその回数を調べるには、引数 `@range_cycle_count` をチェックします。  
  
### <a name="c-example-using-adonet"></a>C. ADO.NET の使用例  
 次の例では、ADO.NET を使用して、テスト RangeSeq から範囲を取得します。  
  
```  
SqlCommand cmd = new SqlCommand();  
cmd.Connection = conn;  
cmd.CommandType = CommandType.StoredProcedure;  
cmd.CommandText = "sys.sp_sequence_get_range";  
cmd.Parameters.AddWithValue("@sequence_name", "Test.RangeSeq");  
cmd.Parameters.AddWithValue("@range_size", 10);  
  
// Specify an output parameter to retrieve the first value of the generated range.  
SqlParameter firstValueInRange = new SqlParameter("@range_first_value", SqlDbType.Variant);  
firstValueInRange.Direction = ParameterDirection.Output;  
cmd.Parameters.Add(firstValueInRange);  
  
conn.Open();  
cmd.ExecuteNonQuery();  
  
// Output the first value of the generated range.  
Console.WriteLine(firstValueInRange.Value);  
  
```  
  
## <a name="see-also"></a>参照  
 [CREATE SEQUENCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-sequence-transact-sql.md)   
 [ALTER SEQUENCE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-sequence-transact-sql.md)   
 [DROP SEQUENCE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-sequence-transact-sql.md)   
 [NEXT VALUE FOR &#40;Transact-SQL&#41;](../../t-sql/functions/next-value-for-transact-sql.md)   
 [シーケンス番号](../../relational-databases/sequence-numbers/sequence-numbers.md)  
  
  
