
## 啟動 PowerShell 工作階段
首先，您必須安裝並執行最新的 [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx)。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../articles/powershell-install-configure.md)。

> [!NOTE]
> SQL Database 的許多新功能只在使用 [Azure Resource Manager 部署模型](../articles/resource-group-overview.md) 時才支援，所以範例會使用適用於 Resource Manager 的 [Azure SQL Database PowerShell Cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)。現有的傳統部署模型 [Azure SQL Database (傳統) Cmdlet](https://msdn.microsoft.com/library/azure/dn546723.aspx) 支援回溯相容性，但是建議使用 Resource Manager Cmdlet。
> 
> 

執行 [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) Cmdlet，您會看到要輸入認證的登入畫面。使用您用來登入 Azure 入口網站的相同認證。

    Add-AzureRmAccount

如果您有多個訂用帳戶，請使用 [**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) Cmdlet 選取您的 PowerShell 工作階段應該使用的訂用帳戶。若要查看目前的 PowerShell 工作階段正在使用哪個訂用帳戶，請執行 [**Get-AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx)。若要查看所有訂用帳戶，請執行 [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx)。

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

<!---HONumber=AcomDC_0803_2016-->