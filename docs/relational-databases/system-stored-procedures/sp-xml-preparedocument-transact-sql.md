---
description: sp_xml_preparedocument (Transact-sql)
title: sp_xml_preparedocument (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_xml_preparedocument_TSQL
- sp_xml_preparedocument
dev_langs:
- TSQL
helpviewer_keywords:
- sp_xml_preparedocument
ms.assetid: 95f41cff-c52a-4182-8ac6-bf49369d214c
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 29377edf5fa911c47b4e3a34fa1be58046c3e915
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890782"
---
# <a name="sp_xml_preparedocument-transact-sql"></a>sp_xml_preparedocument (Transact-sql)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  入力として指定された XML テキストを読み取り、MSXML パーサー (Msxmlsql.dll) を使用してテキストを解析し、解析されたドキュメントを使用できる状態で提供します。 この解析済みドキュメントは、XML ドキュメント内のさまざまなノード (要素、属性、テキスト、コメントなど) のツリー表現です。  
  
 **sp_xml_preparedocument** は、新しく作成された xml ドキュメントの内部表現にアクセスするために使用できるハンドルを返します。 このハンドルは、セッションの間、または **sp_xml_removedocument** を実行してハンドルが無効になるまで有効です。  
  
> [!NOTE]  
>  解析されたドキュメントは、の内部キャッシュに格納され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 MSXML パーサーでは、で使用可能な合計メモリの8分の1を使用でき [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 メモリが不足しないようにするには、 **sp_xml_removedocument** を実行して、ドキュメントが不要になったらすぐにメモリを解放します。 多くの場合、nodes () メソッドはより優れた方法であり、メモリ使用量が過剰にならないようにすることができます。
  
> [!NOTE]  
>  旧バージョンとの互換性のために、 **sp_xml_preparedocument** では、これらの文字がエンティティされている場合でも、属性の CR (char (13)) と LF (char (10)) の文字が折りたたまれます。  
  
> [!NOTE]  
>  **Sp_xml_preparedocument** によって呼び出された XML パーサーは、内部 dtd およびエンティティ宣言を解析できます。 悪意を持って構築された Dtd およびエンティティ宣言を使用してサービス拒否攻撃を行うことができるため、信頼されていないソースからの XML ドキュメントを **sp_xml_preparedocument** に直接渡すことは避けてください。  
>   
>  再帰的なエンティティ拡張攻撃を軽減するために、 **sp_xml_preparedocument** 制限は、ドキュメントの最上位にある1つのエンティティの下に展開できるエンティティの数を1万に制限します。 制限は、文字または数値エンティティには適用されません。 この制限により、多数のエンティティ参照を含むドキュメントを格納できますが、1つのエンティティが1万を超えるチェーンで再帰的に展開されるのを防ぐことができます。  
  
> [!NOTE]  
>  **sp_xml_preparedocument** は、一度に開くことができる要素の数を256に制限します。  

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_xml_preparedocument  
hdoc   
OUTPUT  
[ , xmltext ]  
[ , xpath_namespaces ]   
```  
  
## <a name="arguments"></a>引数  
 *hdoc*  
 新しく作成されたドキュメントへのハンドルを指定します。 *hdoc* は整数です。  
  
 [ *xmltext* ]  
 元の XML ドキュメントを指定します。 MSXML パーサーは、この XML ドキュメントを解析します。 *xmltext* は、 **char**、 **nchar**、 **varchar**、 **nvarchar**、 **text**、 **ntext** 、または **xml** のテキストパラメーターです。 既定値は NULL です。この場合、空の XML ドキュメントの内部表現が作成されます。  
  
> [!NOTE]  
>  **sp_xml_preparedocument** は、テキストまたは型指定されていない xml のみを処理できます。 入力値として使用するインスタンス値が既に型指定された XML である場合は、まずその値を、型指定されない新しい XML インスタンスまたは文字列にキャストし、その後入力値として渡します。 詳細については、「 [型指定された XML と型指定されていない XML の比較](../../relational-databases/xml/compare-typed-xml-to-untyped-xml.md)」を参照してください。  
  
 [ *xpath_namespaces* ]  
 OPENXML の行と列の XPath 式で使用される名前空間宣言を指定します。 *xpath_namespaces* は、 **char**、 **nchar**、 **varchar**、 **nvarchar**、 **text**、 **ntext** 、または **xml** のテキストパラメーターです。  
  
 既定値は **\<root xmlns:mp="urn:schemas-microsoft-com:xml-metaprop">** です。 *xpath_namespaces* は、適切な形式の XML ドキュメントを使用して、OPENXML の xpath 式で使用されるプレフィックスの名前空間 uri を提供します。 名前空間 **urn: schema** を参照するために使用する必要があるプレフィックスを宣言する *xpath_namespaces* 。これにより、解析された XML 要素に関するメタデータが提供されます。 この手法を使用してメタプロパティ名前空間の名前空間プレフィックスを再定義することはできますが、この名前空間は失われません。 このような宣言が含まれていない *xpath_namespaces* 場合でも、 **urn: schema-microsoft-com: xml メタ prop** ではプレフィックス **mp** は引き続き有効です。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または >0 (失敗)  
  
## <a name="permissions"></a>アクセス許可  
 ロール **public** のメンバーシップが必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-preparing-an-internal-representation-for-a-well-formed-xml-document"></a>A. 整形式の XML ドキュメントの内部表現を準備する  
 次の例では、入力として指定された XML ドキュメントの、新しく作成された内部表現へのハンドルを返します。 `sp_xml_preparedocument` の呼び出しでは、既定の名前空間プレフィックス マッピングを使用します。  
  
```  
DECLARE @hdoc int;  
DECLARE @doc varchar(1000);  
SET @doc ='  
<ROOT>  
<Customer CustomerID="VINET" ContactName="Paul Henriot">  
   <Order CustomerID="VINET" EmployeeID="5" OrderDate="1996-07-04T00:00:00">  
      <OrderDetail OrderID="10248" ProductID="11" Quantity="12"/>  
      <OrderDetail OrderID="10248" ProductID="42" Quantity="10"/>  
   </Order>  
</Customer>  
<Customer CustomerID="LILAS" ContactName="Carlos Gonzlez">  
   <Order CustomerID="LILAS" EmployeeID="3" OrderDate="1996-08-16T00:00:00">  
      <OrderDetail OrderID="10283" ProductID="72" Quantity="3"/>  
   </Order>  
</Customer>  
</ROOT>';  
--Create an internal representation of the XML document.  
EXEC sp_xml_preparedocument @hdoc OUTPUT, @doc;  
-- Remove the internal representation.  
exec sp_xml_removedocument @hdoc;  
```  
  
### <a name="b-preparing-an-internal-representation-for-a-well-formed-xml-document-with-a-dtd"></a>B. ウェルフォームド XML ドキュメントの内部表現を DTD を使用して準備する  
 次の例では、入力として指定された XML ドキュメントの、新しく作成された内部表現へのハンドルを返します。 このストアドプロシージャは、ドキュメントに含まれている DTD に対して読み込まれたドキュメントを検証します。 `sp_xml_preparedocument` の呼び出しでは、既定の名前空間プレフィックス マッピングを使用します。  
  
```  
DECLARE @hdoc int;  
DECLARE @doc varchar(2000);  
SET @doc = '  
<?xml version="1.0" encoding="UTF-8" ?>   
<!DOCTYPE root   
[<!ELEMENT root (Customers)*>  
<!ELEMENT Customers EMPTY>  
<!ATTLIST Customers CustomerID CDATA #IMPLIED ContactName CDATA #IMPLIED>]>  
<root>  
<Customers CustomerID="ALFKI" ContactName="Maria Anders"/>  
</root>';  
  
EXEC sp_xml_preparedocument @hdoc OUTPUT, @doc;  
```  
  
### <a name="c-specifying-a-namespace-uri"></a>C. 名前空間 URI を指定する  
 次の例では、入力として指定された XML ドキュメントの、新しく作成された内部表現へのハンドルを返します。 を呼び出す `sp_xml_preparedocument` と、 `mp` メタプロパティ名前空間マッピングのプレフィックスが保持され、その `xyz` 名前空間にマッピングプレフィックスが追加され `urn:MyNamespace` ます。  
  
```  
DECLARE @hdoc int;  
DECLARE @doc varchar(1000);  
SET @doc ='  
<ROOT>  
<Customer CustomerID="VINET" ContactName="Paul Henriot">  
   <Order CustomerID="VINET" EmployeeID="5"   
           OrderDate="1996-07-04T00:00:00">  
      <OrderDetail OrderID="10248" ProductID="11" Quantity="12"/>  
      <OrderDetail OrderID="10248" ProductID="42" Quantity="10"/>  
   </Order>  
</Customer>  
<Customer CustomerID="LILAS" ContactName="Carlos Gonzlez">  
   <Order CustomerID="LILAS" EmployeeID="3"   
           OrderDate="1996-08-16T00:00:00">  
      <OrderDetail OrderID="10283" ProductID="72" Quantity="3"/>  
   </Order>  
</Customer>  
</ROOT>'  
--Create an internal representation of the XML document.  
EXEC sp_xml_preparedocument @hdoc OUTPUT, @doc, '<ROOT xmlns:xyz="urn:MyNamespace"/>';  
```  
  
## <a name="see-also"></a>参照  
 <br>[XML ストアドプロシージャ (Transact-sql)](../../relational-databases/system-stored-procedures/xml-stored-procedures-transact-sql.md)
 <br>[システムストアドプロシージャ (Transact-sql)](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)
 <br>[OPENXML (Transact-sql)](../../t-sql/functions/openxml-transact-sql.md)
 <br>[sys.dm_exec_xml_handles (Transact-sql)](../system-dynamic-management-views/sys-dm-exec-xml-handles-transact-sql.md)
 <br>[sp_xml_removedocument (Transact-sql)](../../relational-databases/system-stored-procedures/sp-xml-removedocument-transact-sql.md)
 <br>[nodes&#40;&#41; Method &#40;xml データ型&#41;](../../t-sql/xml/nodes-method-xml-data-type.md)
  
  
