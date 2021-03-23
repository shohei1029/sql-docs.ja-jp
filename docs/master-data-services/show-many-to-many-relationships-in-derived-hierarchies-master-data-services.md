---
description: 派生階層 (Master Data Services) の多対多リレーションシップを表示する
title: 派生階層の多対多リレーションシップを表示する
ms.custom: seo-lt-2019
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: mds
ms.reviewer: ''
ms.technology: master-data-services
ms.topic: conceptual
ms.assetid: 8b2a9c43-40e0-48f7-a6a9-325beb9f27da
author: lrtoyou1223
ms.author: lle
ms.openlocfilehash: ab2eaf50f73ae59bdde5cb40c75050bb0bc8206f
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833806"
---
# <a name="show-many-to-many-relationships-in-derived-hierarchies-master-data-services"></a>派生階層 (Master Data Services) の多対多リレーションシップを表示する

[!INCLUDE [SQL Server - Windows only ASDBMI  ](../includes/applies-to-version/sql-windows-only-asdbmi.md)]

  派生階層 (DH) には 1 対多のリレーションシップが表示されますが、多対多のリレーションシップも表示できるようになります。  
  
## <a name="many-to-many-m2m-relationships"></a>多対多 (M2M) リレーションシップ  

2 つのエンティティ間の多対多 (M2M) リレーションシップは、両者間のマッピングを提供する第 3 のエンティティを使用してモデリングできます。  
  
![mds_hierarchies_manytomany](../master-data-services/media/mds-hierarchies-manytomany.png "mds_hierarchies_manytomany")  
  
上の例では、マッピング エンティティ **ClassRegistration** に指定された、 **Employee** エンティティと **TrainingClass** エンティティ間に M2M リレーションシップがあります。 1 人の従業員は、複数クラスの受講者として登録できます。また、各クラスには、複数の受講者を含めることができます。  
  
たとえば、クラスごとに学生を表示する派生階層を作成したり、リレーションシップを反転して、学生別にグループ化されたクラスを表示したりすることができます。  

> [!NOTE]
> [!INCLUDE [sssql16-md](../includes/sssql16-md.md)] M2M リレーションシップの派生階層が導入されました。 この機能は、このバージョンより前では使用できませんでした。
  
まず、[派生階層] 管理ページにアクセスし、新しい派生階層を作成します。  
  
 ![mds_hierarchies_add_derived_hierarchy](../master-data-services/media/mds-hierarchies-add-derived-hierarchy.png "mds_hierarchies_add_derived_hierarchy")  
  
 次に、下から順に新しい派生階層にレベルを追加します。 この例では、クラス別にグループ化して受講者 (従業員) を表示するのが目的です。 そのため、 **Employee** エンティティが階層のリーフ レベルで、最初に追加されます。  
  
 ![mds_hierarchies_edit_derived_hierarchy_one](../master-data-services/media/mds-hierarchies-edit-derived-hierarchy-one.PNG "mds_hierarchies_edit_derived_hierarchy_one")  
  
 上のスクリーンショットで、 **Employee** エンティティが唯一のレベルとして **[現在のレベル]** の中間に表示される点に注目してください。 右側の派生階層の **プレビュー** には、 **Employee** エンティティのすべてのメンバーの一覧が表示されます。 左側の **[使用できるレベル]** セクションには、現在の上位レベル (**Employee**) に追加できるレベルが表示されます。 そのほとんどは、 **Department** DBA を含む、 **Employee** エンティティのドメインベースの属性 (DBA) です。  
  
 [!INCLUDE[ssnoversion](../includes/ssnoversion-md.md)]以降は、M2M リレーションシップをモデリングする新しい種類のレベルがあります。例: **Class (ClassRegistration.Student でマッピング)**。 レベル名は他の情報よりも詳細で、マッピング リレーションシップをあいまいに説明するために必要な追加情報を反映しています。 このレベルを **[現在のレベル]** セクションの **[Employee]** レベルにドロップ ダウンします。  
  
 ![mds_hierarchies_edit_derived_hierarchy_two](../master-data-services/media/mds-hierarchies-edit-derived-hierarchy-two.PNG "mds_hierarchies_edit_derived_hierarchy_two")  
  
 これで、プレビューには、登録したトレーニング クラスごとにグループ化された従業員が表示されるようになります。 これは M2M リレーションシップなので、各子メンバーは複数の親を持つ可能性があります。 上の例では、従業員 **6 {Hillman, Reinout N}** は 2 つのクラス **1 {Master Data Services 101}** と 4 **{Career-Limiting Moves}** で受講者として登録されています。  
  
 このマッピングのリレーションシップは、反転させて、受講者別にクラスをグループ化して表示することもできます。  
  
 ![mds_hierarchies_available_entities_and_hierarchies](../master-data-services/media/mds-hierarchies-available-entities-and-hierarchies.PNG "mds_hierarchies_available_entities_and_hierarchies")  
  
 ここでも、1 つの子を複数の親に表示できる方法について説明します。トレーニング クラス **1 {Master Data Services 101}** は、 **6 {Hillman, Reinout N}** と **40 {Ford, Jeffrey L}** の両方に表示されます。  
  
 マッピングエンティティ **Classregistration** のメンバーは、派生階層内のどこにも表示されません。 階層内の親と子メンバー間のリレーションシップを定義するためだけに使用されることはありません。  
  
 マッピング エンティティ メンバーを変更し、次のいずれかを実行して M2M リレーションシップを編集できます。 M2M リレーションシップは、 **[派生階層エクスプローラー]** ページで読み取り専用です。  
  
