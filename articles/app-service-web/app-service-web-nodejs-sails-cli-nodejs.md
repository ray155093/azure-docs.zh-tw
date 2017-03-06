---
title: "將 Sails.js Web 應用程式部署至 Azure App Service | Microsoft Docs"
description: "了解如何將 Node.js 應用程式部署到 Azure App Service。 本教學課程示範如何部署 Sails.js Web 應用程式。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
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
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 9d43405539ced1dadcaa0cb11bb0c3dd42dee2b9
ms.lasthandoff: 03/01/2017


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
- [Azure CLI 2.0](app-service-web-nodejs-sails.md) - 適用於資源管理部署模型的新一代 CLI

## <a name="prerequisites"></a>必要條件
* [Node.js](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [Azure CLI](../xplat-cli-install.md)
* Microsoft Azure 帳戶。 如果您沒有這類帳戶，可以[註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 您可以[試用 App Service](https://azure.microsoft.com/try/app-service/)，而不需要 Azure 帳戶。 建立入門 App，並試用長達一小時。不需要信用卡，也不需簽定合約。
> 
> 

## <a name="step-1-create-a-sailsjs-app-locally"></a>步驟 1︰在本機建立 Sails.js 應用程式
首先，請執行下列步驟 ，在部署環境中快速建立預設 Sails.js 應用程式︰

1. 開啟您選擇的命令列終端機，並 `CD` 到工作目錄。
2. 建立 Sails.js 應用程式並加以執行︰

        sails new <appname>
        cd <appname>
        sails lift

    請確定您可以瀏覽到 http://localhost:1377 上的預設首頁。

## <a name="step-2-create-the-azure-app-resource"></a>步驟 2︰建立 Azure 應用程式資源
接下來，在 Azure 中建立 App Service 資源。 您稍後要將 Sails.js 應用程式部署到其中。

1. 如下所示，登入 Azure：
2. 在相同的終端機中，變更為 ASM 模式並登入 Azure：

        azure config mode asm
        azure login

    依照提示，在瀏覽器中繼續使用具有 Azure 訂用帳戶的 Microsoft 帳戶進行登入。

3. 設定 App Service 的部署使用者。 稍後您將使用認證來部署程式碼。
   
        azure site deployment user set --username <username> --pass <password>

3. 確定您仍在 Sails.js 專案的根目錄中。 在 Azure 中以下一個命令建立具有唯一應用程式名稱的 App Service 應用程式資源。 您的 Web 應用程式 URL 是 http://&lt;appname>.azurewebsites.net。

        azure site create --git <appname>

    依照提示來選取要部署的目標 Azure 區域。 建立 App Service 應用程式資源之後：

   * Sails.js 應用程式會初始化 Git，
   * 本機初始化 Git 的儲存機制會連接至新的 App Service 應用程式做為 Git 遠端，恰如其名為 "azure"，並且
   * 會在根目錄中建立 iisnode.yml 檔案。 您可以使用這個檔案來設定 [iisnode](https://github.com/tjanczuk/iisnode)，讓 App Service 可用以執行 Node.js 應用程式。

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>步驟 3︰設定和部署 Sails.js 應用程式
 使用 App Service 中的 Sails.js 應用程式包含三個主要步驟︰

* 針對此應用程式設定您的應用程式，以便在 App Service 中執行
* 將它部署到 App Service
* 讀取 stderr 和 stdout 記錄來疑難排解任何部署問題

請遵循下列步驟：

1. 在根目錄中開啟新的 iisnode.yml 檔案並新增下列兩行︰

        loggingEnabled: true
        logDirectory: iisnode

    現在已啟用 Azure App Service 用來執行 Node.js 應用程式的 [iisnode](https://github.com/tjanczuk/iisnode) 伺服器記錄功能。 
    如需其運作方式的詳細資訊，請參閱 [從 iisnode 取得 stdout 和 stderr 記錄](app-service-web-nodejs-get-started.md#iisnodelog)。
2. 開啟 config/env/production.js 來設定您的生產環境，並設定 `port` 和 `hookTimeout`：

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    您可以在  [Sails.js 文件](http://sailsjs.org/documentation/reference/configuration/sails-config)中找到這些組態設定的文件。

4. 在 package.json 中新增以下 `engines` 屬性，以將 Node.js 版本設定為我們需要的版本。

        "engines": {
            "node": "6.9.1"
        },
5. 儲存變更並測試變更，以確定您的應用程式仍在本機執行。 若要這樣做，請刪除 `node_modules` 資料夾，然後執行：

        npm install
        sails lift
6. 現在，使用 git 將您的應用程式部署至 Azure：

        git add .
        git commit -m "<your commit message>"
        git push azure master
7. 最後，在瀏覽器中只要啟動即時 Azure 應用程式︰

        azure site browse

    您現在應該會看到相同的 Sails.js 首頁。

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>疑難排解您的部署
如果 Sails.js 應用程式在 App Service 中因為某些原因而失敗，請尋找 stderr 記錄，以協助進行疑難排解。
如需詳細資訊，請參閱 [從 iisnode 取得 stdout 和 stderr 記錄](app-service-web-nodejs-get-started.md#get-stdout-and-stderr-logs-from-iisnode)。
如果它已順利啟動，stdout 記錄應該會顯示您熟悉的訊息︰

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

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbport="<database port>"
        azure site appsetting add dbname="<database name>"

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
    當您執行 `sails lift`時，它會針對應用程式所使用的模型建立遺漏的集合或資料表。
9. 存取您剛剛在瀏覽器中建立的藍圖 API。 例如：

        http://localhost:1337/mywidget/create

    此 API 應該會在瀏覽器視窗中將建立的項目傳回給您，這表示您的資料庫建立成功。

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. 現在，將變更推送至 Azure，並瀏覽至您的應用程式以確定它仍能運作。

         git add .
         git commit -m "<your commit message>"
         git push azure master
         azure site browse
11. 存取 Azure Web 應用程式的藍圖 API。 例如：

         http://<appname>.azurewebsites.net/mywidget/create

     如果此 API 傳回另一個新項目，則您的 Azure Web 應用程式會與您的 DocumentDB (MongoDB) 資料庫通訊。

## <a name="more-resources"></a>其他資源
* [在 Azure App Service 中開始使用 Node.js Web 應用程式](app-service-web-nodejs-get-started.md)
* [使用 Node.js 模組與 Azure 應用程式搭配](../nodejs-use-node-modules-azure-apps.md)

