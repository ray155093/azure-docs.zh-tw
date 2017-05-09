開始這項設定之前，您必須登入您的 Azure 帳戶。 cmdlet 會提示您 Azure 帳戶的登入認證。 登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與 Resource Manager](../articles/powershell-azure-resource-manager.md)。

以提高的權限開啟 PowerShell 主控台並連線至您的帳戶。 使用下列範例來協助您連接：

```powershell
Login-AzureRmAccount
```

如果您有多個 Azure 訂用帳戶，請檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

指定您要使用的訂用帳戶。

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```