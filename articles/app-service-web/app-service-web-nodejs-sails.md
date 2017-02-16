---
title: "將 Sails.js Web 應用程式部署至 Azure App Service | Microsoft Docs"
description: "了解如何將 Node.js 應用程式部署到 Azure App Service。 本教學課程示範如何部署 Sails.js Web 應用程式。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8877ddc8-1476-45ae-9e7f-3c75917b4564
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 2bc84ce01918878abbef1faf539561a4fec4c2e9


---
# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>將 Sails.js Web 應用程式部署至 Azure App Service
本教學課程示範如何將 Sails.js 應用程式部署至 Azure App Service。 過程中，您可以搜集一些如何設定 Node.js 應用程式以在 App Service 中執行的一般知識。

在這裡，您將學習有用的技能，例如︰

* 設定在 App Service 中執行 Sails.js 應用程式。
* 從命令列將應用程式部署至 App Service。
* 讀取 stderr 和 stdout 記錄來疑難排解任何部署問題。
* 儲存原始檔控制外部的環境變數。
* 從您的應用程式存取 Azure 環境變數。
* 連接到資料庫 (MongoDB)。

您應具備 Sails.js 的使用知識。 本教學課程的目的不是協助您了解一般執行 Sail.js 的相關問題。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](app-service-web-nodejs-sails-cli-nodejs.md) – 適用於傳統和資源管理部署模型的 CLI
- [Azure CLI 2.0 (預覽)](app-service-web-nodejs-sails.md) - 適用於資源管理部署模型的新一代 CLI

