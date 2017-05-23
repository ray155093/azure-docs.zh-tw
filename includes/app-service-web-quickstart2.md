使用 [az appservice web create](/cli/azure/appservice/web#create) 命令來建立 Web 應用程式。 在下列命令中，將您看到 `<app_name>` 預留位置之處替代成您自己的唯一應用程式名稱。 `<app_name>` 使用於 Web 應用程式的預設 DNS 網站。 如果 `<app_name>` 不是唯一的，您會收到易懂的錯誤訊息「具有指定名稱 <app_name> 的網站已經存在」。

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
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
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

瀏覽至網站 (`http://<app_name>.azurewebsites.net`) 可查看您剛建立的 Web 應用程式。

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>設定本機 Git 部署

App Service 支援數種將內容部署至 Web 應用程式的方式，例如 FTP、本機 Git、GitHub、Visual Studio Team Services 和 Bitbucket。 

針對這個快速入門，您要使用本機 Git 來進行部署。 這表示您要使用 Git 命令來進行部署，從本機存放庫推送到 Azure 中的存放庫。 

使用 [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 命令來設定 Web 應用程式的本機 Git 存取。

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

輸出具備下列格式：

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

將顯示的 URI 進行儲存。 您在下一步將會用到它。 `<username>` 是您在上一個步驟中建立的[部署使用者](#configure-a-deployment-user)。

## <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

將 Azure 遠端新增至本機 Git 存放庫。

```bash
git remote add azure <URI from previous step>
```

推送到 Azure 遠端來部署您的應用程式。 當您建立部署使用者時，系統會提示您輸入稍早建立的密碼。 務必輸入您在[設定部署使用者](#configure-a-deployment-user)中建立的密碼，而不是您用來登入 Azure 入口網站的密碼。

```azurecli
git push azure master
```

上述命令會顯示類似下列範例的資訊：

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>瀏覽至應用程式


瀏覽至已部署的應用程式︰

```
http://<app_name>.azurewebsites.net
```

