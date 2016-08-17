### 使用 PowerShell 驗證您的連線

您可以使用 `Get-AzureRmVirtualNetworkGatewayConnection` Cmdlet，並在搭配或不搭配 `-Debug` 的情況下驗證連線是否成功。

1. 請使用下列 Cmdlet 範例，並將值設定為與您狀況相符的值。出現提示時，請選取 [A] 以執行 [全部]。在範例中，`-Name` 是指您已建立，而且想要測試之連線的名稱。

		Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. 完成 Cmdlet 之後，請檢視值。在下列範例中，連接狀態會顯示為 [已連接]，且您可以看見輸入和輸出位元組。

		Body:
		{
		  "name": "MyGWConnection",
		  "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
		  "properties": {
		    "provisioningState": "Succeeded",
		    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
		    "virtualNetworkGateway1": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
		teways/vnetgw1"
		    },
		    "localNetworkGateway2": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
		ways/LocalSite"
		    },
		    "connectionType": "IPsec",
		    "routingWeight": 10,
		    "sharedKey": "abc123",
		    "connectionStatus": "Connected",
		    "ingressBytesTransferred": 33509044,
		    "egressBytesTransferred": 4142431
		  }

### 使用 Azure 入口網站驗證您的連線

在 Azure 入口網站中，您可以瀏覽至連線以檢視連線狀態。做法有好幾種。以下是瀏覽至連線的其中一種方式。

1. 在 [Azure 入口網站](http://portal.azure.com)中，瀏覽至 [虛擬網路閘道]。按一下閘道名稱。
2. 在窗格中，按一下 [設定] 下方的 [連線]。您可以看到每個連線的狀態。
3. 如需連線的詳細資訊，請按一下連線名稱。在您連線的 [Essentials] 頁面，請注意 [連線狀態]。當您已成功建立連線時，狀態會是 [成功] 和 [已連線]。藉由查看 [資料輸入] 和 [資料輸出]，您可以觀看流經的資料。

	在下列範例中，[連線狀態] 是 [未連線]。

	![驗證連線](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)

<!---HONumber=AcomDC_0810_2016-->