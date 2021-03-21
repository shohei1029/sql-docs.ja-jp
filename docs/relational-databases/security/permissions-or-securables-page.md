---
title: '[権限] ページまたは [セキュリティ保護可能なリソース] ページ | Microsoft Docs'
description: SQL Server の [権限] ページまたは [セキュリティ保護可能なリソース] ページを使用して、セキュリティ保護可能なリソースに対するアクセス許可を表示または設定します。
ms.custom: ''
ms.date: 01/07/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- sql13.swb.common.permissions.f1
- sql13.swb.SecurableAndEffectPermissions.f1
- sql13.swb.common.columnperm.f1
- sql13.swb.availabilitygroupproperties.permission.f1
- sql13.swb.SecurableAndEffectivePermission.f1
ms.assetid: b3bf077a-bec2-4161-ac0c-460586199906
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: bc8f417dba29049a5845fe450eea82c8f753a5a1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748042"
---
# <a name="permissions-or-securables-page"></a>[権限] ページまたは [セキュリティ保護可能なリソース] ページ
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  **[権限]** ページまたは **[セキュリティ保護可能なリソース]** ページを使用すると、セキュリティ保護可能なリソースに対する権限を表示または設定できます。 このページは、さまざまな場面で開くことができます。 このページの内容は、ページを開くときの状況やページに含まれているアイテムによって多少異なる場合があります。 ページの先頭にあるグリッドは、ページを開いたときに設定されます。それ以外では、空になる場合があります。 アイテムを上のグリッドに追加するには、 **[検索]** をクリックします。 上のグリッドでアイテムを選択した後、 **[明示的]** タブで適切な権限を設定します。集計された権限を表示するには、 **[有効]** タブを使用します。  
  
 セキュリティ保護可能なリソースとプリンシパルの有効な組み合わせの詳細については、「[GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)」に記載されている、セキュリティ保護可能なリソース別の構文に関するリンク先を参照してください。 詳細については、「 [セキュリティ保護可能](../../relational-databases/security/securables.md)」を参照してください。  
  
## <a name="page-header"></a>ページ ヘッダー  
 **[権限]** ページまたは **[セキュリティ保護可能なリソース]** ページのヘッダーは、セキュリティ保護可能なリソースまたはプリンシパルによって異なります。 ここには、アイテムの名前など、アイテムに関連する情報が表示されます。  
  
## <a name="upper-grid"></a>上のグリッド  
 上のグリッドには、権限を設定できるアイテムが 1 つ以上表示されます。 このダイアログ ボックスには、上のグリッドに追加するオブジェクトまたはプリンシパルを選択するための **[検索]** ボタンがあります。 グリッドの名前には、 **[セキュリティ保護可能なリソース]** 、または 1 つ以上の種類のセキュリティ保護可能なリソースやプリンシパルが表示される場合があります。 上のグリッドに表示される列は、プリンシパルまたはセキュリティ保護可能なリソースによって異なります。  
  
 **名前**  
 グリッドに追加される各プリンシパルまたはセキュリティ保護可能なリソースの名前です。  
  
 **Type**  
 各アイテムの種類について説明します。  
  
## <a name="explicit-tab"></a>[明示的] タブ  
 **[明示的]** タブには、上のグリッドで選択されているセキュリティ保護可能なリソースに適用できる権限が表示されます。 権限を構成するには、 **[許可]** (または **[許容]** )、 **[許可の有無]** 、または **[拒否]** チェック ボックスをオンあるいはオフにします。 すべての明示的な権限に対してすべてのオプションを使用できるわけではありません。  
  
 **アクセス許可**  
 権限の名前です。  
  
 **Grantor**  
 権限を許可したプリンシパルです。  
  
 **Grant**  
 この権限をログインに対して許可する場合はオンにします。 この権限を取り消す場合はオフにします。  
  
 **[許可の有無]**  
 一覧表示された権限に対する WITH GRANT オプションの状態を反映します。 このボックスは読み取り専用です。 この権限を適用するには、 [GRANT](../../t-sql/statements/grant-transact-sql.md) ステートメントを使用します。  
  
 **Deny**  
 この権限をログインに対して拒否する場合はオンにします。 この権限を取り消す場合はオフにします。  
  
 **[列権限]**  
 列を含むオブジェクト (テーブル、ビュー、テーブル値関数など) の場合、 **[列権限]** ボタンをクリックすると、 **[列権限]** ダイアログ ボックスが表示されます。 このダイアログ ボックスでは、テーブルまたはビューの各列に対して **[許可]** 、 **[許容]** または **[拒否]** の権限を設定できます。 このオプションは、すべての種類のオブジェクトや権限で使用できるとは限りません。  
  
## <a name="effective-tab"></a>[有効] タブ  
 プリンシパルがセキュリティ保護可能なリソースに関連付けた権限は、複数の異なるプリンシパルに設定されている権限から継承されている場合があります。 たとえば、ログインは個別に権限が与えられるだけでなく、グループのメンバーとしても権限が与えられる場合があります。 **[有効]** タブでは、明示的な権限と、グループまたはロールのメンバーシップから受け取る権限を組み合わせた結果が表示されます。 許可の権限は集計されます。 また、すべての許可の権限を拒否の権限がオーバーライドします。  
  
 **アクセス許可**  
 権限の名前です。  
  
 **列**  
 権限の影響を受ける列の名前です。  
  
## <a name="see-also"></a>参照  
 [データベース レベルのロール](../../relational-databases/security/authentication-access/database-level-roles.md)   
 [SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター](../../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)  
  
  
