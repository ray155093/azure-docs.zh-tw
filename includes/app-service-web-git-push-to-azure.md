## <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

將 Azure 遠端新增至本機 Git 存放庫。

```bash
git remote add azure <URI from previous step>
```

推送到 Azure 遠端來部署您的應用程式。 當您建立部署使用者時，系統會提示您輸入稍早建立的密碼。 務必輸入您在[設定部署使用者](#configure-a-deployment-user)中建立的密碼，而不是您用來登入 Azure 入口網站的密碼。

```bash
git push azure master
```

上述命令會顯示類似下列範例的資訊：
