---
title: Table Properties - SSMS
description: Table Properties - SSMS
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
f1_keywords:
- sql12.SWB.SELECTCOLUMNS.F1
- sql13.SWB.SELECTCOLUMNS.F1
- sql13.swb.tableproperties.storage.f1
- sql13.swb.tableproperties.changetracking.f1
- sql13.swb.tableproperties.general.f1
- sql13.swb.tableproperties.filetable.f1
ms.assetid: ad8a2fd4-f092-4c0f-be85-54ce8b9d725a
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b1deece76c019978bfcb76fc575985105bdefc4e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748942"
---
# <a name="table-properties---ssms"></a>Table Properties - SSMS

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]の [テーブルのプロパティ] ダイアログ ボックスに表示されるテーブルのプロパティについて説明します。 これらのプロパティの表示方法の詳細については、「 [テーブル定義の表示](../../relational-databases/tables/view-the-table-definition.md)」を参照してください。  
  
 **このトピックの内容**  
  
1.  [[全般] ページ](#GeneralPage)  
  
2.  [[変更の追跡] ページ](#ChangeTracking)  
  
3.  [[FileTable] ページ](#FileTable)  
  
4.  [[ストレージ] ページ](#Storage)  

##  <a name="general-page"></a><a name="GeneralPage"></a> [全般] ページ  
 **[データベース]**  
 このテーブルを含むデータベースの名前です。  
  
 **サーバー**  
 現在のサーバー インスタンスの名前です。  
  
 **User**  
 この接続のユーザーの名前です。  
  
 **[作成日]**  
 テーブルが作成された日付と時刻です。  
  
 **名前**  
 テーブルの名前。  
  
 **[スキーマ]**  
 テーブルを所有するスキーマです。  
  
 **[システム オブジェクト]**  
 このテーブルが、内部情報を格納するために [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で使用されるシステム テーブルであることを示します。 システム テーブルを直接変更したり参照したりしないでください。  
  
 **[ANSI NULL]**  
 オブジェクトが、ANSI NULL オプションが ON に設定されて作成されたかどうかを示します。 詳細については、「[SET ANSI_NULLS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-nulls-transact-sql.md)」を参照してください。  
  
 **[引用符で囲まれた識別子]**  
 オブジェクトが、引用符で囲まれた識別子オプションが ON に設定されて作成されたかどうかを指定します。 詳細については、「[SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)」を参照してください。  
  
 **[ロックのエスカレーション]**  
 テーブルのロック エスカレーションの粒度を示します。 データベース エンジンのロックの詳細については、「 [SQL Server トランザクションのロックおよび行のバージョン管理ガイド](../sql-server-transaction-locking-and-row-versioning-guide.md)」をご覧ください。 次のいずれかの値になります。  
  
 AUTO  
 このオプションを使用すると、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] は、テーブル スキーマに適したロック エスカレーションの粒度を選択します。  
  
- テーブルがパーティション分割されている場合は、ロック エスカレーションをヒープまたは B ツリー (HoBT) 粒度に設定できます。 つまり、エスカレーションはパーティション レベルで許可されます。 ロックは HoBT レベルにエスカレートされると、後で TABLE 粒度にエスカレートされません。
- テーブルがパーティション分割されていない場合、TABLE 細分性に対してロックのエスカレーションが実行されます。 
  
 TABLE  
 テーブルがパーティション分割されているかどうかに関係なく、ロック エスカレーションはテーブルレベルの粒度で行われます。 TABLE は既定値です。  
  
 DISABLE  
 ほとんどの場合でロック エスカレーションを禁止します。 テーブルレベルのロックは完全には禁止されません。 たとえば、SERIALIZABLE 分離レベルでクラスター化インデックスがないテーブルをスキャンしている場合は、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] でテーブル ロックを実行して、データの整合性を保護します。  
  
 **[レプリケートされるテーブル]**  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] レプリケーションによってテーブルがいつ別のデータベースにレプリケートされるかを示します。 指定できる値は、 **[True]** または **[False]** です。  
  
##  <a name="change-tracking-page"></a><a name="ChangeTracking"></a> [変更の追跡] ページ  
 **変更の追跡**  
 テーブルに対する変更の追跡が有効かどうかを示します。 既定値は **False** です。  
  
 このオプションは、データベースに対して変更の追跡が有効になっている場合にのみ使用できます。  
  
 変更の追跡を有効にするには、テーブルに主キーが必要です。また、テーブルを変更する権限も必要です。 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)を使用して変更の追跡を構成できます。  
  
 **[更新された追跡列]**  
 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] が更新された列を追跡するかどうかを示します。  
  
 変更の追跡の詳細については、「[変更の追跡について &#40;SQL Server&#41;](../../relational-databases/track-changes/about-change-tracking-sql-server.md)」を参照してください。  
  
##  <a name="filetable-page"></a><a name="FileTable"></a> [FileTable] ページ  
 FileTable に関連するテーブルのプロパティを表示します。 詳細については、「[FileTables &#40;SQL Server&#41;](../../relational-databases/blob/filetables-sql-server.md)」をご覧ください。  
  
 **[FileTable の名前列の照合順序]**  
 FileTable の **Name** 列に適用される照合順序。 **Name** 列には、ファイルとディレクトリの名前が含まれています。  
  
 **[FileTable のディレクトリ名]**  
 FileTable のルート フォルダー。  
  
 **FileTable の名前空間の有効化**  
 **True** の場合、この値はテーブルが FileTable であることを示します。 この値を **False** に変更すると、FileTable が通常のユーザー テーブルに変更されます。 後でテーブルを FileTable に戻す場合は、変換時に FileTable 一貫性チェックを行い、テーブルに問題がないことを確認する必要があります。  
  
##  <a name="storage-page"></a><a name="Storage"></a> [ストレージ] ページ  
 選択されているテーブルのストレージに関連するプロパティを表示します。  
  
### <a name="compression"></a>圧縮  
 **[圧縮の種類]**  
 テーブルの圧縮の種類。 このプロパティは、パーティション分割されていないテーブルでのみ使用できます。 詳細については、「 [Data Compression](../../relational-databases/data-compression/data-compression.md)」を参照してください。  
  
 **[ページの圧縮を使用したパーティション]**  
 ページの圧縮を使用しているパーティション番号。 このプロパティは、パーティション分割されているテーブルでのみ使用できます。  
  
 **[圧縮されていないパーティション]**  
 圧縮されていないパーティション番号。 このプロパティは、パーティション分割されているテーブルでのみ使用できます。  
  
 **[行の圧縮を使用したパーティション]**  
 行の圧縮を使用しているパーティション番号。 このプロパティは、パーティション分割されているテーブルでのみ使用できます。  
  
### <a name="filegroup"></a>[ファイル グループ]  
 **[テキスト ファイル グループ]**  
 テーブルのテキスト データを含むファイル グループの名前です。  
  
 **[ファイル グループ]**  
 このテーブルを含むファイル グループの名前。  
  
 **[パーティション分割されるテーブル]**  
 指定できる値は、 **[True]** および **[False]** です。  
  
 **[Filestream ファイル グループ]**  
 テーブルが FILESTREAM 属性がある **varbinary(max)** 列を持つ場合は、FILESTREAM データ ファイル グループの名前を指定します。 既定値は、既定の FILESTREAM データ ファイル グループです。  
  
 テーブルに FILESTREAM データが含まれていない場合、フィールドは空白です。  
  
### <a name="general"></a>全般  
 **[VarDecimal ストレージ形式が有効]**  
 **[True]** の場合、この読み取り専用の値は、 **10 進** および **数値** の各データ型が vardecimal ストレージ形式で格納されることを示します。 このオプションを変更するには、 **sp_tableoption** の [vardecimal ストレージ形式](../../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)オプションを使用します。 Vardecimal ストレージ形式は非推奨とされます。 代わりに行の圧縮を使用してください。  
  
 **[インデックス領域]**  
 インデックスがテーブル内で占有する領域の容量をメガバイト単位で表示します。 この値には、テーブルの XML インデックスの領域使用状況は含まれません。 テーブルに XML インデックスが含まれている場合は、代わりに [sp_spaceused](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md) を使用してください。  
  
 **[行数]**  
 表の行数。  
  
 **[データ領域]**  
 データがテーブル内で占有する領域の容量をメガバイト単位で表示します。  
  
### <a name="partitioning"></a>パーティション分割  
 このセクションは、テーブルがパーティション分割されている場合にのみ使用できます。 詳細については、「 [Partitioned Tables and Indexes](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」を参照してください。  
  
 **[パーティション列]**  
 テーブルがパーティション分割される列の名前。  
  
 **[パーティション構成]**  
 テーブルがパーティション分割されている場合のパーティション構成の名前。 テーブルがパーティション分割されていない場合、このフィールドは空白です。  
  
 **パーティションの数**  
 テーブルのパーティション数です。  
  
 **[FILESTREAM パーティション構成]**  
 テーブルがパーティション分割されている場合の FILESTREAM パーティション構成の名前。 テーブルがパーティション分割されていない場合、このフィールドは空白です。  
  
 FILESTREAM パーティション構成は、 **[パーティション構成]** オプションで指定した構成と対称である必要があります。  
  
## <a name="see-also"></a>参照  
 [テーブル定義の表示](../../relational-databases/tables/view-the-table-definition.md)   
 [列の変更 &#40;データベース エンジン&#41;](../../relational-databases/tables/modify-columns-database-engine.md)  
  
