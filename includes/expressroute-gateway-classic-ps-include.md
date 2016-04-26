您必須先建立 VNET 和閘道器子網路，才能進行下列工作。如需詳細資訊，請參閱[使用傳統入口網站設定虛擬網路](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) 一文。

## 新增閘道

使用以下的命令建立閘道器。所有的值請務必替換成您自己的值。

	New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard

## 確認已建立閘道

使用下列命令，確認已建立閘道。這個命令也會擷取閘道器識別碼，您在其他作業會需要它。

	Get-AzureVirtualNetworkGateway

## 調整閘道器大小

有三個[閘道器 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md)。您可以使用下列命令隨時變更閘道器 SKU。

	Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## 移除閘道器

使用以下的命令移除閘道器

	Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>

<!---HONumber=AcomDC_0413_2016-->