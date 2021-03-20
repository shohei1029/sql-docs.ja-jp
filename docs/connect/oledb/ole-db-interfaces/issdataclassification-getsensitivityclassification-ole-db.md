---
title: ISSDataClassification::GetSensitivityClassification | Microsoft Docs
description: ISSDataClassification::GetSensitivityClassification
ms.custom: ''
ms.date: 09/30/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: v-daenge
ms.technology: connectivity
ms.topic: reference
apiname:
- ISSDataClassification::GetSensitivityClassification
apitype: COM
helpviewer_keywords:
- GetSensitivityClassification method
author: bazizi
ms.author: v-beaziz
ms.openlocfilehash: a703f659d9107ebc93202c631dc402f394b22afa
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753112"
---
# <a name="issdataclassificationgetsensitivityclassification"></a>ISSDataClassification::GetSensitivityClassification
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW](../../../includes/applies-to-version/sql-asdb-asa.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  アクティブな行セットの秘密度分類データを取得します。 詳細とコード サンプルについては、「[データ分類の使用](../features/using-data-classification.md)」を参照してください。  
  
## <a name="syntax"></a>構文  
  
```  
HRESULT GetSensitivityClassification(
    SENSITIVITYCLASSIFICATION** ppSensitivityClassification);
```  
  
## <a name="arguments"></a>引数  
  *ppSensitivityClassification*[out]  
 SENSITIVITYCLASSIFICATION 構造体ポインターへのポインター。 メソッドが失敗するか、使用できるデータ分類情報がない場合、プロバイダーはメモリの割り当てを行わず、出力時に ppSensitivityClassification 引数に Null ポインターが設定されます。  
  
## <a name="return-code-values"></a>リターン コードの値  
 S_OK  
 メソッドが成功しました。    
  
 E_INVALIDARG  
 *ppSensitivityClassification* 引数が NULL でした。  
  
 E_OUTOFMEMORY  
 OLE DB Driver for SQL Server が、要求を完了するために十分なメモリを割り当てることができませんでした。  

  
## <a name="remarks"></a>注釈  
OLE DB Driver for SQL Server によって、SENSITIVITYCLASSIFICATION 構造体とこの構造体によって参照されるデータを保持するためのメモリ ブロックが割り当てられます。 コンシューマーでは、分類データにアクセスする必要がなくなったときに、[IMalloc::Free](/windows/win32/api/objidl/nf-objidl-imalloc-free) メソッドを呼び出してこのメモリの割り当てを解除する必要があります。  
  
 SENSITIVITYCLASSIFICATION 構造体は次のように定義されています。
  
```cpp
typedef struct tagSensitivityClassification
{
    USHORT                     cSensitivityLabels;
    SENSITIVITYLABEL          *rgSensitivityLabels;
    USHORT                     cInformationTypes;
    INFORMATIONTYPE           *rgInformationTypes;
    USHORT                     cColumnSensitivityMetadata;
    COLUMNSENSITIVITYMETADATA *rgColumnSensitivityMetadata;
    SENSITIVITYRANKENUM        eQuerySensitivityRank;
} SENSITIVITYCLASSIFICATION;
```  

|メンバー|説明|  
|------------|-----------------|  
|*cSensitivityLabels*|*rgSensitivityLabels* 内の SENSITIVITYLABEL 構造体の数。|  
|*rgSensitivityLabels*|SENSITIVITYLABEL 構造体の配列。|  
|*cInformationTypes*|*rgInformationTypes* 内の INFORMATIONTYPE 構造体の数。|  
|*rgInformationTypes*|INFORMATIONTYPE 構造体の配列。|  
|*cColumnSensitivityMetadata*|*rgColumnSensitivityMetadata* 内の COLUMNSENSITIVITYMETADATA 構造体の数。|  
|*rgColumnSensitivityMetadata*|COLUMNSENSITIVITYMETADATA 構造体の配列。|  
|*eQuerySensitivityRank*|行セットを取得するために実行されたクエリの秘密度の相対的な順位付け。|  

SENSITIVITYLABEL 構造体は次のように定義されています。
```cpp
typedef struct tagSENSITIVITYLABEL
{
    LPOLESTR pwszName;
    LPOLESTR pwszId;
} SENSITIVITYLABEL;
```

|メンバー|説明|  
|------------|-----------------|  
|*pwszName*|秘密度ラベルの名前。|  
|*pwszId*|秘密度ラベルの識別子。|  

INFORMATIONTYPE 構造体は次のように定義されています。
```cpp
typedef struct tagINFORMATIONTYPE
{
    LPOLESTR pwszName;
    LPOLESTR pwszId;
} INFORMATIONTYPE;
```

|メンバー|説明|  
|------------|-----------------|  
|*pwszName*|情報の種類の名前。|  
|*pwszId*|情報の種類の識別子。|  

COLUMNSENSITIVITYMETADATA 構造体は次のように定義されています。
```cpp
typedef struct tagCOLUMNSENSITIVITYMETADATA
{
    SENSITIVITYPROPERTY* rgSensitivityProperties;
    USHORT cSensitivityProperties;
} COLUMNSENSITIVITYMETADATA;
```

|メンバー|説明|  
|------------|-----------------|  
|*cSensitivityProperties*|*rgSensitivityProperties* 内の SENSITIVITYPROPERTY 構造体の数。|  
|*rgSensitivityProperties*|SENSITIVITYPROPERTY 構造体の配列。|  

SENSITIVITYRANKENUM 列挙型は次のように定義されています。
```cpp
typedef enum tagSENSITIVITYRANKENUM
{
    SENSITIVITYRANK_NOT_DEFINED = -1,
    SENSITIVITYRANK_NONE = 0,
    SENSITIVITYRANK_LOW = 10,
    SENSITIVITYRANK_MEDIUM = 20,
    SENSITIVITYRANK_HIGH = 30,
    SENSITIVITYRANK_CRITICAL = 40
} SENSITIVITYRANKENUM;
```

SENSITIVITYPROPERTY 構造体は次のように定義されています。
```cpp
typedef struct tagSENSITIVITYPROPERTY
{
    SENSITIVITYLABEL* pSensitivityLabel;
    INFORMATIONTYPE* pInformationType;
    SENSITIVITYRANKENUM eSensitivityRank;
} SENSITIVITYPROPERTY;
```

|メンバー|説明|  
|------------|-----------------|  
|*pSensitivityLabel*|SENSITIVITYLABEL 構造体へのポインター。|  
|*pInformationType*|INFORMATIONTYPE 構造体へのポインター。|  
|*eSensitivityRank*|列ごとのデータの一部である列の秘密度の相対的な順位付け。|  

## <a name="see-also"></a>関連項目  
 [ISSDataClassification](../../oledb/ole-db-interfaces/issdataclassification-ole-db.md)  
 [行セット](../ole-db-rowsets/rowsets.md)  