## <a name="prerequisites"></a>必要條件
* [Node.js](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [Azure CLI 2.0 Preview](/cli/azure/install-az-cli2)
* Microsoft Azure 帳戶。 如果您沒有這類帳戶，可以[註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 您可以[試用 App Service](https://azure.microsoft.com/try/app-service/)，而不需要 Azure 帳戶。 建立入門 App，並試用長達一小時。不需要信用卡，也不需簽定合約。
> 
> 

## <a name="step-1-create-and-configure-a-sailsjs-app-locally"></a>步驟 1︰在本機建立和設定 Sails.js 應用程式
首先，請執行下列步驟 ，在部署環境中快速建立預設 Sails.js 應用程式︰

1. 開啟您選擇的命令列終端機，並 `CD` 到工作目錄。
2. 建立 Sails.js 應用程式並加以執行︰

        sails new <app_name>
        cd <app_name>
        sails lift

    請確定您可以瀏覽到 http://localhost:1377 上的預設首頁。

1. 接下來，啟用 Azure 記錄。 在根目錄中，建立名為 `iisnode.yml` 的檔案並新增下列兩行︰

        loggingEnabled: true
        logDirectory: iisnode

    現在已啟用 Azure App Service 用來執行 Node.js 應用程式的 [iisnode](https://github.com/tjanczuk/iisnode) 伺服器記錄功能。 
    如需其運作方式的詳細資訊，請參閱 [從 iisnode 取得 stdout 和 stderr 記錄](app-service-web-nodejs-get-started.md#iisnodelog)。

2. 接下來，設定 Sails.js 應用程式以使用 Azure 環境變數。 開啟 config/env/production.js 來設定您的生產環境，並設定 `port` 和 `hookTimeout`：

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    您可以在  [Sails.js 文件](http://sailsjs.org/documentation/reference/configuration/sails-config)中找到這些組態設定的文件。

4. 接下來，硬式編碼您要使用的 Node.js 版本。 在 package.json 中新增以下 `engines` 屬性，以將 Node.js 版本設定為我們需要的版本。

        "engines": {
            "node": "6.9.1"
        },

5. 最後，初始化 Git 儲存機制，並認可您的檔案。 在應用程式根目錄中 (在 package.json 的所在位置)，執行下列 Git 命令︰

        git init
        git add .
        git commit -m "<your commit message>"

您的程式碼已準備好進行部署。 

## <a name="step-2-create-an-azure-app-and-deploy-sailsjs"></a>步驟 2︰建立 Azure 應用程式和部署 Sails.js

接下來，在 Azure 中建立 App Service 資源，並將您的 Sails.js 應用程式部署至其中。

1. 如下所示，登入 Azure：

        az login

    依照提示，在瀏覽器中繼續使用具有 Azure 訂用帳戶的 Microsoft 帳戶進行登入。

3. 設定 App Service 的部署使用者。 稍後您將使用這些認證來部署程式碼。
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 使用名稱建立[資源群組](../azure-resource-manager/resource-group-overview.md)。 在此 PHP 教學課程中，您真的不需要知道它是什麼。

        az group create --location "<location>" --name my-sailsjs-app-group

    若要查看您可用於 `<location>` 的可能值，請使用 `az appservice list-locations`CLI 命令。

3. 使用名稱建立「免費」[App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 在此 PHP 教學課程中，只需知道，您可以免費使用本方案中 Web 應用程式。

        az appservice plan create --name my-sailsjs-appservice-plan --resource-group my-sailsjs-app-group --sku FREE

4. 在 `<app_name>` 中使用唯一名稱建立新的 Web 應用程式。

        az appservice web create --name <app_name> --resource-group my-sailsjs-app-group --plan my-sailsjs-appservice-plan

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>步驟 3︰設定和部署 Sails.js 應用程式

1. 使用下列命令，設定新 Web 應用程式的本機 Git 部署︰

        az appservice web source-control config-local-git --name <app_name> --resource-group my-sailsjs-app-group

    您會取得如下所示的 JSON 輸出，這表示已設定遠端 Git 儲存機制︰

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. 將 JSON 中的 URL 新增為本機儲存機制的 Git 遠端 (為了簡單起見，稱為 `azure`)。

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. 將您的範例程式碼部署至 `azure` Git 遠端。 出現提示時，使用您稍早設定的部署認證。

        git push azure master

7. 最後，在瀏覽器中只要啟動即時 Azure 應用程式︰

        az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

    您現在應該會看到相同的 Sails.js 首頁。

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>疑難排解您的部署
如果 Sails.js 應用程式在 App Service 中因為某些原因而失敗，請尋找 stderr 記錄，以協助進行疑難排解。
如需詳細資訊，請參閱 [從 iisnode 取得 stdout 和 stderr 記錄](app-service-web-nodejs-get-started.md#get-stdout-and-stderr-logs-from-iisnode)。
如果應用程式已順利啟動，stdout 記錄應該會顯示您熟悉的訊息︰

                   .-..-.
    
       Sails              <|    .-..-.
       v0.12.11            |\
                          /|.\
                         / || \
                       ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
       __---___--___---___--___---___--___
     ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

您可以在 [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) 檔案中控制 stdout 記錄檔的細微度。

## <a name="connect-to-a-database-in-azure"></a>連接到 Azure 中的資料庫
若要連接到 Azure 中的資料庫，您可以在 Azure 中建立所選擇的資料庫，例如 Azure SQL Database、MySQL、MongoDB、Azure (Redis) 快取等，並使用對應的 [資料存放區配接器](https://github.com/balderdashy/sails#compatibility) 連接到它。 本章節中的步驟示範如何使用可支援 MongoDB 用戶端連線的 [Azure DocumentDB](../documentdb/documentdb-protocol-mongodb.md) 資料庫連線至 MongoDB。

1. [建立具有 MongoDB 通訊協定支援的 DocumentDB 帳戶](../documentdb/documentdb-create-mongodb-account.md)。
2. [建立 DocumentDB 集合和資料庫](../documentdb/documentdb-create-collection.md)。 集合的名稱並不重要，但是當您從 Sails.js 連線時，需要資料庫的名稱。
3. [尋找您 DocumentDB 資料庫的連接資訊](../documentdb/documentdb-connect-mongodb-account.md#a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize)。
2. 從命令列終端機安裝 MongoDB 配接器︰

        npm install sails-mongo --save

3. 開啟 config/connections.js 並將下列連接物件加入至清單︰

        docDbMongo: {
            adapter: 'sails-mongo',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost,
            port: process.env.dbport,
            database: process.env.dbname,
            ssl: true
        },

    > [!NOTE] 
    > `ssl: true` 選項很重要，因為 [Azure DocumentDB 需要](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements)。 
    >
    >

4. 針對每個環境變數 (`process.env.*`)，您需要在 App Service 中加以設定。 若要執行此動作，可從您的終端機執行下列命令。 使用您 DocumentDB 資料庫的連接資訊。

        az appservice web config appsettings update --settings dbuser="<database user>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbpassword="<database password>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbhost="<database hostname>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbport="<database port>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbname="<database name>" --name <app_name> --resource-group my-sailsjs-app-group

    將您的設定放在 Azure 應用程式設定中，可讓敏感性資料脫離原始檔控制 (Git)。 接下來，您將設定開發環境，以便使用相同的連接資訊。
5. 開啟 config/local.js 並新增下列連接物件︰

        connections: {
            docDbMongo: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>",
                database: "<database name>",
                ssl: true
            },
        },

    此組態會覆寫 config/connections.js 檔案中本機環境的設定。 您專案中的預設 .gitignore 會排除這個檔案，因此不會儲存在 Git 中。 您現在可以從 Azure Web 應用程式和從本機開發環境連接到您的 DocumentDB (MongoDB) 資料庫。
6. 開啟 config/env/production.js 來設定您的生產環境，並加入下列 `models` 物件：

        models: {
            connection: 'docDbMongo',
            migrate: 'safe'
        },
7. 開啟 config/env/development.js 來設定您的開發環境，並加入下列 `models` 物件：

        models: {
            connection: 'docDbMongo',
            migrate: 'alter'
        },

    `migrate: 'alter'` 可讓您使用資料庫移轉功能，輕鬆地建立和更新資料庫集合或資料表。 不過，要針對您的 Azure (生產) 環境使用 `migrate: 'safe'`，因為 Sails.js 不允許您在生產環境中使用 `migrate: 'alter'` (請參閱  [Sails.js 文件](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings))。
8. 從終端機中，[產生](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) Sails.js [藍圖 API](http://sailsjs.org/documentation/concepts/blueprints) (就像您平常所做的一樣)，然後執行 `sails lift` 以使用 Sails.js 資料庫移轉來建立資料庫。 例如：

         sails generate api mywidget
         sails lift

    此命令所產生的 `mywidget` 模型是空的，但我們可以用它來顯示我們有資料庫連線能力。
    當您執行 `sails lift`時，它會針對應用程式所使用的模型建立遺漏的集合和資料表。
9. 存取您剛剛在瀏覽器中建立的藍圖 API。 例如：

        http://localhost:1337/mywidget/create

    此 API 應該會在瀏覽器視窗中將建立的項目傳回給您，這表示您的集合建立成功。

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. 現在，將變更推送至 Azure，並瀏覽至您的應用程式以確定它仍能運作。

         git add .
         git commit -m "<your commit message>"
         git push azure master
         az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

11. 存取 Azure Web 應用程式的藍圖 API。 例如：

         http://<appname>.azurewebsites.net/mywidget/create

     如果此 API 傳回另一個新項目，則您的 Azure Web 應用程式會與您的 DocumentDB (MongoDB) 資料庫通訊。

## <a name="more-resources"></a>其他資源
* [在 Azure App Service 中開始使用 Node.js Web 應用程式](app-service-web-nodejs-get-started.md)
* [使用 Node.js 模組與 Azure 應用程式搭配](../nodejs-use-node-modules-azure-apps.md)



<!--HONumber=Jan17_HO3-->


