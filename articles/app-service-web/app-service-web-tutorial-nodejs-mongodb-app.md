---
title: "在 Azure 中建置 Node.js 和 MongoDB Web 應用程式 | Microsoft Docs"
description: "了解如何取得在 Azure 中運作的 Node.js 應用程式，並利用 MongoDB 連接字串連線到 Cosmos DB 資料庫。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 2a3d63b3829e750b62658d720522ae1abf89cd86
ms.contentlocale: zh-tw
ms.lasthandoff: 06/01/2017

---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>在 Azure 中建置 Node.js 和 MongoDB Web 應用程式
本教學課程示範如何在 Azure 中建立 Node.js Web 應用程式，並將它連接到 MongoDB 資料庫。 完成之後，您的 MEAN 應用程式 (MongoDB、Express、AngularJS 及 Node.js) 將會在 [Azure App Service Web Apps](app-service-web-overview.md) 上執行。 為了簡單起見，範例應用程式會使用 [MEAN.js web 架構](http://meanjs.org/)。

![在 Azure App Service 中執行的 MEAN.js 應用程式](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 MongoDB 資料庫
> * 將 Node.js 應用程式連線至 MongoDB
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 來自 Azure 的串流診斷記錄
> * 在 Azure 入口網站中管理應用程式

## <a name="prerequisites"></a>必要條件

執行此範例之前，請在本機安裝下列必要條件︰

1. [下載並安裝 git](https://git-scm.com/)
1. [下載並安裝 Node.js 和 NPM](https://nodejs.org/)
1. [安裝 Gulp.js](http://gulpjs.com/) ([MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started) 的必要項目)
1. [下載、安裝及執行 MongoDB Community 版本](https://docs.mongodb.com/manual/administration/install-community/) 
1. [下載並安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>測試本機的 MongoDB
在此步驟中，您要確定您的本機 MongoDB 資料庫正在執行。

開啟終端機視窗，然後 `cd` 至 MongoDB 安裝的 `bin` 目錄。 

在終端機上執行 `mongo`，以連接到本機的 MongoDB 伺服器。

```bash
mongo
```

如果連接成功，則您的 MongoDB 資料庫已在執行中。 如果沒有，請確定您的本機 MongoDB 資料庫已遵循[下載、安裝及執行 MongoDB Community Edition (英文)](https://docs.mongodb.com/manual/administration/install-community/) 中的步驟來啟動。 MongoDB 通常已安裝，但您仍需要執行 `mongod` 才能將它啟動。 

當您完成測試 MongoDB 資料庫時，在終端機上輸入 `Ctrl`+`C`。 

<a name="step2"></a>

## <a name="create-local-nodejs-app"></a>建立本機的 Node.js 應用程式
在此步驟中，您要設定本機的 Node.js 專案。

### <a name="clone-the-sample-application"></a>複製範例應用程式

開啟終端機視窗，然後 `cd` 至工作目錄。  

執行下列命令來複製範例存放庫。 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

此範例存放庫包含 [MEAN.js 存放庫](https://github.com/meanjs/mean)的副本。 若要在 App Service 上執行，會將它以最低限度的方式修改 (如需詳細資訊，請參閱[讀我檔案](https://github.com/Azure-Samples/meanjs/blob/master/README.md))。

### <a name="run-the-application"></a>執行應用程式

安裝必要的封裝，然後啟動應用程式。

```bash
cd meanjs
npm install
npm start
```

當應用程式完全載入時，您應會看到類似下列的訊息：

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

在瀏覽器中，瀏覽至 `http://localhost:3000`。 按一下上層功能表中的 [註冊]，然後嘗試建立一位虛擬使用者。 

MEAN.js 範例應用程式會將使用者資料儲存於資料庫中。 如果您成功且 MEAN.js 自動登入至所建立的使用者，則您的應用程式正在將資料寫入至本機 MongoDB 資料庫。

![MEAN.js 成功連線至 MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

試著按一下 [系統管理員] > [管理文章] 來新增一些文章。

如需隨時停止 Node.js，請在終端機上輸入 `Ctrl`+`C`。 

## <a name="create-production-mongodb"></a>建立生產環境 MongoDB

在此步驟中，您要在 Azure 中建立 MongoDB 資料庫。 將您的應用程式部署至 Azure 時，它會針對其生產工作負載使用此資料庫。

針對 MongoDB，本教學課程使用 [Azure Cosmos DB](/azure/documentdb/)。 Cosmos DB 支援 MongoDB 用戶端連線。

### <a name="log-in-to-azure"></a>登入 Azure

您將要在終端機視窗中使用 Azure CLI 2.0，來建立在 Azure App Service 中裝載 Node.js 應用程式所需的資源。  使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](../azure-resource-manager/resource-group-overview.md) 來建立[資源群組](/cli/azure/group#create)。 Azure 資源群組是一個邏輯容器，可在其中部署與管理 Azure 資源 (例如 Web 應用程式、資料庫和儲存體帳戶)。 

下列範例會在西歐區域中建立一個資源群組。

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

若要查看您可用於 `--location` 的可能值，請使用 `az appservice list-locations` Azure CLI 命令。

### <a name="create-a-cosmos-db-account"></a>建立 Cosmos DB 帳戶

使用 [az cosmosdb create](/cli/azure/cosmosdb#create) 命令來建立 Cosmos DB 帳戶。

在下列命令中，在您看見 _&lt;cosmosdb_name>_ 預留位置的地方，替代成您自己的唯一 Cosmos DB 名稱。 這個唯一名稱會用來作為 Cosmos DB 端點 `https://<cosmosdb_name>.documents.azure.com/` 的一部分，因此，這個名稱在 Azure 中的所有 Cosmos DB 帳戶上必須是唯一的。 

```azurecli
az cosmosdb create \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup \
    --kind MongoDB
```

`--kind MongoDB` 參數會啟用 MongoDB 用戶端連接。

> [!NOTE]
> _&lt;cosmosdb_name>_ 只能包含小寫字母、數字及 _-_ 字元，且長度必須為 3 到 50 個字元。
>
>

建立 Cosmos DB 帳戶之後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output has been truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>將應用程式連線至生產環境 MongoDB

在此步驟中，您要使用 MongoDB 連接字串，將 MEAN.js 範例應用程式連線至您剛才建立的 Cosmos DB 資料庫。 

### <a name="retrieve-the-database-key"></a>擷取資料庫索引鍵

若要連線至 Cosmos DB 資料庫，您需要資料庫金鑰。 使用 [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) 命令來擷取主要金鑰。

```azurecli
az cosmosdb list-keys \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup
```

Azure CLI 會輸出類似下列範例的資訊：

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

將 `primaryMasterKey` 的值複製到文字編輯器。 您需要在下一個步驟中用到此資訊。

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>在 Node.js 應用程式中設定連接字串

在您的 MEAN.js 存放庫中，開啟 _config/env/production.js_。

在 `db` 物件中，取代 `uri` 的值，如下列範例中所示。 務必也要將這兩個 _&lt;cosmosdb_name>_ 預留位置取代為您的 Cosmos DB 資料庫名稱，並將 _&lt;primary_master_key>_ 預留位置取代為您在上一個步驟中複製的金鑰。

```javascript
db: {
  uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> `ssl=true` 選項很重要，因為 [Cosmos DB 需要 SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements)。 
>
>

儲存您的變更。

### <a name="test-the-application-in-production-mode"></a>在生產模式中測試應用程式 

如同一些其他的 Node.js Web 架構，MEAN.js 會使用 `gulp` 來縮小和組合用於生產環境的指令碼。 這會產生生產環境所需的檔案。 

立即執行 `gulp prod`。

```bash
gulp prod
```

接下來，執行下列命令，可使用您在 _config/env/production.js_ 中設定的連接字串。

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` 會設定環境變數，以告知 Node.js 在生產環境中執行，而 `node server.js` 會使用存放庫根目錄中的 `server.js` 來啟動 Node.js 伺服器。 這是在 Azure 中載入 Node.js 應用程式的方式。 

載入應用程式之後，請檢查以確定它正在生產環境中執行：

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

在瀏覽器中，瀏覽至 `http://localhost:8443`。 按一下上層功能表中的 [註冊]，然後嘗試建立一位虛擬使用者，就像之前一樣。 如果成功，則您的應用程式就會將資料寫入至 Azure 中的 Cosmos DB 資料庫。 

在終端機中，輸入 `Ctrl`+`C` 以停止 Node.js。 

## <a name="deploy-app-to-azure"></a>將應用程式部署到 Azure
在此步驟中，您要將已與 MongoDB 連接的 Node.js 應用程式部署至 Azure App Service。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令來建立 App Service 方案。 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

下列範例會使用**免費**定價層，建立名為 _myAppServicePlan_ 的 App Service 方案：

```azurecli
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

建立 App Service 方案後，Azure CLI 會顯示類似下列範例的資訊：

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
```

### <a name="create-a-web-app"></a>建立 Web 應用程式

現已建立 App Service 方案，請在 _myAppServicePlan_ App Service 方案中建立 Web 應用程式。 Web 應用程式會為您提供裝載空間來部署程式碼，以及提供 URL 讓您能夠檢視已部署的應用程式。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令來建立 Web 應用程式。 

在下列命令中，將 _&lt;app_name>_ 預留位置替代成您自己的唯一應用程式名稱。 這個唯一名稱會用來作為 Web 應用程式預設網域名稱的一部分，因此，這個名稱在 Azure 的所有應用程式中必須是唯一的。 您稍後先將任何自訂 DNS 項目對應至 Web 應用程式，再將它公開給使用者。 

```azurecli
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的資訊： 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-an-environment-variable"></a>設定環境變數

稍早在本教學課程中，您已將資料庫連接字串硬式編碼於 _config/env/production.js_ 中。 基於安全性最佳做法，您會想要將這些敏感性資料保存在您的 Git 存放庫以外的地方。 為了在 Azure 中執行應用程式，您將改用環境變數。

在 App Service 中，您可以使用 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 命令，將環境變數設定為「應用程式設定」。 

下列範例可讓您在 Azure Web 應用程式中設定 `MONGODB_URI` 應用程式設定。 請務必取代預留位置，就像之前一樣。

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

在 Node.js 程式碼中，您可以利用 `process.env.MONGODB_URI` 來存取此應用程式設定，就像存取任何環境變數一樣。 

現在，使用下列命令，將您的變更復原為 _config/env/production.js_：

```bash
git checkout -- .
```

再次開啟 _config/env/production.js_。 請注意，已經將預設的 MEAN.js 應用程式設定為使用您剛才建立的 `MONGODB_URI` 環境變數。

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>設定本機 git 部署 

您可以使用各種方式來將應用程式部署至 Azure App Service，包括 FTP、本機 Git、GitHub、Visual Studio Team Services 和 BitBucket。 對於 FTP 和本機 Git，必須在伺服器上設定部署使用者，才能驗證您的部署。 

使用 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令來建立帳戶層級的認證。 

> [!NOTE] 
> 需要部署使用者，才能將 FTP 和本機 Git 部署至 App Service。 此部署使用者是帳戶層級。 因此，它與您的 Azure 訂用帳戶不同。 您只需設定此部署使用者一次。

```azurecli
az appservice web deployment user set \
    --user-name <specify-a-username> \
    --password <minimum-8-char-capital-lowercase-number>
```

使用 [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 命令，來設定 Azure Web 應用程式的本機 Git 存取。 

```azurecli
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

設定部署使用者時，Azure CLI 會以下列格式顯示 Azure Web 應用程式的部署 URL：

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

複製終端機的輸出，因為下一個步驟中會使用此資訊。 

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

將 Azure 遠端新增至本機 Git 存放庫。 

```bash
git remote add azure <paste_copied_url_here> 
```

推送至 Azure 遠端，以部署您的 Node.js 應用程式。 建立部署使用者時，系統會提示您輸入稍早提供的密碼。 

```bash
git push azure master
```

在部署期間，Azure App Service 會與 Git 溝通其進度。

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

> [!NOTE]
> 您可能會注意到，部署程序會在 `npm install` 之後執行 [Gulp](http://gulpjs.com/)。 App Service 不會在部署期間執行 Gulp 或 Grunt 工作，因此，這個範例存放庫在其根目錄中有兩個其他檔案可啟用它： 
>
> - _.deployment_ - 此檔案會告訴 App Service，執行 `bash deploy.sh` 以作為自訂部署指令碼。
> - _deploy.sh_ - 自訂部署指令碼。 如果您檢閱檔案，您將看到它會在 `npm install` 和 `bower install` 之後執行 `gulp prod`。 
>
> 您可以使用這種方法，將任何步驟新增至您的 Git 部署。
>
> 如果您在任一時間點重新啟動 Azure Web 應用程式，App Service 並不會重新執行這些自動化工作。
>
>

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式 
使用 Web 瀏覽器，瀏覽至已部署的 Web 應用程式。 

```bash 
http://<app_name>.azurewebsites.net 
``` 

按一下上層功能表中的 [註冊]，然後嘗試建立一位虛擬使用者。 

如果成功且應用程式會自動登入已建立的使用者，則您在 Azure 中的 MEAN.js 應用程式就已連線到 MongoDB (Cosmos DB) 資料庫。 

![在 Azure App Service 中執行的 MEAN.js 應用程式](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

試著按一下 [系統管理員] > [管理文章] 來新增一些文章。 

**恭喜！** 您正在 Azure App Service 中執行資料驅動的 Node.js 應用程式。

## <a name="update-data-model-and-redeploy"></a>更新資料模型並重新部署

在此步驟中，您要對 `article` 資料模型進行一些變更，並將變更發佈至 Azure。

### <a name="update-the-data-model"></a>更新資料模型

開啟 _modules/articles/server/models/article.server.model.js_。

在 `ArticleSchema` 中，新增名為 `comment` 的 `String` 類型。 完成時，您的結構描述程式碼應該如下所示：

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

您已完成模型變更。 

### <a name="update-the-articles-code"></a>更新 articles 程式碼

接下來，更新 `articles` 程式碼的其餘部分以使用 `comment`。

總共有五 (5) 個需要修改的檔案、伺服器控制器和四個用戶端檢視。 

首先，開啟 _modules/articles/server/controllers/articles.server.controller.js_。

在 `update` 函式中，新增 `article.comment` 的指派。 完成時，您的 `update` 函式應該如下所示：

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

接下來，開啟 _modules/articles/client/views/view-article.client.view.html_。

就在結尾 `</section>` 標記的正上方，新增下列程式碼行來顯示 `comment` 以及剩餘的文章資料：

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

接下來，開啟 _modules/articles/client/views/list-articles.client.view.html_.

就在結尾 `</a>` 標記的正上方，新增下列程式碼行來顯示 `comment` 以及剩餘的文章資料：

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

接下來，開啟 _modules/articles/client/views/admin/list-articles.client.view.html_.

在 `<div class="list-group">` 標記內部且就在結尾 `comment` 標記的正上方，新增下列程式碼行來顯示 `</a>` 以及剩餘的文章資料：

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

最後，開啟 _modules/articles/client/views/admin/form-article.client.view.html_.

找到包含提交按鈕的 `<div class="form-group">` 標記，如下：

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

就在此標記的正上方，新增另一個 `<div class="form-group">` 標記，讓使用者能夠編輯 `comment` 欄位。 新的標記應該如下所示：

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>本機測試您的變更

儲存您的所有變更。

再次在生產模式中測試您的變更。

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> 請記住，您的 _config/env/production.js_ 已還原，`MONGODB_URI` 環境變數只會設定於 Azure Web 應用程式中，而不會設定於本機電腦上。 如果您查看設定檔，就會發現生產設定預設為使用本機的 MongoDB 資料庫。 如此可確保當您在本機測試程式碼變更時，不會碰觸到生產資料。
>
>

在瀏覽器中，瀏覽至 `http://localhost:8443`，並確定您已登入。

按一下 [系統管理員] > [管理文章]，然後按一下 [+] 按鈕來新增文章。

您現在應該會看到新的 `Comment` 文字方塊。

![已將註解欄位新增到文章中](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

在終端機中，輸入 `Ctrl`+`C` 以停止 Node.js。 

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

在 git 中認可您的變更，然後將程式碼變更推送至 Azure。

```bash
git commit -am "added article comment"
git push azure master
```

完成 `git push` 之後，瀏覽至 Azure Web 應用程式，然後再次嘗試執行新功能。

![發佈至 Azure 的模型和資料庫變更](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

> [!NOTE]
> 如果您先前已新增任何文章，您仍然可以看到它們。 Cosmos DB 中的現有資料不會遺失。 此外，您的變更會更新至資料結構描述，並讓現有資料保留不變。
>
>

## <a name="stream-diagnostic-logs"></a>資料流診斷記錄 

當 Node.js 應用程式在 Azure App Service 中執行時，您可以使用管線將主控台記錄直接傳送至終端機。 這樣一來，您就能取得相同的診斷訊息，以協助您偵錯應用程式錯誤。

若要開始記錄資料流，使用 [az appservice web log tail](/cli/azure/appservice/web/log#tail) 命令。

```azurecli 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

開始記錄資料流之後，重新整理瀏覽器中的 Azure Web 應用程式，以取得部分 Web 流量。 您現在應該會看到使用管線傳送到終端機的主控台記錄。

您隨時可以輸入 `Ctrl`+`C` 以停止記錄資料流。 

## <a name="manage-your-azure-web-app"></a>管理您的 Azure Web 應用程式

請移至 Azure 入口網站，以查看您所建立的 Web 應用程式。

若要這麼做，請登入 [https://portal.azure.com](https://portal.azure.com)。

按一下左側功能表中的 [App Service]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

您已進入 Web 應用程式的_刀鋒視窗_ (水平開啟的入口網站頁面)。

根據預設，Web 應用程式的刀鋒視窗會顯示 [概觀] 頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 刀鋒視窗左側的索引標籤會顯示您可開啟的各種設定頁面。

![Azure 入口網站中的 App Service 刀鋒視窗](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

刀鋒視窗中的索引標籤會顯示您可以新增至 Web 應用程式的許多強大功能。 下表提供幾個可能性︰

* 對應自訂 DNS 名稱
* 繫結自訂 SSL 憑證
* 設定連續部署
* 相應增加和相應放大
* 新增使用者驗證

## <a name="clean-up-resources"></a>清除資源
 
如果您不需要這些資源來進行其他教學課程 (請參閱[後續步驟](#next))，您可以執行下列命令來將這些資源刪除︰ 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 MongoDB 資料庫
> * 將 Node.js 應用程式連線至 MongoDB
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 將記錄從 Azure 串流到終端機
> * 在 Azure 入口網站中管理應用程式

前進至下一個教學課程，了解如何對它對應自訂 DNS 名稱。

> [!div class="nextstepaction"] 
> [將現有的自訂 DNS 名稱對應至 Azure Web Apps](app-service-web-tutorial-custom-domain.md)

