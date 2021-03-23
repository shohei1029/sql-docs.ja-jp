---
description: システム バージョン管理されたテンポラル テーブルの履歴データの保有期間管理
title: システム バージョン管理されたテンポラル テーブルの履歴データの管理
ms.custom: seo-lt-2019
ms.date: 05/18/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
ms.assetid: 7925ebef-cdb1-4cfe-b660-a8604b9d2153
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 3343eda333415def037bd052b9593262fa43e88a
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890663"
---
# <a name="manage-retention-of-historical-data-in-system-versioned-temporal-tables"></a>システム バージョン管理されたテンポラル テーブルの履歴データの保有期間管理


[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]


テンポラル テーブルがシステム バージョン管理されているとき、履歴データによりデータベースのサイズが通常のテーブルより増えることがあります。特に、次の条件下で当てはまります。

- 長期間にわたり履歴データを保持する。
- 大量のデータを定期的に更新または削除する。

大量の履歴データが増加を続けると、ストレージ費用と一時的なクエリ実行による負荷の両方に起因し、問題が引き起こされる可能性があります。 したがって、データの保持ポリシーを作成して履歴テーブルのデータを管理することが、あらゆるテンポラル テーブルのライフサイクルの計画と管理において重要な要素となります。

## <a name="data-retention-management-for-history-table"></a>履歴テーブルのデータ保有期間管理

テンポラル テーブルのデータ保有期間の管理は、テンポラル テーブルごとに必要な保有期間を決定することから始まります。 ほとんどの場合、保有期間ポリシーは、テンポラル テーブルを利用する用途のビジネス ロジックの不可欠要素であると見なすべきです。 たとえば、データ監査時のアプリケーションやタイム トラベルのシナリオでは、オンライン クエリ実行のために履歴データを利用できる期間について、要件が確定されます。

データの保有期間を決定したら、次に、履歴データの保存方法、保存場所、保有期間を超えた履歴データの削除方法など、履歴データを管理するための計画を立てます。 次の 4 つの手法で、テンポラル履歴テーブルの履歴データを管理できます。

