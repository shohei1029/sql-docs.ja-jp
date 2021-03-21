---
description: ALTER CERTIFICATE (Transact-SQL)
title: ALTER CERTIFICATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/22/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ALTER_CERTIFICATE_TSQL
- ALTER CERTIFICATE
dev_langs:
- TSQL
helpviewer_keywords:
- ENCRYPTION BY PASSWORD option
- encryption [SQL Server], certificates
- modifying certificates
- private keys [SQL Server]
- ALTER CERTIFICATE statement
- certificates [SQL Server], modifying
ms.assetid: da4dc25e-72e0-4036-87ce-22de83160836
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2b8b8bd2539a15a6023ec77513679e4fe9e1ca15
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748832"
---
# <a name="alter-certificate-transact-sql"></a>ALTER CERTIFICATE (Transact-SQL)

[!INCLUDE [sql-asdb-pdw](../../includes/applies-to-version/sql-asdb-pdw.md)]

  証明書の秘密キーの暗号化に使用するパスワードを変更したり、秘密キーを削除したり、秘密キーが存在しない場合はインポートします。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] に対して、証明書を使用できるようにするかどうかを変更します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
ALTER CERTIFICATE certificate_name   
      REMOVE PRIVATE KEY  
    | WITH PRIVATE KEY ( <private_key_spec> )  
    | WITH ACTIVE FOR BEGIN_DIALOG = { ON | OFF }  
  
<private_key_spec> ::=   
      {   
        { FILE = 'path_to_private_key' | BINARY = private_key_bits }  
         [ , DECRYPTION BY PASSWORD = 'current_password' ]  
         [ , ENCRYPTION BY PASSWORD = 'new_password' ]  
      }  
    |  
      {  
         [ DECRYPTION BY PASSWORD = 'current_password' ]  
         [ [ , ] ENCRYPTION BY PASSWORD = 'new_password' ]  
      }  
``` 
 
 
```syntaxsql  
-- Syntax for Parallel Data Warehouse  
  
ALTER CERTIFICATE certificate_name   
{  
      REMOVE PRIVATE KEY  
    | WITH PRIVATE KEY (   
        FILE = '<path_to_private_key>',  
        DECRYPTION BY PASSWORD = '<key password>' )
}  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *certificate_name*  
 データベースに認識される証明書の一意な名前を指定します。  
  
 REMOVE PRIVATE KEY  
 秘密キーをデータベース内で保持しないよう指定します。  
  
 WITH PRIVATE KEY は、証明書の秘密キーを SQL Server に読み込むように指定します。

 FILE ='*path_to_private_key*'  
 秘密キーへの完全なパスを、ファイル名を含めて指定します。 このパラメーターには、ローカル パスまたはネットワーク上の場所を示す UNC パスを指定できます。 このファイルには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントのセキュリティ コンテキストでアクセスします。 このオプションを使用する場合は、サービス アカウントに指定したファイルへのアクセス権があることを確認します。
 
 ファイル名だけを指定した場合、そのファイルがインスタンスの既定のユーザー データ フォルダーに保存されます。 このフォルダーは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] DATA フォルダーであってもなくても構いません。 SQL Server Express LocalDB の場合、インスタンスの既定のユーザー データ フォルダーは、インスタンスを作成したアカウントの `%USERPROFILE%` 環境変数で指定されたパスです。  
  
 BINARY ='*private_key_bits*'  
 **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。  
  
 バイナリ定数として指定された秘密キーのビット。 これらのビットは暗号化された形式でもかまいません。 暗号化されている場合は、ユーザーは暗号化解除パスワードを指定する必要があります。 パスワード ポリシーのチェックは、このパスワードに対しては実行されません。 秘密キーのビットは PVK ファイル形式にする必要があります。  
  
 DECRYPTION BY PASSWORD ='*current_password*'  
 秘密キーの暗号化解除に必要なパスワードを指定します。  
  
 ENCRYPTION BY PASSWORD ='*new_password*'  
 データベース内の証明書の秘密キーを暗号化するために使用されるパスワードを指定します。 *new_password* は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを実行するコンピューターの Windows のパスワード ポリシー要件を満たす必要があります。 詳細については、「 [Password Policy](../../relational-databases/security/password-policy.md)」をご参照ください。  
  
 ACTIVE FOR BEGIN_DIALOG **=** { ON | OFF }  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] メッセージ交換の発信側で証明書を使用できるようにします。  
  
