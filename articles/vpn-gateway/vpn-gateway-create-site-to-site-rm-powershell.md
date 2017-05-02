---
title: "將內部部署網路連接至 Azure 虛擬網路：站對站 VPN：PowerShell | Microsoft Docs"
description: "透過公用網際網路建立從內部部署網路至 Azure 虛擬網路之 IPsec 連線的步驟。 這些步驟可協助您使用 PowerShell 建立跨單位的站對站 VPN 閘道連線。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 4ec11ffeae94b4a8e5a65566f0f0c067f45a0134
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>使用 PowerShell 建立具有站對站 VPN 連接的 VNet

本文說明如何使用 PowerShell 來建立從內部部署網路到 VNet 的站對站 VPN 閘道連線。 本文中的步驟適用於 Resource Manager 部署模型。 您也可從下列清單中選取不同的選項，以使用不同的部署工具或部署模型來建立此組態：

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [傳統 - 傳統入口網站](vpn-gateway-site-to-site-create.md)
> 
>


![站對站 VPN 閘道跨單位連線圖表](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-connection-diagram.png)

站對站 VPN 閘道連線可用來透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道，將內部部署網路連線到 Azure 虛擬網路。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 確認您想要使用 Resource Manager 部署模型。 [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* 相容的 VPN 裝置 (以及能夠進行設定的人員)。 如需相容 VPN 裝置和裝置組態的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。
* 您的 VPN 裝置對外開放的公用 IPv4 位址。 此 IP 位址不能位於 NAT 後方。
* 如果您不熟悉位於內部部署網路組態的 IP 位址範圍，您需要與能夠提供那些詳細資料的人協調。 當您建立此組態時，您必須指定 IP 位址範圍的首碼，以供 Azure 路由傳送至您的內部部署位置。 內部部署網路的子網路皆不得與您所要連線的虛擬網路子網路重疊。
* 最新版的 Azure Resource Manager PowerShell Cmdlet。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。

### <a name="example-values"></a>值的範例

本文的範例使用下列值。 您可以使用這些值來建立測試環境，或參考這些值，進一步了解本文中的範例。

```
#Example values

VnetName                = testvnet 
ResourceGroup           = testrg 
Location                = West US 
AddressSpace            = 10.0.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.0.1.0/28 
GatewaySubnet           = 10.0.0.0/27
LocalNetworkGatewayName = LocalSite
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24','20.0.0.0/24
Gateway Name            = vnetgw1
PublicIP                = gwpip
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = myGWConnection
```

## <a name="Login"></a>1.連線至您的訂用帳戶
請確定您切換為 PowerShell 模式以使用 Resource Manager Cmdlet。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。

1. 開啟 PowerShell 主控台並連接到您的帳戶。 使用下列範例來協助您連接：

  ```powershell
  Login-AzureRmAccount
  ```
2. 檢查帳戶的訂用帳戶。

  ```powershell
  Get-AzureRmSubscription
  ```
3. 指定您要使用的訂用帳戶。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

## <a name="VNet"></a>2.建立虛擬網路和閘道器子網路

如果您還沒有虛擬網路，請建立一個。 在建立虛擬網路時，請確定您指定的位址空間沒有與您在內部部署網路上所擁有的任何位址空間重疊。 對於此組態，您也需要有閘道子網路。 虛擬網路閘道所使用的閘道子網路，會包含可供 VPN 閘道服務使用的 IP 位址。 當您建立閘道子網路時，請將它命名為「GatewaySubnet」。 如果您將其命名為其他名字，您會建立子網路，但 Azure 不會將它視為閘道器子網路。

您所要建立的 VPN 閘道組態會決定您需要為閘道子網路指定什麼大小。 雖然您可以建立小至 /29 的閘道子網路，但我們會建議您選取 /27 或 /28，以建立包含更多位址的較大子網路。 使用較大的閘道子網路可讓您擁有足夠的 IP 位址，以因應未來可能的組態。

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>建立虛擬網路和閘道器子網路

此範例會建立虛擬網路和閘道子網路。 如果您已經有虛擬網路，並需要對它新增閘道子網路，請參閱[將閘道子網路新增至您已建立的虛擬網路](#gatewaysubnet)。

建立資源群組：

```powershell
New-AzureRmResourceGroup -Name testrg -Location 'West US'
```

建立虛擬網路。 

1. 設定變數。

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
  ```
2. 建立 VNet。

  ```powershell
  New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
  -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>將閘道器子網路加入至您已建立的虛擬網路

1. 設定變數。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
  ```
2. 建立閘道子網路。

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27 -VirtualNetwork $vnet
  ```
3. 設定組態。

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3.<a name="localnet"></a>建立區域網路閘道

區域網路閘道通常是指您的內部部署位置。 請賦予網站可供 Azure 參考的名稱，然後指定您想要與其建立連線之內部部署 VPN 裝置的 IP 位址。 也請指定 IP 位址首碼，以供系統透過 VPN 閘道路由至 VPN 裝置。 您指定的位址首碼是位於內部部署網路上的首碼。 如果您的內部部署網路有所變更，您可以輕鬆地更新首碼。

輸入下列值：

* *GatewayIPAddress* 是內部部署 VPN 裝置的 IP 位址。 VPN 裝置不能位於 NAT 後方。
* *AddressPrefix* 是您的內部部署位址空間。

- 若要新增具有單一位址前置詞的區域網路閘道：

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix   '10.0.0.0/24'
  ```

- 若要新增具有多個位址前置詞的區域網路閘道：

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

- 修改區域網路閘道的 IP 位址首碼：<br>
有時候您的區域網路閘道首碼會有所變更。 若要修改您的 IP 位址前置詞，採取的步驟取決於您是否已建立 VPN 閘道連線。 請參閱本文的 [修改區域網路閘道的 IP 位址首碼](#modify) 一節。

## <a name="PublicIP"></a>4.要求公用 IP 位址

請要求公用 IP 位址，以配置給虛擬網路 VPN 閘道。 這是您的 VPN 裝置所設定要連線的 IP 位址。

Resource Manager 部署模型的虛擬網路閘道目前僅支援使用動態配置方法的公用 IP 位址。 但是，這不代表 IP 位址會變更。 VPN 閘道 IP 位址只會在刪除或重新建立閘道時變更。 虛擬網路閘道公用 IP 位址不會因為重新調整、重設或 VPN 閘道的其他內部維護/升級而變更。

使用下列 PowerShell 範例：

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5.建立閘道器 IP 位址組態
閘道器組態定義要使用的子網路和公用 IP 位址。 使用下列範例來建立閘道組態：

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6.建立 VPN 閘道

建立虛擬網路 VPN 閘道。 建立 VPN 閘道可能需要 45 分鐘以上的時間才能完成。

輸入下列值：

* 網站間組態的 -GatewayType 是 Vpn。 閘道器類型永遠是您實作的組態的特定類型。 例如，其他閘道器組態可能需要 -GatewayType ExpressRoute。
* -VpnType 可以是 RouteBased (在某些文件中稱為動態閘道器)，或 PolicyBased (在某些文件中稱為靜態閘道器)。 如需 VPN 閘道類型的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。
* -GatewaySku 可以是 Basic、Standard 或 HighPerformance。 某些 SKU 有組態限制。 如需詳細資訊，請參閱[閘道 SKU](vpn-gateway-about-vpngateways.md#gateway-skus)。

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku Standard
```

## <a name="ConfigureVPNDevice"></a>7.設定 VPN 裝置

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

若要使用 PowerShell 尋找虛擬網路閘道的公用 IP 位址，請使用下面範例：

```powershell
Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
```

## <a name="CreateConnection"></a>8.建立 VPN 連線
接下來，在虛擬網路閘道與 VPN 裝置之間建立網站間 VPN 連線。 請務必將值取代為您自己的值。 共用的金鑰必須符合您用於 VPN 裝置設定的值。 請注意，網站間的「-ConnectionType」為 IPsec。

1. 設定變數。
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
  $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
  ```

2. 建立連線。
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName testrg `
  -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

過一會兒，連接將會建立。

## <a name="toverify"></a>9.驗證 VPN 連線
VPN 連線有幾種不同的驗證方式。

[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="modify"></a>修改區域網路閘道的 IP 位址首碼
如果您需要變更區域網路閘道首碼，請使用下列指示。 所提供的指示有兩組。 要選擇哪組指示取決於您是否已建立閘道連線。

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>修改區域網路閘道的閘道 IP 位址
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>後續步驟
*  一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。
* 如需 BGP 的相關資訊，請參閱 [BGP 概觀](vpn-gateway-bgp-overview.md)和[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。

