---
title: プログラムによるパスワードの変更 | Microsoft Docs
description: OLE DB Driver for SQL Server では、OLE DB ドライバーのプログラムおよび SQL Server のサイン イン ダイアログ ボックスを使用したパスワードの期限切れの処理がサポートされています。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- data access [OLE DB Driver for SQL Server], password expiration
- OLE DB Driver for SQL Server, passwords
- passwords [SQL Server], expiration
- MSOLEDBSQL, password expiration
- expiration [OLE DB Driver for SQL Server]
- passwords [SQL Server], modifying
- expired passwords [OLE DB Driver for SQL Server]
- OLE DB Driver for SQL Server, password expiration
- modifying passwords
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: ca8bfec9c9404f2912d288cb434aa064200fea45
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742852"
---
# <a name="changing-passwords-programmatically"></a>プログラムによるパスワードの変更
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] より前のリリースでは、ユーザーのパスワードの有効期限が切れたとき、そのパスワードをリセットできるのは管理者だけでした。 [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] 以降、OLE DB Driver for SQL Server では、OLE DB Driver のプログラムから、または **[SQL Server ログイン]** ダイアログ ボックスでの変更によってパスワードの期限切れを処理することができます。  
  
> [!NOTE]  
>  可能であれば、実行時にユーザーの資格情報を入力し、それらの資格情報を永続的な形式で保存しないように求めるメッセージが表示されます。 資格情報を保持する必要がある場合は、[Win32 Crypto API](/windows/win32/seccrypto/cryptography-reference) を使用して暗号化してください。 パスワードの使用に関する詳細については、「[強力なパスワード](../../../relational-databases/security/strong-passwords.md)」を参照してください。  
  
## <a name="sql-server-login-error-codes"></a>SQL Server ログイン エラー コード  
 認証の問題により接続できない場合、アプリケーションでは次のいずれかの SQL Server エラー コードを使用して、診断と復旧に役立てることができます。  
  
|SQL Server エラー コード|エラー メッセージ|  
|---------------------------|-------------------|  
|15113|ユーザー '%.*ls' はログインできませんでした。理由: パスワードの検証に失敗しました。 アカウントはロックアウトされています。|  
|18463|ユーザー '%.*ls' はログインできませんでした。 理由: パスワードを変更できませんでした。 この時点ではパスワードを使用できません。|  
|18464|ユーザー '%.*ls' はログインできませんでした。 理由: パスワードを変更できませんでした。 このパスワードは短すぎるので、Windows ポリシーの要件を満たしません。|  
|18465|ユーザー '%.*ls' はログインできませんでした。 理由: パスワードを変更できませんでした。 パスワードは長すぎるため、ポリシー要件を満たしません。|  
|18466|ユーザー '%.*ls' はログインできませんでした。 理由: パスワードを変更できませんでした。 このパスワードはあまり複雑ではないので、Windows のポリシー要件を満たしません。|  
|18467|ユーザー '%.*ls' はログインできませんでした。 理由: パスワードを変更できませんでした。 パスワードがパスワード フィルター DLL の要件を満たしていません。|  
|18468|ユーザー '%.*ls' はログインできませんでした。 理由: パスワードを変更できませんでした。 パスワードの検証で予期しないエラーが発生しました。|  
|18487|ユーザー '%.*ls' はログインできませんでした。 理由: このアカウントのパスワードの有効期限が切れています。|  
|18488|ユーザー '%.*ls' はログインできませんでした。 理由: このアカウントのパスワードを変更する必要があります。|  
  
## <a name="ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server  
 OLE DB Driver for SQL Server では、パスワードの期限切れの処理がユーザー インターフェイスおよびプログラムからサポートされます。  
  
### <a name="ole-db-user-interface-password-expiration"></a>OLE DB ユーザー インターフェイスによるパスワード期限切れの処理  
 OLE DB Driver for SQL Server では、 **[SQL Server ログイン]** ダイアログ ボックスに行われた変更によって、パスワードの期限切れの処理がサポートされます。 DBPROP_INIT_PROMPT の値を DBPROMPT_NOPROMPT に設定すると、パスワードの有効期限が切れている場合に、最初の接続試行が失敗します。  
  
 DBPROP_INIT_PROMPT を DBPROMPT_NOPROMPT 以外の値に設定している場合は、パスワードの有効期限が切れているかどうかに関係なく、 **[SQL Server ログイン]** ダイアログ ボックスが表示されます。 ユーザーは、 **[オプション]** をクリックし、 **[パスワードの変更]** チェック ボックスをオンにしてパスワードを変更できます。  
  
 パスワードの有効期限が切れている場合にユーザーが [OK] をクリックすると、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] からユーザーに **[パスワードの変更]** ダイアログ ボックスを使用して新しいパスワードを入力し、確認することを要求されます。  
  
