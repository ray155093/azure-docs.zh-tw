您可以使用 `Get-AzureVNetConnection` 來驗證傳統虛擬網路閘道的連線。 

1. 請使用下列 Cmdlet 範例，並將值設定為與您狀況相符的值。 如果虛擬網路的名稱包含空格，則必須以引號括住。

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. 完成 Cmdlet 之後，請檢視值。 在下列範例中，[連線狀態] 會顯示為 [已連接]，而您可以看見輸入和輸出位元組。

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal
