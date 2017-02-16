## <a name="set-up-azure-powershell-for-azure-dns"></a>針對 Azure DNS 設定 Azure PowerShell SDK

### <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您具備下列項目。

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
* 您必須安裝最新版的 Azure Resource Manager PowerShell Cmdlet。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

### <a name="sign-in-to-your-azure-account"></a>登入您的 Azure 帳戶

開啟 PowerShell 主控台並連接到您的帳戶。 如需詳細資訊，請參閱[搭配使用 PowerShell 與 Resource Manager](../articles/azure-resource-manager/powershell-azure-resource-manager.md)。

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>選取訂用帳戶
 
檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

選擇要使用哪一個 Azure 訂用帳戶。

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>建立資源群組

Azure Resource Manager 需要所有的資源群組指定一個位置。 此位置用來作為該資源群組中資源的預設位置。 然而，因為所有 DNS 資源是全球性，而非區域性，資源群組位置的選擇不會對 Azure DNS 造成影響。

如果您使用現有的資源群組，則可略過此步驟。

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>註冊資源提供者

Azure DNS 服務由 Microsoft.Network 資源提供者管理。 您的 Azure 訂用帳戶必須註冊為使用此資源提供者，您才能使用 Azure DNS。 每個訂用帳戶只需執行一次此作業。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

<!--HONumber=Dec16_HO3-->


