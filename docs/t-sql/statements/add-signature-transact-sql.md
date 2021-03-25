---
description: ADD SIGNATURE (Transact-SQL)
title: ADD SIGNATURE (Transact-SQL)
ms.prod: sql
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ADD SIGNATURE
- ADD_SIGNATURE_TSQL
helpviewer_keywords:
- ADD SIGNATURE statement
- adding digital signatures
- signatures [SQL Server]
- digital signatures [SQL Server]
ms.assetid: 64d8b682-6ec1-4e5b-8aee-3ba11e72d21f
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.custom: ''
ms.date: 03/16/2021
ms.openlocfilehash: c33f34611db384dfcafabefeb2e9c1eee1b135f5
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611232"
---
# <a name="add-signature-transact-sql"></a>ADD SIGNATURE (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

ストアド プロシージャ、関数、アセンブリ、または DML トリガーにデジタル署名を追加します。 また、ストアド プロシージャ、関数、アセンブリ、または DML トリガーに副署名を追加します。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  

## <a name="syntax"></a>構文

```syntaxsql
ADD [ COUNTER ] SIGNATURE TO module_class::module_name
    BY <crypto_list> [ ,...n ]
  
<crypto_list> ::=  
    CERTIFICATE cert_name  
    | CERTIFICATE cert_name [ WITH PASSWORD = 'password' ]
    | CERTIFICATE cert_name WITH SIGNATURE = signed_blob
    | ASYMMETRIC KEY Asym_Key_Name  
    | ASYMMETRIC KEY Asym_Key_Name [ WITH PASSWORD = 'password'.]
    | ASYMMETRIC KEY Asym_Key_Name WITH SIGNATURE = signed_blob
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

*module_class*  
署名を追加するモジュールのクラスです。 スキーマ スコープのモジュールの既定値は OBJECT です。  
  
 *module_name*  
 署名または副署名の対象となるストアド プロシージャ、関数、アセンブリ、またはトリガーの名前を指定します。  
  
 CERTIFICATE *cert_name*  
 ストアド プロシージャ、関数、アセンブリ、またはトリガーの署名または副署名に使用する証明書の名前です。  
  
 WITH PASSWORD ='*password*'  
 証明書または非対称キーの秘密キーの暗号化の解除に必要なパスワードです。 この句は、秘密キーがデータベースのマスター キーで保護されていない場合にのみ必要です。  
  
 SIGNATURE =*signed_blob*  
 モジュールの署名付きバイナリ ラージ オブジェクト (BLOB) を指定します。 この句は、秘密キーを配布せずにモジュールを配布する場合に便利です。 この句を使用する場合、署名付きバイナリ ラージ オブジェクトをデータベースに追加するときに必要となるのは、モジュール、署名、公開キーだけです。 *signed_blob* はそれ自体 16 進数形式の BLOB です。  
  
 ASYMMETRIC KEY *Asym_Key_Name*  
 ストアド プロシージャ、関数、アセンブリ、またはトリガーの署名または副署名に使用する非対称キーの名前です。  
  
## <a name="remarks"></a>解説

署名または副署名されるモジュールと、署名に使用する証明書または非対称キーは、あらかじめ存在している必要があります。 署名の確認で、モジュールのすべての文字が確認されます。 これには、先頭のキャリッジ リターンとライン フィードも含まれます。  
  
 モジュールには、任意の数の証明書と非対称キーを使用して署名や副署名を行うことができます。  
  
 モジュールが変更された場合、モジュールの署名は削除されます。  
  
 モジュールに EXECUTE AS 句が含まれる場合、署名の処理の一部としてプリンシパルのセキュリティ ID (SID) も含まれます。  
  
> [!CAUTION]
> モジュールの署名は権限の許可のみに使用し、権限の拒否または取り消しには使用しないでください。  
  
 データ定義言語 (DDL) トリガーとインライン テーブル値関数に署名することはできません。  
  
 署名に関する情報は、sys.crypt_properties カタログ ビューで確認できます。  
  
> [!WARNING]
>  署名のプロシージャを再作成する場合は、元のバッチ内のすべてのステートメントが再作成のバッチと一致する必要があります。 バッチの一部が異なる場合は、スペースやコメントの違いだけであっても、異なる署名になります。  
  
## <a name="countersignatures"></a>副署名  
 署名付きモジュールの実行時に、署名が SQL トークンに一時的に追加されますが、モジュールで別のモジュールが実行されるかモジュールで実行が終了すると、署名は失われます。 副署名は特殊な形式の署名です。 副署名のみで権限が付与されることはありませんが、副署名されたオブジェクトに対して行われた呼び出しの間、同じ証明書または非対称キーで作成された署名を保持できるようになります。  
  
 たとえば、ユーザー Alice がプロシージャ ProcForAlice を呼び出すとします。このプロシージャを呼び出すと、テーブル T1 から選択を行うプロシージャ ProcSelectT1 が呼び出されます。 Alice は ProcForAlice および ProcSelectT1 に対する EXECUTE 権限を持っていますが、T1 に対する SELECT 権限は持っておらず、このチェーン全体に組み合わせ所有権は関係しません。 Alice は、テーブル T1 に直接アクセスすることも、ProcForAlice および ProcSelectT1 を使用してアクセスすることもできません。 Alice がアクセスのために常に ProcForAlice を使用するようにしたいので、ProcSelectT1 を実行するための権限は Alice に付与したくありません。 このためにはどうすればよいでしょうか。  
  
-   ProcSelectT1 に署名して ProcSelectT1 が T1 にアクセスできるようにすると、Alice は ProcSelectT1 を直接呼び出すことができるようになり、ProcForAlice を呼び出す必要がありません。  
  
-   ProcSelectT1 に対する EXECUTE 権限を Alice に付与しないということもできますが、Alice は ProcForAlice を使用して ProcSelectT1 を呼び出すことができなくなります。
  
-   ProcForAlice への署名は ProcSelectT1 の呼び出し時に失われるので、この署名だけでは効果がありません。  
  
ただし、ProcForAlice への署名に使用した証明書と同じ証明書を使用して ProcSelectT1 に副署名すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] において呼び出しチェーン全体で署名が保持され、T1 にアクセスできるようになります。 Alice が ProcSelectT1 を直接呼び出そうとしても、副署名では権限は付与されないので、T1 にはアクセスできません。 以下の例 C で、この例の [!INCLUDE[tsql](../../includes/tsql-md.md)] を示します。  

:::image type="content" source="media/signing-and-countersignature.png" alt-text="署名の例":::

## <a name="permissions"></a>アクセス許可  

オブジェクトに対する ALTER 権限と、証明書または非対称キーに対する CONTROL 権限が必要です。 関連付けられている秘密キーがパスワードで保護されている場合、ユーザーはそのパスワードも保持している必要があります。  
  
## <a name="examples"></a>例  
  
### <a name="a-signing-a-stored-procedure-by-using-a-certificate"></a>A. 証明書を使用してストアド プロシージャに署名する

 次の例では、ストアド プロシージャ `HumanResources.uspUpdateEmployeeLogin` に対して、証明書 `HumanResourcesDP` を使用して署名を行います。  
  
```sql  
USE AdventureWorks2012;  
ADD SIGNATURE TO HumanResources.uspUpdateEmployeeLogin   
    BY CERTIFICATE HumanResourcesDP;  
