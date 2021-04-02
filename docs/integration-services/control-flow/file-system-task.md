---
description: ファイル システム タスク
title: ファイル システム タスク | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.dts.designer.filesystemtask.f1
- sql13.dts.designer.filesystemtask.general.f1
helpviewer_keywords:
- File System task [Integration Services]
ms.assetid: 7dd79a6a-e066-4028-a385-1d40f31056f8
author: chugugrace
ms.author: chugu
ms.openlocfilehash: d75ea560e7e3d186013664ee89e66b956e0405fa
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105980963"
---
# <a name="file-system-task"></a>ファイル システム タスク

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


  ファイル システム タスクは、ファイル システム内のファイルとディレクトリの操作を実行します。 たとえば、ファイル システム タスクを使用すると、パッケージはディレクトリやファイルの作成、移動、または削除を実行できます。 また、ファイル システム タスクを使用して、ファイルやディレクトリの属性を設定することもできます。 たとえば、ファイル システム タスクを使用すると、ファイルを非表示にしたり読み取り専用にできます。  
  
 ファイル システム タスクのすべての操作では、ファイルまたはディレクトリをソースとして使用します。 たとえば、タスクによりコピーされるファイルや、タスクにより削除されるディレクトリは、ソースと呼ばれます。 ソースを指定するには、ファイル接続マネージャーを使用してディレクトリまたはファイルをポイントするか、ソースへのパスが含まれる変数の名前を指定します。 詳細については、「[ファイル接続マネージャー](../../integration-services/connection-manager/file-connection-manager.md)」および「[Integration Services (SSIS) の変数](../../integration-services/integration-services-ssis-variables.md)」を参照してください。  
  
 ファイルやディレクトリをコピーまたは移動する操作、およびファイルの名前を変更する操作では、その操作の出力先となるファイルまたはディレクトリと、基になるファイルまたはディレクトリを使用します。 操作の出力先となるファイルまたはディレクトリは、ファイル接続マネージャーまたは変数を使用して指定します。 ファイル システム タスクの操作は、出力先のファイルやディレクトリの上書きを許可するように構成できます。 新しいディレクトリを作成する操作は、そのディレクトリが既に存在していても失敗するのではなく、指定した名前の既存のディレクトリが使用されるように構成できます。  
  
## <a name="predefined-file-system-operations"></a>定義済みのファイル システム操作  
 ファイル システム タスクには、定義済みの操作のセットが含まれています。 次の表では、これらの操作について説明します。  
  
|Operation|説明|  
|---------------|-----------------|  
|ディレクトリのコピー|ある場所から別の場所にフォルダーをコピーします。|  
|ファイルのコピー|ある場所から別の場所にファイルをコピーします。|  
|ディレクトリの作成|指定した場所にフォルダーを作成します。|  
|ディレクトリの削除|指定した場所のフォルダーを削除します。|  
|ディレクトリ コンテンツの削除|フォルダー内のすべてのファイルおよびフォルダーを削除します。|  
|ファイルの削除|指定した場所のファイルを削除します。|  
|ディレクトリの移動|ある場所から別の場所にフォルダーを移動します。|  
|ファイルの移動|ある場所から別の場所にファイルを移動します。|  
|ファイル名の変更|指定した場所にあるファイル名を変更します。|  
|属性の設定|ファイルやディレクトリの属性を設定します。 属性には、アーカイブ、非表示、標準、読み取り専用、およびシステムが含まれます。 標準には属性がなく、他の属性と組み合わせることはできません。 他のすべての属性は、組み合わせて使用できます。|  
  
 ファイル システム タスクは、1 つのファイルまたはディレクトリのみを処理できます。 したがって、このタスクでは、ワイルドカード文字を使用して複数のファイルに同じ処理を行うことはできません。 ファイル システム タスクが複数のファイルやディレクトリに対して同じ処理を繰り返し行うようにするには、次の手順に示すように、ファイル システム タスクを Foreach ループ コンテナー内に配置します。  
  
