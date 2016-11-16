
## <a name="start-your-powershell-session"></a>啟動 PowerShell 工作階段
首先，您需安裝並執行最新的 [Azure PowerShell](https://msdn.microsoft.com/library/mt619274\(v=azure.300\).aspx) 。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](../articles/powershell-install-configure.md)。

> [!NOTE]
> SQL Database 的許多新功能只有在使用 [Azure Resource Manager 部署模型](../articles/azure-resource-manager/resource-group-overview.md)時才支援，所以範例會使用適用於 Resource Manager 的 [Azure SQL Database PowerShell Cmdlet](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)。 服務管理 (傳統) 部署模型 [Azure SQL Database 服務管理 Cmdlet](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) 支援回溯相容性，但是建議使用 Resource Manager Cmdlet。
> 
> 

執行 [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) Cmdlet，您會看到要輸入認證的登入畫面。 使用您用來登入 Azure 入口網站的相同認證。

    Add-AzureRmAccount

如果您有多個訂用帳戶，請使用 [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) Cmdlet 選取您的 PowerShell 工作階段應該使用的訂用帳戶。 若要查看目前的 PowerShell 工作階段正在使用哪個訂用帳戶，請執行 [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx)。 若要查看所有訂用帳戶，請執行 [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx)。

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


<!--HONumber=Nov16_HO2-->


