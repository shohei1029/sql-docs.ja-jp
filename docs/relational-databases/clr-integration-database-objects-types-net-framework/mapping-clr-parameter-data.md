---
title: CLR パラメーターデータのマッピング |Microsoft Docs
description: この記事では、SQL Server の CLR に相当する Microsoft SQL Server データ型と、.NET Framework に相当するネイティブ CLR の一覧を示します。
ms.custom: ''
ms.date: 08/01/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: clr
ms.topic: reference
helpviewer_keywords:
- SqlBinary data type
- SqlInt16 data type
- SqlMoney data type
- SqlString data type
- SqlSingle data type
- data types [CLR integration]
- SqlInt64 data type
- SqlDateTime data type
- SqlXml data type
- SqlBoolean data type
- SqlDecimal data type
- SqlBytes data type
- mapping data types [CLR integration]
- SqlChars data type
- SqlInt32 data type
ms.assetid: 89b43ee9-b9ad-4281-a4bf-c7c8d116daa2
author: rothja
ms.author: jroth
ms.openlocfilehash: bffc04b0015e6d9634aae15fff4e4312d9cc213b
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981002"
---
# <a name="mapping-clr-parameter-data"></a>CLR パラメーター データのマッピング
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  次の表は、 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SqlTypes 名前空間のの共通言語ランタイム (CLR) に相当するデータ型 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と、それらのネイティブ CLR に .NET Framework 相当するものを示して [!INCLUDE[msCoName](../../includes/msconame-md.md)] います。  
  
