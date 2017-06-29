使用 Azure CLI 來取得 API 應用程式的遠端部署 URL，然後設定可以推送至遠端的本機 Git 部署。

```bash
giturl=$(az webapp deployment source config-local-git -n $app_name \ -g myResourceGroup --query [url] -o tsv)

git remote add azure $giturl
```

推送到 Azure 遠端來部署您的應用程式。 當您建立部署使用者時，系統會提示您輸入稍早建立的密碼。 務必輸入先前在快速入門中建立的密碼，而不是您用來登入 Azure 入口網站的密碼。

```bash
git push azure master
```
