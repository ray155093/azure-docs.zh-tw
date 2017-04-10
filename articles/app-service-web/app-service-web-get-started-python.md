---
title: "在 5 分鐘內將您的第一個 Python Web 應用程式建立在 Azure 中 | Microsoft Docs"
description: "短短幾分鐘內在 App Service Web 應用程式中部署第一個 Python Hello World。"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: f60e1188d1eb8baf8c6d5e77e2ff91a449351e1e
ms.lasthandoff: 04/04/2017


---
# <a name="create-a-python-application-on-web-app"></a>在 Web 應用程式上建立 Python 應用程式

本快速入門教學課程會逐步解說如何開發 Python 應用程式及部署至 Azure 。 我們將使用 Linux 型 Azure App Service 來執行應用程式，以及使用 Azure CLI 在其中建立和設定新的 Web 應用程式。 我們接著會使用 git 將 Python 應用程式部署至 Azure。

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

您可以使用 Mac、Windows 或 Linux 電腦，依照下面步驟操作。 完成所有步驟只需要大約 5 分鐘的時間。

## <a name="before-you-begin"></a>開始之前

執行此範例之前，請在本機安裝下列必要條件︰

1. [下載並安裝 git](https://git-scm.com/)
1. [下載並安裝 Python](https://Python.net)
1. 下載並安裝 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="download-the-sample"></a>下載範例

將 Hello World 範例應用程式存放庫複製到本機電腦。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

> [!TIP]
> 或者，您也可以[下載範例](https://github.com/Azure-Samples/Python-docs-hello-world/archive/master.zip)成為 .zip 檔案並將它解壓縮。

變更為包含範例程式碼的目錄。

```bash
cd Python-docs-hello-world
```

## <a name="run-the-app-locally"></a>在本機執行應用程式

在本機執行應用程式，做法是開啟終端機視窗並使用範例的 `Python` 命令列，以啟動內建 Python Web 伺服器。

```bash
Python -S localhost:8080
```

開啟網頁瀏覽器，然後瀏覽至範例。

```bash
http://localhost:8080
```

您可以看到來自範例應用程式的 **Hello World** 訊息顯示在網頁中。

![localhost-hello-world-in-browser](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

在終端機視窗中，按 **Ctrl+C** 結束 web 伺服器。

## <a name="log-in-to-azure"></a>登入 Azure

我們現在會在終端機視窗中使用 Azure CLI 2.0 ，以建立在 Azure 中裝載 Python 應用程式所需的資源。 使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>設定部署使用者

對於 FTP 和本機 Git，必須在伺服器上設定部署使用者才能驗證您的部署。 建立部署使用者是一次性設定，請記下使用者名稱和密碼，因為下面步驟將使用這些資訊。

> [!NOTE]
> 需要部署使用者，才能將 FTP 和本機 Git 部署至 Web 應用程式。
> `username` 和 `password` 屬於帳戶層級，因此會與 Azure 訂用帳戶認證不同。 **這些認證只需要建立一次**。
>

使用 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令來建立帳戶層級的認證。

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源 (如 Web 應用程式、資料庫和儲存體帳戶) 的邏輯容器。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>建立 Azure App Service

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令來建立 Linux 型 App Service 方案。

> [!NOTE]
> App Service 方案代表用來裝載應用程式的實體資源集合。 所有指派給 App Service 方案的所有應用程式都會共用它所定義的資源，而讓您節省裝載多個應用程式時的成本。
>
> App Service 方案可定義：
> * 區域 (北歐、美國東部、東南亞)
> * 執行個體大小 (小型、中型、大型)
> * 級別計數 (一、二或三個執行個體等)
> * SKU (免費、共用、基本、標準、進階)
>

下列範例會使用**標準**定價層，在名為 `quickStartPlan` 的 Linux 背景工作上建立 App Service 方案。

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku S1 --is-linux
```

建立 App Service 方案後，Azure CLI 會顯示類似下列範例的資訊。

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "kind": "linux",
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

現已建立 App Service 方案，請在 `quickStartPlan` App Service 方案中建立 Web 應用程式。 Web 應用程式會提供裝載空間來部署我們的程式碼，以及提供 URL 讓我們檢視已部署的應用程式。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令來建立 Web 應用程式。

在下列命令中，請將 <app_name> 預留位置替換成您自己的唯一應用程式名稱。 <app_name> 將做為 Web 應用程式的預設 DNS 網站，所以此名稱在 Azure 的所有應用程式中必須是唯一的名稱。 您稍後先將任何自訂 DNS 項目對應至 Web 應用程式，再將它公開給使用者。

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

![app-service-web-service-created](media/app-service-web-get-started-python/app-service-web-service-created.png)

我們現已在 Azure 中建立空的新 Web 應用程式。 現在設定 Web 應用程式以使用 Python 並將應用程式部署到它。

## <a name="configure-to-use-python"></a>設定成使用 Python

使用 [az appservice web config update](/cli/azure/app-service/web/config#update) 命令來設定 Web 應用程式以使用 Python 版本 `7.0.x`。

> [!TIP]
> 以這種方式設定 Python 版本，可使用平台所提供的預設容器，如果您想要使用自己的容器，請參照 CLI 參考中的 [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) 命令。

```azurecli
az appservice web config update --name <app_name> --resource-group myResourceGroup
```

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

推送到 Azure 遠端來部署您的應用程式。 建立部署使用者時，系統會提示您輸入稍早提供的密碼。

```azurecli
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
remote: Copying file: 'main.py'
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

這次，顯示 Hello World 訊息的頁面會使用 Python 程式碼當作 Azure App Service Web 應用程式執行。



## <a name="updating-and-deploying-the-code"></a>更新和部署程式碼

使用本機文字編輯器，開啟 Python 應用程式內的 `main.py` 檔案，並且對 `echo` 旁邊字串內的文字進行小幅變更：

```python
echo "Hello Azure!";
```

在 git 中認可您的變更，然後將程式碼變更推送至 Azure。

```bash
git commit -am "updated output"
git push azure master
```

部署完成後，切換回在「瀏覽至應用程式」步驟中開啟的瀏覽器視窗，然後按 [重新整理]。

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>管理新的 Azure Web 應用程式

移至 Azure 入口網站，查看您剛建立的 Web 應用程式。

若要這麼做，請登入 [https://portal.azure.com](https://portal.azure.com)。

按一下左側功能表中的 [App Service]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/app-service-web-get-started-python/Python-docs-hello-world-app-service-list.png)

您已進入 Web 應用程式的_刀鋒視窗_ (水平開啟的入口網站頁面)。

根據預設，Web 應用程式的刀鋒視窗會顯示 [概觀] 頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 刀鋒視窗左側的索引標籤會顯示您可開啟的各種設定頁面。

![Azure 入口網站中的 App Service 刀鋒視窗](media/app-service-web-get-started-python/Python-docs-hello-world-app-service-detail.png)

刀鋒視窗中的索引標籤會顯示您可以新增至 Web 應用程式的許多強大功能。 下表提供幾個可能性︰

* 對應自訂 DNS 名稱
* 繫結自訂 SSL 憑證
* 設定連續部署
* 相應增加和相應放大
* 新增使用者驗證

**恭喜！** 您已將第一個 Python 應用程式部署至 App Service。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

瀏覽預先建立的 [Web Apps CLI 指令碼](app-service-cli-samples.md)。