|**SQL Server のデータ型**|型 (System.Data.SqlTypes または Microsoft.SqlServer.Types)|**CLR データ型 (.NET Framework)**|  
|-|-|-|  
|**bigint**|**SqlInt64**|**Int64、Nullable\<Int64>**|  
|**[バイナリ]**|**SqlBytes、Sqlbytes**|**Byte[]**|  
|**bit**|**SqlBoolean**|**ブール型、Null 値を許容\<Boolean>**|  
|**char**|なし|なし|  
|**cursor**|なし|なし|  
|**date**|**SqlDateTime**|**DateTime、Nullable\<DateTime>**|  
|**datetime**|**SqlDateTime**|**DateTime、Nullable\<DateTime>**|  
|**datetime2**|なし|**DateTime、Nullable\<DateTime>**|  
|**DATETIMEOFFSET**|**なし**|**DateTimeOffset、Nullable\<DateTimeOffset>**|  
|**decimal**|**SqlDecimal**|**Decimal、Nullable\<Decimal>**|  
|**float**|**SqlDouble**|**Double、Nullable\<Double>**|  
|**geography**|**SqlGeography**<br /><br /> **Sqlgeography** は Microsoft.SqlServer.Types.dll で定義されており、SQL Server と共にインストールされ、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] [feature pack](https://www.microsoft.com/download/details.aspx?id=100430)からダウンロードできます。|なし|  
|**geometry**|**SqlGeometry**<br /><br /> **Sqlgeometry** は Microsoft.SqlServer.Types.dll で定義されています。これは SQL Server と共にインストールされ、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] [feature pack](https://www.microsoft.com/download/details.aspx?id=100430)からダウンロードできます。|なし|  
|**hierarchyid**|**SqlHierarchyId**<br /><br /> **Sqlhierarchyid** は Microsoft.SqlServer.Types.dll で定義されており、SQL Server と共にインストールされ、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] [feature pack](https://www.microsoft.com/download/details.aspx?id=100430)からダウンロードできます。|なし|  
|**image**|なし|なし|  
|**int**|**SqlInt32**|**Int32、Nullable\<Int32>**|  
|**money**|**SqlMoney**|**Decimal、Nullable\<Decimal>**|  
|**nchar**|**SqlChars、SqlString**|**String, Char[]**|  
|**ntext**|なし|なし|  
|**numeric**|**SqlDecimal**|**Decimal、Nullable\<Decimal>**|  
|**nvarchar**|**SqlChars、SqlString**<br /><br /> **Sqlchars** は、データの転送とアクセスに適しています。また、 **SQLString** は、文字列操作を実行する場合に適しています。|**String, Char[]**|  
|**nvarchar (1)、nchar (1)**|**SqlChars、SqlString**|**Char、String、Char []、Nullable\<char>**|  
|**real**|**Sqlsingle** ( **sqlsingle** の範囲は **real** より大きい)|**Single、Nullable\<Single>**|  
|**rowversion**|なし|**Byte[]**|  
|**smallint**|**SqlInt16**|**Int16、Nullable\<Int16>**|  
|**smallmoney**|**SqlMoney**|**Decimal、Nullable\<Decimal>**|  
|**sql_variant**|なし|**Object**|  
|**テーブル**|なし|なし|  
|**text**|なし|なし|  
|**time**|なし|**TimeSpan、Nullable\<TimeSpan>**|  
|**timestamp**|なし|なし|  
|**tinyint**|**SqlByte**|**Byte、Nullable\<Byte>**|  
|**uniqueidentifier**|**SqlGuid**|**Guid、Nullable\<Guid>**|  
|**User-defined type(UDT)**|なし|同じアセンブリまたは依存アセンブリ内のユーザー定義型にバインドされている同じクラス|  
|**varbinary**|**SqlBytes、Sqlbytes**|**Byte[]**|  
|**varbinary (1)、binary (1)**|**SqlBytes、Sqlbytes**|**byte、Byte []、Nullable\<byte>**|  
|**varchar**|なし|なし|  
|**xml**|**SqlXml**|なし|  
  
## <a name="automatic-data-type-conversion-with-out-parameters"></a>out パラメーターによるデータ型の自動変換  
 入力パラメーターが **SqlTypes** 名前空間の clr データ型で、呼び出し元のプログラムが入力パラメーターとして対応するデータ型を指定している場合、clr メソッドは呼び出し **元のコード** **\<Out()> またはプログラム** に情報を返すことができます。これに Visual Basic より、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] clr メソッドがデータ型を返すと、型変換が自動的に行われます。  
  
 たとえば、次の CLR ストアドプロシージャには、 **out** (C#) または **\<Out()> ByRef** (Visual Basic) でマークされた **SqlInt32** CLR データ型の入力パラメーターがあります。  
  
```csharp  
[Microsoft.SqlServer.Server.SqlProcedure]  
public static void PriceSum(out SqlInt32 value)  
{ ... }  
```  
  
```vb  
\<Microsoft.SqlServer.Server.SqlProcedure> _  
Public Shared Sub PriceSum( \<Out()> ByRef value As SqlInt32)  
...  
End Sub  
```  
  
 アセンブリがデータベースに構築されて作成されると、次の Transact-sql を使用してストアドプロシージャが作成され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。これは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 出力パラメーターとして **int** のデータ型を指定します。  
  
```  
CREATE PROCEDURE PriceSum (@sum int OUTPUT)  
AS EXTERNAL NAME TestStoredProc.StoredProcedures.PriceSum  
```  
  
 CLR ストアドプロシージャを呼び出すと、 **SqlInt32** データ型が自動的に **int** データ型に変換され、呼び出し元のプログラムに返されます。  
  
 ただし、out パラメーターにより自動的にすべての CLR データ型を同等な [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型に変換できるとは限りません。 次の表に、これらの例外を示します。  
  
|**CLR データ型 (SQL Server)**|**SQL Server のデータ型**|  
|-|-|  
|**Decimal**|smallmoney|  
|**SqlMoney**|smallmoney|  
|**Decimal**|money|  
|**DateTime**|smalldatetime|  
|**SQLDateTime**|smalldatetime|  
  
## <a name="change-history"></a>変更履歴  
  
|変更内容|  
|---------------------|  
|マッピングテーブルに **Sqlgeography**、 **sqlgeography**、および **sqlgeography** 型を追加しました。|  
  
## <a name="see-also"></a>参照  
 [.NET Framework での SQL Server データ型](../../relational-databases/clr-integration-database-objects-types-net-framework/sql-server-data-types-in-the-net-framework.md)  
  
  
