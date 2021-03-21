---
title: プリンシパルに対する権限の許可 | Microsoft Docs
description: SQL Server Management Studio または Transact-SQL を使用して SQL Server のプリンシパルに権限を許可する方法、およびベスト プラクティスについて説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- Grant permission to a principal
ms.assetid: 4107389d-05b6-4aa3-9fa8-95b40cdf05dc
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 78c6fc0078ff91190a0211a902239a5aa0cf1127
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750392"
---
# <a name="grant-a-permission-to-a-principal"></a>プリンシパルに対する権限の許可
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  このトピックでは、 [!INCLUDE[ssnoversion](../../../includes/ssnoversion-md.md)] または [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] を使用して [!INCLUDE[tsql](../../../includes/tsql-md.md)]でプリンシパルに権限を与える方法について説明します。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [制限事項と制約事項](#Restrictions)  
  
     [Security](#Security)  
  
-   **プリンシパルに権限を与える方法:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 制限事項と制約事項  
 権限の管理を容易にする次のベスト プラクティスを考慮してください。  
  
-   個々のログインまたはユーザーではなく、ロールに権限を与えます。 ユーザーが入れ替わった場合、そのユーザーをロールから削除し、新しいユーザーをロールに追加します。 ロールに関連付けられている多くの権限が自動的に新しいユーザーに与えられます。 組織内の複数のユーザーが同じ権限を必要とする場合は、それぞれの権限をロールに追加します。そうすることで、それぞれのユーザーに同じ権限が与えられます。  
  
-   類似するセキュリティ保護可能なリソース (テーブル、ビュー、およびプロシージャ) をスキーマによって所有されるように構成した後、スキーマに対して権限を与えます。 たとえば、給与スキーマは、さまざまなテーブル、ビュー、およびストアド プロシージャを所有できます。 そのスキーマへのアクセスを許可することにより、給与の機能を実行するために必要なすべての権限を同時に与えることができます。 権限を付与できるセキュリティ保護可能なリソースの詳細については、「 [Securables](../../../relational-databases/security/securables.md)」を参照してください。  
  
###  <a name="security"></a><a name="Security"></a> セキュリティ  
  
####  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 権限の許可者 (または AS オプションで指定されたプリンシパル) は、GRANT OPTION によって与えられた権限を保持しているか、権限が暗黙的に与えられる上位の権限を保持している必要があります。 **sysadmin** 固定サーバー ロールのメンバーは、すべての権限を許可できます。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="to-grant-permission-to-a-principal"></a>プリンシパルに権限を与えるには  
  
1.  オブジェクト エクスプローラーで、権限を与えるオブジェクトが格納されているデータベースを展開します。  
  
    > [!NOTE]  
    >  次の手順は、ストアド プロシージャに対する権限の付与方法を説明していますが、同様の手順で、テーブル、ビュー、関数、およびセキュリティ保護可能なリソースに対して権限を追加できます。 詳細については、「[GRANT &#40;Transact-SQL&#41;](../../../t-sql/statements/grant-transact-sql.md)」を参照してください。  
  
2.  **[プログラミング]** フォルダーを展開します。  
  
3.  **[ストアド プロシージャ]** フォルダーを展開します。  
  
4.  ストアド プロシージャを右クリックし、 **[プロパティ]** をクリックします。  
  
5.  **[ストアド プロシージャのプロパティ - _stored\_procedure\_name_]** ダイアログ ボックスの [ページの選択] で、 **[権限]** を選択します。 このページを使用して、ユーザーまたはロールをストアド プロシージャに追加し、追加したユーザーまたはロールが所有する権限を指定します。  
  
6.  完了したら、 **[OK]** をクリックします。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
  
#### <a name="to-grant-permission-to-a-principal"></a>プリンシパルに権限を与えるには  
  
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    -- Grants EXECUTE permission on stored procedure HumanResources.uspUpdateEmployeeHireInfo to an application role called Recruiting11.   
    USE AdventureWorks2012;  
    GO  
    GRANT EXECUTE ON OBJECT::HumanResources.uspUpdateEmployeeHireInfo  
        TO Recruiting11;  
    GO  
    ```  
  
 詳細については、「[GRANT &#40;Transact-SQL&#41;](../../../t-sql/statements/grant-transact-sql.md)」と「[GRANT Object Permissions &#40;Transact-SQL&#41;](../../../t-sql/statements/grant-object-permissions-transact-sql.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [プリンシパル &#40;データベース エンジン&#41;](../../../relational-databases/security/authentication-access/principals-database-engine.md)  
  
  