-   Excel 用のマスター データ サービス アドイン、またはデータ ステージングを使用して、 **[エンティティ エクスプローラー]** ページでマッピング エンティティ メンバーを変更します。  
  
-   **派生階層エクスプローラー** ページで親の間に子ノードをドラッグアンドドロップします。  
  
     このメソッドは、可能であれば既存のメンバーを変更し、必要に応じて新しいメンバーを追加します。 既存のメンバーは削除されません。  
  
     たとえば、ClassRegistration マッピング エンティティがあり、受講者を未使用のノードに移動すると、対応するマッピング エンティティ メンバーのクラス属性値は null に変更されますが、メンバーは削除されません。 反対に、未使用のノードから受講者をいずれかのクラスに移動するときに、クラスが null の場合に受講者に対応する既存のマッピング メンバーがあると、そのメンバーは、null から新しい親に変更することで修正します。 このようなメンバーがない場合は、追加します。  
  
     このプロセスでは、メンバーの削除を回避することで、他のユーザー データの不要な削除を回避します。たとえば、マッピング エンティティに、親子のリレーションシップを定義する 2 つの属性を除く他の属性が含まれている場合などです。 ユーザーは、マッピング エンティティで直接、明示的に削除を実行する必要があります。  
  
 新しい M2M レベルは、ドメインベースの属性 (DBA) レベルが許可されている派生階層内の任意の場所で使用できます。 M2M レベルは、上の例のように最上位にすることができます。 再帰レベルを含め、DBA レベルより上位または下位の場合があります。 明示的階層 (非推奨) のキャップ レベルより下位の場合があります。 同じ派生階層内に複数の M2M リレーションシップを連結できます。  
  
 M2M レベルは、他の派生階層レベルと同様に非表示にすることができます。  
   
### <a name="m2m-relationship-in-sample-model"></a><a name="M2MSample"></a> サンプル モデル内の M2M リレーションシップ  
M2M リレーションシップのデモを見るには、 [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]に含まれている Customer サンプル モデル内の Region Climate 派生階層を表示します。   
  
次の画像に示すように、このリレーションシップをモデル化したレベル名は ![mds_Number1](../master-data-services/media/mds-number1.png)**Climate です(RegionClimate.Region でマッピング)**。 ![mds_Number2](../master-data-services/media/mds-number2.png)**Preview** には、地域が、関連付けられている気候の種類によってグループ化されて表示されます。 複数の気候 (親) に関連付けられた地域 (子メンバー) が存在するため、これは M2M リレーションシップです。 たとえば、 ![mds_Number3](../master-data-services/media/mds-number3.png)**APCR {Asia Pacific}** は ![mds_Number4](../master-data-services/media/mds-number4.png)**A {Tropical}** と ![mds_Number5](../master-data-services/media/mds-number5.png)**B {Dry}** に関連付けられています。  
  
![mds_M2MRelationship_Example_CustomerModel](../master-data-services/media/mds-m2mrelationship-example-customermodel.png)  
  
Customer サンプル モデルや、 [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]に含まれている他のサンプル モデルを配置する手順については、「 [サンプル モデルとデータを配置する](~/master-data-services/sql-server-samples-model-deployment-packages-mds.md)」を参照してください。   
  
## <a name="one-many-relationship"></a>1 対多のリレーションシップ  
 DH のメンバーは、多くの子メンバーの親になることができますが、一般的に、複数の親を持つことはできません (例外については、「 [メンバーのセキュリティ](#bkmk_member_security)」を参照してください)。 たとえば、Employee と Department という 2 つのエンティティがあるとします。各従業員 (employee) は 1 つの部門 (department) に属します。 このリレーションシップは、Employee エンティティに、Department エンティティを参照するドメインベースの属性 (DBA) を追加してモデリングします。  
  
 ![mds_hierarchies_onetomany](../master-data-services/media/mds-hierarchies-onetomany.png "mds_hierarchies_onetomany")  
  
 各従業員は 1 つの部門にのみ属し、各部門には複数の従業員が属する可能性があるので、これは 1 対多のリレーションシップです。 部門別にグループ化された従業員を表示する派生階層を作成できます。  
  
 ![mds_hierarchies_dh_screenshot](../master-data-services/media/mds-hierarchies-dh-screenshot.png "mds_hierarchies_dh_screenshot")  
  
##  <a name="member-security"></a><a name="bkmk_member_security"></a> メンバーのセキュリティ  
 メンバーの重複を許可する (1 人のメンバーが複数の親を持つことを許可する) 階層を使用して、メンバーのセキュリティ アクセス許可を割り当てることはできません。 次に例を示します。  
  
-   Null 再帰をアンカーしない再帰派生階層 (RDH) (再帰レベルの各メンバーは、ルートと再帰的な親の両方に表示されます)。  
  
-   再帰レベルより上位のレベルの再帰的派生階層 (再帰レベルの各メンバーは、再帰的ではない親と再帰的な親の両方に表示されます)。  
  
-   M2M レベルの派生階層 (子は多数の親にマップされる場合があります)。  
  
## <a name="collections"></a>コレクション  
 コレクションと明示的階層は非推奨とされます。 変換ストアド プロシージャ (udpConvertCollectionAndConsolidatedMembersToLeaf) は、コレクション メンバーをリーフ メンバーに変換し、多対多の派生階層を作成し、コレクション メンバー情報をキャプチャします。  
  
## <a name="see-also"></a>参照  
 [派生階層 (マスター データ サービス)](../master-data-services/derived-hierarchies-master-data-services.md)  
  
  