- [Stretch Database](#using-stretch-database-approach)
- [テーブル パーティション分割](#using-table-partitioning-approach)
- [カスタム クリーンアップ スクリプト](#using-custom-cleanup-script-approach)
- [保有期間ポリシー](#using-temporal-history-retention-policy-approach)

 いずれの手法でも、履歴データを移行またはクリーンアップするためのロジックは、現在のテーブルの期間終了に相当する列に基づきます。 各行の期間終了値により、そのバージョンの行が "閉じられる"、つまり、履歴テーブルに入るタイミングが決定されます。 たとえば、条件 `SysEndTime < DATEADD (DAYS, -30, SYSUTCDATETIME ())` では、1 か月以上経過した履歴データは履歴テーブルから削除されます。

> [!NOTE]
> このトピックの例はこの[テンポラル テーブル例](creating-a-system-versioned-temporal-table.md)を利用しています。

## <a name="using-stretch-database-approach"></a>Stretch Database 手法の利用

> [!NOTE]
> Stretch Database 手法の利用は [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] にのみ適用され、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] には適用されません。

[Stretch Database](../../sql-server/stretch-database/stretch-database.md) の [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、履歴データが Azure に透過的に移行されます。 セキュリティ強化のために、SQL Server の [Always Encrypted](../security/encryption/always-encrypted-database-engine.md) 機能で移行中のデータを暗号化できます。 また、 [行レベルのセキュリティ](../../relational-databases/security/row-level-security.md) やその他の高度な SQL Server セキュリティ機能を Temporal/Stretch Database と共に使用し、データを保護できます。

Stretch Database 手法の利用では、一時的な履歴テーブルの一部または全部を Azure にストレッチできます。SQL Server は履歴データを Azure に通知なしで移行します。 履歴テーブルのストレッチを有効にしても、データ変更や一時的なクエリ実行で、テンポラル テーブルの操作が変わることはありません。

- **履歴テーブル全体を拡張する:** データを頻繁に変更するが、履歴データをクエリすることは比較的まれな環境で、主なシナリオがデータ監査である場合、履歴テーブル全体に Stretch Database を構成します。 つまり、一時的なクエリ実行の性能が重要ではない場合にこの手法を利用します。 その場合、Azure が与える対費用効果は強力なものになります。 履歴テーブル全体をストレッチするとき、Stretch ウィザードまたは Transact-SQL を利用できます。 両方の例を以下に示します。

- **履歴テーブルの一部を拡張する:** 主なシナリオが最近の履歴データにクエリを実行することであるが、必要なときに古い履歴データにもクエリを実行できて、しかも、そのデータを離れた場所に低いコストで保管する場合、履歴テーブルの一部にのみ Stretch Database を構成し、パフォーマンスを向上させます。 Transact-SQL を利用すればそれが可能です。すべての行を移行するのではなく、述語関数を指定し、履歴テーブルから移行する行を選択します。 テンポラル テーブルを利用するとき、時間条件 (履歴テーブルの行バージョンの経過時間) に基づいてデータを移行すると効果的です。

  決定性の述語関数を利用することで、現行データと同じデータベースに履歴の一部を保有できます。残りは Azure に移行されます。 例と制限については、「 [フィルター関数を使用して移行する行を選択する (Stretch Database)](../../sql-server/stretch-database/select-rows-to-migrate-by-using-a-filter-function-stretch-database.md)」を参照してください。 非決定性の関数は有効ではないため、スライディング ウィンドウで履歴データを転送する場合、ローカルで保有する行の時間枠が経過時間において変わらないように、インライン述語関数の定義を定期的に変更する必要性が生じることがあります。 スライディング ウィンドウでは、1 か月以上経過した履歴データを Azure に継続的に移動できます。 この手法の例は次のようになります。

> [!NOTE]
> Stretch Database はデータを Azure に移行します。 そのため、Azure アカウントとサブスクリプションを請求のために用意する必要があります。 無料の試用版 Azure アカウントを入手するには、[1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)をクリックしてください。

一時的な履歴テーブルの Stretch は Stretch ウィザードまたは Transact-SQL で構成できます。システム バージョン管理を **オン** に設定しているとき、一時的な履歴テーブルのストレッチを有効にできます。 現行テーブルはストレッチできません。ストレッチする意味がないためです。

### <a name="using-the-stretch-wizard-to-stretch-the-entire-history-table"></a>Stretch ウィザードを利用し、履歴テーブル全体をストレッチする

初心者にとって最も簡単な方法は、Stretch ウィザードを利用して、データベース全体でストレッチを有効にし、Stretch ウィザード内で一時的な履歴テーブルを選択することです (この例では、本来であれば空のデータベースで、Department テーブルをシステム バージョン管理のテンポラル テーブルとして構成しているものと想定しています)。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]では一時的な履歴テーブル自体を右クリックし、[Stretch] をクリックすることはできません。

1. データベースを右クリックし、 **[タスク]** をポイントし、 **[Stretch]** をポイントします。それから、 **[有効化]** をクリックしてウィザードを起動します。
2. **[テーブルの選択]** ウィンドウで、一時的な履歴テーブルのチェック ボックスを選択し、[次へ] をクリックします。

    ![[テーブルの選択] ページで履歴テーブルを選択する](../../relational-databases/tables/media/stretch-wizard-2-for-temporal.png "[テーブルの選択] ページで履歴テーブルを選択する")
3. **[Azure の構成]** ウィンドウで、ログイン資格情報を指定します。 Microsoft Azure にサインインするか、アカウントを作成します。 使用するサブスクリプションを選択し、Azure 領域を選択します。 次に、新しいサーバーを作成するか、既存のサーバーを選択します。 **[次へ]** をクリックします。

    ![新しい Azure サーバーを作成 - Stretch Database ウィザード](../../relational-databases/tables/media/stretch-wizard-4.png "新しい Azure サーバーを作成 - Stretch Database ウィザード")
4. **[セキュリティで保護された資格情報]** ウィンドウで、ソース SQL Server データベースの資格情報を保護するためのデータベース マスターキーのパスワードを入力し、[次へ] をクリックします。

    ![Stretch Database ウィザードの [セキュリティで保護された資格情報] ページ](../../relational-databases/tables/media/stretch-wizard-6.png "Stretch Database ウィザードの [セキュリティで保護された資格情報] ページ")
5. **[IP アドレスの選択]** ウィンドウで、Azure サーバーで SQL Server と通信するために、SQL Server の IP アドレス範囲を入力します (ファイアウォール規則が既に設定されている既存のサーバーを選択する場合、ここで [次へ] をクリックし、既存のファイアウォール規則を使用します)。 **[次へ]** をクリックして **[完了]** をクリックし、Stretch Database を有効にしてテンポラル履歴テーブルを拡張します。

    ![Stretch Database ウィザードの [IP アドレスの選択] ページ](../../relational-databases/tables/media/stretch-wizard-7.png "Stretch Database ウィザードの [IP アドレスの選択] ページ")
6. ウィザードが終わったら、データベースのストレッチが有効になっていることを確認してください。 データベースが拡張されたことは、オブジェクト エクスプローラーのアイコンに示されるので注目してください。

> [!NOTE]
> データベースのストレッチを有効化できなかった場合、エラー ログを確認してください。 ファイアウォール ルールの構成が間違えていることが多々あります。

関連項目:

- [データベースに対して Stretch Database を有効にする](../../sql-server/stretch-database/enable-stretch-database-for-a-database.md)
- [まずはデータベースのストレッチの有効化ウィザードを実行する](../../sql-server/stretch-database/get-started-by-running-the-enable-database-for-stretch-wizard.md)
- [テーブルに対して Stretch Database を有効にする](../../sql-server/stretch-database/enable-stretch-database-for-a-table.md)

### <a name="using-transact-sql-to-stretch-the-entire-history-table"></a>Transact-SQL を利用して履歴テーブル全体をストレッチする

Transact-SQL を使用して、ローカル サーバー上で Stretch を有効にすることや、 [データベースに対して Stretch Database を有効にする](../../sql-server/stretch-database/enable-stretch-database-for-a-database.md)こともできます。 それから、[Transact-SQL を使用して、テーブルで Stretch Database を有効にする](../../sql-server/stretch-database/enable-stretch-database-for-a-table.md)ことができます。 以前に、データベースの Stretch Database を有効にした場合、次の TRANSACT-SQL スクリプトを実行して、既存のシステム バージョン管理の一時的な履歴テーブルをストレッチできます。

```sql
ALTER TABLE <history table name>
SET (REMOTE_DATA_ARCHIVE = ON (MIGRATION_STATE = OUTBOUND));
```

### <a name="using-transact-sql-to-stretch-a-portion-of-the-history-table"></a>Transact-SQL を利用して履歴テーブルの一部をストレッチする

履歴テーブルの一部のみをストレッチするには、最初に [インライン述語関数](../../sql-server/stretch-database/select-rows-to-migrate-by-using-a-filter-function-stretch-database.md)を作成します。 たとえば、2015 年 12 月 1 日に初めてインライン述語関数を構成していて、2015 年 11 月 1 日より前のすべての履歴データを Azure にストレッチすると想定します。 この場合、最初に次の関数を作成します。

```sql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20151101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2015-11-01T00:00:00', 101) ;
```

次に、次のスクリプトを利用し、フィルター述語を履歴テーブルに追加し、移行状態を OUTBOUND に設定し、履歴テーブルに対して述語ベースのデータ移行を有効にします。

```sql
ALTER TABLE <history table name>
SET (
      REMOTE_DATA_ARCHIVE = ON
        (
          FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20151101 (SysEndTime)
            , MIGRATION_STATE = OUTBOUND
        )
    )
;
```

スライディング ウィンドウを維持するには、毎日、述語関数を正確にする必要があります (つまり、毎日、行のフィルター処理条件を 1 日単位で変更します)。 次のスクリプトは、2015 年 12 月 2 日に実行する必要があるスクリプトです。

```sql
BEGIN TRAN
/*(1) Create new predicate function definition */
  CREATE FUNCTION dbo.fn_StretchBySystemEndTime20151102(@systemEndTime datetime2)
   RETURNS TABLE
    WITH SCHEMABINDING
      AS
        RETURN SELECT 1 AS is_eligible
          WHERE @systemEndTime < CONVERT(datetime2,'2015-11-02T00:00:00', 101)
  GO
 
/*(2) Set the new function as filter predicate */
  ALTER TABLE <history table name>
    SET
      (
        REMOTE_DATA_ARCHIVE = ON
          (
            FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20151102(SysEndTime),
              MIGRATION_STATE = OUTBOUND
          )
      )
COMMIT ;
```

述語関数の定義が常に有効になるように、SQL Server Agent またはその他のスケジュール作成メカニズムを利用します。

## <a name="using-table-partitioning-approach"></a>テーブル パーティション分割手法の利用

[テーブル パーティション分割](../partitions/create-partitioned-tables-and-indexes.md) を利用すると、大規模なテーブルが管理しやすくなり、拡張性が上がります。 テーブルのパーティション分割手法では、履歴テーブルのパーティションを利用し、時間条件を基準にカスタム データ クリーンアップやオフライン アーカイブを実装できます。 テーブル パーティション分割では、パーティションの削除を利用し、データ履歴のサブセットについてテンポラル テーブルにクエリを実行するとき、パフォーマンス上の利点もあります。

テーブル パーティション分割では、スライディング ウィンドウ手法を実行し、経過時間に基づき、履歴テーブルから古い履歴データを除外し、保有部分のサイズを一定に維持できます。必須保有期間に相当するデータを履歴テーブルで維持します。 履歴テーブルからデータをスイッチ アウトする操作は、SYSTEM_VERSIONING がオンになっているときにサポートされます。つまり、保守管理ウィンドウを導入したり、通常の作業負荷をブロックしたりしなくても履歴データの一部をクリーンアップできます。

> [!NOTE]
> パーティションを切り替えるには、履歴テーブルのクラスター化インデックスがパーティション分割スキーマと連携している必要があります (SysEndTime が含まれている必要があります)。 システムにより作成される既定の履歴テーブルには、SysEndTime 列と SysStartTime 列を含むクラスター化インデックスが含まれており、パーティション分割、新しい履歴データの挿入、標準的な一時的クエリ実行に最適です。 詳細については、「 [Temporal Tables](../../relational-databases/tables/temporal-tables.md)」を参照してください。

スライディング ウィンドウ手法は 2 セットのタスクからなり、ユーザーがそれを実行する必要があります。

- パーティション分割構成タスク
- 定期的パーティション保守管理タスク

説明のために、履歴データを 6 か月保持し、月ごとのデータを個別のパーティションに保管するものとします。 また、2015 年 9 月にシステムのバージョン管理を有効にすると仮定します。

パーティション分割構成タスクでは、履歴テーブルの初回パーティション分割構成を作成します。 この例では、スライディング ウィンドウのサイズ (月数) と同じ数のパーティションを作成し、さらに空の追加パーティションを事前に用意しておきます (下記で説明します)。 この構成により、初めて定期的パーティション保守管理タスクを始めたとき、システムで新しいデータが適切に格納されることが確保され、高額になるデータ移動を回避するためにパーティションをデータで分割しないことが保証されます。 このタスクは Transact-SQL を利用し、以下のサンプル スクリプトのように実行します。

次はデータを 6 か月維持する初回パーティション分割構成の図です。

![データを 6 か月維持する初回パーティション分割構成を示す図。](../../relational-databases/tables/media/partitioning.png "パーティション分割")

> [!NOTE]
> パーティション分割を構成するとき、RANGE LEFT または RANGE RIGHT を利用するときのパフォーマンス上の違いについては、下の「テーブル パーティション分割におけるパフォーマンス上の考慮事項」を参照してください。

最初と最後のパーティションがそれぞれ、下と上の境界で "オープン" になっており、パーティション分割列の値に関係なく、すべての新しい行に対象パーティションがあることを確実にします。 時間の経過と共に、履歴テーブルの新しい行が上位のパーティションに入ります。 6 番目のパーティションがいっぱいになると、目標とした保有期間に到達したことになります。 その瞬間、定期 (繰り返し) パーティション保守管理タスクが初めて始まります (定期的に実行するようにスケジュールする必要があり、この例では月 1 回になっています)。

次の図は、定期パーティション保守管理タスクの例です (下に詳しい手順があります)。

![定期的パーティション保守管理タスクを示す図。](../../relational-databases/tables/media/partitioning2.png "パーティション分割 2")

定期パーティション保守管理タスクの詳しい手順:

1. SWITCH OUT:ステージング テーブルを作成し、[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md) ステートメントと SWITCH PARTITION 引数を利用し、履歴テーブルとステージング テーブルの間でパーティションを切り替えます (「例 C. テーブル間のパーティションの切り替え」を参照してください)。

    ```sql
    ALTER TABLE <history table> SWITCH PARTITION 1 TO <staging table>
    ```

    パーティション切り替え後、任意でデータをステージング テーブルからアーカイブし、それから、この定期パーティション保守管理タスクを次に実行するときのために、ステージング テーブルを削除するか、切り詰めることができます。
2. MERGE RANGE:[ALTER PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-function-transact-sql.md) と MERGE RANGE を利用し、空のパーティション 1 とパーティション 2 をマージします (例 B を参照)。 この関数で一番下の境界を削除することで、空のパーティション 1 と前のパーティション 2 をマージし、新しいパーティション 1 を効果的に作成します。 結果として、他のパーティションの序数も変更されます。
3. SPLIT RANGE:[ALTER PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-function-transact-sql.md) と SPLIT RANGE を利用し、新しい空のパーティション 7 を作成します (例 A を参照)。 この関数で上位の境界を新しく追加することで、翌月のために別個のパーティションを効果的に作成します。

### <a name="use-transact-sql-to-create-partitions-on-history-table"></a>Transact-SQL を利用し、履歴テーブルでパーティションを作成する

下のコード ウィンドウでは、Transact-SQL を利用し、パーティション関数とパーティション スキーマを作成します。また、クラスター化インデックスを再作成し、パーティションをパーティション スキーマに合わせて調整します。 この例では、月単位のパーティションが 2015 年 9 月に開始する 6 か月のスライディング ウィンドウ手法を作成します。

```sql
BEGIN TRANSACTION
/*Create partition function*/
    CREATE PARTITION FUNCTION [fn_Partition_DepartmentHistory_By_SysEndTime] (datetime2(7))
        AS RANGE LEFT FOR VALUES
          (
            N'2015-09-30T23:59:59.999'
          , N'2015-10-31T23:59:59.999'
          , N'2015-11-30T23:59:59.999'
          , N'2015-12-31T23:59:59.999'
          , N'2016-01-31T23:59:59.999'
          , N'2016-02-29T23:59:59.999'
          )
/*Create partition scheme*/
    CREATE PARTITION SCHEME [sch_Partition_DepartmentHistory_By_SysEndTime]
        AS PARTITION [fn_Partition_DepartmentHistory_By_SysEndTime]
            TO ([PRIMARY], [PRIMARY], [PRIMARY], [PRIMARY], [PRIMARY], [PRIMARY], [PRIMARY])
/*Re-create index to be partition-aligned with the partitioning schema*/
    CREATE CLUSTERED INDEX [ix_DepartmentHistory] ON [dbo].[DepartmentHistory]
        (
            [SysEndTime] ASC
          , [SysStartTime] ASC
        )  
    WITH
        (
            PAD_INDEX = OFF
          , STATISTICS_NORECOMPUTE = OFF
          , SORT_IN_TEMPDB = OFF
          , DROP_EXISTING = ON
          , ONLINE = OFF
          , ALLOW_ROW_LOCKS = ON
          , ALLOW_PAGE_LOCKS = ON
          , DATA_COMPRESSION = PAGE
        )
    ON [sch_Partition_DepartmentHistory_By_SysEndTime] ([SysEndTime])

COMMIT TRANSACTION;
```

### <a name="using-transact-sql-to-maintain-partitions-in-sliding-window-scenario"></a>Transact-SQL を利用し、スライディング ウィンドウ シナリオのパーティションを維持する

下のコード ウィンドウでは Transact-SQL スクリプトを利用し、スライディング ウィンドウ シナリオのパーティションを維持します。 この例では、MERGE RANGE を利用して 2015 年 9 月にパーティションをスイッチ アウトし、SPLIT RANGE を利用して 2016 年 3 月に新しいパーティションを追加します。

```sql
BEGIN TRANSACTION
/*(1) Create staging table */
    CREATE TABLE [dbo].[staging_DepartmentHistory_September_2015]
        (
            [DeptID] [int] NOT NULL
          , [DeptName] [varchar](50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
          , [ManagerID] [int] NULL
          , [ParentDeptID] [int] NULL
          , [SysStartTime] [datetime2](7) NOT NULL
          , [SysEndTime] [datetime2](7) NOT NULL
        ) ON [PRIMARY]
    WITH
        (
            DATA_COMPRESSION = PAGE
        )
/*(2) Create index on the same filegroups as the partition that will be switched out*/
    CREATE CLUSTERED INDEX [ix_staging_DepartmentHistory_September_2015]
        ON [dbo].[staging_DepartmentHistory_September_2015]
        (
            [SysEndTime] ASC
          , [SysStartTime] ASC
        )
    WITH
        (
            PAD_INDEX = OFF
          , SORT_IN_TEMPDB = OFF
          , DROP_EXISTING = OFF
          , ONLINE = OFF
          , ALLOW_ROW_LOCKS = ON
          , ALLOW_PAGE_LOCKS = ON
        )
    ON [PRIMARY]
/*(3) Create constraints matching the partition that will be switched out*/
    ALTER TABLE [dbo].[staging_DepartmentHistory_September_2015] WITH CHECK
        ADD CONSTRAINT [chk_staging_DepartmentHistory_September_2015_partition_1]
            CHECK ([SysEndTime]<=N'2015-09-30T23:59:59.999')
    ALTER TABLE [dbo].[staging_DepartmentHistory_September_2015]
        CHECK CONSTRAINT [chk_staging_DepartmentHistory_September_2015_partition_1]
/*(4) Switch partition to staging table*/
    ALTER TABLE [dbo].[DepartmentHistory]
        SWITCH PARTITION 1 TO [dbo].[staging_DepartmentHistory_September_2015]
        WITH (WAIT_AT_LOW_PRIORITY (MAX_DURATION = 0 MINUTES, ABORT_AFTER_WAIT = NONE))
/*(5) [Commented out] Optionally archive the data and drop staging table
      INSERT INTO [ArchiveDB].[dbo].[DepartmentHistory]
      SELECT * FROM [dbo].[staging_DepartmentHistory_September_2015];
      DROP TABLE [dbo].[staging_DepartmentHIstory_September_2015];
*/
/*(6) merge range to move lower boundary one month ahead*/
    ALTER PARTITION FUNCTION [fn_Partition_DepartmentHistory_By_SysEndTime]()
        MERGE RANGE(N'2015-09-30T23:59:59.999')
/*(7) Create new empty partition for "April and after" by creating new boundary point and specifying NEXT USED file group*/
    ALTER PARTITION SCHEME [sch_Partition_DepartmentHistory_By_SysEndTime] NEXT USED [PRIMARY]
        ALTER PARTITION FUNCTION [fn_Partition_DepartmentHistory_By_SysEndTime]() SPLIT RANGE(N'2016-03-31T23:59:59.999')
COMMIT TRANSACTION
```

上記のスクリプトを少々変更すれば、毎月の保守管理プロセスに利用できます。

1. 手順 (1) では、削除する月に新しいステージング テーブルを作成します (例では、10 月が次の月になります)。
2. 手順 (3) では、削除するデータの月に一致する制約を作成し、確認します。10 月のパーティションの `[SysEndTime]<=N'2015-10-31T23:59:59.999'` です。
3. 手順 (4) では、パーティション 1 を新しく作成したステージング テーブルに切り替えます。
4. 手順 (6) では、10 月のデータを移した後に、下位の境界を結合し、パーティション関数を変更します。`MERGE RANGE(N'2015-10-31T23:59:59.999'` です。
5. 手順 (7) では、10 月のデータを移した後に、上位の境界を新しく作成し、パーティション関数を分割します。 `SPLIT RANGE (N'2016-04-30T23:59:59.999'` です。

ただし、最適なソリューションは、スクリプトを変更しなくても、毎月、適切なアクションを実行できる汎用 Transact-SQL スクリプトを定期的に実行することでしょう。 指定されたパラメーター (マージする必要がある下位の境界とパーティション分割で作成する新しい境界) に基づいて動作するように上記のスクリプトを汎用化できます。 毎月のステージング テーブル作成を避けるために、1 つのステージング テーブルを事前に作成し、スイッチ アウトされるパーティションに一致するようにチェック制約を変更して再利用できます。Transact-SQL スクリプトを使用した [スライディング ウィンドウの完全自動化](/previous-versions/sql/sql-server-2005/administrator/aa964122(v=sql.90)) については、後続のページを参照することで理解できます。

### <a name="performance-considerations-with-table-partitioning"></a>テーブル パーティション分割に関するパフォーマンス上の考慮事項

パフォーマンス上、データ移動は大幅なオーバーヘッドを引き起こす可能性があるため、MERGE 操作と SPLIT RANGE 操作を実行し、あらゆるデータ移動を回避することが重要です。 詳細については、「[パーティション関数の変更](../../relational-databases/partitions/modify-a-partition-function.md)」を参照してください。これは、[CREATE PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-function-transact-sql.md) の実行時に、RANGE RIGHT ではなく、RANGE LEFT で実現します。

最初に RANGE LEFT オプションと RANGE RIGHT オプションの意味を図で説明します。

![RANGE LEFT と RANGE RIGHT のオプションを示す図。](../../relational-databases/tables/media/partitioning3.png "パーティション分割 3")

パーティション関数を RANGE LEFT として定義すると、指定値はパーティションの上位境界になります。 RANGE RIGHT を利用するとき、指定値はパーティションの下位境界になります。 MERGE RANGE 操作でパーティション関数定義から境界を削除するとき、基礎となる実装は、境界を含むパーティションも削除します。 そのパーティションが空ではない場合、MERGE RANGE 操作の結果となるパーティションにデータが移動します。

スライディング ウィンドウ シナリオでは、常に一番下のパーティション境界を削除します。

- RANGE LEFT の場合:RANGE LEFT の場合、一番下のパーティション境界は空であるパーティション 1 に属します (パーティションのスイッチ アウト後)。そのため、MERGE RANGE ではいかなるデータ移動も発生しません。
- RANGE RIGHT の場合:RANGE RIGHT の場合、一番下のパーティション境界は空ではないパーティション 2 に属します。スイッチ アウトで空になるのはパーティション 1 であるためです。この場合、MERGE RANGE はデータ移動を発生させます (パーティション 2 からのデータはパーティション 1 に移動します)。 これを回避するには、スライディング ウィンドウ シナリオの RANGE RIGHT に、常に空であるパーティション 1 を与える必要があります。 つまり、RANGE RIGHT を使用する場合、RANGE LEFT 場合と比較して 1 つ追加でパーティションを作成し、保守管理する必要があります。

**結論**: スライディング パーティションに RANGE LEFT を利用することはパーティション管理としてはるかに簡単であり、データ移動が回避されます。 RANGE RIGHT でパーティション境界を定義する場合、日時タイム ティック問題を扱う必要がなく、少しばかり簡単になります。

## <a name="using-custom-cleanup-script-approach"></a>カスタム クリーンアップ スクリプト手法の利用

Stretch Database とテーブル パーティション分割を利用する手法が実行できない場合、3 番目の手法は、カスタム クリーンアップ スクリプトを利用して履歴テーブルからデータを削除することになります。 履歴テーブルからデータを削除することは、 **SYSTEM_VERSIONING = OFF** のときにのみ可能です。 データの不整合を回避するために、保守管理の時間枠内 (データを変更するワークロードがアクティブではないとき) か、トランザクション (他のワークロードが効果的にブロックされる) 内でクリーンアップを実行します。 この操作には、現行テーブルと履歴テーブルの **CONTROL** 権限が必要になります。

通常のアプリケーションとユーザー クエリを最小限ブロックするには、トランザクション内でクリーンアップ スクリプトを実行するとき、遅延ありで、データの小規模なまとまりを削除します。 削除されるデータ チャンクごとのサイズについては、あらゆるシナリオに最適なサイズというものはありませんが、1 回のトランザクションで 10,000 行以上を削除すると、大幅な影響が現れる可能性があります。

クリーンアップ ロジックは、すべてのテンポラル テーブルで同じです。そのため、一般的なストアド プロシージャで比較的簡単に自動化し、データ履歴を制限するテンポラル テーブルごとに、定期的に実行できます。

次の図は、実行中のワークロードに対する影響を抑えるように、1 つのテーブルのクリーンアップ ロジックを調整する方法を示しています。

![実行中のワークロードに対する影響を抑えるように、1 つのテーブルのクリーンアップ ロジックをどのように調整するかを示す図。](../../relational-databases/tables/media/customcleanupscriptdiagram.png "CustomCleanUpScriptDiagram")

プロセスの実装については、上位のガイドラインがあります。 クリーンアップ ロジックを毎日実行するようにスケジュールし、データ クリーンアップが必要なすべてのテンポラル テーブルに繰り返して起用します。 SQL Server Agent または別のツールを利用し、このプロセスをスケジュールします。

- 最古の行から最新の行まで、すべてのテンポラル テーブルの履歴データを削除します。上の図のように、数回繰り返して小さいチャンクで実行し、1 回のトランザクションで全行を削除することは避けます。
- 履歴テーブルから一部のデータを削除する汎用ストアド プロシージャを呼び出すことですべての繰り返しを実行します (この手順については、下のコード例を参照してください)。
- プロセスを呼び出すたびに、個々のテンポラル テーブルに対して、削除する行を計算します。 それと必要な繰り返しの数に基づき、手順の呼び出しごとに動的な分割点を決定します。
- 1 つテーブルに対して繰り返しの間に一定の遅延を与え、テンポラル テーブルにアクセスするアプリケーションに与える影響を抑えます。

1 つのテンポラル テーブルのデータを削除するストアド プロシージャは次のコード スニペットのようになります (このコードを注意深く確認し、調整してから自分の環境で適用してください)。

```sql
DROP PROCEDURE IF EXISTS sp_CleanupHistoryData;
GO

CREATE PROCEDURE sp_CleanupHistoryData
        @temporalTableSchema sysname
      , @temporalTableName sysname
      , @cleanupOlderThanDate datetime2
AS
    DECLARE @disableVersioningScript nvarchar(max) = '';
    DECLARE @deleteHistoryDataScript nvarchar(max) = '';
    DECLARE @enableVersioningScript nvarchar(max) = '';

DECLARE @historyTableName sysname
DECLARE @historyTableSchema sysname
DECLARE @periodColumnName sysname

/*Generate script to discover history table name and end of period column for given temporal table name*/
EXECUTE sp_executesql
    N'SELECT @hst_tbl_nm = t2.name, @hst_sch_nm = s2.name, @period_col_nm = c.name
        FROM sys.tables t1
            JOIN sys.tables t2 on t1.history_table_id = t2.object_id
        JOIN sys.schemas s1 on t1.schema_id = s1.schema_id
        JOIN sys.schemas s2 on t2.schema_id = s2.schema_id
           JOIN sys.periods p on p.object_id = t1.object_id
           JOIN sys.columns c on p.end_column_id = c.column_id and c.object_id = t1.object_id
                  WHERE
                 t1.name = @tblName and s1.name = @schName'
                , N'@tblName sysname
                , @schName sysname
                , @hst_tbl_nm sysname OUTPUT
                , @hst_sch_nm sysname OUTPUT
                , @period_col_nm sysname OUTPUT'
                , @tblName = @temporalTableName
                , @schName = @temporalTableSchema
                , @hst_tbl_nm = @historyTableName OUTPUT
                , @hst_sch_nm = @historyTableSchema OUTPUT
                , @period_col_nm = @periodColumnName OUTPUT
  
IF @historyTableName IS NULL OR @historyTableSchema IS NULL OR @periodColumnName IS NULL
    THROW 50010, 'History table cannot be found. Either specified table is not system-versioned temporal or you have provided incorrect argument values.', 1

/*Generate 3 statements that will run inside a transaction:
  (1) SET SYSTEM_VERSIONING = OFF,
  (2) DELETE FROM history_table,
  (3) SET SYSTEM_VERSIONING = ON
  On SQL Server 2016, it is critical that (1) and (2) run in separate EXEC statements, or SQL Server will generate the following error:
  Msg 13560, Level 16, State 1, Line XXX
  Cannot delete rows from a temporal history table '<database_name>.<history_table_schema_name>.<history_table_name>'.
*/
SET @disableVersioningScript = @disableVersioningScript + 'ALTER TABLE [' + @temporalTableSchema + '].[' + @temporalTableName + '] SET (SYSTEM_VERSIONING = OFF)'
SET @deleteHistoryDataScript = @deleteHistoryDataScript + ' DELETE FROM [' + @historyTableSchema + '].[' + @historyTableName + ']
    WHERE ['+ @periodColumnName + '] < ' + '''' + convert(varchar(128), @cleanupOlderThanDate, 126) + ''''
SET @enableVersioningScript = @enableVersioningScript + ' ALTER TABLE [' + @temporalTableSchema + '].[' + @temporalTableName + ']
    SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = [' + @historyTableSchema + '].[' + @historyTableName + '], DATA_CONSISTENCY_CHECK = OFF )); '

BEGIN TRAN
    EXEC (@disableVersioningScript);
    EXEC (@deleteHistoryDataScript);
    EXEC (@enableVersioningScript);
COMMIT;
```

## <a name="using-temporal-history-retention-policy-approach"></a>テンポラル履歴保有期間ポリシー手法の利用

> [!NOTE]
> テンポラル履歴保有期間ポリシーを使う方法は、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] および SQL Server 2017 の CTP 1.3 以降に適用されます。

テンポラル履歴保有期間は個々のテーブル レベルで構成でき、ユーザーは柔軟なエージング ポリシーを作成できます。 テンポラル保有期間は簡単に適用できます。必要なのは、テーブル作成時またはスキーマ変更時にパラメーターを 1 つ設定することだけです。

保有期間ポリシーを定義した後、Azure SQL Database は自動データ クリーンアップの対象になる履歴行があるかどうかの定期的な確認を開始します。 一致する行の識別と履歴テーブルからの削除は、システムによってスケジュール設定されて実行されるバックグラウンド タスクにおいて透過的に行われます。 履歴テーブルの行の経過時間の条件は、SYSTEM_TIME 期間の終了を表す列に基づいてチェックされます。 たとえば、保有期間が 6 か月に設定されている場合、クリーンアップの対象となるテーブルの行は次の条件を満たします。

```sql
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```
前の例では、ValidTo 列が SYSTEM_TIME 期間の終了に対応しているものと想定されています。

### <a name="how-to-configure-retention-policy"></a>リテンション ポリシーの構成方法

テンポラル テーブルの保有期間ポリシーを構成する前にまず、データベース レベルでテンポラル履歴保有期間が有効になっているかどうかを確認します。

```sql
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

データベースのフラグ **is_temporal_history_retention_enabled** は既定で ON に設定されていますが、ユーザーは ALTER DATABASE ステートメントでそれを変更できます。 ポイントインタイム リストア操作の後では、OFF に自動的に設定されます。 データベースのテンポラル履歴保有期間のクリーンアップを有効にするには、次のステートメントを実行します。

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION ON
```

保有期間ポリシーは、テーブル作成時に HISTORY_RETENTION_PERIOD パラメーターの値を指定することによって構成します。

```sql
CREATE TABLE dbo.WebsiteUserInfo
(
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )
 WITH
 (
    SYSTEM_VERSIONING = ON
    (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
    )
 );
```

保有期間は、次のさまざまな時間単位を使って指定できます。DAYS、WEEKS、MONTHS、YEARS。 HISTORY_RETENTION_PERIOD を省略すると、INFINITE 保有期間と見なされます。 INFINITE キーワードを明示的に使うこともできます。
シナリオによっては、テーブル作成後に保有期間を構成すること、または以前に構成した値を変更することが必要になる場合があります。 その場合は、ALTER TABLE ステートメントを使います。

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

保有期間ポリシーの現在の状態を確認するには、データベース レベルのテンポラル保有期間有効化フラグと個々のテーブルの保有期間を結合する次のクエリを使います。

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName, SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```

### <a name="how-sql-database-deletes-aged-rows"></a>SQL Database によって期限切れの行が削除されるしくみ

クリーンアップ プロセスは、履歴テーブルのインデックスのレイアウトに依存します。 *有限の保持期間ポリシーを構成できるのはクラスター化インデックス (B ツリーまたは列ストア) を使っている履歴テーブルだけである* ことに注意する必要があります。 有限の保有期間を持つすべてのテンポラル テーブルの期限切れデータをクリーンアップするために、バックグラウンド タスクが作成されます。 行ストア (B ツリー) クラスター化インデックスのクリーンアップ ロジックは、データベース ログと I/O サブシステムへの負荷を最小限に抑えるため、小さいチャンク (最大 10 K) で期限切れの行を削除します。 クリーンアップ ロジックは必要な B ツリー インデックスを利用しますが、保有期間より古い行の削除の順序は確実には保証できません。 そのため、"*アプリケーションではクリーンアップ順序に依存しないでください*"。

クラスター化列ストアのクリーンアップ タスクは、行グループ全体を一度に削除します (通常、各グループには 100 万行が含まれます)。これは非常に効率的であり、履歴データが速いペースで生成されているときは特にそうです。

![クラスター化列ストア保有期間](../../relational-databases/tables/media/cciretention.png "クラスター化列ストア保有期間")

優れたデータ圧縮と効率的な保有期間のクリーンアップにより、クラスター化列ストア インデックスはワークロードが急速に大量の履歴データを生成するシナリオに最適な選択肢になります。 このようなパターンは、変更の追跡と監査、傾向分析、または IoT のデータ取り込みにテンポラル テーブルを使うトランザクション処理の多いワークロードで一般的なものです。

詳しくは、「[リテンション ポリシーを使用したテンポラル テーブルでの履歴データの管理](/azure/sql-database/sql-database-temporal-tables-retention-policy)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- [テンポラル テーブル](../../relational-databases/tables/temporal-tables.md)
- [システム バージョン管理されたテンポラル テーブルの概要](../../relational-databases/tables/getting-started-with-system-versioned-temporal-tables.md)
- [テンポラル テーブルのシステム一貫性のチェック](../../relational-databases/tables/temporal-table-system-consistency-checks.md)
- [テンポラル テーブルでのパーティション分割](../../relational-databases/tables/partitioning-with-temporal-tables.md)
- [テンポラル テーブルの考慮事項と制約](../../relational-databases/tables/temporal-table-considerations-and-limitations.md)
- [テンポラル テーブル セキュリティ](../../relational-databases/tables/temporal-table-security.md)
- [メモリ最適化テーブルでのシステム バージョン管理されたテンポラル テーブル](../../relational-databases/tables/system-versioned-temporal-tables-with-memory-optimized-tables.md)
- [テンポラル テーブル メタデータのビューおよび関数](../../relational-databases/tables/temporal-table-metadata-views-and-functions.md)