### <a name="to-verify-your-connection-by-using-powershell"></a>使用 PowerShell 驗證您的連線
您可以使用 `Get-AzureRmVirtualNetworkGatewayConnection` Cmdlet，並在搭配或不搭配 `-Debug` 的情況下驗證連線是否成功。 

1. 請使用下列 Cmdlet 範例，並將值設定為與您狀況相符的值。 出現提示時，請選取 [A] 以執行 [全部]。 在範例中，`-Name` 是指您已建立，而且想要測試之連線的名稱。
   
        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
2. 完成 Cmdlet 之後，請檢視值。 在下列範例中，連接狀態會顯示為 [已連接]，且您可以看見輸入和輸出位元組。
   
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

### <a name="to-verify-your-connection-by-using-the-azure-portal"></a>使用 Azure 入口網站驗證您的連線
在 Azure 入口網站中，您可以瀏覽至連線以檢視連線狀態。 做法有好幾種。 下列步驟顯示瀏覽至連線並進行驗證的其中一種方式。

1. 在 [Azure 入口網站](http://portal.azure.com)中，按一下 [所有資源]瀏覽至您的虛擬網路閘道。
2. 在虛擬網路閘道的刀鋒視窗上，按一下 [連線]。 您可以看到每個連線的狀態。
3. 按一下您要驗證的連線名稱以開啟 **Essentials**。 在 Essentials 中，您可以檢視連線的相關詳細資訊。 當您成功建立連線時，[狀態]會是 [成功] 和 [已連線]。
   
    ![驗證連線](./media/vpn-gateway-verify-connection-rm-include/connectionsucceeded.png)



<!--HONumber=Nov16_HO2-->


