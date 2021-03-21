---
description: BACKUP CERTIFICATE (Transact-SQL)
title: BACKUP CERTIFICATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/16/2020
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DUMP_CERTIFICATE_TSQL
- BACKUP CERTIFICATE
- sql13.swb.exportcertificate.f1
- DUMP CERTIFICATE
- BACKUP_CERTIFICATE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- encryption [SQL Server], certificates
- decryption [SQL Server], certificates
- exporting certificates
- certificates [SQL Server], exporting
- BACKUP CERTIFICATE statement
- backing up certificates [SQL Server]
- decryption [SQL Server]
- cryptography [SQL Server], certificates
ms.assetid: 509b9462-819b-4c45-baae-3d2d90d14a1c
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||>=sql-server-2016||>=sql-server-linux-2017'
ms.openlocfilehash: 942b16b089c4f5324073c679345bd88ad77e4d60
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104740012"
---
# <a name="backup-certificate-transact-sql"></a>BACKUP CERTIFICATE (Transact-SQL)
[!INCLUDE [sql-pdw](../../includes/applies-to-version/sql-pdw.md)]

  証明書をファイルにエクスポートします。  
  
 ![リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "[リンク] アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server  
  
BACKUP CERTIFICATE certname TO FILE = 'path_to_file'  
    [ WITH PRIVATE KEY   
      (   
        FILE = 'path_to_private_key_file' ,  
        ENCRYPTION BY PASSWORD = 'encryption_password'   
        [ , DECRYPTION BY PASSWORD = 'decryption_password' ]   
      )   
    ]  
```  
  
   
```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
BACKUP CERTIFICATE certname TO FILE ='path_to_file'  
      WITH PRIVATE KEY   
      (   
        FILE ='path_to_private_key_file',  
        ENCRYPTION BY PASSWORD ='encryption_password'   
      )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *certname*  
 バックアップする証明書の名前。

 TO FILE = '*path_to_file*'  
 証明書を保存するファイルの完全なパスを、ファイル名を含めて指定します。 このパスには、ローカル パスまたはネットワーク上の場所を示す UNC パスを指定できます。 ファイル名のみを指定すると、ファイルがインスタンスの既定のユーザー データ フォルダー ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] DATA フォルダーであってもなくても構いません) に保存されます。 SQL Server Express LocalDB の場合、インスタンスの既定のユーザー データ フォルダーは、インスタンスを作成したアカウントの `%USERPROFILE%` 環境変数で指定されたパスです。  

 WITH PRIVATE KEY は、証明書の秘密キーをファイルに保存するように指定します。 この句は省略可能です。

 FILE = '*path_to_private_key_file*'  
 秘密キーを保存するファイルの完全なパスを、ファイル名を含めて指定します。 このパスには、ローカル パスまたはネットワーク上の場所を示す UNC パスを指定できます。 ファイル名のみを指定すると、ファイルがインスタンスの既定のユーザー データ フォルダー ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] DATA フォルダーであってもなくても構いません) に保存されます。 SQL Server Express LocalDB の場合、インスタンスの既定のユーザー データ フォルダーは、インスタンスを作成したアカウントの `%USERPROFILE%` 環境変数で指定されたパスです。  

 ENCRYPTION BY PASSWORD = '*encryption_password*'  
 バックアップ ファイルに秘密キーを書き込む前に、キーを暗号化するため使用するパスワードを指定します。 パスワードに対しては、複雑性がチェックされます。  
  
 DECRYPTION BY PASSWORD = '*decryption_password*'  
 秘密キーをバックアップする前に、秘密キーの暗号化を解除するため使用するパスワードを指定します。 証明書がマスター キーによって暗号化されている場合、この引数は必要ありません。 
  
