---
title: "在 Azure Web 應用程式上建立 PHP 應用程式 | Microsoft Docs"
description: "短短幾分鐘內在 App Service Web 應用程式中部署第一個 PHP Hello World。"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/04/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0541778e07193c4903a90ce0b91db224bdf60342
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-php-application-on-web-app"></a>在 Web 應用程式上建立 PHP 應用程式

本快速入門教學課程會逐步解說如何開發 PHP 應用程式及部署至 Azure 。 我們將使用 [Azure App Service 方案](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)來執行應用程式，以及使用 Azure CLI 在其中建立和設定新的 Web 應用程式。 我們接著會使用 git 將 PHP 應用程式部署至 Azure。

![hello-world-in-browser](media/app-service-web-get-started-php/hello-world-in-browser.png)

您可以使用 Mac、Windows 或 Linux 電腦，依照下面步驟操作。 完成所有步驟只需要大約 5 分鐘的時間。

## <a name="prerequisites"></a>必要條件

建立這個範例之前，請下載並安裝下列各項︰

* [Git](https://git-scm.com/)
* [PHP](https://php.net)
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下載範例

將 Hello World 範例應用程式存放庫複製到本機電腦。

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
```

變更為包含範例程式碼的目錄。

```bash
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>在本機執行應用程式

在本機執行應用程式，做法是開啟終端機視窗並使用範例的 `php` 命令列，以啟動內建 PHP Web 伺服器。

```bash
php -S localhost:8080
```

開啟網頁瀏覽器，然後瀏覽至範例。

```bash
http://localhost:8080
```

您可以看到來自範例應用程式的 **Hello World** 訊息顯示在網頁中。

![localhost-hello-world-in-browser](media/app-service-web-get-started-php/localhost-hello-world-in-browser.png)

在終端機視窗中，按 **Ctrl+C** 結束 web 伺服器。

## <a name="log-in-to-azure"></a>登入 Azure

我們現在會在終端機視窗中使用 Azure CLI 2.0 ，以建立在 Azure 中裝載 PHP 應用程式所需的資源。 使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli
az login
```

<!-- ## Configure a Deployment User -->
[!INCLUDE [login-to-azure](../../includes/configure-deployment-user.md)]

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源 (如 Web 應用程式、資料庫和儲存體帳戶) 的邏輯容器。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service-plan"></a>建立 Azure App Service 方案

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令來建立「免費」[App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

<!--
 An App Service plan represents the collection of physical resources used to ..
-->
[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

下列範例會使用**免費**定價層，建立名為 `quickStartPlan` 的 App Service 方案。

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

建立 App Service 方案後，Azure CLI 會顯示類似下列範例的資訊。

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "location": "West Europe",
    "sku": {
    "capacity": 1,
    "family": "S",
    "name": "S1",
    "tier": "Standard"
    },
    "status": "Ready",
    "type": "Microsoft.Web/serverfarms"
}
```

## <a name="create-a-web-app"></a>建立 Web 應用程式

現已建立 App Service 方案，請在 `quickStartPlan` App Service 方案中建立 [Web 應用程式](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview)。 Web 應用程式會提供裝載空間來部署我們的程式碼，以及提供 URL 讓我們檢視已部署的應用程式。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令來建立 Web 應用程式。

在下列命令中，請將 `<app_name>` 預留位置替換成您自己的唯一應用程式名稱。 `<app_name>` 使用於 Web 應用程式的預設 DNS 網站。 如果 `<app_name>` 不是唯一的，您會收到易懂的錯誤訊息「具有指定名稱 <app_name> 的網站已經存在」。

<!-- removed per https://github.com/Microsoft/azure-docs-pr/issues/11878
You can later map any custom DNS entry to the web app before you expose it to your users.
-->

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的資訊。

```json
{
    "clientAffinityEnabled": true,
    "defaultHostName": "<app_name>.azurewebsites.net",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
    "isDefaultContainer": null,
    "kind": "app",
    "location": "West Europe",
    "name": "<app_name>",
    "repositorySiteName": "<app_name>",
    "reserved": true,
    "resourceGroup": "myResourceGroup",
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "state": "Running",
    "type": "Microsoft.Web/sites",
}
```

瀏覽至網站以查看您剛建立的 Web 應用程式。

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-php/app-service-web-service-created.png)

我們現已在 Azure 中建立空的新 Web 應用程式。

## <a name="configure-local-git-deployment"></a>設定本機 git 部署

您可以用各種方式部署至 Web 應用程式，包括 FTP、本機 Git 以及 GitHub、Visual Studio Team Services 和 Bitbucket。

使用 [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 命令來設定 Web 應用程式的本機 git 存取。

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

複製終端機的輸出，因為下一個步驟中會使用此資訊。

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

將 Azure 遠端新增至本機 Git 存放庫。

```bash
git remote add azure <paste-previous-command-output-here>
```

推送到 Azure 遠端來部署您的應用程式。 當您建立部署使用者時，系統會提示您輸入稍早提供的密碼。 務必您輸入在[設定部署使用者](#configure-a-deployment-user)中建立的密碼，不是您用來登入 Azure 入口網站的密碼。

```bash
git push azure master
```

在部署期間，Azure App Service 將與 Git 溝通其進度。

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>瀏覽至應用程式

使用 web 瀏覽器瀏覽至已部署的應用程式。

```bash
http://<app_name>.azurewebsites.net
```

這次，顯示 Hello World 訊息的頁面會使用 PHP 程式碼當作 Azure App Service Web 應用程式執行。



## <a name="updating-and-deploying-the-code"></a>更新和部署程式碼

使用本機文字編輯器，開啟 PHP 應用程式內的 `index.php` 檔案，並且對 `echo` 旁邊字串內的文字進行小幅變更：

```php
echo "Hello Azure!";
```

在 git 中認可您的變更，然後將程式碼變更推送至 Azure。

```bash
git commit -am "updated output"
git push azure master
```

部署完成後，切換回在「瀏覽至應用程式」步驟中開啟的瀏覽器視窗，然後按 [重新整理]。

![hello-world-in-browser](media/app-service-web-get-started-php/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>管理新的 Azure Web 應用程式

移至 Azure 入口網站，查看您剛建立的 Web 應用程式。

若要這麼做，請登入 [https://portal.azure.com](https://portal.azure.com)。

按一下左側功能表中的 [應用程式服務]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/app-service-web-get-started-php/php-docs-hello-world-app-service-list.png)

您已進入 Web 應用程式的_刀鋒視窗_ (水平開啟的入口網站頁面)。

根據預設，Web 應用程式的刀鋒視窗會顯示 [概觀] 頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 刀鋒視窗左側的索引標籤會顯示您可開啟的各種設定頁面。

![Azure 入口網站中的 App Service 刀鋒視窗](media/app-service-web-get-started-php/php-docs-hello-world-app-service-detail.png)

刀鋒視窗中的索引標籤會顯示您可以新增至 Web 應用程式的許多強大功能。 下表提供幾個可能性︰

* 對應自訂 DNS 名稱
* 繫結自訂 SSL 憑證
* 設定連續部署
* 相應增加和相應放大
* 新增使用者驗證

**恭喜！** 您已將第一個 PHP 應用程式部署至 App Service。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

> [!div class="nextstepaction"]
> [探索範例 Web Apps CLI 指令碼](app-service-cli-samples.md)


