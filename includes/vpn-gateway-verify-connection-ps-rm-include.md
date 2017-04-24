您可以使用 **Get-AzureRmVirtualNetworkGatewayConnection** Cmdlet，並在搭配或不搭配 **-Debug** 的情況下驗證連線是否成功。 

1. 請使用下列 Cmdlet 範例，並將值設定為與您狀況相符的值。 出現提示時，請選取 [A] 以執行 [全部]。 在範例中，**-Name** 是指您已建立，而且想要測試之連線的名稱。

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. 完成 Cmdlet 之後，請檢視值。 在下列範例中，連接狀態會顯示為 [已連接]，且您可以看見輸入和輸出位元組。
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```
  