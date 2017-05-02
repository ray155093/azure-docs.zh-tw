### <a name="noconnection"></a>修改首碼 - 沒有閘道連線

- 若要新增其他位址首碼：

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```

- 若要移除位址首碼：<br>
  省略您不再需要的首碼。 此範例中不再需要首碼 20.0.0.0/24 (來自先前的範例)，因此我們將更新區域網路閘道，並排除該首碼。

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
  ```

### <a name="withconnection"></a>修改首碼 - 現有閘道連線
如果您有閘道連線並想要新增或移除包含在區域網路閘道中的 IP 位址首碼，您必須依序執行下列步驟。 這會導致您 VPN 連線的停機時間。

> [!IMPORTANT]
> 請勿刪除 VPN 閘道。 如果您這樣做，您必須重新執行各個步驟來重新建立閘道。 此外，您必須以新的 VPN 閘道 IP 位址來更新內部部署 VPN 裝置。
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
3. 建立連線。 在此範例中，我們會設定 IPsec 連線類型。 當您重新建立連線時，請使用針對設定指定的連線類型。 對於其他連線類型，請參閱 [PowerShell Cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 頁面。
   
  設定 VirtualNetworkGateway 的變數。

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  建立連線。 此範例使用您在步驟 2 中設定的 $local 變數。

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```