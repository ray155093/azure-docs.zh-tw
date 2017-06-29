使用 [az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 命令設定 API 應用程式的本機 Git 部署。   

App Service 支援數種將內容部署至 Web 應用程式的方式，例如 FTP、本機 Git、GitHub、Visual Studio Team Services 和 Bitbucket。 針對這個快速入門，您要使用本機 Git 來進行部署。 這表示您要使用 Git 命令來進行部署，從本機存放庫推送到 Azure 中的存放庫。  

使用下列指令碼來設定您將在推送程式碼時使用的帳戶層級部署認證，並確定包含您自己的使用者名稱和密碼值。   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
