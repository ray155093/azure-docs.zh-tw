### <a name="noconnection"></a>如何新增或移除首碼 - 無閘道連線
### <a name="to-add-additional-prefixes"></a>若要新增其他前置詞

若要將其他位址首碼新增到您建立的區域網路閘道中，但您尚未建立閘道連線，請使用下列範例。 請務必將值變更為自己的值。

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
```
### <a name="to-remove-an-address-prefix"></a>若要移除位址首碼

若要從本機網路閘道中移除沒有 VPN 連線的位址首碼，請使用以下範例。 省略您不再需要的首碼。 此範例中不再需要首碼 20.0.0.0/24 (來自先前的範例)，因此我們將更新區域網路閘道，並排除該首碼。

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
```

### <a name="withconnection"></a>如何新增或移除首碼 - 現有閘道連線
如果您已建立閘道連線並想要新增或移除包含在區域網路閘道中的 IP 位址首碼，您必須依序執行下列步驟。 這會導致您 VPN 連線的停機時間。 更新首碼時會先移除連線、修改首碼，然後建立新的連線。 在以下範例中，請務必將值變更為自己的值。

> [!IMPORTANT]
> 請勿刪除 VPN 閘道。 如果您這樣做，必須再次進行這些步驟來重新建立它，並以新的設定重新設定您的內部部署路由器。
> 
> 

1. 移除連線。

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
  ```
2. 修改區域網路閘道的位址首碼。
   
  設定 LocalNetworkGateway 的變數。

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
  ```
   
  修改首碼。
   
  ```powershell
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```
3. 建立連線。 在此範例中，我們要設定 IPsec 連線類型。 當您重新建立連線時，請使用針對設定指定的連線類型。 對於其他連線類型，請參閱 [PowerShell Cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 頁面。
   
  設定 VirtualNetworkGateway 的變數。

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  建立連線。 請注意，此範例會使用您在上一個步驟中設定的變數 $local。

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```
