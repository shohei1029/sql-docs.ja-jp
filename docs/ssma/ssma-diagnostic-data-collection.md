---
title: SSMA の使用状況と診断データの収集
description: SQL Server Migration Assistant での使用状況と診断データの収集について説明します。
author: nahk-ivanov
ms.prod: sql
ms.custom: ''
ms.date: 04/02/2021
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.author: alexiva
ms.openlocfilehash: d306193accdf97ab04b45724cc8a275c890bec80
ms.sourcegitcommit: f1a571b6ce02a39c385ad32508ceff23475ed9f5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387384"
---
# <a name="ssma-usage-and-diagnostic-data-collection"></a>SSMA の使用状況と診断データの収集

SQL Server Migration Assistant (SSMA) には、匿名機能の使用状況と診断データを収集して Microsoft に送信できる、インターネット対応の機能が含まれています。

## <a name="collected-data"></a>収集されるデータ

SSMA は、標準的なコンピューター情報と、Microsoft に転送され、SSMA の品質、セキュリティ、および信頼性を向上させる目的で分析される可能性のある使用とパフォーマンスに関する情報を収集する場合があります。 お客様の名前、住所などの連絡先情報は収集されません。 詳細については、「[Microsoft プライバシーに関する声明](https://privacy.microsoft.com/privacystatement)」と「[SQL Server のプライバシーの補足情報](../sql-server/sql-server-privacy.md)」を参照してください。
## <a name="enable-or-disable-usage-and-diagnostic-data-collection-in-ssma"></a>SSMA で使用状況と診断データの収集を有効または無効にする

次のレジストリエントリを使用すると、データ収集を有効または無効にすることができます。

レジストリ エントリ名 = `DisableTelemetry`  
エントリの種類 `STRING` : `True` オプトアウトが選択されている `False` か、存在しません。

また、スタートアップ中の新しいツールバージョンを自動的にチェックするには、次のエントリを追加します。

レジストリ エントリ名 = `DisableAutoUpdate`  
エントリの種類 `STRING` : `True` オプトアウトが選択されている `False` か、存在しません。

移行ソースに応じて、エントリは次のいずれかのレジストリキーの下に配置する必要があります。

- アクセスの SQL Server Migration Assistant:

  サブキー = `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server Migration Assistant for Access`  
  サブキー (64 ビット OS にインストールされた32ビットアプリケーション) = `HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server Migration Assistant for Access`  

- DB2 の SQL Server Migration Assistant:

  サブキー = `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server Migration Assistant for DB2`  
  サブキー (64 ビット OS にインストールされた32ビットアプリケーション) = `HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server Migration Assistant for DB2`  

- MySQL の SQL Server Migration Assistant:

  サブキー = `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server Migration Assistant for MySQL`  
  サブキー (64 ビット OS にインストールされた32ビットアプリケーション) = `HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server Migration Assistant for MySQL`  

- Oracle の SQL Server Migration Assistant の場合:

  サブキー = `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server Migration Assistant for Oracle`  
  サブキー (64 ビット OS にインストールされた32ビットアプリケーション) = `HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server Migration Assistant for Oracle`  

- Sybase の SQL Server Migration Assistant:

  サブキー = `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server Migration Assistant for Sybase`  
  サブキー (64 ビット OS にインストールされた32ビットアプリケーション) = `HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server Migration Assistant for Sybase`  
