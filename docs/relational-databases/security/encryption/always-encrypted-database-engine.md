---
title: Always Encrypted | Microsoft Docs
description: SQL Server および Azure SQL Database での透過的なクライアント側暗号化と機密コンピューティングをサポートする Always Encrypted の概要
ms.custom: ''
ms.date: 10/30/2019
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- encryption [SQL Server], Always Encrypted
- Always Encrypted
- TCE Always Encrypted
- Always Encrypted, about
- SQL13.SWB.COLUMNMASTERKEY.CLEANUP.F1
ms.assetid: 54757c91-615b-468f-814b-87e5376a960f
author: jaszymas
ms.author: jaszymas
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: cfda09e184933ae537cdf3b4c6430e6d6d4b20fc
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633152"
---
# <a name="always-encrypted"></a>Always Encrypted
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]

  ![常に暗号化](../../../relational-databases/security/encryption/media/always-encrypted.png "Always Encrypted")  
  
 Always Encrypted は、[!INCLUDE[ssSDSFull](../../../includes/sssdsfull-md.md)] または [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータベースに格納されたクレジット カード番号や身分登録番号 (アメリカの社会保障番号など) などの機微なデータを保護するために設計された機能です。 Always Encrypted を使用すると、クライアントは [!INCLUDE[ssDE](../../../includes/ssde-md.md)] ([!INCLUDE[ssSDS](../../../includes/sssds-md.md)] または [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]) に暗号化キーを開示することなく、クライアント アプリケーション内の機微なデータを暗号化することができます。 結果として、Always Encrypted により、データを所有していて見ることができるユーザーと、データを管理するがアクセスできてはならないユーザーが分離されます。 Always Encrypted を使うと、オンプレミスのデータベース管理者、クラウド データベース オペレーター、または高い特権を持つものの許可されていないユーザーは、暗号化されたデータにアクセスできなくなり、顧客は直接管理できない機密データを安心して格納できます。 これにより、組織はデータを Azure に格納し、オンプレミスのデータベースの管理をサード パーティに委任したり、自社の DBA スタッフによる取り扱い許可の要件を緩和したりできます。

 Always Encrypted を使うと、暗号化されたデータに対する一部のクエリを [!INCLUDE[ssDE](../../../includes/ssde-md.md)]で処理できるようになる一方で、データの機密性は維持され、上記のセキュリティ上の利点が提供されることにより、機密コンピューティング機能が実現されます。 [!INCLUDE[sssql16-md](../../../includes/sssql16-md.md)]、[!INCLUDE[sssSQLv14](../../../includes/sssql17-md.md)]、[!INCLUDE[ssSDSFull](../../../includes/sssdsfull-md.md)] の Always Encrypted では、決定論的な暗号化により等値比較がサポートされます。 「[明確な暗号化またはランダム化された暗号化の選択](#selecting--deterministic-or-randomized-encryption)」を参照してください。 

  > [!NOTE] 
  > [!INCLUDE[sql-server-2019](../../../includes/sssql19-md.md)] では、セキュリティで保護されたエンクレーブのパターン マッチング、他の比較演算子、およびインプレース暗号化により、Always Encrypted の機密コンピューティング機能が大幅に拡張されます。 「[セキュリティで保護されたエンクレーブが設定された Always Encrypted](always-encrypted-enclaves.md)」をご覧ください。

 Always Encrypted は、アプリケーションに対して暗号化を透過的に実行します。 クライアント コンピューターにインストールされている、Always Encrypted が有効のドライバーは、クライアント アプリケーション内の機微なデータを自動的に暗号化および暗号化解除することで、この処理を実行します。 ドライバーは、 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]にデータを渡す前に機微な列のデータを暗号化し、アプリケーションに対するセマンティクスが維持されるように自動的にクエリを書き換えます。 また、暗号化されたデータベース列に格納され、クエリ結果に含まれているデータを、同じように透過的に暗号化解除します。  
  
 Always Encrypted は、[!INCLUDE[ssSDSFull](../../../includes/sssdsfull-md.md)] のすべてのエディション、[!INCLUDE[sssql16-md](../../../includes/sssql16-md.md)] 以降、および [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] のすべてのサービス レベルで利用できます。 ([!INCLUDE[ssSQL15_md](../../../includes/sssql16-md.md)] SP1 より前は、Always Encrypted は Enterprise Edition 限定でした。)Always Encrypted が含まれている Channel 9 のプレゼンテーションについては、「 [Keeping Sensitive Data Secure with Always Encrypted](https://channel9.msdn.com/events/DataDriven/SQLServer2016/AlwaysEncrypted)」 (Always Encrypted を使用して機微なデータの安全を確保する) を参照してください。  

  
## <a name="typical-scenarios"></a>標準のシナリオ  
  
### <a name="client-and-data-on-premises"></a>クライアントとデータがオンプレミス  
 顧客はオンプレミスのクライアント アプリケーションと [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の両方を社内で運用しています。 外部ベンダーを雇用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]の管理を委任することを考えています。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]に格納された機微なデータを保護するために、顧客は Always Encrypted を使用してデータベース管理者とアプリケーション管理者の役割分担を明確にしています。 顧客は Always Encrypted のキーのプレーンテキスト値を、クライアント アプリケーションがアクセスできる信頼できるキー ストアに格納しています。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の管理者はキーにアクセスできないため、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]に格納された機微なデータを暗号化解除できません。  
  
