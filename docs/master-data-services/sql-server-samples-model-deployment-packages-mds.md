---
description: 'SQL Server の例: モデルの配置パッケージ (MDS)'
title: モデルの配置パッケージの例
ms.custom: ''
ms.date: 07/28/2017
ms.prod: sql
ms.prod_service: mds
ms.reviewer: ''
ms.technology: master-data-services
ms.topic: conceptual
keywords:
- マスター データ サービス
- サンプル
ms.assetid: 9b31b7b6-319b-4840-b67d-eb383e7762b1
author: lrtoyou1223
ms.author: lle
ms.openlocfilehash: 3dbb973b72d2df5a3a23ea40684ad7da47c19777
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833735"
---
# <a name="sql-server-examples-model-deployment-packages-mds"></a>SQL Server の例: モデルの配置パッケージ (MDS)

[!INCLUDE [SQL Server - Windows only ASDBMI  ](../includes/applies-to-version/sql-windows-only-asdbmi.md)]

  [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]をインストールすると、データの入ったサンプル モデル パッケージが追加されます。 これらのパッケージファイルの既定の場所は、"130" \<drive> SQL \ (マスターデータ services\samples\packages です。  
  
 サンプル モデル パッケージを配置する方法については、「 [サンプル モデルとデータを配置する](../master-data-services/master-data-services-installation-and-configuration.md#deploySample)」を参照してください。 サンプル モデル パッケージは、 [MDSModelDeploy ツール](../master-data-services/deploy-a-model-deployment-package-by-using-mdsmodeldeploy.md)を使用して配置します。  
  
> [!IMPORTANT]
>  **サンプルの更新: [!INCLUDE[ssnoversion](../includes/ssnoversion-md.md)]**  
> 
>  サンプルパッケージでは、次の機能がサポートされています。  
> 
>  -   多対多リレーションシップの表示。  
> 
>      詳細については、「 [サンプル モデル内の M2M リレーションシップ](../master-data-services/show-many-to-many-relationships-in-derived-hierarchies-master-data-services.md#M2MSample)」を参照してください。  
> 
> -   ドメイン ベースの属性の指定できる値を制限します。  
> 
>      詳細については、「[ドメイン ベースの属性を作成する (マスター データ サービス)](../master-data-services/create-a-domain-based-attribute-master-data-services.md)」を参照してください。  
> -   エンティティの変更に承認が必要。  
> 
>      詳細については、「 [承認が必要 &#40;マスターデータサービス&#41;](../master-data-services/approval-required-master-data-services.md)」を参照してください。  
> -   ビジネス ルールでの Not および Else 演算子の使用。  
> 
>      詳細については、「 [ビジネス ルールの例](../master-data-services/business-rule-examples-master-data-services.md)」を参照してください。  
> -   カスタム インデックスの実装  
> 
>      詳細については、「[カスタム インデックス (マスター データ サービス)](../master-data-services/custom-index-master-data-services.md)」を参照してください。  
 

 
 マスター データ サービスのパッケージは、配置可能なモデル構造と (必要に応じて) モデル データを含んだ XML ファイルです。 モデル パッケージを使用して、モデルのコピーを MDS 環境間で移動したり、既存の [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 環境に新しいモデルを作成したりすることができます。  
  
## <a name="see-also"></a>参照  
 [MDSModelDeploy を使用したモデルの配置パッケージの配置](../master-data-services/deploy-a-model-deployment-package-by-using-mdsmodeldeploy.md)  
  
  
