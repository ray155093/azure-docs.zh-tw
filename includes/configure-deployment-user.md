## <a name="configure-a-deployment-user"></a>設定部署使用者  

對於 FTP 和本機 Git，您必須在伺服器上設定部署使用者，才能驗證您的部署。

> [!NOTE]
> 需要部署使用者，才能將 FTP 和本機 Git 部署至 Web 應用程式。
> `username` 和 `password` 為帳戶層級。 它們與您的 Azure 訂用帳戶認證不同。
>

執行 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令來建立部署認證。

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

使用者名稱必須是唯一的，而密碼必須是強式密碼。 如果您收到 ` 'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 ` 'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。

您只需要建立此部署使用者一次；您可以將它使用於所有 Azure 部署。

記錄使用者名稱和密碼，因為它們會使用於您稍後部署應用程式的步驟中。