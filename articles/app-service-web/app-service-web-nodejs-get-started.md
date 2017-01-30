---
title: "在 Azure App Service 中開始使用 Node.js Web 應用程式 | Microsoft Docs"
description: "了解如何將 Node.js 應用程式部署到 Azure App Service 中的 Web 應用程式。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 88405a9e67eb748acc9564022283004b5ebfcf48
ms.openlocfilehash: 63210a5539d1e5e5b7d1f5a60048d507e53038a5


---
# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>在 Azure App Service 中開始使用 Node.js Web 應用程式
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

本教學課程示範如何建立簡單的 [Node.js] 應用程式，並透過 cmd.exe 或 bash 之類的命令列環境將其部署至 [Azure App Service]。 本教學課程中的指示可運用在任何能夠執行 Node.js 應用程式的作業系統上。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

<a name="prereq"></a>

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](app-service-web-nodejs-get-started-cli-nodejs.md) – 適用於傳統和資源管理部署模型的 CLI
- [Azure CLI 2.0 (預覽)](app-service-web-nodejs-get-started.md) - 適用於資源管理部署模型的新一代 CLI

## <a name="prerequisites"></a>必要條件
* [Node.js]
* [Bower]
* [Yeoman]
* [Git]
* [Azure CLI 2.0 Preview](/cli/azure/install-az-cli2)
* Microsoft Azure 帳戶。 如果您沒有這類帳戶，可以[註冊免費試用版]，或是[啟用自己的 Visual Studio 訂閱者權益]。