#### <a name="ole-db-prompt-behavior-and-locked-accounts"></a>OLE DB プロンプトの動作とロックされたアカウント  
 アカウントがロックされていることにより、接続に失敗することがあります。 **[SQL Server ログイン]** ダイアログ ボックスが表示された後にこの現象が発生する場合は、サーバー エラー メッセージが表示され、接続試行が中止されます。 また、 **[パスワードの変更]** ダイアログ ボックスが表示された後にユーザーが古いパスワードに無効な値を入力すると、この現象が発生することもあります。 この場合も同じエラー メッセージが表示され、接続試行が中止されます。  
  
#### <a name="ole-db-connection-pooling-password-expiration-and-locked-accounts"></a>OLE DB 接続プーリング、パスワードの期限切れ、およびロックされたアカウント  
 接続プール内で接続がアクティブな状態になっている間に、アカウントがロックされたり、そのアカウントのパスワードの有効期限が切れたりすることがあります。 サーバーでは、期限切れのパスワードとロックされたアカウントを 2 回チェックします。 最初のチェックは、初回接続時に行われます。 2 回目のチェックは、接続のリセット時、接続がプールから取り除かれるときに行われます。  
  
 リセットの試行が失敗すると、その接続はプールから削除され、エラーが返されます。  
  
### <a name="ole-db-programmatic-password-expiration"></a>OLE DB プログラムによるパスワード期限切れの処理  
 OLE DB Driver for SQL Server では、DBPROPSET_SQLSERVERDBINIT プロパティ セットに追加された SSPROP_AUTH_OLD_PASSWORD (型 VT_BSTR) プロパティによって、パスワードの期限切れがサポートされます。  
  
 既存の "Password" プロパティは DBPROP_AUTH_PASSWORD を参照し、新しいパスワードを保存する場合に使用されます。  
  
> [!NOTE]  
>  接続文字列の "Old Password" プロパティは SSPROP_AUTH_OLD_PASSWORD に設定され、現在の (または期限切れの) パスワードが設定されます。このパスワードをプロバイダー文字列のプロパティ経由で使用することはできません。  
  
 プロバイダーでは、このプロパティの値が保存されません。 このプロパティを設定すると新しい接続が行われるので、プロバイダーでは最初の接続に接続プールが使用されません。 パスワード変更が正常に行われた場合でも、現在の接続にはまだ古いパスワードが含まれているので再利用できません。古いパスワードとは、変更後に無効になるパスワードのことです。 また、ログインに成功すると、このプロパティはクリアされます。 クリア後に古いパスワードを取得しようとすると、VT_EMPTY が返されます。  
  
> [!NOTE]  
>  SSPROP_AUTH_OLD_PASSWORD は、パスワードの有効期限が切れたときにしか使用されないので、保存しないでください。  
  
 "Old Password" プロパティが設定されると、プロバイダーでは、パスワードの変更が試行されていることが想定されます。ただし、Windows 認証も指定されている場合は除きます。この場合は、常に Windows 認証が優先されるためです。  
  
 Windows 認証を使用している場合、古いパスワードを REQUIRED と指定するか OPTIONAL と指定するかによって、それぞれ DB_E_ERRORSOCCURRED または DB_S_ERRORSOCCURRED のいずれかが返され、*dwStatus* に状態値として DBPROPSTATUS_CONFLICTINGBADVALUE が返されます。 これは、**IDBInitialize::Initialize** を呼び出したときに検出されます。  
  
 パスワード変更の試行が予期せず失敗すると、サーバーからエラー コード 18468 が返されます。 接続試行からは標準の OLE DB エラーが返されます。  
  
 DBPROPSET_SQLSERVERDBINIT プロパティ セットの詳細については、「[初期化プロパティと承認プロパティ](../../oledb/ole-db-data-source-objects/initialization-and-authorization-properties.md)」を参照してください。  

  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)  
  