GO  
```  
  
### <a name="b-signing-a-stored-procedure-by-using-a-signed-blob"></a>B. 署名付き BLOB を使用してストアド プロシージャに署名する

次の例では、新しいデータベースを作成し、この例で使用する証明書を作成します。 まず、単純なストアド プロシージャを作成して署名し、`sys.crypt_properties` から署名付き BLOB を取得します。 その後、署名は削除され、再度追加されます。 次の例では、WITH SIGNATURE 構文を使用して、このプロシージャに署名します。  
  
```sql  
CREATE DATABASE TestSignature ;  
GO  
USE TestSignature ;  
GO  
-- Create a CERTIFICATE to sign the procedure.  
CREATE CERTIFICATE cert_signature_demo   
    ENCRYPTION BY PASSWORD = 'pGFD4bb925DGvbd2439587y'  
    WITH SUBJECT = 'ADD SIGNATURE demo';  
GO  

-- Create a simple procedure.  
CREATE PROC [sp_signature_demo]  
AS  
    PRINT 'This is the content of the procedure.' ;  
GO  
-- Sign the procedure.  
ADD SIGNATURE TO [sp_signature_demo]   
    BY CERTIFICATE [cert_signature_demo]   
    WITH PASSWORD = 'pGFD4bb925DGvbd2439587y' ;  
GO  

-- Get the signature binary BLOB for the sp_signature_demo procedure.  
SELECT cp.crypt_property  
    FROM sys.crypt_properties AS cp  
    JOIN sys.certificates AS cer  
        ON cp.thumbprint = cer.thumbprint  
    WHERE cer.name = 'cert_signature_demo' ;  