-   **Foreach ループ コンテナーの構成** [Foreach ループ エディター] の **[コレクション]** ページで **[Foreach File 列挙子]** に列挙子を設定して、 **[ファイル]** の列挙子の構成としてワイルドカード式を入力します。 [Foreach ループ エディター] の **[変数のマッピング]** ページで、ファイル名を一度に 1 つずつファイル システム タスクに渡すために使用する変数をマップします。  
  
-   **ファイル システム タスクの追加と構成** ファイル システム タスクを Foreach ループ コンテナーに追加します。 [ファイル システム タスク エディター] の **[全般]** ページで、 **SourceVariable** プロパティまたは **DestinationVariable** プロパティに、Foreach ループ コンテナーで定義した変数を設定します。  
  
## <a name="custom-log-entries-available-on-the-file-system-task"></a>ファイル システム タスクで使用できるカスタム ログ エントリ  
 次の表では、ファイル システム タスクのカスタム ログ エントリを説明します。 詳細については、「[Integration Services (SSIS) のログ記録](../../integration-services/performance/integration-services-ssis-logging.md)」をご覧ください。  
  
|ログ エントリ|説明|  
|---------------|-----------------|  
|**FileSystemOperation**|タスクで実行される操作を報告します。 ログ エントリは、ファイル システム操作の開始時に書き込まれます。これには、操作の基になるファイルと操作対象のファイルに関する情報が含まれます。|  
  
## <a name="configuring-the-file-system-task"></a>ファイル システム タスクの構成  
 プロパティを設定するには [!INCLUDE[ssIS](../../includes/ssis-md.md)] デザイナーから行うか、またはプログラムによって設定します。  
  
 [!INCLUDE[ssIS](../../includes/ssis-md.md)] デザイナーで設定できるプロパティの詳細については、次のトピックを参照してください。  
  
-   [[ファイル システム タスク エディター]\([全般] ページ)]()  
  
-   [[式] ページ](../../integration-services/expressions/expressions-page.md)  
  
 [!INCLUDE[ssIS](../../includes/ssis-md.md)] デザイナーでこれらのプロパティを設定する方法については、次のトピックを参照してください。  
  
-   [タスクまたはコンテナーのプロパティを設定する](./add-or-delete-a-task-or-a-container-in-a-control-flow.md)  
  
 プログラムでこれらのプロパティを設定する方法については、次のトピックを参照してください。  
  
-   <xref:Microsoft.SqlServer.Dts.Tasks.FileSystemTask.FileSystemTask>  
  
## <a name="related-tasks"></a>Related Tasks  
 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] には、データ ファイルをダウンロードまたはアップロードし、サーバー上のディレクトリを管理するタスクが含まれます。 詳細については、「 [FTP タスク](../../integration-services/control-flow/ftp-task.md)」を参照してください。  
  
## <a name="file-system-task-editor-general-page"></a>[ファイル システム タスク エディター] \([全般] ページ)
  **[ファイル システム タスク エディター]** ダイアログ ボックスの **[全般]** ページを使用すると、タスクで実行するファイル システム操作を構成できます。  
  
 SourceConnection プロパティと DestinationConnection プロパティを設定して、ソースとターゲットの接続マネージャーを指定する必要があります。 タスクでソースまたはターゲットとして使用されるファイルを指すファイル接続マネージャーの名前を指定することも、ファイルのパスが変数に格納されていれば変数の名前を指定することもできます。 変数を使用してファイル パスを保存するには、最初に、IsSourcePathVariable オプション (ソース接続) および IsDestinationPatheVariable オプション (ターゲット接続) を **True** に設定しておく必要があります。 その後で、既存のシステム変数またはユーザー定義変数を選択するか、新しい変数を作成できます。 変数のスコープは、 **[変数の追加]** ダイアログ ボックスで構成および指定できます。 スコープは、ファイル システム タスクまたは親コンテナーにする必要があります。 詳細については、「[Integration Services (SSIS) Variables](../../integration-services/integration-services-ssis-variables.md)」(Integration Services (SSIS) の変数) と「[Use Variables in Packages](../integration-services-ssis-variables.md)」(パッケージで変数を使用する) を参照してください。  
  