### <a name="client-on-premises-with-data-in-azure"></a>クライアントがオンプレミス、データが Azure 内  
 顧客はオンプレミスのクライアント アプリケーションを社内で運用しています。 アプリケーションは、Azure にホストされたデータベース (Microsoft Azure の仮想マシンで実行されている[!INCLUDE[ssSDS](../../../includes/sssds-md.md)] または [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ) に格納された機微なデータを元に運用しています。 顧客は Always Encrypted を使用して、Always Encrypted のキーをオンプレミスにホストされた信頼できるキー ストアに格納することで、 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] クラウドの管理者が機微なデータにアクセスできないようにします。  
  
### <a name="client-and-data-in-azure"></a>クライアントとデータの両方が Azure 内にある  
 顧客には、(worker ロールや Web ロールなどで) Microsoft Azure でホストされているクライアント アプリケーションがあります。このアプリケーションは、Azure でホストされているデータベースに格納されている機密データに影響があります。 Always Encrypted には、クラウド管理者からのデータの完全な分離機能がありませんが、データとキーの両方がクライアント層をホストするプラットフォームのクラウド管理者に公開されているので、セキュリティ攻撃の対象となる領域を減らすという利点が顧客にあります (データベース内のデータは常に暗号化されています)。  
 
## <a name="how-it-works"></a>機能

機密データを含む各データベース列に Always Encrypted を構成できます。 列の暗号化を設定するときに、列のデータを保護するために使用する暗号化アルゴリズムと暗号化キーに関する情報を指定します。 Always Encrypted では、列暗号化キーと列マスター キーという 2 種類のキーを使用します。 列暗号化キーは、暗号化された列のデータを暗号化するために使用されます。 列マスター キーは、1 つ以上の列暗号化キーを暗号化するキー保護キーです。 

データベース エンジンは、データベース メタデータの各列の暗号化構成を格納します。 ただし、データベース エンジンは、いずれの種類も、プレーンテキストで格納または使用しません。 列暗号化キーの暗号化値と、列マスター キーの場所に関する情報のみが格納されます。列マスター キーは、Azure Key Vault、クライアント コンピューターの Windows 証明書ストア、ハードウェア セキュリティ モジュールなどの外部の信頼できるキー ストアに格納されます。

プレーンテキストで暗号化された列に格納されているデータにアクセスするには、アプリケーションで、Always Encrypted が有効なクライアント ドライバーを使用する必要があります。 アプリケーションがパラメーター化クエリを発行すると、ドライバーはデータベース エンジンと透過的に連携し、暗号化されている列 (つまり暗号化する必要がある列) を対象にするパラメーターを決定します。 暗号化する必要がある各パラメーターについて、ドライバーは、暗号化アルゴリズムと列の列暗号化キーの暗号化値、パラメーターの対象、対応する列暗号化キーに関する情報を取得します。

次に、ドライバーは、暗号化された列暗号化キー値を復号化するために、列マスター キーを含むキー ストアに接続し、プレーンテキスト列の暗号化キーを使用してパラメーターを暗号化します。 以降、同じ列の暗号化キーを使用する際に、キー ストアに対するラウンド トリップ数を軽減するために、結果のプレーンテキスト列暗号化キーはキャッシュされます。 ドライバーは、暗号化された列を対象とするパラメーターのプレーンテキスト値を、暗号化値と置き換え、処理を実行するサーバーにクエリを送信します。