## <a name="remarks"></a>解説  
 秘密キーは、*certificate_name* で指定する公開キーに対応している必要があります。  
  
 DECRYPTION BY PASSWORD 句は、ファイル内のパスワードが NULL パスワードで保護されている場合は省略できます。  
  
 データベースに既に存在する証明書の秘密キーをインポートするとき、その秘密キーはデータベース マスター キーにより自動的に保護されます。 秘密キーをパスワードで保護するには、ENCRYPTION BY PASSWORD 句を使用します。  
  
 REMOVE PRIVATE KEY オプションを指定すると、データベースから証明書の秘密キーが削除されます。 署名の確認に証明書が使用される場合、または秘密キーを必要としない [!INCLUDE[ssSB](../../includes/sssb-md.md)] シナリオの場合は、秘密キーを削除できます。 対称キーを保護する証明書の秘密キーを削除しないでください。 秘密キーは、証明書を使用して検証する必要がある追加のモジュールまたは文字列に署名するため、または証明書を使用して暗号化されている値を暗号化解除するため、復元される必要があります。   
  
 秘密キーがデータベース マスター キーを使って暗号化される場合は、暗号化解除のパスワードを指定する必要はありません。  
 
 秘密キーの暗号化に使用するパスワードを変更するには、FILE 句と BINARY 句のいずれも指定しないでください。
  
> [!IMPORTANT]  
>  データベースから秘密キーを削除する前には、必ず秘密キーの保存用コピーを作成してください。 詳細については、「[BACKUP CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/backup-certificate-transact-sql.md)」と「[CERTPRIVATEKEY &#40;Transact-SQL&#41;](../../t-sql/functions/certprivatekey-transact-sql.md)」を参照してください。  
  
 WITH PRIVATE KEY オプションは、包含データベースでは使用できません。  
  
## <a name="permissions"></a>アクセス許可  
 証明書に対する ALTER 権限が必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-removing-the-private-key-of-a-certificate"></a>A. 証明書の秘密キーを削除する  
  
```sql  
ALTER CERTIFICATE Shipping04   
    REMOVE PRIVATE KEY;  
GO  
```  
  
### <a name="b-changing-the-password-that-is-used-to-encrypt-the-private-key"></a>B. 秘密キーの暗号化に使用するパスワードを変更する  
  
```sql  
ALTER CERTIFICATE Shipping11   
    WITH PRIVATE KEY (DECRYPTION BY PASSWORD = '95hkjdskghFDGGG4%',  
    ENCRYPTION BY PASSWORD = '34958tosdgfkh##38');  
GO  
```  
  
### <a name="c-importing-a-private-key-for-a-certificate-that-is-already-present-in-the-database"></a>C. データベースに存在する証明書の秘密キーをインポートする  
  
```sql  
ALTER CERTIFICATE Shipping13   
    WITH PRIVATE KEY (FILE = 'c:\importedkeys\Shipping13',  
    DECRYPTION BY PASSWORD = 'GDFLKl8^^GGG4000%');  
GO  
```  
  
### <a name="d-changing-the-protection-of-the-private-key-from-a-password-to-the-database-master-key"></a>D. 秘密キーの保護をパスワードからデータベース マスター キーに変更する  
  
```sql  
ALTER CERTIFICATE Shipping15   
    WITH PRIVATE KEY (DECRYPTION BY PASSWORD = '95hk000eEnvjkjy#F%');  
GO  
```  
  
## <a name="see-also"></a>参照  
 [CREATE CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/create-certificate-transact-sql.md)  
 [DROP CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-certificate-transact-sql.md)  
 [BACKUP CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/backup-certificate-transact-sql.md)  
 [暗号化階層](../../relational-databases/security/encryption/encryption-hierarchy.md)  
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
 [CERTENCODED &#40;Transact-SQL&#41;](../../t-sql/functions/certencoded-transact-sql.md)  
 [CERTPRIVATEKEY &#40;Transact-SQL&#41;](../../t-sql/functions/certprivatekey-transact-sql.md)  
 [CERT_ID &#40;Transact-SQL&#41;](../../t-sql/functions/cert-id-transact-sql.md)  
 [CERTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/certproperty-transact-sql.md)  
  
  

