若要修改閘道 IP 位址，請使用 `New-AzureRmVirtualNetworkGatewayConnection` Cmdlet。 只要您將區域網路閘道的名稱保持與現有名稱完全相同，設定就能覆寫。 "Set" Cmdlet 目前不支援修改閘道 IP 位址。

### <a name="gwipnoconnection"></a>如何修改閘道 IP 位址 - 無閘道連線
若要更新尚無連線之區域網路閘道的閘道 IP 位址，請使用以下範例。 您也可以同時更新位址首碼。 您指定的設定會覆寫現有設定。 請務必使用區域網路閘道的現有名稱。 否則您將會建立新的區域網路閘道，而不是覆寫現有閘道。

使用下列範例，並將值替換為您自己的值。

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>如何修改閘道 IP 位址 - 現有閘道連線
如果閘道連線已存在，您將需要先移除連線。 接著，您可以修改閘道 IP 位址，然後重新建立新連線。 這會導致您 VPN 連線的停機時間。

> [!IMPORTANT]
> 請勿刪除 VPN 閘道。 如果您這樣做，必須再次進行這些步驟來重新建立它，並以即將指派給新建立之閘道的 IP 位址重新設定內部部署路由器。
> 
> 

1. 移除連線。 您可以使用 `Get-AzureRmVirtualNetworkGatewayConnection` Cmdlet 找出連線名稱。

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. 修改 GatewayIpAddress 值。 如果需要的話，此時您也可以修改位址首碼。 請注意，這會覆寫現有的區域網路閘道設定。 修改時，請使用區域網路閘道的現有名稱，讓設定得以覆寫。 否則您將會建立新的區域網路閘道，而不是修改現有閘道。

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. 建立連線。 在此範例中，我們要設定 IPsec 連線類型。 當您重新建立連線時，請使用針對設定指定的連線類型。 如需其他連線類型的相關資訊，請參閱 [PowerShell Cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 頁面。  若要取得 VirtualNetworkGateway 名稱，您可以執行 `Get-AzureRmVirtualNetworkGateway` Cmdlet。
   
    設定變數︰

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    建立連線：

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```