サーバーは結果セットを計算し、結果セットに暗号化された列が含まれている場合、ドライバーは列の暗号化メタデータ (暗号化アルゴリズムや対応するキーに関する情報など) をアタッチします。 ドライバーにより、最初にローカル キャッシュ内でプレーンテキスト列の暗号化キーの検索が試みられ、キャッシュ内にキーが見つからなかった場合にのみ、列マスター キーが検索されます。 次に、ドライバーは結果を復号化し、プレーンテキスト値をアプリケーションに返します。

 クライアント ドライバーは、列マスター キー ストア プロバイダーを使用して、列マスター キーを含むキー ストアに接続します。このプロバイダーは、列マスター キーを含むキー ストアをカプセル化するクライアント側ソフトウェア コンポーネントです。 キー ストアの一般的な種類のプロバイダーは、Microsoft からのクライアント側ドライバーのライブラリで、またはスタンドアロンのダウンロードとして入手できます。 独自のプロバイダーを実装することもできます。 組み込みの列マスター キー ストア プロバイダーを含む Always Encrypted の機能は、ドライバー ライブラリとそのバージョンによって異なります。 

特定のクライアント ドライバーで Always Encrypted を使用してアプリケーションを開発する方法の詳細については、「[Always Encrypted を使用したアプリケーションの開発](always-encrypted-client-development.md)」を参照してください。

## <a name="remarks"></a>解説

暗号化および復号化は、クライアント ドライバーを介して実行されます。 つまり、Always Encrypted を使用する場合、サーバー側でのみ発生する一部のアクションは動作しないことを意味します。 このようなアクションには以下のものが含まれます (ただし、これらだけではありません)。 
- UPDATE、BULK INSERT (T-SQL)、SELECT INTO、INSERT..SELECT を使用して、ある列から別の列にデータをコピーすること。 
- トリガー、テンポラル テーブル、スパース列、フルテキスト、インメモリ OLTP、および変更データ キャプチャ (CDC)。 


結果セットをクライアントに返さずに、暗号化された列から暗号化されない列にデータを移動しようとする UPDATE の例を次に示します。 

```sql
update dbo.Patients set testssn = SSN
```

SSN が Always Encrypted を使用して暗号化されている列の場合、前述の更新ステートメントは次のようなエラーで失敗します。

```
Msg 206, Level 16, State 2, Line 89
Operand type clash: char(11) encrypted with (encryption_type = 'DETERMINISTIC', encryption_algorithm_name = 'AEAD_AES_256_CBC_HMAC_SHA_256', column_encryption_key_name = 'CEK_1', column_encryption_key_database_name = 'ssn') collation_name = 'Latin1_General_BIN2' is incompatible with char
```

列を正常に更新するには、次の手順を実行します。

1. SELECT を使用して SSN 列からデータを選択し、結果セットとしてアプリケーションに格納します。 こうすることで、アプリケーション (クライアント *ドライバー*) は列を復号化できます。
2. INSERT を使用して結果セットのデータを SQL Server に挿入します。 

 >[!IMPORTANT]
 > このシナリオで、対象の列は暗号化されたデータを受け入れない通常の varchar なので、データをサーバーに戻すと、暗号化は解除されます。 
  
## <a name="selecting-deterministic-or-randomized-encryption"></a><a name="selecting--deterministic-or-randomized-encryption"></a> 明確な暗号化またはランダム化された暗号化の選択  
 データベース エンジンは、暗号化された列に格納されているプレーンテキスト データに対して動作しませんが、列の暗号化の種類によっては、暗号化されたデータでも一部のクエリをサポートしています。 Always Encrypted は、ランダム化された暗号化と明確な暗号化の 2 種類の暗号化をサポートします。  
  
- 明確な暗号化は、任意のプレーン テキストを指定した値の場合、常に同じ暗号化された値を生成します。 明確な暗号化を使用すると、暗号化された列で、ポイント参照、等価結合、グループ化、インデックス作成を行うことができます。 ただし、権限のないユーザーが、暗号化された列のパターンを調べることで、暗号化された値に関する情報を推測できる可能性もあります。特に、True/False や、North/South/East/West 地域など、暗号化可能な値セットが小規模である場合は注意が必要です。 明確な暗号化では、バイナリ 2 文字型の列の並べ替え順序を持つ列の照合順序を使用する必要があります。

