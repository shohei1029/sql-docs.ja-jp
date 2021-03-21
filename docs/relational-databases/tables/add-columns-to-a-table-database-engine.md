---
description: テーブルへの列の追加 (データベース エンジン)
title: テーブルへの列の追加 (データベース エンジン) | Microsoft Docs
ms.custom: ''
ms.date: 10/27/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- inserting columns
- columns [SQL Server], adding
- adding columns
ms.assetid: abeb8d52-d562-4e29-9e1e-2923ae874859
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1f10eec1fac96247e2d909c6014fcc98c5568d91
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754962"
---
# <a name="add-columns-to-a-table-database-engine"></a>テーブルへの列の追加 (データベース エンジン)

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

この記事では、[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] で [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../includes/tsql-md.md)] を使用してテーブルに新しい列を追加する方法について説明します。

## <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに

### <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 制限事項と制約事項

 ALTER TABLE ステートメントを使用してテーブルに列を追加すると、これらの列は自動的にテーブルの最後に追加されます。 テーブル内の列を特定の順序で表示する場合は、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]を使用します。 ただし、これはデータベース デザインのベスト プラクティスではないことに注意してください。 列が返される順序をアプリケーションおよびクエリ レベルで指定することをお勧めします。 テーブルで定義されている順序に基づいて、すべての列が予想される順序で返されるようにするために、SELECT * の使用に依存しないでください。 クエリまたはアプリケーションでは必ず、表示される順序で列の名前を指定してください。

### <a name="security"></a><a name="Security"></a> セキュリティ

#### <a name="permissions"></a><a name="Permissions"></a> Permissions

テーブルに対する ALTER 権限が必要です。

## <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用

### <a name="to-insert-columns-into-a-table-with-table-designer"></a>テーブル デザイナーでテーブルに列を挿入するには

1. **オブジェクト エクスプローラー** で、列を追加するテーブルを右クリックし、 **[デザイン]** をクリックします。
2. **[列名]** 列内の最初の空白セルをクリックします。
3. セルに列名を入力します。 [列名] には値が必要です。
4. Tab キーを押して **[データ型]** セルに移動し、ドロップダウンからデータ型を選択します。

   データ型は必須の値です。選択しない場合は既定の値が割り当てられます。

   > [!NOTE]
   >  この既定の値は、 **[データベース ツール]** の下の **[オプション]** ダイアログ ボックスで変更できます。

5. 次に **[列のプロパティ]** タブで他の列のプロパティを定義します。

    > [!NOTE]
    > 新しい列の作成時には、列プロパティの既定の値が追加されますが、 **[列のプロパティ]** タブで値を変更できます。

6. 列の追加が完了したら、 **[ファイル]** メニューで [ _<テーブル名>_ **を保存**] を選択します。
  
## <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用
  
### <a name="to-insert-columns-into-a-table"></a>テーブルに列を挿入するには  
  
次の例では、 `dbo.doc_exa`テーブルに列を 2 つ追加する方法を示します。

```sql
ALTER TABLE dbo.doc_exa ADD column_b VARCHAR(20) NULL, column_c INT NULL ;
```

#### <a name="for-more-information-see-alter-table-40transact-sql41"></a><a name="FollowUp"></a> 詳細については、「[ALTER TABLE (Transact-SQL)](../../t-sql/statements/alter-table-transact-sql.md)」を参照してください。
