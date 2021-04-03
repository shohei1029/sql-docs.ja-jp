---
description: HASHBYTES (Transact-SQL)
title: HASHBYTES (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- HASHBYTES_TSQL
- HASHBYTES
dev_langs:
- TSQL
helpviewer_keywords:
- hash input
- HASHBYTES
ms.assetid: 0ea6a4d1-313e-4f70-b939-dd2cd570f6d6
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: fae7a123c69f485dc1f398a877f98249367eb85c
ms.sourcegitcommit: 14f2051d329b69a7b5ff7bce1d136cf7f25bb219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106232129"
---
# <a name="hashbytes-transact-sql"></a>HASHBYTES (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で入力の MD2、MD4、MD5、SHA、SHA1、または SHA2 ハッシュを返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)   
  
## <a name="syntax"></a>構文  
  
```syntaxsql
HASHBYTES ( '<algorithm>', { @input | 'input' } )  
  
<algorithm>::= MD2 | MD4 | MD5 | SHA | SHA1 | SHA2_256 | SHA2_512   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

`<algorithm>`  
入力のハッシュに使用するハッシュ アルゴリズムを指定します。 これは必須の引数で、既定値はありません。 単一引用符で囲む必要があります。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、SHA2_256 と SHA2_512 以外のすべてのアルゴリズムは非推奨とされました。  
  
`@input`  
ハッシュされるデータを含む変数を指定します。 `@input` は、**varchar**、**nvarchar**、または **varbinary** です。  
  
'*input*'  
ハッシュする文字またはバイナリ文字列に評価される式を指定します。  
  
 出力はアルゴリズムの標準に準拠します。MD2、MD4、および MD5 の場合は 128 ビット (16 バイト)、SHA および SHA1 の場合は 160 ビット (20 バイト)、SHA2_256 の場合は 256 ビット (32 バイト)、SHA2_512 の場合は 512 ビット (64 バイト) です。  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降
  
 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以前の場合、指定できる入力値は 8000 バイトまでに制限されます。  
  
## <a name="return-value"></a>戻り値  
 **varbinary** (最大 8,000 バイト)  

## <a name="remarks"></a>注釈  
ハッシュ値を計算するための別の方法として、`CHECKSUM` または `BINARY_CHECKSUM` の使用を検討してください。

[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、MD2、MD4、MD5、SHA、SHA1 のアルゴリズムは非推奨です。 代わりに SHA2_256 または SHA2_512 を使用してください。 以前のアルゴリズムは引き続き機能しますが、Deprecation イベントが発生します。

## <a name="examples"></a>例  
### <a name="return-the-hash-of-a-variable"></a>変数のハッシュを返す  
 次の例では、変数 `@HashThis` に格納されている **nvarchar** 型のデータの `SHA2_256` ハッシュを返します。  
  
```sql  
DECLARE @HashThis NVARCHAR(32);  
SET @HashThis = CONVERT(NVARCHAR(32),'dslfdkjLK85kldhnv$n000#knf');  
SELECT HASHBYTES('SHA2_256', @HashThis);  
```  
  
### <a name="return-the-hash-of-a-table-column"></a>テーブル列のハッシュを返す  
 次の例では、テーブル `Test1` 内の列 `c1` の値の SHA2_256 ハッシュを返します。  
  
```sql  
CREATE TABLE dbo.Test1 (c1 NVARCHAR(32));  
INSERT dbo.Test1 VALUES ('This is a test.');  
INSERT dbo.Test1 VALUES ('This is test 2.');  
SELECT HASHBYTES('SHA2_256', c1) FROM dbo.Test1;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
-------------------------------------------  
0x741238C01D9DB821CF171BF61D72260B998F7C7881D90091099945E0B9E0C2E3 
0x91DDCC41B761ACA928C62F7B0DA61DC763255E8247E0BD8DCE6B22205197154D  
(2 row(s) affected)  
```  
  
## <a name="see-also"></a>参照  
[暗号化アルゴリズムの選択](../../relational-databases/security/encryption/choose-an-encryption-algorithm.md)
[CHECKSUM_AGG &#40;Transact-SQL&#41;](../../t-sql/functions/checksum-agg-transact-sql.md)
[CHECKSUM &#40;Transact-SQL&#41;](../../t-sql/functions/checksum-transact-sql.md)
[BINARY_CHECKSUM  &#40;Transact-SQL&#41;](../../t-sql/functions/binary-checksum-transact-sql.md)