## <a name="remarks"></a>解説  
 データベースで秘密キーがパスワードによって暗号化されている場合は、暗号化解除パスワードを指定する必要があります。  
  
 秘密キーをファイルにバックアップする場合は、暗号化が必要です。 ファイル内の秘密キーの保護に使用するパスワードは、データベースで証明書の秘密キーの暗号化に使用するパスワードとは異なります。  

 秘密キーは、PVK ファイル形式で保存されます。

 秘密キーを使用してまたは使用せずにバックアップした証明書を復元するには、[CREATE CERTIFICATE](../../t-sql/statements/create-certificate-transact-sql.md) ステートメントを使用します。
 
 データベース内の既存の証明書に秘密キーを復元するには、[ALTER CERTIFICATE](../../t-sql/statements/alter-certificate-transact-sql.md) ステートメントを使用します。
 
 バックアップを実行すると、ファイルは SQL Server インスタンスのサービス アカウントに対して ACL されます。 別のアカウントで実行しているサーバーに証明書を復元する必要がある場合は、ファイルに対するアクセス許可を調整して、新しいアカウントでファイルの読み取りを実行できるようにする必要があります。 
  
## <a name="permissions"></a>アクセス許可  
 証明書に対する CONTROL 権限と、秘密キーの暗号化に使用するパスワードの情報が必要です。 証明書のパブリックの部分だけをバックアップする場合、このコマンドには証明書に関する権限の一部が必要です。また、呼び出し元に対して証明書の VIEW 権限が拒否されていないことも必要になります。  
  
## <a name="examples"></a>例  
  
### <a name="a-exporting-a-certificate-to-a-file"></a>A. 証明書をファイルにエクスポートする  
 次の例では、証明書をファイルにエクスポートします。  
  
```sql
BACKUP CERTIFICATE sales05 TO FILE = 'c:\storedcerts\sales05cert';  
GO  
```  
  
### <a name="b-exporting-a-certificate-and-a-private-key"></a>B. 証明書と秘密キーをエクスポートする  
 次の例では、バックアップする証明書の秘密キーをパスワード `997jkhUbhk$w4ez0876hKHJH5gh` で暗号化します。  
  
```sql
BACKUP CERTIFICATE sales05 TO FILE = 'c:\storedcerts\sales05cert'  
    WITH PRIVATE KEY ( FILE = 'c:\storedkeys\sales05key' ,   
    ENCRYPTION BY PASSWORD = '997jkhUbhk$w4ez0876hKHJH5gh' );  
GO  
```  
  
### <a name="c-exporting-a-certificate-that-has-an-encrypted-private-key"></a>C. 秘密キーが暗号化されている証明書をエクスポートする  
 次の例では、データベースで証明書の秘密キーが暗号化されています。 この秘密キーは、パスワード `9875t6#6rfid7vble7r` を使って暗号化を解除する必要があります。 証明書をバックアップ ファイルに保存するときには、秘密キーをパスワード `9n34khUbhk$w4ecJH5gh` で暗号化します。  
  
```sql
BACKUP CERTIFICATE sales09 TO FILE = 'c:\storedcerts\sales09cert'   
    WITH PRIVATE KEY ( DECRYPTION BY PASSWORD = '9875t6#6rfid7vble7r' ,  
    FILE = 'c:\storedkeys\sales09key' ,   
    ENCRYPTION BY PASSWORD = '9n34khUbhk$w4ecJH5gh' );  
GO  
```  
  
## <a name="see-also"></a>参照  
 [CREATE CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/create-certificate-transact-sql.md)   
 [ALTER CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-certificate-transact-sql.md)   
 [DROP CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-certificate-transact-sql.md)  
 [CERTENCODED &#40;Transact-SQL&#41;](../../t-sql/functions/certencoded-transact-sql.md)  
 [CERTPRIVATEKEY &#40;Transact-SQL&#41;](../../t-sql/functions/certprivatekey-transact-sql.md)  
 [CERT_ID &#40;Transact-SQL&#41;](../../t-sql/functions/cert-id-transact-sql.md)  
 [CERTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/certproperty-transact-sql.md)  
  
  

