---
title: 「データベースのプロパティ」 ([ファイル] ページ) | Microsoft Docs
description: SQL Server Management Studio で、データベースのプロパティを表示または変更する方法と、新しいデータベースを作成する方法について説明します。
ms.custom: ''
ms.date: 03/09/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
f1_keywords:
- sql13.swb.databaseproperties.files.f1
ms.assetid: 3c030e51-db82-4b43-b1e5-8547ddd3de87
author: stevestein
ms.author: sstein
ms.openlocfilehash: 3a8068945e91b56537a16ddf87cebfc9aa27fb3c
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890647"
---
# <a name="database-properties-files-page"></a>「データベースのプロパティ」 ([ファイル] ページ)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  このページを使用すると、新しいデータベースを作成したり、選択したデータベースのプロパティを表示または変更したりできます。 このトピックは、既存のデータベースの **[データベースのプロパティ] ([ファイル] ページ)** 、および **[新しいデータベース] ([全般] ページ)** に該当します。  
  
## <a name="ui-element-list"></a>UI 要素の一覧  
 **データベース名**  
 データベースの名前を追加または表示します。  
  
 **所有者**  
 データベースの所有者を一覧から選択して指定します。  
  
 **フルテキスト インデックスを使用する**  
 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]ではフルテキスト インデックスが常に有効になっているため、このチェック ボックスはオンに設定され、変更することはできません。 詳細については、「 [フルテキスト検索](../../relational-databases/search/full-text-search.md)」を参照してください。  
  
 **データベース ファイル**  
 関連付けられたデータベースのデータベース ファイルを追加、表示、変更、または削除します。 データベース ファイルには、次のプロパティがあります。  
  
 **[論理名]**  
 ファイルの名前を入力または変更します。  
  
 **[ファイルの種類]**  
 ファイルの種類を一覧から選択します。 ファイルの種類は、 **データ**、 **ログ**、 **Filestream データ** のいずれかです。 既存のファイルの種類は変更できません。  
  
 メモリ最適化ファイル グループにファイル (コンテナー) を追加する場合は、 **FILESTREAM データ** を選択します。  
  
 FILESTREAM データ ファイル グループにファイル (コンテナー) を追加するには、FILESTREAM を有効にする必要があります。 FILESTREAM を有効にするには、 [[サーバーのプロパティ] ([詳細設定] ページ)](../../database-engine/configure-windows/server-properties-advanced-page.md) ダイアログ ボックスを使用します。  
  
 **[ファイル グループ]**  
 ファイルのファイル グループを一覧から選択します。 既定のファイル グループは PRIMARY です。 新しいファイル グループを作成するには、 **\<new filegroup>** を選択し、ファイル グループに関する情報を **[新しいファイル グループ]** ダイアログ ボックスに入力します。 また、 **[ファイル グループ]** ページで新しいファイル グループを作成することもできます。 既存のファイルのファイル グループは変更できません。  
  
 メモリ最適化ファイル グループにファイル (コンテナー) を追加すると、データベースのメモリ最適化ファイル グループ名が **[ファイル グループ]** フィールドに表示されます。  
  
 **[初期サイズ]**  
 ファイルの初期サイズをメガバイト単位で入力または変更します。 既定値は **[モデル]** データベースの値となります。  
  
 FILESTREAM ファイルの場合、このフィールドは無効です。  
  
 メモリ最適化ファイル グループのファイルの場合、このフィールドは変更できません。  
  
 **[自動拡張]**  
 ファイルの自動拡張プロパティを選択または表示します。 これらのプロパティは、ファイルが最大ファイル サイズに到達したときにファイルをどのように拡張するかを制御します。 自動拡張値を編集するには、目的のファイルの自動拡張プロパティの横にある編集ボタンをクリックし、 **[自動拡張の変更]** ダイアログ ボックスで値を変更します。 既定値は **model** データベースの値となります。  
  
 FILESTREAM ファイルの場合、このフィールドは無効です。  
  
 メモリ最適化ファイル グループのファイルの場合、このフィールド **[無制限]** にする必要があります。  
  
 **パス**  
 選択されているファイルのパスを表示します。 新しいファイルのパスを指定するには、ファイルのパスの横にある編集ボタンをクリックし、目的のフォルダーに移動します。 既存のファイルのパスは変更できません。  
  
 FILESTREAM ファイルの場合、このパスはフォルダーになります。 このフォルダーには、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] によって基になるファイルが作成されます。  
  
 **[ファイル名]**  
 ファイルの名前を表示します。  
  
 メモリ最適化ファイル グループのファイルを含めて、FILESTREAM ファイルの場合、このフィールドは無効です。  
  
 **追加**  
 データベースに新しいファイルを追加します。  
  
 **Remove**  
 選択されているファイルをデータベースから削除します。 空でないファイルは削除できません。 プライマリ データ ファイルおよびログ ファイルは削除できません。  
  
 ファイルの詳細については、「 [データベース ファイルとファイル グループ](../../relational-databases/databases/database-files-and-filegroups.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)  
  
  