GO  
```  
  
 このステートメントで返される `crypt_property` 署名は、プロシージャを作成するたびに異なります。 この結果は、この例の後半で使用するためにメモしておきます。 たとえば、`0x831F5530C86CC8ED606E5BC2720DA835351E46219A6D5DE9CE546297B88AEF3B6A7051891AF3EE7A68EAB37CD8380988B4C3F7469C8EABDD9579A2A5C507A4482905C2F24024FFB2F9BD7A953DD5E98470C4AA90CE83237739BB5FAE7BAC796E7710BDE291B03C43582F6F2D3B381F2102EEF8407731E01A51E24D808D54B373` という結果が表示されます。  
  
```sql  
-- Drop the signature so that it can be signed again.  
DROP SIGNATURE FROM [sp_signature_demo]   
    BY CERTIFICATE [cert_signature_demo];  
GO  

-- Add the signature. Use the signature BLOB obtained earlier.  
ADD SIGNATURE TO [sp_signature_demo]   
    BY CERTIFICATE [cert_signature_demo]  
    WITH SIGNATURE = 0x831F5530C86CC8ED606E5BC2720DA835351E46219A6D5DE9CE546297B88AEF3B6A7051891AF3EE7A68EAB37CD8380988B4C3F7469C8EABDD9579A2A5C507A4482905C2F24024FFB2F9BD7A953DD5E98470C4AA90CE83237739BB5FAE7BAC796E7710BDE291B03C43582F6F2D3B381F2102EEF8407731E01A51E24D808D54B373 ;  
GO  
```  
  
### <a name="c-accessing-a-procedure-using-a-countersignature"></a>C. 副署名を使用してプロシージャにアクセスする

次の例では、副署名によってオブジェクトへのアクセスを制御する方法を示します。  
  
```sql  
-- Create tesT1 database  
CREATE DATABASE testDB;  
GO  
USE testDB;  
GO  
-- Create table T1  
CREATE TABLE T1 (c VARCHAR(11));  
INSERT INTO T1 VALUES ('This is T1.');  
  
-- Create a TestUser user to own table T1  
CREATE USER TestUser WITHOUT LOGIN;  
ALTER AUTHORIZATION ON T1 TO TestUser;  
  
-- Create a certificate for signing  
CREATE CERTIFICATE csSelectT  
  ENCRYPTION BY PASSWORD = 'SimplePwd01'  
  WITH SUBJECT = 'Certificate used to grant SELECT on T1';  
CREATE USER ucsSelectT1 FROM CERTIFICATE csSelectT;  
GRANT SELECT ON T1 TO ucsSelectT1;  
  
-- Create a principal with low privileges  
CREATE LOGIN Alice WITH PASSWORD = 'SimplePwd01';  
CREATE USER Alice;  
  
-- Verify Alice cannoT1 access T1;  
EXECUTE AS LOGIN = 'Alice';  
    SELECT * FROM T1;  
REVERT;  
  
-- Create a procedure that directly accesses T1  
CREATE PROCEDURE procSelectT1 AS  
BEGIN  
    PRINT 'Now selecting from T1...';  
    SELECT * FROM T1;  
END;  
GO  
GRANT EXECUTE ON ProcSelectT1 to public;  
  
-- Create special procedure for accessing T1  
CREATE PROCEDURE  ProcForAlice AS  
BEGIN  
   IF USER_ID() <> USER_ID('Alice')  
    BEGIN  
        PRINT 'Only Alice can use this.';  
        RETURN  
    END  
   EXEC ProcSelectT1;  
END;  
GO;  
GRANT EXECUTE ON ProcForAlice TO PUBLIC;  
  
-- Verify procedure works for a sysadmin user  
EXEC ProcForAlice;  
  
-- Alice still can't use the procedure yet  
EXECUTE AS LOGIN = 'Alice';  
    EXEC ProcForAlice;  
REVERT;  
  
-- Sign procedure to grant it SELECT permission  
ADD SIGNATURE TO ProcForAlice BY CERTIFICATE csSelectT   
WITH PASSWORD = 'SimplePwd01';  
  
-- Counter sign ProcSelectT1, to make this work  
ADD COUNTER SIGNATURE TO ProcSelectT1 BY CERTIFICATE csSelectT   
WITH PASSWORD = 'SimplePwd01';  
  
-- Now the proc works.   
-- Note that calling ProcSelectT1 directly still doesn't work  
EXECUTE AS LOGIN = 'Alice';  
    EXEC ProcForAlice;  
    EXEC ProcSelectT1;  
REVERT;  
  
-- Cleanup  
USE master;  
GO  
DROP DATABASE testDB;  
DROP LOGIN Alice;  
```  
  
## <a name="see-also"></a>参照

- [sys.crypt_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-crypt-properties-transact-sql.md)
- [DROP SIGNATURE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-signature-transact-sql.md)
