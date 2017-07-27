---
title: "開發 Azure 函數並在本機執行 | Microsoft Docs"
description: "在於 Azure Functions 上執行 Azure 函式之前，先了解如何撰寫 Azure 函式並在本機電腦上進行測試。"
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 478c2ffbb0bfe5b1595bc6ea1bebb2144aebc728
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017

---
# <a name="code-and-test-azure-functions-locally"></a>撰寫 Azure 函式並在本機進行測試

若要在本機執行 Azure Functions 執行階段，您可以使用您最愛的程式碼編輯器和本機開發工具。 在 Azure 中的事件上觸發並偵錯 C# 和 JavaScript 函式。

若要開始，請從 npm 安裝 [Azure Functions Core Tools]。 Azure Functions Core Tools 是 Azure Functions 執行階段的本機版本，可讓您在本機 Windows 電腦上執行。 它不是模擬器。 它與在 Azure 中執行的執行階段是相同的執行階段。

[Azure Functions Core Tools]新增了下列命令別名：
- `func`
- `azfun`
- `azurefunctions`

Azure Functions Core Tools 是[開放原始碼且裝載於 GitHub 上](https://github.com/azure/azure-functions-cli)。 若要提出錯誤或功能要求，[請開啟 GitHub 問題](https://github.com/azure/azure-functions-cli/issues)。

## <a name="create-a-local-functions-project"></a>建立本機的 Functions 專案

在本機執行時，Functions 專案是具有 host.json 和 local.settings.json 檔案的目錄。 此目錄相當於 Azure 中的函式應用程式。 若要深入了解 Azure Functions 的資料夾結構，請參閱 [Azure Functions 的開發人員指南](functions-reference.md#folder-structure)。

在命令提示字元中，執行下列命令：

```
func init MyFunctionProj
```

輸出看起來會像下列範例：

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

若要選擇不建立 Git 存放庫，請使用 `--no-source-control [-n]` 選項。

<a name="local-settings"></a>

## <a name="local-settings-file"></a>本機設定檔

local.settings.json 檔案會儲存應用程式設定、連接字串和 Azure Functions Core Tools 的設定。 其結構如下：

```json
{
  "IsEncrypted": false,   // If set to true, all values are encrypted by using a local machine key. Use with "func settings" commands.
  "Values": {
    "AzureWebJobsStorage": "<connection string>",   // This is required for all triggers except HTTP.
    "AzureWebJobsDashboard": "<connection string>", // Optional, controls whether to log to the Monitor tab in the portal.
  },
  "Host": {
    "LocalHttpPort": 7071, // If specified, this is the default port for "host start" and "run". Can be overridden by using the --port command-line option.
    "CORS": "*"            // Origins to allow in the CORS setting.
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

在 **Values** 集合中指定應用程式設定。 這些設定就可以讀取作為環境變數。 在 C# 中，使用 `System.Environment.GetEnvironmentVariable` 或 `ConfigurationManager.AppSettings`。 在 JavaScript 中，使用 `process.env`。 如果指定相同的設定作為系統環境變數，則其優先順序高於 local.settings.json 中的值。

應用程式設定 **AzureWebJobsStorage** 是特殊的設定，Azure Functions 執行階段除了 HTTP 以外的所有觸發程序皆需要此設定。 就內部而言，執行階段會建立佇列來管理此儲存體帳戶中的觸發程序。 如果未指定 **AzureWebJobsStorage** 的值，且您是使用 HTTP 以外的觸發程序，就會看到下列訊息：

*在 local.settings.json 中遺失 AzureWebJobsStorage 的值。這對 HTTP 以外的所有觸發程序是必要的。您可以執行 'func azure functionapp fetch-app-settings <functionAppName>' 或指定 local.settings.json 中的連接字串。*

> [!NOTE]
> 可以透過 "AzureWebJobsStorage": "UseDevelopmentStorage=true" 的連接字串使用本機儲存體模擬器。 不過，相較於 Azure 儲存體服務，可能會有行為上的差異。

下列設定可自訂本機 Functions 主機：
- `LocalHttpPort`。 用於 `func host start` `func run`的預設連接埠。 `--port` 命令列選項的優先順序高於此值。
- `CORS`。 CORS 允許的來源，為不含空格的逗號分隔清單。 使用 "*" 可全部允許。

您可以提供 `ConnectionStrings` 物件中的連接字串。 它們會新增至包含提供者名稱 **System.Data.SqlClient** 的環境。

大部分的觸發程序和繫結都有 **Connection** 屬性，是環境變數的名稱或 local.settings.json 中的應用程式設定。 如果遺失應用程式設定值，您會看到下列訊息：

*警告：找不到 local.settings.json 中名為 'MyStorageConnection' 的值，該值在 'BlobTriggerCSharp\function.json' 中符合 'blobTrigger' 上之 'connection' 屬性設定。您可以執行 'func azure functionapp fetch-app-settings <functionAppName>' 或指定 local.settings.json 中的連接字串。*

local.settings.json 檔案僅供 Azure Functions Core Tools 使用。 若要在 Azure 中設定應用程式設定和連接字串，請使用 [應用程式設定] 刀鋒視窗。

### <a name="configure-app-settings"></a>進行應用程式設定

若要設定連接字串的值，您可以執行下列其中一項：
- 從 [Azure 儲存體總管](http://storageexplorer.com/)手動輸入連接字串。
- 使用 **func azure functionapp fetch-app-settings \<FunctionAppName\>**。 需要 **azure 登入**。
- 使用 **func azure functionapp storage fetch-connection-string \<StorageAccountName\>**。 需要 **azure 登入**。

## <a name="create-a-function"></a>建立函式

若要建立函式，請執行 `func new`。 此命令有下列選擇性的引數︰

- `--language [-l]`。 範本程式語言，例如 C#、F# 或 JavaScript。
- `--template [-t]`。 範本名稱。
- `--name [-n]`。 函式名稱。

例如，若要建立 JavaScript HTTP 觸發程序，請執行：

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

若要建立佇列所觸發的函式，請執行：

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```

## <a name="run-functions-locally"></a>在本機執行函式

若要執行 Functions 專案，請執行 Functions 主機。 主機可允許專案中所有函式的觸發程序：

```
func host start
```

您可以使用下列選項搭配 `func host start`：

- `--port [-p]`。 要接聽的本機連接埠。 預設值：7071。
- `--debug <type>`。 選項為 VSCode 和 VS。
- `--cors`。 以逗號分隔的 CORS 來源清單，不含空格。
- `--nodeDebugPort [-n]`。 要使用的節點偵錯工具連接埠。 預設值：Launch.json 中的值或 5858。
- `--debugLevel [-d]`。 主控台追蹤層級 (off、verbose、info、warning 或 error)。 預設：info。
- `--timeout [-t]`。 Functions 主機要啟動的逾時 (以秒為單位)。 預設值：20 秒。
- `--useHttps`。 繫結至 https://localhost:{port} 而不是 http://localhost:{port}。 根據預設，此選項會在您的電腦上建立受信任的憑證。
- `--pause-on-error`。 暫停以在結束處理程序之前取得其他輸入。 當您從整合式開發環境 (IDE) 啟動 Azure Functions Core Tools 時很有用。

Functions 主機啟動時，它會輸出 HTTP 觸發函式的 URL：

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug"></a>偵錯

若要連結偵錯工具，請傳遞 `--debug` 引數。 若要偵錯 JavaScript 函式，請使用 Visual Studio Code。 對於 C# 函式，請使用 Visual Studio。

若要偵錯 C# 函式，請使用 `--debug vs`。 或者，使用 [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/)。 

若要啟動主機並設定 JavaScript 偵錯，請執行：

```
func host start --debug vscode
```

然後，在 Visual Studio Code 的 [偵錯] 檢視中，選取 [連結至 Azure Functions]。 您可以附加中斷點、檢查變數及逐步執行程式碼。

![使用 Visual Studio Code 進行 JavaScript 偵錯](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="call-a-function-by-using-test-data"></a>使用測試資料來呼叫函式

您可以也使用 `func run <FunctionName>` 直接叫用函式。 這個命令是類似於 Azure 入口網站中的 [測試] 索引標籤，您可以在其中提供函式的輸入資料。 這個命令會啟動整個 Functions 主機。

您可以使用下列選項搭配 `func run`：

- `--content [-c]`。 內嵌內容。
- `--debug [-d]`。 在執行函式之前，請先將偵錯工具附加到主機處理序。
- `--timeout [-t]`。 本機 Functions 主機就緒前的等候時間 (以秒為單位)。
- `--file [-f]`。 要用來作為內容的檔案名稱。
- `--no-interactive`。 不會提示輸入。 適用於自動化情節。

例如，若要呼叫 HTTP 觸發的函式並傳遞內容的內文，請執行下列命令：

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-a-function-app"></a>發佈函式應用程式

若要將 Functions 專案發佈至 Azure 中的函式應用程式，請使用 `publish` 命令：

```
func azure functionapp publish <FunctionAppName>
```

您可以使用下列選項：

- `--publish-local-settings [-i]`。  將 local.settings.json 中的設定發佈至 Azure，若設定已經存在，則提示進行覆寫。
- `--overwrite-settings [-y]`。 必須與 `-i` 搭配使用。 使用本機值在 Azure 中覆寫 AppSettings (如果不同)。 預設值為提示。

`publish` 命令會將 Functions 專案目錄的內容上傳。 如果您在本機將檔案刪除，此命令不會從 Azure 刪除它們。 若要將這些檔案刪除，請在 Azure Functions 入口網站中使用 Kudu。 若要啟動 Kudu，請在 Azure Functions 入口網站中選取 [平台功能] > [進階工具 (Kudu)]。 


<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools

