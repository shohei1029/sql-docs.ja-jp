---
title: Azure Synapse Pathway プレビューに関する FAQ
description: Azure Synapse Pathway についてよく寄せられる質問
author: anshul82-ms
ms.author: anrampal
ms.topic: overview
ms.date: 03/02/2021
ms.prod: sql
ms.technology: tools-other
monikerRange: =azure-sqldw-latest
ms.custom: template-overview
ms.openlocfilehash: a23ca1052e2a82afd6277a2fc308d72fa600aaf1
ms.sourcegitcommit: e4b71e5d432a29b6c76ea457b00aa0abd4b6c77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106273487"
---
# <a name="azure-synapse-pathway-preview-faq"></a>Azure Synapse Pathway プレビューに関する FAQ
[!INCLUDE [Azure Synapse Analytics](../../includes/applies-to-version/asa.md)]

このガイドでは、Azure Synapse Pathway Preview についてよく寄せられる質問を紹介します。

## <a name="general"></a>全般

### <a name="q-what-is-azure-synapse-pathway"></a>Q. Azure Synapse Pathway とは何ですか?

A. Azure Synapse Pathway はコード翻訳ツールです。これは、T-SQL ベースの Azure Synapse SQL に準拠するために既存のデータ ウェアハウスのコード変換を自動化することで、最新のデータ ウェアハウス プラットフォームにアップグレードする際に役立ちます。

### <a name="q-how-can-i-download-azure-synapse-pathway"></a>Q. Azure Synapse Pathway をダウンロードするにはどうすればよいですか?

A. Synapse Pathway は [Microsoft ダウンロード センター](https://aka.ms/synapse-pathway-download)からダウンロードできます。

### <a name="q-how-much-does-azure-synapse-pathway-cost"></a>Q. Azure Synapse Pathway のコストはどれくらいですか?

A. Synapse Pathway のダウンロードとこれを使用するコード翻訳の実行に関するコストはありません。

### <a name="q-what-sourcetarget-pairs-does-azure-synapse-pathway-currently-support"></a>Q. Azure Synapse Pathway では現在、どのようなソースとターゲットのペアがサポートされていますか?

A. このプレビュー バージョンの Synapse Pathway には、次のデータ ウェアハウスがソースとして含まれています。
- Microsoft SQL Server
- Snowflake
- Netezza

### <a name="q-what-is-included-as-part-of-the-code-conversion"></a>Q. コード変換には何が含まれていますか?

A. Synapse Pathway のプレビュー バージョンでは、テーブル、スキーマ、およびビューのコード翻訳がサポートされています。

| ソース プラットフォーム| サポートされているステートメントの種類 | 
|:-------------------:|:------------------|
| IBM Netezza  | データベースの作成/変更/ドロップ<br /> スキーマの作成/変更/ドロップ <br /> テーブルの作成/変更/ドロップ |
|Microsoft SQL Server  | データベースの作成/変更/ドロップ<br /> スキーマの作成/変更/ドロップ <br /> テーブルの作成/変更/ドロップ | 
| Snowflake |  データベースの作成/変更/ドロップ<br /> スキーマの作成/変更/ドロップ <br /> テーブルの作成/変更/ドロップ |                       

  
### <a name="q-can-it-also-scan-my-environment-and-provide-an-assessment-report-of-all-the-objects-that-need-to-be-convertedtranslated"></a>Q. 環境をスキャンし、変換または翻訳する必要があるすべてのオブジェクトの評価レポートを提供することもできますか?

A. このプレビュー バージョンの Synapse Pathway では、変換する必要がある DDL または DML スクリプトへのリンクはユーザーが提供する必要があります。 翻訳する必要があるオブジェクトを特定するために、Synapse Pathway によって現在の環境がスキャンされることはありません。

### <a name="q-what-information-does-azure-synapse-pathway-capture-about-my-current-data-warehouse-instance"></a>Q. 現在のデータ ウェアハウス インスタンスについて、Azure Synapse Pathway によってどのような情報が取り込まれますか?

A. ローカル環境で Synapse Pathway を実行できるため、Microsoft ダウンロード センターから MSI をダウンロードするときに必要な名前と組織以外に取り込まれるデータはありません。

### <a name="q-where-can-i-raise-issues-encountered-in-azure-synapse-pathway"></a>Q. Azure Synapse Pathway で発生した問題はどこで報告すればよいですか?

A. 現在、Azure Synapse Pathway は **プレビュー** 段階にあります。   Synapse Pathway のサポートは、Microsoft サポート チャネルを通じてご利用いただけます。 チケットは、Azure portal または Standard (通常はオンプレミスのサポート) ポータルで提出することができます。

> [!NOTE] 
> 他の Azure サービスと同様に、すべてのプレビュー サービスは、SLA が適用されないだけで、サポートの対象にはなります。

## <a name="next-steps"></a>次のステップ

[Azure Synapse Pathway をダウンロードする](synapse-pathway-download.md)