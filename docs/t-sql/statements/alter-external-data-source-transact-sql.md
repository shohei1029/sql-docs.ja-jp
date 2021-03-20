---
description: ALTER EXTERNAL DATA SOURCE (Transact-SQL)
title: ALTER EXTERNAL DATA SOURCE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ALTER EXTERNAL DATA SOURCE
- ALTER_EXTERNAL_DATA_SOURCE
dev_langs:
- TSQL
helpviewer_keywords:
- polybase, alter external data source statement
- ALTER EXTERNAL DATA SOURCE statement
ms.assetid: a34b9e90-199d-46d0-817a-a7e69387bf5f
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 642c52250c5802968595e68c1b428ed9d1b2e668
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751892"
---
# <a name="alter-external-data-source-transact-sql"></a>ALTER EXTERNAL DATA SOURCE (Transact-SQL)
[!INCLUDE [sqlserver2016-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdbmi-asa-pdw.md)]

  外部テーブルを作成するために使用する外部データ ソースを変更します。 外部データ ソースとして使用できるのは、SQL SERVER の場合は Hadoop または Azure Blob Storage (WASBS)、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] の場合は Azure Blob Storage (WASBS) または Azure Data Lake storage (ABFSS/ADL) です。 

## <a name="syntax"></a>構文  

```syntaxsql
-- Modify an external data source
-- Applies to: SQL Server (2016 or later) and APS
ALTER EXTERNAL DATA SOURCE data_source_name SET
    {   
        LOCATION = '<prefix>://<path>[:<port>]' [,] |
        RESOURCE_MANAGER_LOCATION = <'IP address;Port'> [,] |
        CREDENTIAL = credential_name
    }  
    [;]  

-- Modify an external data source pointing to Azure Blob storage
-- Applies to: SQL Server (starting with 2017)
ALTER EXTERNAL DATA SOURCE data_source_name
    SET
        LOCATION = 'https://storage_account_name.blob.core.windows.net'
        [, CREDENTIAL = credential_name ] 

-- Modify an external data source pointing to Azure Blob storage or Azure Data Lake storage
-- Applies to: Azure Synapse Analytics
ALTER EXTERNAL DATA SOURCE data_source_name
    SET
        [LOCATION = '<location prefix>://<location path>']
        [, CREDENTIAL = credential_name ] 
```

## <a name="arguments"></a>引数  
 data_source_name: データ ソースのユーザー定義の名前を指定します。 名前は一意である必要があります。

 LOCATION = '<prefix>://<path>[:<port>]': 外部データ ソースへの接続プロトコル、パス、ポートを指定します。 有効な場所のオプションについては、「[CREATE EXTERNAL DATA SOURCE &#40;Transact-SQL&#41;](create-external-data-source-transact-sql.md#location--prefixpathport)」を参照してください。

 RESOURCE_MANAGER_LOCATION = '\<IP address;Port>' ([!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] には適用されない) Hadoop リソース マネージャーの場所を指定します。 指定した場合、クエリ オプティマイザーは Hadoop の計算の機能を使用して、PolyBase クエリのデータを事前処理こともできます。 これは、コストベースの判断です。 述語のプッシュダウンが呼び出されると、この Hadoop と SQL の間で転送されるデータ量が大幅に減少し、クエリのパフォーマンスが向上します。

 CREDENTIAL = Credential_Name: 名前付きの資格情報を指定します。 「[CREATE DATABASE SCOPED CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/create-database-scoped-credential-transact-sql.md)」を参照してください。

TYPE = [HADOOP | BLOB_STORAGE]   
**適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)]。
一括操作の場合のみ、`LOCATION` は Azure BLOB ストレージの有効な URL にする必要があります。 `LOCATION` URL の末尾に、 **/** 、ファイル名、または Shared Access Signature パラメーターを配置しないでください。
使用される資格情報は、`SHARED ACCESS SIGNATURE` を使用して ID として作成する必要があります。 Shared Access Signature に関する詳細については、「[Shared Access Signature (SAS) を使用](/azure/storage/storage-dotnet-shared-access-signature-part-1)」を参照してください。

  

## <a name="remarks"></a>解説
 一度に変更できるソースは 1 つだけです。 同じソースを変更する複数の要求が同時に発生すると、1 つのステートメントが待機状態になります。 ただし、さまざまなソースを同時に変更できます。 このステートメントは、他のステートメントと同時実行できます。

## <a name="permissions"></a>アクセス許可  
 ALTER ANY EXTERNAL DATA SOURCE 権限が必須です。
 > [!IMPORTANT]  
 > ALTER ANY EXTERNAL DATA SOURCE 権限は、あらゆる外部データ ソース オブジェクトを作成し、変更する能力をプリンシパルに与えます。そのため、データベース上のすべてのデータベース スコープ資格情報にアクセスする能力も与えます。 この権限は特権として考える必要があります。したがって、システム内の信頼できるプリンシパルにのみ与える必要があります。


## <a name="examples"></a>例  
 次の例では、既存のデータソースの場所とリソース マネージャーの場所を変更します。

```sql  
ALTER EXTERNAL DATA SOURCE hadoop_eds SET
     LOCATION = 'hdfs://10.10.10.10:8020',
     RESOURCE_MANAGER_LOCATION = '10.10.10.10:8032'
    ;
```

 次の例では、既存のデータ ソースに接続できるように資格情報を変更します。

```sql 
ALTER EXTERNAL DATA SOURCE hadoop_eds SET
   CREDENTIAL = new_hadoop_user
    ;
```
 次の例では、資格情報を新しい LOCATION に変更します。 この例は、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] に対して作成される外部データ ソースです。 

```sql  
ALTER EXTERNAL DATA SOURCE AzureStorage_west SET
   LOCATION = 'wasbs://loadingdemodataset@updatedproductioncontainer.blob.core.windows.net',
   CREDENTIAL = AzureStorageCredential
```