- ランダム化された暗号化は低い予測可能な方法でデータを暗号化するためのメソッドを使用します。 ランダム化された暗号化は安全性が上がりますが、暗号化された列に対して検索、グループ化、インデックス作成、結合ができなくなります。

パラメーターの検索またはグループ化に使用される列については、明確な暗号化を使用します。 たとえば、身分登録番号などです。 他のレコードとグループ化されておらず、テーブルの結合に使用されていない信用調査情報などのデータについては、ランダム化された暗号化を使用します。
Always Encrypted による暗号化アルゴリズムの詳細については、「[Always Encrypted による暗号](../../../relational-databases/security/encryption/always-encrypted-cryptography.md)」を参照してください。

## <a name="configuring-always-encrypted"></a>Always Encrypted の構成

 データベースで Always Encrypted を初めてセットアップする場合、Always Encrypted キーの生成、キー メタデータの作成、選択したデータベース列の暗号化プロパティの構成、暗号化する必要がある列に既に存在する可能性があるデータの暗号化の一部またはすべてを行います。 これらのタスクの一部は Transact-SQL でサポートされていないので、クライアント側ツールを使用する必要があります。 Always Encrypted キーと保護対象の機密データは、サーバーに対してプレーンテキストで暴露されることはないので、データベース エンジンがキー プロビジョニングに関与し、データ暗号化または復号化操作を実行することはできません。 このようなタスクには、SQL Server Management Studio または PowerShell を使用できます。 

|タスク|SSMS|PowerShell|T-SQL|
|:---|:---|:---|:---
|対応する列マスター キーを使用した列マスター キー、列暗号化キー、暗号化された列の暗号化キーをプロビジョニングする。|はい|はい|いいえ|
|データベース内にキー メタデータを作成する。|はい|はい|はい|
|暗号化された列がある新しいテーブルを作成する|はい|はい|はい|
|選択されたデータベース列内にあるデータを暗号化する|はい|はい|いいえ|

> [!NOTE]
> [セキュリティで保護されたエンクレーブが設定された Always Encrypted](always-encrypted-enclaves.md) ([!INCLUDE[sql-server-2019](../../../includes/sssql19-md.md)] で導入) では、Trasact-SQL を使用した既存データの暗号化がサポートされます。 また、暗号化操作対象のデータの外部にあるデータを移動する必要もなくなります。

> [!NOTE]
> 必ず、データベースをホストするコンピューターと異なるコンピューターで、キー プロビジョニングまたはデータ暗号化ツールを実行してください。 そうしないと、機密データやキーがサーバー環境に漏れ、Always Encrypted を使用する利点が少なくなる可能性があります。  

Always Encrypted の構成の詳細については、以下を参照してください。
- [SSMS を使用した Always Encrypted の構成](../../../relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio.md)
- [PowerShell を使用した Always Encrypted の構成](../../../relational-databases/security/encryption/configure-always-encrypted-using-powershell.md)

## <a name="getting-started-with-always-encrypted"></a>Always Encrypted の作業の開始

[Always Encrypted Wizard](../../../relational-databases/security/encryption/always-encrypted-wizard.md) を使用すると、Always Encrypted ですぐに作業を開始できます。 このウィザードで必要なキーがプロビジョニングされ、選択した列の暗号化が構成されます。 暗号化を設定している列に、既に何らかのデータが含まれている場合、そのデータは暗号化されます。 次の例は、列を暗号化するプロセスを示します。

