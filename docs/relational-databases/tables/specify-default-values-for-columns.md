---
description: 列の既定値の指定
title: 列の既定値の指定 | Microsoft Docs
ms.custom: ''
ms.date: 03/17/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- columns [SQL Server], defaults
- default values
ms.assetid: 64514aed-b846-407b-992e-cf813f9a1a91
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: dc4f26877483a1213b1c5ecd3363dd1ba0c75fee
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104738102"
---
# <a name="specify-default-values-for-columns"></a>列の既定値の指定

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を使用して、テーブルの列に入力される既定値を指定できます。 既定値を設定するには、ユーザー インターフェイスのオブジェクト エクスプローラーを使用するか、[!INCLUDE[tsql](../../includes/tsql-md.md)] を送信します。

列に既定値を割り当てなかった場合、ユーザーが列に何も入力しないと、次のようになります。

- NULL 値を許可するオプションを設定している場合は、列に NULL が挿入されます。

- NULL 値を許可するオプションを設定していない場合は、列が空白のままになります。ただし、列に値を指定しないと、行を保存できません。

## <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 制限事項と制約事項

始める前に、次の制限事項と制約事項に注意してください。

- バインドされた既定値 (かっこなしで表示されている値) を **[既定値]** フィールドに入力した値で置き換える場合は、既定値のバインドを解除し、新しい既定値で置き換えるかどうかを確認するメッセージが表示されます。

- 文字列を入力するには、値を単一引用符 (') で囲みます。二重引用符 (") では囲まないでください。二重引用符は、二重引用符で囲む必要がある識別子のために予約されています。

- 数値の既定値を入力するには、引用符で囲まずに番号を入力します。

- オブジェクトまたは関数を入力するには、引用符で囲まずにオブジェクト名または関数名を入力します。

### <a name="security-permissions"></a><a name="Security"></a> セキュリティのアクセス許可

この記事で説明するアクションには、テーブルに対する ALTER アクセス許可が必要です。

## <a name="use-ssms-to-specify-a-default"></a><a name="SSMSProcedure"></a> SSMS を使用して既定値を指定する

オブジェクト エクスプローラーを使用して、テーブルの列の既定値を指定できます。

### <a name="object-explorer"></a>オブジェクト エクスプローラー

1. **オブジェクト エクスプローラー** で、有効桁数を変更する列が含まれているテーブルを右クリックし、 **[デザイン]** をクリックします。

2. 既定値を指定する列を選択します。

3. **[列のプロパティ]** タブで、 **[既定値またはバインド]** プロパティに新たな既定値を入力します。

   > [!NOTE]
   > 数値の既定値を入力するには、数値を入力します。 オブジェクトまたは関数の場合は、その名前を入力します。 英数字の場合は、その値を単一引用符で囲んで入力します。

4. **[ファイル]** メニューの **[** _<テーブル名>_ を保存] をクリックします。

## <a name="use-transact-sql-to-specify-a-default"></a><a name="TsqlProcedure"></a> Transact-SQL を使用して既定値を指定する

SSMS を使用して T-SQL を送信することにより、さまざまな方法で列の既定値を指定できます。

### <a name="alter-table-t-sql"></a>ALTER TABLE (T-SQL)

1. **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]のインスタンスに接続します。

2. [標準] ツール バーの **[新しいクエリ]** をクリックします。

3. 次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。

   ```sql
   CREATE TABLE dbo.doc_exz (column_a INT, column_b INT); -- Allows nulls.
   GO
   INSERT INTO dbo.doc_exz (column_a) VALUES (7);
   GO
   ALTER TABLE dbo.doc_exz
     ADD CONSTRAINT DF_Doc_Exz_Column_B
     DEFAULT 50 FOR column_b;
   GO
   ```

<!--
The following two T-SQL code examples were offered by 'nycdotnet' (Steve) via public PR 1660, Feb 2019.
-->

### <a name="create-table-t-sql"></a>CREATE TABLE (T-SQL)

```sql
    CREATE TABLE dbo.doc_exz (
      column_a INT,
      column_b INT DEFAULT 50);
```

### <a name="named-constraint-t-sql"></a>名前付き制約 (T-SQL)

```sql
    CREATE TABLE dbo.doc_exz (
      column_a INT,
      column_b INT CONSTRAINT DF_Doc_Exz_Column_B DEFAULT 50);
```

詳細については、「[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)」を参照してください。
