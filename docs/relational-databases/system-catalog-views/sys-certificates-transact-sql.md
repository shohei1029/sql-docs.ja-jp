---
description: sys.certificates (Transact-SQL)
title: sys. 証明書 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- certificates
- certificates_TSQL
- sys.certificates_TSQL
- sys.certificates
dev_langs:
- TSQL
helpviewer_keywords:
- sys.certificates catalog view
ms.assetid: e5046102-a65c-401e-b80d-05636884dec9
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: acd618039ca5aefe1f7b21007087840fa614dd04
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104736232"
---
# <a name="syscertificates-transact-sql"></a>sys.certificates (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベース内の証明書ごとに1行のデータを返します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|証明書の名前。 データベース内で一意です。|  
|**certificate_id**|**int**|証明書の ID。 データベース内で一意です。|  
|**principal_id**|**int**|この証明書を所有するデータベースプリンシパルの ID。|  
|**pvt_key_encryption_type**|**char(2)**|秘密キーの暗号化方法。<br /><br /> NA = 証明書に秘密キーはありません。<br /><br /> MK = 秘密キーはマスターキーによって暗号化されています<br /><br /> PW = 秘密キーは、ユーザー定義のパスワードによって暗号化されます。<br /><br /> SK = 秘密キーは、サービスマスターキーによって暗号化されます。|  
|**pvt_key_encryption_type_desc**|**nvarchar(60)**|秘密キーの暗号化方法の説明。<br /><br /> NO_PRIVATE_KEY<br /><br /> ENCRYPTED_BY_MASTER_KEY<br /><br /> ENCRYPTED_BY_PASSWORD<br /><br /> ENCRYPTED_BY_SERVICE_MASTER_KEY|  
|**is_active_for_begin_dialog**|**bit**|1の場合、この証明書は暗号化されたサービスダイアログを開始するために使用されます。|  
|**issuer_name**|**nvarchar (442)**|証明書の発行者の名前。|  
|**cert_serial_number**|**nvarchar (64)**|証明書のシリアル番号。|  
|**sid**|**varbinary(85)**|この証明書のログイン SID。|  
|**string_sid**|**nvarchar(128)**|この証明書のログイン SID の文字列表現|  
|**subject**|**nvarchar (4000)**|証明書のサブジェクト。|  
|**expiry_date**|**datetime**|証明書の有効期限が切れた場合。|  
|**start_date**|**datetime**|証明書が有効になったとき。|  
|**thumbprint**|**varbinary(32)**|証明書の SHA-1 ハッシュ。 SHA-1 ハッシュはグローバルに一意です。|  
|**attested_by**|**nvarchar(260)**|システムでのみ使用されます。|  
|**pvt_key_last_backup_date**|**datetime**|証明書の秘密キーが最後にエクスポートされた日付と時刻。|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [セキュリティ カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [暗号化階層](../../relational-databases/security/encryption/encryption-hierarchy.md)   
 [CREATE CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/create-certificate-transact-sql.md)  
  
  
