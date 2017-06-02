---
title: "在 Azure 中建置 Docker Python 和 PostgreSQL Web 應用程式 | Microsoft Docs"
description: "了解如何取得在 Azure 中運作的 Docker Python 應用程式，並連線至 PostgreSQL 資料庫。"
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/03/2017
ms.author: beverst
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: ca086f76e50cb27f012bb2e7f05595be5fdcb241
ms.contentlocale: zh-tw
ms.lasthandoff: 06/01/2017

---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>在 Azure 中建置 Docker Python 和 PostgreSQL Web 應用程式
本教學課程會示範如何在 Azure 中建立基本的 Docker Python Web 應用程式。 您也會將此應用程式連線至 PostgreSQL 資料庫。 完成之後，您在 [Azure App Service Web Apps](app-service-web-overview.md) 上就會有 Docker 容器內執行的 Python Flask 應用程式。

![Azure App Service 中的 Docker Python Flask 應用程式](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="before-you-begin"></a>開始之前

執行此範例之前，請在本機安裝下列必要條件︰

1. [下載並安裝 git](https://git-scm.com/)
1. [下載並安裝 Python](https://www.python.org/downloads/)
1. [下載、安裝及執行 PostgreSQL](https://www.postgresql.org/download/)
1. [下載及安裝 Docker Community 版本](https://www.docker.com/community-edition)
1. [下載並安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-postgresql-installation-and-create-a-database"></a>測試本機 PostgreSQL 安裝並建立資料庫
在此步驟中，您要確定您的本機 PostgreSQL 資料庫正在執行。

開啟終端機視窗，然後執行 `psql postgres` 來連線至本機 PostgreSQL 伺服器。

```bash
psql postgres
```

如果連線成功，則您的 PostgreSQL 資料庫就已在執行中。 如果沒有，請確定您的本機 PostgresQL 資料庫已遵循[下載、安裝及執行 PostgresQL](https://www.postgresql.org/download/) 中的步驟來啟動。

建立名為 `eventregistration` 的資料庫，並使用 `supersecretpass` 密碼來設定名為 `manager` 的個別資料庫使用者。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
輸入 `\q` 來結束 PostgreSQL 用戶端。 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>建立本機 Python Flask 應用程式
在此步驟中，您要設定本機 Python Flask 專案。

### <a name="clone-the-sample-application"></a>複製範例應用程式

開啟終端機視窗，然後 `CD` 至工作目錄。  

執行下列命令來複製範例存放庫，然後前往 `0.1-initialapp`版本。

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.gi
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

此範例存放庫包含 [Flask](http://http://flask.pocoo.org/) 應用程式。 

### <a name="run-the-application"></a>執行應用程式

> [!NOTE] 
> 我們將在稍後步驟中簡化這個程序，方法是建立可與生產環境資料庫搭配使用的 Docker 容器。

安裝必要的封裝，然後啟動應用程式。

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

當應用程式完全載入時，您應會看到類似下列的訊息：

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在瀏覽器中，瀏覽至 `http://127.0.0.1:5000`。 按一下 [註冊!] 然後嘗試建立空的註冊。

![在本機執行的 Python Flask 應用程式](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

Flask 範例應用程式會將使用者資料儲存於資料庫中。 如果成功且能夠在應用程式中檢視註冊，您的應用程式就會將資料寫入本機 PostgreSQL 資料庫。

如需隨時停止 Flask 伺服器，請在終端機上輸入 `Ctrl`+`C`。 

## <a name="create-a-production-postgresql-database"></a>建立生產環境 PostgreSQL 資料庫

在此步驟中，您要在 Azure 中建立 PostgreSQL 資料庫。 將您的應用程式部署至 Azure 時，我們會針對其生產環境工作負載來指定此資料庫。

### <a name="log-in-to-azure"></a>登入 Azure

您即將在終端機視窗中使用 Azure CLI 2.0，來建立在 Azure App Service 中裝載 Python 應用程式所需的資源。  使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](../azure-resource-manager/resource-group-overview.md) 來建立[資源群組](/cli/azure/group#create)。 Azure 資源群組是一個邏輯容器，可在其中部署與管理 Azure 資源 (例如 Web 應用程式、資料庫和儲存體帳戶)。 

下列範例會在美國西部區域中建立一個資源群組：

```azurecli
az group create --name myResourceGroup --location "West US"
```

若要查看您可用於 `--location` 的可能值，請使用 `az appservice list-locations` Azure CLI 命令。

### <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器

使用 [az postgres server create](/cli/azure/documentdb#create) 命令來建立 PostgreSQL 伺服器。

在下列命令中，在您看見 `<postgresql_name>` 預留位置的地方，替代成您自己的唯一 PostgreSQL 伺服器名稱。 這個唯一名稱會用來作為 PostgreSQL 端點 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，所以在 Azure 的所有伺服器中必須是唯一的名稱。 

```azurecli
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <my_admin_username>
```

需要 `--admin-user` 才能建立初始資料庫管理使用者帳戶。 系統會提示您選取此使用者的密碼。

建立適用於 PostgreSQL 的 Azure 資料庫伺服器後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="creating-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則

我們現在必須先讓所有 IP 位址可連線至資料庫，才能存取資料庫。 這可透過下列 Azure CLI 命令來完成：

```azurecli
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

建立防火牆後，Azure CLI 會確認規則存在，如下所示︰

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>將 Python Flask 應用程式連線至資料庫

在此步驟中，您要將 Python Flask 範例應用程式連線至適用於您所建立之適用於 PostgreSQL 的 Azure 資料庫伺服器。

### <a name="creating-an-empty-database-and-setting-up-a-new-database-application-user"></a>建立空的資料庫並設定新的資料庫應用程式使用者

我們會建立新的資料庫使用者，並僅提供單一資料庫的存取權。 這個步驟可避免我們的應用程式透過管理員認證提供伺服器的完整存取權。

連線至資料庫 (系統會提示您輸入管理員密碼)。
```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

然後從 PostgreSQL CLI 建立資料庫和使用者。
```
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

輸入 `\q` 來結束 PostgreSQL 用戶端。

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>針對 Azure PostgreSQL 資料庫本機測試應用程式 

現在回到複製的 Github 存放庫 `app` 資料夾，我們只要更新資料庫環境變數，就可以執行 Python Flask 應用程式。

```bash
FLASK_APP=app.py;DBHOST="<postgresql_name>.postgres.database.azure.com";DBUSER="manager@<postgresql_name>";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

當應用程式完全載入時，您應會再次看到類似下列的訊息：

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在瀏覽器中，瀏覽至 `http://127.0.0.1:5000`。 按一下 [註冊!] 然後嘗試建立空的註冊。 現在您要將資料寫入 Azure 中的生產環境資料庫。

![在本機執行的 Python Flask 應用程式](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>從 Docker 容器執行應用程式

我們現在要建置 Docker 容器映像，並在本機從 Docker 容器內執行應用程式，同時仍連線至 Azure 中的 PostgreSQL 生產環境資料庫。

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker 會顯示已成功建立容器的確認。

```
Successfully built 7548f983a36b
```

讓我們將資料庫環境變數新增至環境變數檔案 `db.env`。

```
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

我們現在要從 Docker 容器內執行應用程式。 我們會指定環境變數檔案，並將預設 Flask 連接埠 5000 對應至我們的本機連接埠 5000。

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

不意外，輸出與之前類似。 不過，不再需要執行初始資料庫移轉，因此會予以略過。
```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 ```

 資料庫已包含我們先前建立的註冊。

 ![以 Docker 容器為基礎在本機執行的 Python Flask 應用程式](./media/app-service-web-tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>將 Docker 容器上傳至容器登錄
在此步驟中，您會將我們所建立的 Docker 容器上傳至容器登錄。 不過，我們將使用 Azure Container Registry，您也可以使用 Docker 中樞等其他熱門的項目。

### <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry

在下列命令中，建立容器登錄，將 `<registry_name>` 取代為您選擇的唯一 Azure Container Registry 名稱。

```azurecli
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

輸出
```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>擷取用來推送及提取 Docker 映像的登錄認證

我們必須先啟用管理員模式後，才可以存取認證。

```azurecli
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

您會看到兩個密碼。 請記下使用者名稱和接下來的第一個密碼。
```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>將 Docker 容器上傳至 Azure Container Registry

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>將 Docker Python Flask 應用程式部署至 Azure
在此步驟中，您可以將以 Docker 容器為基礎的 Python Flask 應用程式部署至 Azure App Service。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令來建立 App Service 方案。 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

下列範例會使用 S1 定價層，建立名為 `myAppServicePlan` 之以 Linux 為基礎的 App Service 方案：

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

建立 App Service 方案後，Azure CLI 會顯示類似下列範例的資訊：

```json 
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
``` 

### <a name="create-a-web-app"></a>建立 Web 應用程式

現已建立 App Service 方案，請在 `myAppServicePlan` App Service 方案中建立 Web 應用程式。 Web 應用程式會為您提供裝載空間來部署程式碼，以及提供 URL 讓您能夠檢視已部署的應用程式。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令來建立 Web 應用程式。 

在下列命令中，使用您自己唯一的應用程式名稱來替代 `<app_name>` 預留位置。 這個唯一名稱將用來做為 Web 應用程式預設網域名稱的一部分，因此，這個名稱在 Azure 的所有應用程式中必須是唯一的。 您稍後先將任何自訂 DNS 項目對應至 Web 應用程式，再將它公開給使用者。 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的資訊： 

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
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-the-database-environment-variables"></a>設定資料庫環境變數

稍早在本教學課程中，您手動定義了環境變數來連線至 PostgreSQL 資料庫。

在 App Service 中，您可以使用 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 命令，將環境變數設定為「應用程式設定」。 

以下可讓您指定資料庫連線詳細資料作為應用程式設定。 此外，我們會使用 `PORT` 變數，指定我們想要從我們的 Docker 容器對應連接埠 5000，以接收連接埠 80 的 HTTP 流量。

```azurecli
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>設定 Docker 容器部署 

AppService 會自動下載及執行 Docker 容器。

使用 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令來建立帳戶層級的認證。 

```azurecli
az appservice web config container update --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

每當我們更新 Docker 容器或變更上述設定時，需重新啟動應用程式，以確保將所有設定套用，且從登錄將最新的容器進行提取。

```azurecli
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式 
使用 Web 瀏覽器，瀏覽至已部署的 Web 應用程式。 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> 您對容器設定進行變更起，第一次存取 web 應用程式時，請允許額外的時間以供下載及啟動容器。

您會看到先前已註冊的來賓，儲存至上一個步驟中的 Azure 生產環境資料庫。

 ![以 Docker 容器為基礎在本機執行的 Python Flask 應用程式](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**恭喜！** 您要在 Azure App Service 中執行以 Docker 容器為基礎的 Python Flask 應用程式。


## <a name="update-data-model-and-redeploy"></a>更新資料模型並重新部署

在此步驟中，我們會更新來賓模式，將出席者的人數新增至每個事件註冊。

請使用下列 git 命令查看 `0.2-migration`版本。
```bash
git checkout tags/0.2-migration
```

此版本已對檢視、控制器及我們的模型進行必要變更。 它也會包含透過 alembic (`flask db migrate`) 產生的資料庫移轉。 您可以看到透過下列 git 命令所進行的所有變更。

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>本機測試您的變更

透過執行 Flask 伺服器，可執行下列命令在本機測試您的變更。

Mac / Linux：
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

在瀏覽器中瀏覽至 `http://127.0.0.1:5000` 可檢視變更。 建立新的虛擬註冊。

![以 Docker 容器為基礎在本機執行的 Python Flask 應用程式](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

建立新的 Docker 映像、將其推送至容器登錄，並重新啟動應用程式。

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

瀏覽至 Azure Web 應用程式，然後再次嘗試執行新功能。 建立另一個事件註冊。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service 中的 Docker Python Flask 應用程式](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>管理您的 Azure Web 應用程式

請移至 Azure 入口網站，以查看您所建立的 Web 應用程式。

若要這麼做，請登入 [https://portal.azure.com](https://portal.azure.com)。

按一下左側功能表中的 [App Service]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/app-service-web-tutorial-docker-python-postgresql-app/app-resource.png)

您已進入 Web 應用程式的_刀鋒視窗_ (水平開啟的入口網站頁面)。

根據預設，Web 應用程式的刀鋒視窗會顯示 [概觀] 頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 刀鋒視窗左側的索引標籤會顯示您可開啟的各種設定頁面。

![Azure 入口網站中的 App Service 刀鋒視窗](./media/app-service-web-tutorial-docker-python-postgresql-app/app-mgmt.png)

刀鋒視窗中的索引標籤會顯示您可以新增至 Web 應用程式的許多強大功能。 下表提供幾個可能性︰

* 對應自訂 DNS 名稱
* 繫結自訂 SSL 憑證
* 設定連續部署
* 相應增加和相應放大
* 新增使用者驗證


