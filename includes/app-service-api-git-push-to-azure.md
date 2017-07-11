使用 Azure CLI 取得 API 應用程式的遠端部署 URL。 在下列命令中，使用您 Web 應用程式的名稱取代 *\<app_name>*。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

將本機 Git 部署設定為可以推送至遠端。

```bash
git remote add azure <URI from previous step>
```

推送到 Azure 遠端來部署您的應用程式。 當您建立部署使用者時，系統會提示您輸入稍早建立的密碼。 務必輸入先前在快速入門中建立的密碼，而不是您用來登入 Azure 入口網站的密碼。

```bash
git push azure master
```
