---
description: グローバル設定 (ログ) (DB2ToSQL)
title: グローバル設定 (ログ記録) (DB2ToSQL) |Microsoft Docs
ms.prod: sql
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: d314a2ca-ea2e-46e0-ae5e-8774841da91b
author: nahk-ivanov
ms.author: alexiva
ms.openlocfilehash: 997f0c11bf895dd20eb6b02ed79050ae70701ea4
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054045"
---
# <a name="global-settings-logging-db2tosql"></a>グローバル設定 (ログ) (DB2ToSQL)
[ **グローバル設定** ] ダイアログボックスを使用して、ssma のログ設定を指定します。 通常、これらの設定を変更するのは、製品サポートを使用する場合のみです。  
  
このダイアログボックスにアクセスするには、[ **ツール** ] メニューの [ **グローバル設定** ] を選択し、左側のウィンドウの下部にある [ **ログ記録** ] ボタンをクリックします。  
  
## <a name="options"></a>Options  
**メッセージレベル**  
[ **メッセージレベル**] では、次のオプションを使用できます。  
  
|オプション|説明|  
|----------|---------------|  
|**[すべてのカテゴリ]**|次のすべてのオプションのログ記録レベルを設定するために使用します。|  
|**コレクター**|送信元スキーマに関するメタデータを収集し、プロジェクトに保存します。|  
|**Converter**|テーブルやストアドプロシージャなどのソースデータベースオブジェクトの構造を、対応する構造体に変換し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。|  
|**データ migrator**|転送元データベースからにデータを移行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。|  
|**フォーマッタ**|スキーマのスクリプトを生成するコンバーターのサブコンポーネント [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|**グラフィカルユーザーインターフェイス**|SSMA ツールを使用するときに表示されるメッセージ。|  
|**リンカー**|SQL 識別子を解決し、他のコンポーネントに情報を提供します。|  
|**その他**|他のカテゴリに含まれていないすべてのメッセージ。|  
|**Parser**|送信元スキーマを解析します。|  
|**シンクロナイザー**|ソースデータベースオブジェクトをに読み込み [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。|  
|**TreeConverter**|ソースメタデータ内のオブジェクトを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] メタデータに変換します。|  
|**テスト担当者**|SSMA Tester を使用するときに表示されるメッセージ。|  
  
[ **メッセージレベル**] の各オプションについて、ssma の次のいずれかのログ記録レベルを構成します。  
  
|Level|説明|  
|-|-|  
|**致命的なエラー**|致命的なエラーメッセージのみをログに書き込みます。|  
|**Error**|エラーメッセージと致命的なエラーメッセージをログに書き込みます。|  
|**警告**|警告、エラー、および致命的なエラーメッセージをログに書き込みます。|  
|**情報**|情報、警告、エラー、および致命的なエラーメッセージをログに書き込みます。|  
|**デバッグ**|デバッグメッセージを含むすべてのメッセージをログに書き込みます。|  
  
**ログ ファイルのパス**  
SSMA ログファイルのファイルパスと名前。 別の名前を指定するには、現在のパスをクリックし、参照ボタン ([.**..**]) をクリックします。  
  
**ログ ファイルのサイズ**  
ログファイルの最大サイズ (KB 単位)。 最小サイズは 10 KB です。 既定のサイズは 10240 KB です。  
  
**ログファイルの合計数**  
1つのログがいっぱいになると、SSMA によってログファイルの名前が変更され、新しいログファイルが開始されます。 この設定を使用して、保持するログファイルの最大数を指定します。 最小値は2です。  
  
