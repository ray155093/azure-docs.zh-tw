---
title: "在 Azure 中建置 Java 和 MySQL Web 應用程式 | Microsoft Docs"
description: "了解如何取得連線至在 Azure Appservice 中運作的 Azure MySQL 資料庫服務之 Java 應用程式。"
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/06/2017
ms.author: bbenz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fab7759154b38b8043a17c933f896d7af9514c85
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>在 Azure 中建置 Java 和 MySQL Web 應用程式
本教學課程示範如何在 Azure 中建立連線至 MySQL 資料庫的 Java Web 應用程式。 第一個步驟是將應用程式複製到本機電腦，並讓它可使用本機 MySQL 執行個體。
下一個步驟是設定適用於 Java 應用程式和 MySQL 的 Azure 服務，然後將應用程式部署至 Azure Appservice。
當您完成時，您會有一份待辦事項清單，說明要在 Azure 上執行並連線至 Azure MySQL 資料庫服務的應用程式。

![在 Azure Appservice 中執行的 Java 應用程式](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

## <a name="before-you-begin"></a>開始之前

執行此範例之前，請在本機安裝下列必要條件︰

1. [下載並安裝 git](https://git-scm.com/)
1. [下載並安裝 Java 7 或更新版本](http://Java.net/downloads.Java)
1. [下載並安裝 Maven](https://maven.apache.org/download.cgi)
1. [下載、安裝並啟動 MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [下載並安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql-database"></a>準備本機 MySQL 資料庫

在此步驟中，您可以在本機 MySQL 伺服器中建立資料庫，供您在本教學課程中使用。

### <a name="connect-to-mysql-server"></a>連線至 MySQL 伺服器
從命令列連線至您的本機 MySQL 伺服器︰

```bash
mysql -u root -p
```

如果您的命令執行成功，表示您的 MySQL 伺服器已經在執行中。 如果沒有，請遵循 [MySQL 後續安裝步驟](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)，確定您已啟動本機 MySQL 伺服器。

如果系統提示您輸入密碼，請輸入 `root` 帳戶的密碼。 如果您不記得根帳戶密碼，請參閱 [MySQL︰如何重設根密碼](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)。


### <a name="create-a-database-and-table"></a>建立資料庫和資料表

在 `mysql` 提示中，建立待辦事項的資料庫和資料表。

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

輸入 `quit` 即可結束您的伺服器連線。

```sql
quit
```

## <a name="create-local-java-application"></a>建立本機的 Java 應用程式
在此步驟中，您要複製 GitHub 存放庫、設定 MySQL 資料庫連線，並在本機執行應用程式。 

### <a name="clone-the-sample"></a>複製範例

從命令提示字元中，瀏覽到工作目錄。  

執行下列命令來複製範例存放庫。 

```bash
git clone https://github.com/bbenz/azure-mysql-java-todo-app
```

接下來，請遵循存放庫讀我檔案中的步驟來設定 lombok.jar。


### <a name="configure-mysql-connection"></a>設定 MySQL 連線

此應用程式會使用 Maven Jetty 外掛程式，在本機執行應用程式，並連線至 MySQL 資料庫。
若要啟用本機 MySQL 執行個體的存取權，請在 WebContent/WEB-INF/jetty-env.xml 中設定您的本機 MySQL 使用者識別碼和密碼。

使用本機 MySQL 執行個體的使用者識別碼和密碼，來更新使用者和密碼的值︰

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql://localhost:3306/itemdb</Set>
           <Set name="User">root</Set>
           <Set name="Password"></Set>
        </New>
     </Arg>
    </New>
</Configure>

```

&gt; [!NOTE]
&gt; 如需 Jetty 如何使用 `jetty-env.xml` 檔案的相關資訊，請參閱 [Jetty XML 參考](http://www.eclipse.org/jetty/documentation/9.4.x/jetty-env-xml.html)。
&gt; &gt;

### <a name="run-the-sample"></a>執行範例

使用 Maven 命令來執行範例︰ 

```bash
mvn package jetty:run
```

接下來，在瀏覽器中，瀏覽至 `http://localhost:8080`。 在頁面中新增幾項工作。

若要隨時停止應用程式，請在命令提示字元中輸入 `Ctrl`+`C`。 

## <a name="create-a-mysql-database-in-azure"></a>在 Azure 中建立 MySQL 資料庫

在此步驟中，您會在[適用於 MySQL 的 Azure 資料庫 (預覽)](/azure/mysql) 中建立 MySQL 資料庫。 稍後，您要將 Java 應用程式設定為連線至此資料庫。

### <a name="log-in-to-azure"></a>登入 Azure

在終端機視窗中使用 Azure CLI 2.0，來建立在 Azure Appservice 中裝載 Java 應用程式所需的資源。 使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立[資源群組](../azure-resource-manager/resource-group-overview.md)。 Azure 資源群組是一個邏輯容器，可在其中部署與管理 Azure 資源 (例如 Web 應用程式、資料庫和儲存體帳戶)。 

下列範例會在北歐區域中建立一個資源群組：

```azurecli
az group create --name myResourceGroup  --location "North Europe"
```

若要查看可用於 `--location` 的值，請使用 [az appservice list-locations](/cli/azure/appservice#list-locations) 命令。

### <a name="create-a-mysql-server"></a>建立 MySQL 伺服器

使用 [az mysql server create](/cli/azure/mysql/server#create) 命令，在適用於 MySQL 的 Azure 資料庫 (預覽) 中建立伺服器。

在您看見 `&lt;mysql_server_name&gt;` 預留位置的地方，替代成您自己的唯一 MySQL 伺服器名稱。 這個名稱是 MySQL 伺服器主機名稱 `&lt;mysql_server_name&gt;.database.windows.net` 的一部分，因此它必須是全域唯一的。 另外，請將 `&lt;admin_user&gt;` 和 `&lt;admin_password&gt;` 替代成您自己的值。

```azurecli
az mysql server create --name &lt;mysql_server_name&gt; --resource-group myResourceGroup --location "North Europe" --user &lt;admin_user&gt; --password &lt;admin_password&gt;
```

建立 MySQL 伺服器後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "administratorLogin": "&lt;admin_user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql_server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql_server_name&gt;",
  "location": "northeurope",
  "name": "&lt;mysql_server_name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-a-server-firewall"></a>設定伺服器防火牆

使用 [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) 命令，建立 MySQL 伺服器的防火牆規則來允許用戶端連線。 

```azurecli
az mysql server firewall-rule create --name allIPs --server &lt;mysql_server_name&gt; --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

&gt; [!NOTE]
&gt; 適用於 MySQL 的 Azure 資料庫 (預覽) 尚未啟用僅來自 Azure 服務的連線。 隨著您將 Azure 中的 IP 位址進行動態指派，目前最好是啟用所有的 IP 位址。 因為服務為預覽中，很快就會啟用更好的方法，來保護您的資料庫安全。
&gt; &gt;

### <a name="connect-to-the-mysql-server"></a>連線至 MySQL 伺服器

在終端機視窗中，連線至 Azure 中的 MySQL 伺服器。 針對 `&lt;admin_user&gt;` 和 `&lt;mysql_server_name&gt;`，使用您先前指定的值。

```bash
mysql -u &lt;admin_user&gt;@&lt;mysql_server_name&gt; -h &lt;mysql_server_name&gt;.database.windows.net -P 3306 -p
```

### <a name="create-a-database-and-table-in-the-azure-mysql-service"></a>在 Azure MySQL 服務中建立資料庫和資料表

在 `mysql` 提示中，建立待辦事項的資料庫和資料表。

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

### <a name="create-a-user-with-permissions"></a>建立具有權限的使用者

建立資料庫使用者，並將 `todoItemDb` 資料庫中所有的權限授權給它。 將 `&lt;Javaapp_user&gt;` 和 `&lt;Javaapp_password&gt;` 預留位置取代您自己的唯一應用程式名稱。

```sql
CREATE USER '&lt;Javaapp_user&gt;' IDENTIFIED BY '&lt;Javaapp_password&gt;'; 
GRANT ALL PRIVILEGES ON todoItemDb.* TO '&lt;Javaapp_user&gt;';
```

輸入 `quit` 即可結束您的伺服器連線。

```sql
quit
```

### <a name="configure-the-local-mysql-connection-with-the-new-azure-mysql-service"></a>使用新的 Azure MySQL 服務來設定本機 MySQL 連線

在此步驟中，您要將 Java 應用程式連線至您在適用於 MySQL 的 Azure 資料庫 (預覽) 中建立的 MySQL 資料庫。 

若要啟用從本機應用程式存取 Azure MySQL 服務，請在 WebContent/WEB-INF/jetty-env.xml 中設定您新的 MySQL 端點、使用者識別碼和密碼︰

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql:<paste the endpoint for the Azure MySQL Service>/itemdb</Set>
           <Set name="User">Azure MySQL userID</Set>
           <Set name="Password">Azure MySQL Password</Set>
        </New>
     </Arg>
    </New>
</Configure>

```

儲存您的變更。

## <a name="test-the-application"></a>測試應用程式

使用和之前相同的 Maven 命令，再次於本機執行範例，但這次要連線至 Azure MySQL 服務： 

```bash
mvn package jetty:run
```

在瀏覽器中，瀏覽至 `http://localhost:8080`。 如果頁面載入無誤，您的 Java 應用程式就會連線至 Azure 中的 MySQL 資料庫。 

您應該不需要在頁面中新增幾項工作。

若要隨時停止應用程式，請在終端機中輸入 `Ctrl`+`C`。 

### <a name="secure-sensitive-data"></a>保護機密資料

請確定 `WebContent/WEB-INF/jetty-env.xml` 中的機密資料尚未認可至 Git。

若要這樣做，請從存放庫的根目錄開啟 `.gitignore`，並在新的一行中新增 `WebContent/WEB-INF/jetty-env.xml`。 儲存您的變更。

將變更認可至 `.gitignore`。

```bash
git add .gitignore
git commit -m "keep sensitive data in WebContent/WEB-INF/jetty-env.xml out of git"
```

## <a name="deploy-the-java-application-to-azure"></a>將 Java 應用程式部署至 Azure
接下來，我們要將 Java 應用程式部署至 Azure Appservice。

### <a name="create-an-appservice-plan"></a>建立 Appservice 方案

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令來建立 Appservice 方案。 

&gt; [!NOTE] 
&gt; Appservice 方案代表用來裝載應用程式的實體資源集合。 所有指派給 Appservice 方案的應用程式都會共用它所定義的資源，從而讓您節省裝載多個應用程式時的成本。 
&gt; &gt; Appservice 方案定義︰&gt; &gt; * 區域 (北歐、美國東部、東南亞) &gt; * 執行個體大小 (小型、中型、大型) &gt; * 級別計數 (一、二或三個執行個體等) &gt; * SKU (免費、共用、基本、標準、進階) &gt; 

下列範例會使用**免費**定價層，建立名為 `myAppServicePlan` 的 Appservice 方案：

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

建立 Appservice 方案後，Azure CLI 會顯示類似下列範例的資訊：

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
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### <a name="create-an-azure-web-app"></a>建立 Azure Web 應用程式

現已建立 Appservice 方案，請在 `myAppServicePlan` Appservice 方案內建立 Azure Web 應用程式。 Web 應用程式會為您提供裝載空間來部署程式碼，以及提供 URL 讓您能夠檢視已部署的應用程式。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令來建立 Web 應用程式。 

在下列命令中，將 `&lt;app_name&gt;` 預留位置替代成您自己的唯一應用程式名稱。 這個唯一名稱將用來做為 Web 應用程式預設網域名稱的一部分，因此，這個名稱在 Azure 的所有應用程式中必須是唯一的。 您稍後先將任何自訂 DNS 項目對應至 Web 應用程式，再將它公開給使用者。 

```azurecli
az appservice web create --name &lt;app_name&gt; --resource-group myResourceGroup --plan myAppServicePlan
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
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "&lt;app_name&gt;.azurewebsites.net",
    "&lt;app_name&gt;.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "&lt;app_name&gt;.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    &lt; JSON data removed for brevity. &gt;
}
```

### <a name="set-the-java-version-the-java-application-server-type-and-the-application-server-version"></a>設定 Java 版本、Java 應用程式伺服器類型和應用程式伺服器版本

使用 [az appservice web config update](/cli/azure/appservice/web/config#update) 命令來設定 Java 版本、Java App Server (容器) 和容器版本。

下列命令會將 Java 版本設為 8、Java 應用程式伺服器設為 Jetty，以及將 Jetty 版本設為最新的 Jetty 9.3。

```azurecli
az appservice web config update --name &lt;app_name&gt; --resource-group myResourceGroup --java-version 1.8 --java-container Jetty --java-container-version 9.3
```


### <a name="get-credentials-for-deployment-to-the-web-app-using-ftp"></a>取得使用 FTP 部署至 Web 應用程式的認證 

您可以使用各種方式來將應用程式部署至 Azure Appservice，包括 FTP、本機 Git、GitHub、Visual Studio Team Services 和 BitBucket。 此範例中，我們會使用 Maven 來編譯 .WAR 檔案，並使用 FTP 將 .WAR 檔案部署至 Web 應用程式

若要判斷哪些認證要在 ftp 命令中傳遞至 Web 應用程式，請使用 [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles) 命令，像這樣︰ 

```azurecli

az appservice web deployment list-publishing-profiles --name &lt;app_name&gt; --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table

```
### <a name="compile-the-local-application-to-deply-to-the-web-app"></a>編譯本機應用程式以部署至 Web 應用程式 

若要準備在 Azure Web 應用程式上執行本機的 Java 應用程式，請將 Java 應用程式中的所有資源重新編譯成就緒可部署的單一 .WAR 檔案。 瀏覽至應用程式 pom.xml 所在的目錄，然後輸入︰

```bash 
mvn clean package
``` 
在 Maven 套件處理結束時，您會看到 .WAR 檔案的位置。  輸出應該會看起來像這樣：

```bash

[INFO] Processing war project
[INFO] Copying webapp resources [local-location\GitHub\mysql-java-todo-app\WebContent]
[INFO] Webapp assembled in [1519 msecs]
[INFO] Building war: C:\Users\your\localGitHub\mysql-java-todo-app\target\azure-appservice-mysql-java-sample-0.0.1-SNAPSHOT.war
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

```

將 .War 檔案的位置記下，並使用您最喜愛的 FTP 方法，將 WAR 檔案部署至 Jetty WebApps 資料夾。  請注意，Jetty WebApps 資料夾位於 Azure Web 應用程式中的 /site/wwwroot/webapps。 

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式

瀏覽至 `http://&lt;app_name&gt;.azurewebsites.net/&lt;app_name&gt;` 並將幾項工作新增至清單。 

![在 Azure Appservice 中執行的 Java 應用程式](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)


**恭喜！** 您正在 Azure Appservice 中執行資料驅動的 Java 應用程式。
若要更新應用程式，請重複 Maven 全新套件命令，並透過 FTP 將應用程式重新部署。

## <a name="manage-your-azure-web-app"></a>管理您的 Azure Web 應用程式

移至 Azure 入口網站，可查看您所建立的 web 應用程式，方法是登入 [https://portal.azure.com](https://portal.azure.com)。

按一下左側功能表中的 [AppService]，然後按一下 Azure Web 應用程式的名稱。

您現在應該位於 Web 應用程式的_刀鋒視窗_ (水平開啟的入口網站頁面)。

根據預設，Web 應用程式的刀鋒視窗會顯示 [概觀] 頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 刀鋒視窗左側的索引標籤會顯示您可開啟的各種設定頁面。

在 [應用程式設定] 頁面上， 

![Azure AppService Web 應用程式的應用程式設定](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)



刀鋒視窗中的索引標籤會顯示您可以新增至 Web 應用程式的許多強大功能。 下表提供幾個可能性︰

* 對應自訂 DNS 名稱
* 繫結自訂 SSL 憑證
* 設定連續部署
* 相應增加和相應放大
* 新增使用者驗證

## <a name="more-resources"></a>其他資源

- [將現有的自訂 DNS 名稱對應至 Azure Web Apps](app-service-web-tutorial-custom-domain.md)
- [將現有的自訂 SSL 憑證繫結至 Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
- [Web Apps CLI 指令碼](app-service-cli-samples.md)</PRE>

