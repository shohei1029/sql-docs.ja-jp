---
description: 照合順序関数 - COLLATIONPROPERTY (Transact-SQL)
title: COLLATIONPROPERTY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- COLLATIONPROPERTY_TSQL
- COLLATIONPROPERTY
dev_langs:
- TSQL
helpviewer_keywords:
- collations [SQL Server], properties
- COLLATIONPROPERTY function
ms.assetid: f5029e74-a1db-4f69-b0f5-5ee920c3311d
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9b58b98e0ea326e62b86e9b8f2c64c26f8856cd7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735067"
---
# <a name="collation-functions---collationproperty-transact-sql"></a>照合順序関数 - COLLATIONPROPERTY (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、指定された照合順序の要求されたプロパティを返します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
COLLATIONPROPERTY( collation_name , property )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*collation_name*  
照合順序の名前です。 *collation_name* 引数は、**nvarchar (128)** データ型で、既定値はありません。
  
*property*  
collation プロパティ。 *property* 引数は、**varchar (128)** データ型で、次のいずれかの値を持つことができます。
  
|プロパティ名|説明|  
|---|---|
|**CodePage**|照合順序の Unicode 以外のコード ページ。 これは、**varchar** データで使用される文字セットです。 これらの値を変換してその文字マッピングを確認するには、「[Appendix G Mapping Tables](/previous-versions/cc194886(v=msdn.10))」(付録 G: DBCS/Unicode マッピングテーブル) と「[Appendix H Code Pages](/previous-versions/cc195051(v=msdn.10))」(付録H: コード ページ) を参照してください。<br /><br />基本データ型: **int**|  
|**LCID**|照合順序の Windows ロケール ID。 これは、並べ替えおよび比較規則に使用されるカルチャです。 これらの値を変換するには、「[LCID Structure](/openspecs/windows_protocols/ms-lcid/63d3d639-7fd2-4afb-abbe-0d5b5551eef8)」(LCID 構造) を参照してください (最初に **varbinary** に変換する必要があります)。<br /><br />基本データ型: **int**|  
|**ComparisonStyle**|照合順序の Windows 比較形式。 (\_BIN) と (\_BIN2) の両方と、すべてのプロパティに区別がある場合 ((\_CS\_AS\_KS\_WS)、(\_CS\_AS\_KS\_WS\_SC)、(\_CS\_AS\_KS\_WS\_VSS))、バイナリ照合順序に対して 0 を返します。 ビットマスク値:<br /><br /> 大文字と小文字を区別しない: 1<br /><br /> アクセントを無視する: 2<br /><br /> ひらがなとカタカナを区別しない: 65536<br /><br /> 全角と半角を区別しない: 131072<br /><br /> 注: 比較動作に影響する場合でも、variation-selector-sensitive (\_VSS) オプションはこの値では表されません。<br /><br />基本データ型: **int**|  
|**Version**|照合順序のバージョン。 0 から 3 の間の値が返されます。<br /><br /> 名前に "140" が含まれる照合順序では、3 が返されます。<br /><br /> 名前に "100" が含まれる照合順序では、2 が返されます。<br /><br /> 名前に "90" が含まれる照合順序では、1 が返されます。<br /><br /> 他のすべての照合順序では 0 が返されます。<br /><br />基本データ型: **tinyint**|  
  
## <a name="return-types"></a>戻り値の型
**sql_variant**
  
## <a name="examples"></a>例  
  
```sql
SELECT COLLATIONPROPERTY('Traditional_Spanish_CS_AS_KS_WS', 'CodePage');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
1252   
```  
  
[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
```sql
SELECT COLLATIONPROPERTY('Traditional_Spanish_CS_AS_KS_WS', 'CodePage')  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
1252   
```  
  
## <a name="see-also"></a>関連項目
[sys.fn_helpcollations &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-helpcollations-transact-sql.md)
  