> [!NOTE]  
>  **SourceConnection** プロパティと **DestinationConnection** プロパティに選択した変数をオーバーライドするには、**Source** プロパティと **Destination** プロパティに式を入力します。 式は、 **ファイル システム タスク エディター** の **[式]** ページで入力します。 たとえば、タスクで変換先として使用するファイルのパスを設定するには、特定の状況下では変数 A、別の状況下では変数 B を使用することがあります。  
  
> [!NOTE]  
>  ファイル システム タスクは、1 つのファイルまたはディレクトリのみを処理できます。 したがって、このタスクでは、ワイルドカード文字を使用して複数のファイルまたはディレクトリに同じ処理を行うことはできません。 ファイル システム タスクが複数のファイルやディレクトリに対して同じ処理を繰り返し行うようにするには、ファイル システム タスクを Foreach ループ コンテナー内に配置します。 詳細については、「 [File System Task](../../integration-services/control-flow/file-system-task.md)」(ファイル システム タスク) を参照してください。  
  
 さまざまな変数を使用する式を使用できます。  
  
### <a name="options"></a>Options  
 **[IsDestinationPathVariable]**  
 対象になるパスを変数に格納するかどうかを示します。 このプロパティのオプションを次の表に示します。  
  
|値|説明|  
|-----------|-----------------|  
|**True**|対象になるパスは変数に格納されます。 この値を選択すると、動的オプション **[DestinationVariable]** が表示されます。|  
|**False**|対象になるパスは、ファイル接続マネージャーで指定されます。 この値を選択すると、動的オプション **[DestinationConnection]** が表示されます。|  
  
 **[OverwriteDestination]**  
 操作によって対象になるディレクトリ内のファイルを上書きできるかどうかを指定します。  
  
 **名前**  
 ファイル システム タスクの一意な名前を指定します。 この名前は、タスク アイコンのラベルとして使用されます。  
  
> [!NOTE]  
>  タスク名はパッケージ内で一意である必要があります。  
  
 **説明**  
 ファイル システム タスクの説明を入力します。  
  
 **操作**  
 実行するファイル システム操作を選択します。 このプロパティのオプションを次の表に示します。  
  
|値|説明|  
|-----------|-----------------|  
|**ディレクトリのコピー**|ディレクトリをコピーします。 この値を選択すると、ソースとターゲットを指定するための動的オプションが表示されます。|  
|**ファイルのコピー**|ファイルをコピーします。 この値を選択すると、ソースとターゲットを指定するための動的オプションが表示されます。|  
|**ディレクトリの作成**|ディレクトリを作成します。 この値を選択すると、ソース ディレクトリとターゲット ディレクトリを指定するための動的オプションが表示されます。|  
|**ディレクトリの削除**|ディレクトリを削除します。 この値を選択すると、ソースを指定するための動的オプションが表示されます。|  
|**ディレクトリ コンテンツの削除**|ディレクトリのコンテンツを削除します。 この値を選択すると、ソースを指定するための動的オプションが表示されます。|  
|**ファイルの削除**|ファイルを削除します。 この値を選択すると、ソースを指定するための動的オプションが表示されます。|  
|**ディレクトリの移動**|ディレクトリを移動します。 この値を選択すると、ソースとターゲットを指定するための動的オプションが表示されます。|  
|**ファイルの移動**|ファイルを移動します。 この値を選択すると、ソースとターゲットを指定するための動的オプションが表示されます。 ファイルを移動するときは、移動先として指定するディレクトリ パスにファイル名を含めないようにします。|  
|**ファイル名の変更**|ファイルの名前を変更します。 この値を選択すると、ソースとターゲットを指定するための動的オプションが表示されます。 ファイル名を変更するときは、変更先として指定するディレクトリ パスに新しいファイル名を含めます。|  
|**属性の設定**|ファイルまたはディレクトリの属性を設定します。 この値を選択すると、ソースと操作を指定するための動的オプションが表示されます。|  
  
 **[IsSourcePathVariable]**  
 対象になるパスを変数に格納するかどうかを示します。 このプロパティのオプションを次の表に示します。  
  
