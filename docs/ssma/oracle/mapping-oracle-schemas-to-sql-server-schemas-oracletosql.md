---
title: SQL Server スキーマ (OracleToSQL) への Oracle スキーマのマッピング |Microsoft Docs
description: SSMA for Oracle スキーマと SQL Server 間のマッピングをカスタマイズする方法、または既定値をそのまま使用する方法について説明します。
ms.prod: sql
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: 0edeaa08-9c5d-4e3a-bc15-b9a1f0c8a9dc
author: nahk-ivanov
ms.author: alexiva
manager: alexiva
ms.openlocfilehash: dc144017c6bf45a21721fe647dbf5affa486f8ef
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105632921"
---
# <a name="mapping-oracle-schemas-to-sql-server-schemas-oracletosql"></a>SQL Server スキーマへの Oracle スキーマのマッピング (OracleToSQL)
Oracle では、各データベースに1つ以上のスキーマがあります。 既定では、SSMA は、Oracle スキーマ内のすべてのオブジェクトを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、スキーマのという名前のデータベースに移行します。 ただし、Oracle スキーマとデータベース間のマッピングをカスタマイズすることはでき [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
## <a name="oracle-and-sql-server-schemas"></a>Oracle スキーマと SQL Server スキーマ  
Oracle データベースには、スキーマが含まれています。 のインスタンスには [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複数のデータベースが含まれており、それぞれが複数のスキーマを持つことができます。  
  
スキーマの Oracle の概念は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースの概念とそのスキーマの1つにマップされます。 たとえば、Oracle には **HR** という名前のスキーマが存在する場合があります。 のインスタンスには [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、 **sampledatabase** という名前のデータベースがあり、そのデータベース内には複数のスキーマが存在する場合があります。 既定では、Oracle スキーマの **HR** は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースとスキーマ **SampleDatabase.HR** にマップされます。 SSMA [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] とは、スキーマとしてのデータベースとスキーマの組み合わせを意味します。  
  
Oracle とスキーマ間のマッピングを変更でき [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
## <a name="modifying-the-target-database-and-schema"></a>ターゲットデータベースとスキーマの変更  
SSMA では、Oracle スキーマを任意の使用可能なスキーマにマップでき [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
**データベースとスキーマを変更するには**  
  
1.  Oracle メタデータエクスプローラーで、[ **スキーマ**] を選択します。  
  
    [ **スキーママッピング** ] タブは、個々のデータベース、 **スキーマ** フォルダー、または個々のスキーマを選択した場合にも使用できます。 [ **スキーママッピング** ] タブの一覧は、選択したオブジェクトに合わせてカスタマイズされています。  
  
2.  右ペインで、[ **スキーママッピング** ] タブをクリックします。  
  
    すべての Oracle スキーマの一覧が表示され、その後にターゲット値が表示されます。 このターゲットは、のオブジェクトとデータを移行する2つの部分表記 (*データベーススキーマ*) で示され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ています。  
  
3.  変更するマッピングを含む行を選択し、[ **変更**] をクリックします。  
  
    [ **ターゲットスキーマの選択** ] ダイアログボックスで、使用可能なターゲットデータベースとスキーマを参照するか、2つの部分表記 (データベーススキーマ) のテキストボックスにデータベースとスキーマ名を入力し、[ **OK**] をクリックします。  
  
4.  [ **スキーママッピング** ] タブでターゲットが変更されます。  
  
**マッピングのモード**  
  
-   SQL Server へのマッピング  
  
ソースデータベースを任意のターゲットデータベースにマップできます。 既定では、ソースデータベースは SSMA を使用して接続したターゲットデータベースにマップされ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 マップされているターゲットデータベースがに存在しない場合は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **"データベースまたはスキーマがターゲットメタデータに存在しません" というメッセージが表示され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。同期中に作成されます。続行しますか? "** [はい] をクリックします。 同様に、スキーマを、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 同期中に作成されるターゲットデータベース下に存在しないスキーマにマップすることもできます。  
  
## <a name="reverting-to-the-default-database-and-schema"></a>既定のデータベースとスキーマに戻す  
Oracle スキーマとスキーマ間のマッピングをカスタマイズする場合は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] マッピングを既定値に戻すことができます。  
  
**既定のデータベースとスキーマに戻すには**  
  
1.  [スキーママッピング] タブで、任意の行を選択し、[既定 **値にリセット** ] をクリックして既定のデータベースとスキーマに戻します。  
  
## <a name="next-steps"></a>次の手順  
Oracle オブジェクトからオブジェクトへの変換を分析する場合は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、 [変換レポートを作成](assessing-oracle-schemas-for-conversion-oracletosql.md)できます。 それ以外の場合は [、Oracle データベースオブジェクト定義](converting-oracle-schemas-oracletosql.md) をオブジェクト定義に変換でき [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
## <a name="see-also"></a>参照  
[SQL Server &#40;OracleToSQL&#41;に接続しています ](../../ssma/oracle/connecting-to-sql-server-oracletosql.md)  
[Oracle データベースの SQL Server &#40;OracleToSQL&#41;への移行 ](../../ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql.md)  
  
