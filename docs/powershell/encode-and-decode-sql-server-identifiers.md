---
title: SQL Server 識別子のエンコードとデコード
description: SQL Server の区切られた識別子に使用できる文字には、Windows PowerShell パスでサポートされないものがあります。 16 進数値で表すことで、それらを含める方法について学習します。
ms.prod: sql
ms.technology: sql-server-powershell
ms.topic: conceptual
author: markingmyname
ms.author: maghan
ms.reviewer: matteot, drskwier
ms.custom: ''
ms.date: 10/14/2020
ms.openlocfilehash: 46eb3f29ce21aada716458d554dd329b1bd10dff
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981188"
---
# <a name="encode-and-decode-sql-server-identifiers"></a>SQL Server 識別子のエンコードとデコード

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

SQL Server の区切られた識別子には、Windows PowerShell パスでサポートされない文字が含まれる場合があります。 これらの文字は、16 進数値にエンコードすることによって指定できます。

[!INCLUDE [sql-server-powershell-version](../includes/sql-server-powershell-version.md)]

Windows PowerShell パス名でサポートされない文字は、" **%** xx" のように、"%" 文字の後に文字を表すビット パターンの 16 進値を付加して表す、つまりエンコードすることができます。 エンコードは、Windows PowerShell パスでサポートされない文字を処理する場合にいつでも使用できます。

**Encode-SqlName** コマンドレットは、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 識別子を入力として受け取ります。 また、Windows PowerShell 言語ではサポートされないすべての文字が "%xx" でエンコードされた文字列を出力します。 **Decode-SqlName** コマンドレットは、エンコードされた [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 識別子を入力として受け取り、元の識別子を返します。  

## <a name="limitations-and-restrictions"></a>制限事項と制約事項

**Encode-Sqlname** コマンドレットと **Decode-Sqlname** コマンドレットでエンコードまたはデコードできるのは、SQL Server の区切られた識別子ではサポートされるものの PowerShell パスではサポートされない文字のみです。 **Encode-SqlName** によってエンコードされ、**Decode-SqlName** によってデコードされる文字を次に示します。

|**文字**|\ |/|:|%|\<|>|*|?|[|]|&#124;|  
|-|-|-|-|-|-|-|-|-|-|-|-|
|**16 進エンコード**|%5C|%2F|%3A|%25|%3C|%3E|%2A|%3F|%5B|%5D|%7C|

## <a name="encoding-an-identifier"></a>識別子のエンコード  

### <a name="to-encode-a-sql-server-identifier-in-a-powershell-path"></a>PowerShell パス内で SQL Server 識別子をエンコードするには

- SQL Server 識別子をエンコードするには、次の 2 つの方法のいずれかを使用します。
    - サポートされていない文字の 16 進数コードを、%XX という構文 (XX は 16 進数コード) を使用して指定します。
    - 識別子を引用符で囲まれた文字列として **Encode-Sqlname** コマンドレットに渡します。

### <a name="examples-encoding"></a>例 (エンコード)

この例では、":" 文字のエンコードされたバージョン (%3A) を指定します。

```powershell
Set-Location Table%3ATest
```

あるいは、 **Encode-SqlName** を使用して Windows PowerShell でサポートされる名前を作成できます。

```powershell
Set-Location (Encode-SqlName "Table:Test")
```

## <a name="decoding-an-identifier"></a>識別子のデコード

### <a name="to-decode-a-sql-server-identifier-from-a-powershell-path"></a>PowerShell パスの SQL Server 識別子をデコードするには

16 進数エンコードを、そのエンコードが表す文字に置換するには、 **Decode-Sqlname** コマンドレットを使用します。

### <a name="examples-decoding"></a>例 (デコード)

次の例では、"Table:Test" を返します。

```powershell
Decode-SqlName "Table%3ATest"
```

## <a name="see-also"></a>参照

- [PowerShell での SQL Server 識別子](sql-server-identifiers-in-powershell.md)
- [SQL Server PowerShell プロバイダー](sql-server-powershell-provider.md)
- [SQL Server PowerShell](sql-server-powershell.md)  