> [!NOTE]
> 您可以[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，而不需要 Azure 帳戶。 建立入門 App，並試用長達一小時。不需要信用卡，也不需簽定合約。
> 
> 

## <a name="create-and-configure-a-simple-nodejs-app-for-azure"></a>建立及設定適用於 Azure 的簡單 Node.js 應用程式
1. 開啟您選擇的命令列終端機，並安裝 [適用於 Yeoman 的 Express 產生器]。
   
        npm install -g generator-express

2. `CD` 至工作目錄，並使用下列語法產生快速應用程式︰
   
        yo express
   
    在系統提示時選擇下列選項︰  
   
    `? Would you like to create a new directory for your project?` **是**  
    `? Enter directory name` **{應用程式名稱}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **無**  
    `? Select a database to use:` **無**  
    `? Select a build tool to use:` **Grunt**

3. `CD` 至新應用程式的根目錄，並加以啟動以確保它會在您的開發環境中執行︰
   
        npm start
   
    在瀏覽器中瀏覽至 <http://localhost:3000> 以確定您可以看到 Express 首頁。 一旦您確認應用程式正確執行，請使用 `Ctrl-C` 來停止它。

6. 從應用程式根目錄開啟 ./config/config.js 檔案，並將生產連接埠變更為 `process.env.port`；`config` 物件中的 `production` 屬性看起來應該會像下列範例：
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    > [!NOTE] 
    > 根據預設，Azure App Service 會使用 `production` 環境變數 (`process.env.NODE_ENV="production"`) 執行 Node.js 應用程式。
    > 因此，這裡的組態可讓 Azure 中的 Node.js 應用程式在 iisnode 所接聽的預設連接埠上回應 Web 要求。
    >
    >

7. 開啟 ./package.json，並新增 `engines` 屬性以 [指定所需的 Node.js 版本](#version)。
   
        "engines": {
            "node": "6.9.1"
        }, 

8. 儲存變更，然後在應用程式的根目錄中初始化 Git 儲存機制，並認可您的程式碼︰
   
        git add .
        git add -f config
        git commit -m "{your commit message}"

## <a name="deploy-your-nodejs-app-to-azure"></a>將 Node.js 應用程式部署至 Azure

1. 登入 Azure (您需要 [Azure CLI 2.0 Preview](#prereq))：
   
        az login
   
    依照提示，在瀏覽器中繼續使用具有 Azure 訂用帳戶的 Microsoft 帳戶進行登入。

3. 設定 App Service 的部署使用者。 稍後您將使用這些認證來部署程式碼。
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 建立新的[資源群組](../azure-resource-manager/resource-group-overview.md)。 在此 node.js 教學課程中，您真的不需要知道它是什麼。

        az group create --location "<location>" --name my-nodejs-app-group

    若要查看您可用於 `<location>` 的可能值，請使用 `az appservice list-locations`CLI 命令。

3. 建立新的「免費」[App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 在此 node.js 教學課程中，只需知道，您可以免費使用本方案中 Web 應用程式。

        az appservice plan create --name my-nodejs-appservice-plan --resource-group my-nodejs-app-group --sku FREE

4. 在 `<app_name>` 中使用唯一名稱建立新的 Web 應用程式。

        az appservice web create --name <app_name> --resource-group my-nodejs-app-group --plan my-nodejs-appservice-plan

5. 使用下列命令，設定新 Web 應用程式的本機 Git 部署︰

        az appservice web source-control config-local-git --name <app_name> --resource-group my-nodejs-app-group

    您會取得如下所示的 JSON 輸出，這表示已設定遠端 Git 儲存機制︰

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. 將 JSON 中的 URL 新增為本機儲存機制的 Git 遠端 (為了簡單起見，稱為 `azure`)。

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. 將您的範例程式碼部署至 `azure` Git 遠端。 出現提示時，使用您稍早設定的部署認證。

        git push azure master
   
    Express 產生器已提供 .gitignore 檔案，因此您的 `git push` 不會取用頻寬來嘗試上傳 node_modules/ 目錄。

9. 最後，在瀏覽器中啟動即時 Azure 應用程式︰
   
        az appservice web browse --name <app_name> --resource-group my-nodejs-app-group
   
    您現在應該會看到 Node.js Web 應用程式在 Azure App Service 中即時執行。
   
    ![瀏覽至已部署的應用程式的範例。][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>更新您的 Node.js Web 應用程式
若要更新在 App Service 中執行的 Node.js Web 應用程式，只需和您最初部署 Web 應用程式時一樣執行 `git add`、`git commit` 和 `git push`。

## <a name="how-app-service-deploys-your-nodejs-app"></a>App Service 如何部署您的 Node.js 應用程式
Azure App Service 使用 [iisnode] 來執行 Node.js 應用程式。 Azure CLI 2.0 Preview 和 Kudu 引擎 (Git 部署) 會一同合作，讓您在透過命令列開發和部署 Node.js 應用程式時獲得流暢的體驗。 

* 您可以在根目錄中建立 iisnode.yml 檔案，並使用它來自訂 iisnode 屬性。 所有可設定的設定都記載於[這裡](https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/iisnode.yml)。
* 在 `git push azure master` 中，Kudu 會自動執行下列部署工作︰
  
  * 如果 package.json 位於儲存機制根目錄中，請執行 `npm install --production`。
  * 為 iisnode 產生指向 package.json 中的啟動指令碼的 Web.config (例如 server.js 或 app.js)。
  * 自訂 Web.config 以讓應用程式準備好使用節點偵測器進行偵錯。

## <a name="use-a-nodejs-framework"></a>使用 Node.js 架構
如果您使用熱門的 Node.js 架構 (例如 [Sails.js][SAILSJS] 或 [MEAN.js][MEANJS]) 來開發應用程式，您可以將這些應用程式部署到 App Service。 熱門的 Node.js 架構有其特定的行為模式，而且其封裝相依性會不斷更新。 不過，App Service 可提供 stdout 和 stderr 記錄給您，讓您確實了解應用程式發生了什麼事並據以變更。 如需詳細資訊，請參閱 [從 iisnode 取得 stdout 和 stderr 記錄](#iisnodelog)。

下列教學課程將為您示範如何在 App Service 中使用特定架構：

* [將 Sails.js Web 應用程式部署至 Azure App Service]
* [在 Azure App Service 中使用 Socket.IO 建立 Node.js 聊天應用程式]
* [如何搭配使用 io.js 和 Azure App Service Web Apps]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>使用特定的 Node.js 引擎
和平常在 package.json 中的方式一樣，您可以在一般工作流程中告知 App Service 使用特定的 Node.js 引擎。
例如：

    "engines": {
        "node": "6.9.1"
    }, 

Kudu 部署引擎會依下列順序決定要使用哪個 Node.js 引擎︰

* 首先，查看 iisnode.yml 以確認是否已指定 `nodeProcessCommandLine`。 如果是，則使用它。
* 接下來，查看 package.json 以確認是否已在 `engines` 物件中指定 `"node": "..."`。 如果是，則使用它。
* 依預設選擇預設的 Node.js 版本。

如需 Azure App Service 中所有支援的 Node.js/NPM 版本更新清單，請存取您應用程式的下列 URL：

    https://<app_name>.scm.azurewebsites.net/api/diagnostics/runtime

> [!NOTE]
> 建議您明確定義想要的 Node.js 引擎。 預設的 Node.js 版本可以變更，但因為預設的 Node.js 版本不適用於您的 Azure Web 應用程式，您可能會在應用程式中收到錯誤。
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>從 iisnode 取得 stdout 和 stderr 記錄
若要讀取 iisnode 記錄，請遵循下列步驟。

> [!NOTE]
> 在完成這些步驟之後，可能要等到發生錯誤時才會有記錄檔。
> 
> 

1. 開啟 Azure CLI 2.0 Preview 提供的 iisnode.yml 檔案。
2. 設定下列兩個參數︰ 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    它們會一起告訴 App Service 中的 iisnode 將其 stdout 和 stderror 輸出放在 D:\home\site\wwwroot\*iisnode* 目錄中。
3. 儲存變更，然後使用下列 Git 命令將變更推送至 Azure：
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode 現在已設定好。 接下來的步驟會示範如何存取這些記錄。
4. 在瀏覽器中存取應用程式的 Kudu 偵錯主控台，其位於︰
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    此 URL 不同於 Web 應用程式 URL，因為前者將「.scm.」 加入了 DNS 名稱中。 如果您未在 URL 中加入此項目，就會收到 404 錯誤。
5. 瀏覽至 D:\home\site\wwwroot\iisnode
   
    ![瀏覽至 iisnode 記錄檔的位置。][iislog-kudu-console-find]
6. 按一下您想讀取之記錄的 [編輯]  圖示。 如果您想要的話，也可以按一下 [下載] 或 [刪除]。
   
    ![開啟 iisnode 記錄檔。][iislog-kudu-console-open]
   
    現在您可以查看記錄以協助您偵錯 App Service 部署。
   
    ![檢查 iisnode 記錄檔。][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>使用節點偵測器偵錯應用程式
如果您使用節點偵測器來偵錯 Node.js 應用程式，您可以將它用於您的即時 App Service 應用程式。 節點偵測器會預先安裝在 App Service 的 iisnode 安裝中。 如果您透過 Git 部署，則從 Kudu 自動產生的 Web.config 已包含啟用節點偵測器所需的所有組態。

若要啟用節點偵測器，請遵循下列步驟︰

1. 開啟位於儲存機制根目錄中的 iisnode.yml，並指定下列參數︰ 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. 儲存變更，然後使用下列 Git 命令將變更推送至 Azure：
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. 現在，只需要在 URL 中加上 /debug 以瀏覽至 package.json 中的啟動指令碼所指定的應用程式啟動檔案。 例如，
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    或者，
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>其他資源
* [在 Azure 應用程式中指定 Node.js 版本](../nodejs-specify-node-version-azure-apps.md)
* [Azure 上 Node.js 應用程式的最佳作法和疑難排解指南](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [如何在 Azure App Service 中偵錯 Node.js Web 應用程式](web-sites-nodejs-debug.md)
* [使用 Node.js 模組與 Azure 應用程式搭配](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps：Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Node.js 開發人員中心](/develop/nodejs/)
* [在 Azure App Service 中開始使用 Web 應用程式](app-service-web-get-started.md)
* [探索神秘無比的 Kudu 偵錯主控台]

<!-- URL List -->

[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[啟用自己的 Visual Studio 訂閱者權益]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[在 Azure App Service 中使用 Socket.IO 建立 Node.js 聊天應用程式]: ./web-sites-nodejs-chat-app-socketio.md
[將 Sails.js Web 應用程式部署至 Azure App Service]: ./app-service-web-nodejs-sails.md
[探索神秘無比的 Kudu 偵錯主控台]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[適用於 Yeoman 的 Express 產生器]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[如何搭配使用 io.js 和 Azure App Service Web Apps]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[註冊免費試用版]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Jan17_HO3-->