> [!NOTE]  
>  ウィザードの利用方法については、「 [Getting Started with Always Encrypted with SSMS](https://channel9.msdn.com/Shows/Data-Exposed/Getting-Started-with-Always-Encrypted-with-SSMS)」(SSMS での Always Encrypted の作業の開始) の動画をご覧ください。

1.  Management Studio の **オブジェクト エクスプローラー** を使用して暗号化する列があるテーブルを含む既存のデータベースに接続するか、新しいデータベースを作成し、暗号化する列がある 1 つ以上のテーブルを作成し、接続します。
2.  データベースを右クリックして **[タスク]** をポイントし、 **[列の暗号化]** をクリックして **Always Encrypted ウィザード** を開きます。
3.  **[概要]** ページの内容を確認し、 **[次へ]** をクリックします。
4.  **[列の選択]** ページで、テーブルを展開して暗号化する列を選択します。
5.  暗号化する選択した各列で、 **[暗号化の種類]** を *[明確]* または *[ランダム化]* のいずれかに設定します。
6.  暗号化する選択した各列で、 **[暗号化キー]** を選択します。 以前にこのデータベースに対して暗号化キーを作成していない場合は、新しく自動作成されたキーの既定の選択肢を選び、 **[次へ]** をクリックします。
7.  **[マスター キーの構成]** ページで、新しいキーを格納する場所を選択し、マスター キー ソースを選択し、 **[次へ]** をクリックします。
8.  **[検証]** ページで、スクリプトをすぐに実行するか PowerShell スクリプトを作成するかを選択し、 **[次へ]** をクリックします。
9.  **[概要]** ページで、選択したオプションを確認し、 **[完了]** をクリックします。 完了したらウィザードを閉じます。

  
## <a name="feature-details"></a>機能の詳細  
  
-   クエリでは、明確な暗号化を使用して暗号化された列の等値比較を実行できますが、その他の演算 (より大きい/より小さい、LIKE 演算子を使用したパターン一致、算術演算など) は実行できません。  
  
-   ランダム化された暗号化を使用して暗号化された列に対するクエリでは、これらの列に対していずれの操作も実行できません。 ランダム化された暗号化を使用して暗号化された列のインデックス作成はサポートされていません。  
 > [!NOTE] 
 > [!INCLUDE[sql-server-2019](../../../includes/sssql19-md.md)] で導入された[セキュリティで保護されたエンクレーブが設定された Always Encrypted](always-encrypted-enclaves.md) を使うと、ランダム化された暗号化を使用する列に対するパターン マッチング、比較演算子、インデックス作成が有効になり、上記の制限に対処できます。

-   列の暗号化キーには、それぞれ異なる列のマスター キーで暗号化された値を最大で 2 つ設定できます。 2 つ設定すると、列マスター キーの交換が容易になります。  
  
-   明確な暗号化には、列に [*バイナリ 2* 照合順序](../../../relational-databases/collations/collation-and-unicode-support.md)の 1 つが必要です。  

-   暗号化されたオブジェクトの定義を変更した後、[sp_refresh_parameter_encryption](../../../relational-databases/system-stored-procedures/sp-refresh-parameter-encryption-transact-sql.md) を実行して、オブジェクトに対する Always Encrypted のメタデータを更新します。
  
以下の特性を持つ列に対しては、Always Encrypted はサポートされていません。 たとえば、次の条件のいずれかが列に適用される場合、列に対する `CREATE TABLE/ALTER TABLE` で `ENCRYPTED WITH` 句を使用することはできません。  
  
-   `xml`、`timestamp`/`rowversion`、`image`、`ntext`、`text`、`sql_variant`、`hierarchyid`、`geography`、`geometry`、別名型、ユーザー定義型のいずれかのデータ型が使用されている列。  
- `FILESTREAM` 列  
- `IDENTITY` プロパティを持つ列。  
- `ROWGUIDCOL` プロパティを持つ列。  
- 非 BIN2 照合順序を持つ文字列 (`varchar`、`char` など) 列。  
- ランダム化された暗号化を使用するときのクラスター化インデックスおよび非クラスター化インデックスのキーである列 (決定論的な暗号化がサポートされています)。
- フルテキスト インデックスに含まれる列 (Always Encrypted で[フルテキスト検索](../../../relational-databases/search/full-text-search.md)はサポートされていません)。  
- 計算列。
- 計算列によって参照される列 (式が Always Encrypted でサポート外の演算を実行するとき)。  
- スパース列セット。  
- ランダム化された暗号化を使用するときに統計情報によって参照される列 (決定論的な暗号化がサポートされています)。  
- 別名型を使用する列。  
- パーティション分割列。  
- 既定の制約を含む列。  
- ランダムな暗号化を使用するときに UNIQUE 制約によって参照される列 (明確な暗号化はサポートされます)。  
- ランダムな暗号化を使用するときの主キー列 (明確な暗号化はサポートされます)。  
- ランダム化された暗号化または明確な暗号化を使用する際の、外部キー制約での列の参照 (参照元および参照先の列が異なるキーまたはアルゴリズムを使用する場合)。  
- CHECK 制約によって参照される列。  
- 変更データ キャプチャを使用してキャプチャまたは追跡される列。  
- 変更の追跡を持つテーブル上の主キー列。  
- マスキングされている列 (動的データ マスクを使用)。  
- Stretch Database テーブル内の列。 (Always Encrypted で暗号化された列を持つテーブルは Stretch で有効にできます)。  
- 外部 (PolyBase) テーブルの列 (注: 外部テーブルと暗号化された列を持つテーブルを同じクエリで使用することはサポートされています)。  
- 暗号化された列を対象にしたテーブル値パラメーターはサポートされていません。  

次の句は、暗号化された列では使用できません。

- `FOR XML`
- `FOR JSON PATH`

次の機能は、暗号化された列では機能しません。

- トランザクション レプリケーションまたはマージ レプリケーション
- 分散クエリ (リンク サーバー、`OPENROWSET` (T-SQL)、`OPENDATASOURCE` (T-SQL))

ツールの要件

- 暗号化された列から取得した結果を復号化するクエリ、または暗号化された列を挿入、更新、フィルター処理するクエリを実行するには、SQL Server Management Studio バージョン 18 以降をお勧めします。 
- `sqlcmd` バージョン 13.1 以降が必要です。これは、[ダウンロード センター](https://go.microsoft.com/fwlink/?LinkID=825643)から入手できます。

  
## <a name="database-permissions"></a>データベース権限  
 Always Encrypted には 4 つのアクセス許可があります。  
  
*  `ALTER ANY COLUMN MASTER KEY` (列マスター キーの作成と削除に必要です。)  
  
*  `ALTER ANY COLUMN ENCRYPTION KEY` (列暗号化キーの作成と削除に必要です。) 
  
*  `VIEW ANY COLUMN MASTER KEY DEFINITION` (キーの管理または暗号化列のクエリに使用する列マスター キーのメタデータのアクセスと読み取りに必要です。)  
  
*  `VIEW ANY COLUMN ENCRYPTION KEY DEFINITION` (キーの管理または暗号化列のクエリに使用する列暗号化キーのメタデータのアクセスと読み取りに必要です。)  
  
 次の表は、一般的な操作に必要なアクセス許可をまとめたものです。  
  
|シナリオ|<code>ALTER ANY COLUMN MASTER KEY</code>|<code>ALTER ANY COLUMN ENCRYPTION KEY</code>|<code>VIEW ANY COLUMN MASTER KEY DEFINITION</code>|<code>VIEW ANY COLUMN ENCRYPTION KEY DEFINITION</code>|  
|--------------|-----------------------------------|---------------------------------------|---------------------------------------------|-------------------------------------------------|  
|キー管理 (データベース内のキーの作成/変更/確認)|X|x|x|X|  
|暗号化された列のクエリ|||X|X|  
  
 **重要な注意点:**  
  
-   アクセス許可は、[!INCLUDE[tsql](../../../includes/tsql-md.md)][!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)] (ダイアログ ボックスとウィザード)、または PowerShell を使用するアクションに適用されます。  
  
-   2 つの *VIEW* アクセス許可は、ユーザーが列を復号化するアクセス許可を持っていない場合でも、暗号化された列を選択する場合に必要です。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]では、 *固定データベース ロールに対して、両方の* VIEW `public` アクセス許可が既定で付与されます。 データベース管理者は、 *ロールに対する* VIEW `public` アクセス許可を失効 (または拒否) し、特定のロールまたはユーザーに付与して、より制限が大きい制御を実装することができます。  
  
-   [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] では、`public` 固定データベース ロールに対して、*VIEW* アクセス許可が既定で付与されません。 これによって、(旧バージョンの DacFx を使用する) 一部の既存のレガシ ツールが正常に動作するようになります。 最終的に、暗号化された列を操作する場合 (復号化しない場合でも)、データベース管理者は 2 つの *VIEW* アクセス許可を明示的に付与する必要があります。  

  
## <a name="example"></a>例  
 次の [!INCLUDE[tsql](../../../includes/tsql-md.md)] は、列のマスター キー メタデータ、列の暗号化キー メタデータ、暗号化された列を持つテーブルを作成します。 メタデータで参照されるキーの作成方法の詳細については、以下を参照してください。
- [SSMS を使用した Always Encrypted の構成](../../../relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio.md)
- [PowerShell を使用した Always Encrypted の構成](../../../relational-databases/security/encryption/configure-always-encrypted-using-powershell.md)

  
```sql  
CREATE COLUMN MASTER KEY MyCMK  
WITH (  
     KEY_STORE_PROVIDER_NAME = 'MSSQL_CERTIFICATE_STORE',   
     KEY_PATH = 'Current User/Personal/f2260f28d909d21c642a3d8e0b45a830e79a1420'  
   );  
---------------------------------------------  
CREATE COLUMN ENCRYPTION KEY MyCEK   
WITH VALUES  
(  
    COLUMN_MASTER_KEY = MyCMK,   
    ALGORITHM = 'RSA_OAEP',   
    ENCRYPTED_VALUE = 0x01700000016C006F00630061006C006D0061006300680069006E0065002F006D0079002F003200660061006600640038003100320031003400340034006500620031006100320065003000360039003300340038006100350064003400300032003300380065006600620063006300610031006300284FC4316518CF3328A6D9304F65DD2CE387B79D95D077B4156E9ED8683FC0E09FA848275C685373228762B02DF2522AFF6D661782607B4A2275F2F922A5324B392C9D498E4ECFC61B79F0553EE8FB2E5A8635C4DBC0224D5A7F1B136C182DCDE32A00451F1A7AC6B4492067FD0FAC7D3D6F4AB7FC0E86614455DBB2AB37013E0A5B8B5089B180CA36D8B06CDB15E95A7D06E25AACB645D42C85B0B7EA2962BD3080B9A7CDB805C6279FE7DD6941E7EA4C2139E0D4101D8D7891076E70D433A214E82D9030CF1F40C503103075DEEB3D64537D15D244F503C2750CF940B71967F51095BFA51A85D2F764C78704CAB6F015EA87753355367C5C9F66E465C0C66BADEDFDF76FB7E5C21A0D89A2FCCA8595471F8918B1387E055FA0B816E74201CD5C50129D29C015895CD073925B6EA87CAF4A4FAF018C06A3856F5DFB724F42807543F777D82B809232B465D983E6F19DFB572BEA7B61C50154605452A891190FB5A0C4E464862CF5EFAD5E7D91F7D65AA1A78F688E69A1EB098AB42E95C674E234173CD7E0925541AD5AE7CED9A3D12FDFE6EB8EA4F8AAD2629D4F5A18BA3DDCC9CF7F352A892D4BEBDC4A1303F9C683DACD51A237E34B045EBE579A381E26B40DCFBF49EFFA6F65D17F37C6DBA54AA99A65D5573D4EB5BA038E024910A4D36B79A1D4E3C70349DADFF08FD8B4DEE77FDB57F01CB276ED5E676F1EC973154F86  
);  
---------------------------------------------  
CREATE TABLE Customers (  
    CustName nvarchar(60)   
        COLLATE  Latin1_General_BIN2 ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = MyCEK,  
        ENCRYPTION_TYPE = RANDOMIZED,  
        ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256'),   
    SSN varchar(11)   
        COLLATE  Latin1_General_BIN2 ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = MyCEK,  
        ENCRYPTION_TYPE = DETERMINISTIC ,  
        ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256'),   
    Age int NULL  
);  
GO  
  
```  
## <a name="see-also"></a>参照  
- [SSMS を使用した Always Encrypted の構成](../../../relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio.md)   
- [PowerShell を使用した Always Encrypted の構成](../../../relational-databases/security/encryption/configure-always-encrypted-using-powershell.md)   
- [Always Encrypted を使用したアプリケーションの開発](always-encrypted-client-development.md) 
- [Always Encrypted ウィザードを使用して列暗号化を構成する](always-encrypted-wizard.md)
- [Always Encrypted による暗号](../../../relational-databases/security/encryption/always-encrypted-cryptography.md)   
- [CREATE COLUMN MASTER KEY &#40;Transact-SQL&#41;](../../../t-sql/statements/create-column-master-key-transact-sql.md)   
- [CREATE COLUMN ENCRYPTION KEY &#40;Transact-SQL&#41;](../../../t-sql/statements/create-column-encryption-key-transact-sql.md)   
- [CREATE TABLE &#40;Transact-SQL&#41;](../../../t-sql/statements/create-table-transact-sql.md)   
- [column_definition &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-table-column-definition-transact-sql.md)   
- [sys.column_encryption_keys  &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-column-encryption-keys-transact-sql.md)  
- [sys.column_encryption_key_values &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-column-encryption-key-values-transact-sql.md)   
- [sys.column_master_keys &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-column-master-keys-transact-sql.md)   
- [sys.columns &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
- [sp_refresh_parameter_encryption (Transact-SQL)](../../../relational-databases/system-stored-procedures/sp-refresh-parameter-encryption-transact-sql.md)   
  
  
