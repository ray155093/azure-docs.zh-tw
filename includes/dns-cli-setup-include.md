## <a name="set-up-azure-cli-for-azure-dns"></a>設定適用於 Azure DNS 的 Azure CLI

### <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您具備下列項目。

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
* 安裝最新版的 Azure CLI，該 CLI 適用於 Windows、Linux 或 MAC。 您可以在 [安裝 Azure CLI](../articles/cli-install-nodejs.md)中取得詳細資訊。

### <a name="sign-in-to-your-azure-account"></a>登入您的 Azure 帳戶

開啟主控台視窗，並驗證您的認證。 如需詳細資訊，請參閱[從 Azure CLI 登入 Azure](../articles/xplat-cli-connect.md)

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>切換 CLI 模式

Azure DNS 使用 Azure Resource Manager。 請確定您已將 CLI 模式切換為使用 Azure Resource Manager 命令。

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>選取訂用帳戶

檢查帳戶的訂用帳戶。

```azurecli
azure account list
```

選擇要使用哪一個 Azure 訂用帳戶。

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>建立資源群組

Azure Resource Manager 需要所有的資源群組指定一個位置。 這用來作為該資源群組中資源的預設位置。 然而，因為所有 DNS 資源是全球性，而非區域性，資源群組位置的選擇不會對 Azure DNS 造成影響。

如果您使用現有的資源群組，則可略過此步驟。

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>註冊資源提供者

Azure DNS 服務由 Microsoft.Network 資源提供者管理。 您的 Azure 訂用帳戶必須註冊為使用此資源提供者，您才能使用 Azure DNS。 每個訂用帳戶只需執行一次此作業。

```azurecli
azure provider register --namespace Microsoft.Network
```

