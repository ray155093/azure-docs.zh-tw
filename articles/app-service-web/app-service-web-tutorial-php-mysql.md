---
title: "在 Azure 中建置 PHP 和 MySQL Web 應用程式 | Microsoft Docs"
description: "了解如何取得在 Azure 中運作的 PHP 應用程式，並連線至 Azure 中的 MySQL 資料庫。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3ad716fab4f5084c38c83f4bc90a616856666b38
ms.contentlocale: zh-tw
ms.lasthandoff: 06/01/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>在 Azure 中建置 PHP 和 MySQL Web 應用程式
本教學課程示範如何在 Azure 中建立 PHP Web 應用程式，並將它連線到 MySQL 資料庫。 當您完成時，將擁有在 [Azure App Service Web Apps](app-service-web-overview.md) 上執行的 [Laravel](https://laravel.com/) 應用程式。

![在 Azure App Service 中執行的 PHP 應用程式](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 MySQL 資料庫
> * 將 PHP 應用程式連線至 MySQL
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 來自 Azure 的串流診斷記錄
> * 在 Azure 入口網站中管理應用程式

## <a name="prerequisites"></a>必要條件

執行此範例之前，請在本機安裝下列必要條件︰

1. [下載並安裝 git](https://git-scm.com/)
1. [下載並安裝 PHP 5.6.4 或更新版本](http://php.net/downloads.php)
1. [下載並安裝 Composer](https://getcomposer.org/doc/00-intro.md)
1. 啟用下列 PHP 擴充功能 Laravel 需求︰OpenSSL、PDO-MySQL、Mbstring、Tokenizer、XML
1. [下載、安裝並啟動 MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [下載並安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>準備本機 MySQL

在此步驟中，您可以在本機 MySQL 伺服器中建立資料庫，供您在本教學課程中使用。

### <a name="connect-to-mysql-server"></a>連線至 MySQL 伺服器
在終端機視窗中，連線到您的本機 MySQL 伺服器。

```bash
mysql -u root -p
```

如果系統提示您輸入密碼，請輸入 `root` 帳戶的密碼。 如果您不記得根帳戶密碼，請參閱 [MySQL︰如何重設根密碼](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)。

如果您的命令執行成功，表示您的 MySQL 伺服器已經在執行中。 如果沒有，請遵循 [MySQL 後續安裝步驟](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)，確定您已啟動本機 MySQL 伺服器。

### <a name="create-a-database"></a>建立資料庫

在 `mysql` 提示中，建立資料庫。

```sql
CREATE DATABASE sampledb;
```

輸入 `quit` 即可結束您的伺服器連線。

```sql
quit
```

<a name="step2"></a>

## <a name="create-local-php-app"></a>建立本機 PHP 應用程式
在此步驟中，您會取得 Laravel 範例應用程式、設定其資料庫連線，並在本機執行。 

### <a name="clone-the-sample"></a>複製範例

開啟終端機視窗，然後 `cd` 至工作目錄。  

執行下列命令來複製範例存放庫。 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` 至您複製的目錄，並安裝必要的套件。

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>設定 MySQL 連線

在存放庫根目錄中，建立 _.env_ 檔案，並將下列變數複製到其中。 將 _&lt;root_password>_ 預留位置取代為根使用者的密碼。

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

> [!NOTE]
> 如需有關 Laravel 如何使用此 _.env_ 檔案的資訊，請參閱 [Laravel 環境設定](https://laravel.com/docs/5.4/configuration#environment-configuration)。
>
>

### <a name="run-the-sample"></a>執行範例

執行 [Laravel 資料庫移轉](https://laravel.com/docs/5.4/migrations)來建立您應用程式所需的資料表。 若要查看移轉中會建立哪些資料表，請參閱 Git 存放庫中的 _database/migrations_ 目錄。

```bash
php artisan migrate
```

產生新的 Laravel 應用程式金鑰。

```bash
php artisan key:generate 
```

執行應用程式。

```bash
php artisan serve
```

在瀏覽器中，瀏覽至 `http://localhost:8000`。 在頁面中新增幾項工作。

![PHP 成功連線至 MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

若要隨時停止 PHP，請在終端機中輸入 `Ctrl`+`C`。 

## <a name="create-production-mysql-in-azure"></a>在 Azure 中建立生產環境 MySQL

在此步驟中，您會在[適用於 MySQL 的 Azure 資料庫 (預覽)](/azure/mysql) 中建立 MySQL 資料庫。 稍後，您要將 PHP 應用程式設定為連線至此資料庫。

### <a name="log-in-to-azure"></a>登入 Azure

您將要在終端機視窗中使用 Azure CLI 2.0，來建立在 Azure App Service 中裝載 PHP 應用程式所需的資源。 使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立[資源群組](../azure-resource-manager/resource-group-overview.md)。 Azure 資源群組是一個邏輯容器，可在其中部署與管理 Azure 資源 (例如 Web 應用程式、資料庫和儲存體帳戶)。 

下列範例會在北歐區域中建立一個資源群組：

```azurecli
az group create --name myResourceGroup --location "North Europe"
```

若要查看可用於 `--location` 的可能值，請使用 [az appservice list-locations](/cli/azure/appservice#list-locations) 命令。

### <a name="create-a-mysql-server"></a>建立 MySQL 伺服器

使用 [az mysql server create](/cli/azure/mysql/server#create) 命令，在適用於 MySQL 的 Azure 資料庫 (預覽) 中建立伺服器。

在下列命令中，在您看見 _&lt;mysql_server_name>_ 預留位置的地方，替代成您自己的唯一 MySQL 伺服器名稱。 這個名稱是 MySQL 伺服器主機名稱 `<mysql_server_name>.database.windows.net` 的一部分，因此它必須是全域唯一的。 另外，請將 _&lt;admin_user>_ 和 _&lt;admin_password>_ 替代成您自己的值。

```azurecli
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --user <admin_user> \
    --password <admin_password>
```

建立 MySQL 伺服器後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "administratorLogin": "<admin_user>",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>設定伺服器防火牆

使用 [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) 命令，建立 MySQL 伺服器的防火牆規則來允許用戶端連線。 

```azurecli
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> 適用於 MySQL 的 Azure 資料庫 (預覽) 尚未啟用僅來自 Azure 服務的連線。 隨著您將 Azure 中的 IP 位址進行動態指派，目前最好是啟用所有的 IP 位址。 因為服務為預覽中，很快就會啟用更好的方法，來保護您的資料庫安全。
>
>

### <a name="connect-to-production-mysql-server"></a>連線到生產環境 MySQL 伺服器

在終端機視窗中，連線至 Azure 中的 MySQL 伺服器。 使用您先前針對 _&lt;admin_user>_ 和 _&lt;mysql_server_name>_ 指定的值。

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>建立生產環境資料庫

在 `mysql` 提示中，建立資料庫。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>建立具有權限的使用者

建立資料庫使用者，並將 `sampledb` 資料庫中所有的權限授權給它。 將 _&lt;phpapp_user>_ 和 _&lt;phpapp_password>_ 預留位置取代為您自己的唯一應用程式名稱。

```sql
CREATE USER '<phpapp_user>' IDENTIFIED BY '<phpapp_password>'; 
GRANT ALL PRIVILEGES ON sampledb.* TO '<phpapp_user>';
```

輸入 `quit` 即可結束您的伺服器連線。

```sql
quit
```

## <a name="connect-app-to-production-mysql"></a>將應用程式連線至生產環境 MySQL

在此步驟中，您要將 PHP 應用程式連線至您剛才在適用於 MySQL 的 Azure 資料庫 (預覽) 中建立的 MySQL 資料庫。 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>設定連線 

在存放庫根目錄中，建立 _.env.production_ 檔案，並將下列變數複製到其中。 取代 _&lt;mysql_server_name>_、_&lt;phpapp_user>_ 和 _&lt;phpapp_password>_ 預留位置。

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=<phpapp_user>@<mysql_server_name>
DB_PASSWORD=<phpapp_password>
```

儲存您的變更。

### <a name="test-the-application"></a>測試應用程式

使用 _.env.production_ 作為環境檔案來執行 Laravel 資料庫移轉，可在適用於 MySQL 的 Azure 資料庫 (預覽) 中建立資料表。

```bash
php artisan migrate --env=production --force
```

_.env.production_ 沒有有效的應用程式金鑰。 在終端機中為它產生一個新的。 

```bash
php artisan key:generate --env=production --force
```

使用 _.env.production_ 作為環境檔案來執行範例應用程式。

```bash
php artisan serve --env=production
```

在瀏覽器中，瀏覽至 `http://localhost:8000`。 如果頁面載入無誤，您的 PHP 應用程式就會連線至 Azure 中的 MySQL 資料庫。 

在頁面中新增幾項工作。

![PHP 順利連線至適用於 MySQL 的 Azure 資料庫 (預覽)](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

若要隨時停止 PHP，請在終端機中輸入 `Ctrl`+`C`。 

### <a name="secure-sensitive-data"></a>保護機密資料

您必須確定 _.env.production_ 中的機密資料尚未認可至 Git。

若要這樣做，請從存放庫的根目錄開啟 _.gitignore_，並在新的一行中新增檔名：

```
.env.production
```

儲存變更，然後將變更認可至 Git。

```bash
git add .gitignore
git commit -m "keep sensitive data out of git"
```

稍後，您將了解如何設定 App Service 中的環境變數，來連線至適用於 MySQL 的 Azure 資料庫 (預覽)，如此您在 App Service 中就不需要任何 `.env` 檔案。 

## <a name="deploy-php-app-to-azure"></a>將 PHP 應用程式部署至 Azure
在此步驟中，您要將已與 MySQL 連線的 PHP 應用程式部署至 Azure App Service。

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
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
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

在下列命令中，將 _&lt;appname>_ 預留位置替代成您自己的唯一應用程式名稱。 這個唯一名稱會用來作為 Web 應用程式預設網域名稱的一部分，因此，這個名稱在 Azure 的所有應用程式中必須是唯一的。 您稍後先將任何自訂 DNS 項目對應至 Web 應用程式，再將它公開給使用者。 

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

### <a name="set-the-php-version"></a>設定 PHP 版本

使用 [az appservice web config update](/cli/azure/appservice/web/config#update) 命令來設定應用程式所需的 PHP 版本。

下列命令會將 PHP 版本設定為 _7.0_。

```azurecli
az appservice web config update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>設定資料庫設定

如先前所指出，您可以使用 App Service 中的環境變數，連線至 Azure MySQL 資料庫。

在 App Service 中，您可以使用 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 命令，將環境變數設定為「應用程式設定」。 

下列命令可讓您設定 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME` 和 `DB_PASSWORD` 應用程式設定。 取代 _&lt;appname>_、_&lt;mysql_server_name>_、_&lt;phpapp_user>_ 和 _&lt;phpapp_password>_ 預留位置。

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="<phpapp_user>@<mysql_server_name>" DB_PASSWORD="<phpapp_password>"
```

您可以使用 PHP [getenv()](http://www.php.net/manual/function.getenv.php) 方法來存取這些設定。 Laravel 程式碼會透過 PHP `getenv()` 使用 [env()](https://laravel.com/docs/5.4/helpers#method-env) 包裝函式。 例如，在 _config/database.php_ 中的 MySQL 設定看起來像這樣︰

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>設定 Laravel 環境變數

如同在本機電腦，Laravel 在 App Service 中也需要應用程式金鑰。 您也可以使用應用程式設定將它進行設定。

使用 `php artisan` 產生新的應用程式金鑰，而不將它儲存為 _.env_。

```bash
php artisan key:generate --show
```

使用 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 命令來設定 App Service Web 應用程式中的應用程式金鑰。 取代 _&lt;appname>_ 和 _&lt;outputofphpartisankey:generate>_ 預留位置。

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

> [!NOTE]
> 如果您已部署的 web 應用程式遇到錯誤，`APP_DEBUG="true"` 會告訴 Laravel 傳回偵錯資訊。 執行生產環境應用程式時，您應該將它改成設定為 `false` 來提升安全性。
>
>

### <a name="set-the-virtual-application-path"></a>設定虛擬應用程式路徑

設定 web 應用程式的虛擬應用程式路徑。 您只需要執行此步驟，因為 [Laravel 應用程式生命週期](https://laravel.com/docs/5.4/lifecycle)會在_公用_目錄，而不是應用程式的根目錄中啟動。 在根目錄中啟動生命週期的其他 PHP 架構，不需要手動設定虛擬應用程式路徑即可運作。

使用 [az resource update](/cli/azure/resource#update) 命令，設定虛擬應用程式路徑。 取代 _&lt;appname>_ 預留位置。

```bash
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

> [!NOTE]
> 依預設，Azure App Service 會將根虛擬應用程式路徑 (_/_) 指向您已部署應用程式檔案的根目錄 (_sites\wwwroot_)。 
>
>

### <a name="configure-a-deployment-user"></a>設定部署使用者

對於 FTP 和本機 Git，必須在伺服器上設定部署使用者，才能驗證您的部署。 

> [!NOTE] 
> 需要部署使用者，才能將 FTP 和本機 Git 部署至 Web 應用程式。 使用者名稱和密碼屬於帳戶等級，因此會與 Azure 訂用帳戶認證不同。

如果您先前已建立部署使用者名稱和密碼，可以使用下列命令來顯示使用者名稱︰

```azurecli
az appservice web deployment user show
```

如果您還沒有部署使用者，請執行 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令來建立部署認證。 

```azurecli
az appservice web deployment user set \
    --user-name <username> \
    --password <minimum-8-char-capital-lowercase-number>
```

使用者名稱必須是唯一的，而密碼必須是強式密碼。 如果您收到 `'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 `'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。

您只需要建立此部署使用者一次；您可以將它用於所有 Azure 部署。

請記錄使用者名稱和密碼，因為您稍後部署應用程式時會用到它們。

### <a name="configure-local-git-deployment"></a>設定本機 git 部署 

您可以使用各種方式來將應用程式部署至 Azure App Service，包括 FTP、本機 Git、GitHub、Visual Studio Team Services 和 BitBucket。 

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

推送至 Azure 遠端來部署您的 PHP 應用程式。 建立部署使用者時，系統會提示您輸入稍早提供的密碼。 

```bash
git push azure master
```

在部署期間，Azure App Service 會與 Git 溝通其進度。

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
``` 

> [!NOTE]
> 您可能會注意到，部署程序會在結尾安裝 [Composer](https://getcomposer.org/) 套件。 App Service 不會在預設部署期間執行這些自動化，因此，這個範例存放庫在其根目錄中有三個其他檔案可啟用它： 
>
> - `.deployment` - 此檔案會告訴 App Service，執行 `bash deploy.sh` 以做為自訂部署指令碼。
> - `deploy.sh` - 自訂部署指令碼。 如果您檢閱檔案，您將看到它會在 `npm install` 之後執行 `php composer.phar install`。 
> - `composer.phar` - Composer 套件管理員。
>
> 您可以使用這種方法，將您任何以 Git 為基礎的部署步驟新增至 App Service。 如需相關資訊，請參閱[自訂部署指令碼](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。
>
>

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式

瀏覽至 `http://<app_name>.azurewebsites.net` 並將幾項工作新增至清單。 

![在 Azure App Service 中執行的 PHP 應用程式](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

**恭喜！** 您正在 Azure App Service 中執行資料驅動的 PHP 應用程式。

## <a name="update-data-model-and-redeploy"></a>更新資料模型並重新部署

在此步驟中，您要對 `task` 資料模型進行一些變更，並將變更發佈至 Azure。

針對工作案例，您要修改應用程式，才可將工作標示為完成。 

### <a name="add-a-column"></a>新增資料行

在終端機中，請確定您是在 Git 存放庫的根目錄，然後再產生 `tasks` 資料表的新資料庫移轉。

```bash
php artisan make:migration add_complete_column --table=tasks
```

此命令會顯示所產生的移轉檔案名稱。 請在 _database/migrations_ 尋找此檔案，並在文字編輯器中將它開啟。

將 up() 方法取代為下列程式碼：

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

這個程式碼會在名為 `complete` 的 `tasks` 資料表中，新增布林值資料行。

將 down() 方法取代為下列程式碼以進行復原動作︰

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

在終端機中，在本機執行 Laravel 資料庫移轉，可在本機資料庫中進行變更。

```bash
php artisan migrate
```

根據 [Laravel 命名慣例](https://laravel.com/docs/5.4/eloquent#defining-models)，`Task` 模型依預設 (請參閱 _app/Task.php_) 會對應至 `tasks` 資料表，因此您就完成更新資料模型。

### <a name="update-application-logic"></a>更新應用程式邏輯

開啟 _routes/web.php_。 範例應用程式會在這裡定義其路由和商務邏輯。

在檔案的結尾，使用下列程式碼新增路由︰

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

此程式碼會切換 `complete` 的值，對資料模型進行簡單的更新。

### <a name="update-the-view"></a>更新檢視

開啟 _resources/views/tasks.blade.php_。 尋找 `<tr>` 開頭標記，並將它取代為︰

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

這會視工作是否已完成，將資料列色彩進行變更。

在下一行中，您會有下列程式碼︰

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

將這一整行取代為下列程式碼：

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

這個程式碼新增的 [提交] 按鈕，會參考您稍早定義的路由。

### <a name="test-your-changes-locally"></a>本機測試您的變更

從 Git 存放庫的根目錄，再次執行程式開發伺服器。

```bash
php artisan serve
```

在瀏覽器中，瀏覽至 `http://localhost:8000`然後按一下核取方塊可查看隨之變更的工作狀態。

![已將核取方塊新增至工作](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

在終端機中，使用生產環境連接字串來執行 Laravel 資料庫移轉，以在 Azure 的生產環境資料庫中進行變更。

```bash
php artisan migrate --env=production --force
```

在 Git 中認可您的變更，然後將程式碼變更推送至 Azure。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

完成 `git push` 之後，再次瀏覽至 Azure Web 應用程式，然後嘗試執行新功能。

![發佈至 Azure 的模型和資料庫變更](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

> [!NOTE]
> 如果您先前已新增任何工作，仍然可以看到它們。 您對資料結構描述進行的更新，會讓現有資料保留不變。
>
>

## <a name="stream-diagnostic-logs"></a>資料流診斷記錄 

當 PHP 應用程式在 Azure App Service 中執行時，您可以使用管線將主控台記錄直接傳送至終端機。 這樣一來，您就能取得相同的診斷訊息，以協助您偵錯應用程式錯誤。

若要開始記錄資料流，使用 [az appservice web log tail](/cli/azure/appservice/web/log#tail) 命令。

```azurecli 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

開始記錄資料流之後，重新整理瀏覽器中的 Azure Web 應用程式，以取得部分 Web 流量。 您現在應該會看到使用管線傳送到終端機的主控台記錄。

若要隨時停止記錄資料流，輸入 `Ctrl`+`C`。 

> [!TIP]
> PHP 應用程式可以使用標準 [error_log()](http://php.net/manual/function.error-log.php) 輸出到主控台。 範例應用程式會在 _app/Http/routes.php_ 中使用這種方法。
>
> 如同 web 架構，[Laravel 會使用 Monolog](https://laravel.com/docs/5.4/errors) 作為記錄提供者。 若要了解如何使 Monolog 將訊息輸出至主控台，請參閱 [PHP︰如何使用 Monolog 記錄至主控台 (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out)。
>
>

## <a name="manage-your-azure-web-app"></a>管理您的 Azure Web 應用程式

請移至 Azure 入口網站，以查看您所建立的 Web 應用程式。

若要這麼做，請登入 [https://portal.azure.com](https://portal.azure.com)。

按一下左側功能表中的 [App Service]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/app-service-web-tutorial-php-mysql/access-portal.png)

您已進入 Web 應用程式的_刀鋒視窗_ (水平開啟的入口網站頁面)。

根據預設，Web 應用程式的刀鋒視窗會顯示 [概觀] 頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 刀鋒視窗左側的索引標籤會顯示您可開啟的各種設定頁面。

![Azure 入口網站中的 App Service 刀鋒視窗](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

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
> * 在 Azure 中建立 MySQL 資料庫
> * 將 PHP 應用程式連線至 MySQL
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 來自 Azure 的串流診斷記錄
> * 在 Azure 入口網站中管理應用程式

前進至下一個教學課程，了解如何對它對應自訂 DNS 名稱。

> [!div class="nextstepaction"] 
> [將現有的自訂 DNS 名稱對應至 Azure Web Apps](app-service-web-tutorial-custom-domain.md)