|値|説明|  
|-----------|-|  
|**True**|対象になるパスは変数に格納されます。 この値を選択すると、動的オプション **[SourceVariable]** が表示されます。|  
|**False**|対象になるパスは、ファイル接続マネージャーで指定されます。 この値を選択すると、動的オプション **[DestinationVariable]** が表示されます。|  
  
### <a name="isdestinationpathvariable-dynamic-options"></a>[IsDestinationPathVariable] の動的オプション  
  
#### <a name="isdestinationpathvariable--true"></a>[IsDestinationPathVariable] = [True]  
 **[DestinationVariable]**  
 一覧から変数名を選択するか、[\<**New variable...**>] をクリックして新しい変数を作成します。  
  
 **関連トピック:** [Integration Services &#40;SSIS&#41; の変数](../../integration-services/integration-services-ssis-variables.md)、[変数の追加](../integration-services-ssis-variables.md)  
  
#### <a name="isdestinationpathvariable--false"></a>[IsDestinationPathVariable] = [False]  
 **DestinationConnection**  
 一覧でファイル接続マネージャーを選択するか、[\<**New connection...**>] をクリックして新しい接続マネージャーを作成します。  
  
 **関連トピック:** [ファイル接続マネージャー](../../integration-services/connection-manager/file-connection-manager.md)、[ファイル接続マネージャー エディター](../connection-manager/file-connection-manager.md)  
  
### <a name="issourcepathvariable-dynamic-options"></a>[IsSourcePathVariable] の動的オプション  
  
#### <a name="issourcepathvariable--true"></a>[IsSourcePathVariable] = [True]  
 **[SourceVariable]**  
 一覧から変数名を選択するか、[\<**New variable...**>] をクリックして新しい変数を作成します。  
  
 **関連トピック:** [Integration Services &#40;SSIS&#41; の変数](../../integration-services/integration-services-ssis-variables.md)、[変数の追加](../integration-services-ssis-variables.md)  
  
#### <a name="issourcepathvariable--false"></a>[IsSourcePathVariable] = [False]  
 **SourceConnection**  
 一覧でファイル接続マネージャーを選択するか、[\<**New connection...**>] をクリックして新しい接続マネージャーを作成します。  
  
 **関連トピック:** [ファイル接続マネージャー](../../integration-services/connection-manager/file-connection-manager.md)  
  
### <a name="operation-dynamic-options"></a>[Operation] の動的オプション  
  
#### <a name="operation--set-attributes"></a>[Operation] = [属性の設定]  
 **[非表示]**  
 ファイルまたはディレクトリを表示するかどうかを示します。  
  
 **ReadOnly**  
 ファイルが読み取り専用かどうかを示します。  
  
 **Archive**  
 ファイルまたはディレクトリをアーカイブするかどうかを示します。  
  
 **システム**  
 ファイルがオペレーティング システム ファイルかどうかを示します。  
  
#### <a name="operation--create-directory"></a>[Operation] = [ディレクトリの作成]  
 **UseDirectoryIfExists**  
 **[ディレクトリの作成]** 操作で、新しいディレクトリを作成せずに、指定された名前の既存のディレクトリを使用するかどうかを指定します。  
  
## <a name="see-also"></a>参照  
 [Integration Services タスク](../../integration-services/control-flow/integration-services-tasks.md)   
 [制御フロー](../../integration-services/control-flow/control-flow.md)  
  
