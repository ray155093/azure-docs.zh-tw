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
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 1d0addd4f04fb597e7962c21ccbcb61e165a6c1e
ms.lasthandoff: 03/17/2017


---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>使用 PowerShell 建立具有站對站 VPN 連接的 VNet
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [傳統 - 傳統入口網站](vpn-gateway-site-to-site-create.md)
>
>

本文逐步引導您使用 Azure Resource Manager 部署模型建立虛擬網路以及內部部署網路的網站間 VPN 閘道連線。 網站間連線可以用於跨單位與混合式組態。

![站對站 VPN 閘道跨單位連線圖表](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>網站間連接的部署模型和方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表顯示網站間組態目前可用的部署模型和方法。 當包含設定的文章推出時，我們會直接從此資料表連結至該文章。

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>其他組態
如果您想要將 VNet 連接在一起，但不要建立對內部部署位置的連線，請參閱 [設定 VNet 對 VNet 連線](vpn-gateway-vnet-vnet-rm-ps.md)。 如果您想要網站間連接新增至已經有連接的 VNet，請參閱[將 S2S 連接新增至已有現有 VPN 閘道連接的 VNet](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)。

## <a name="before-you-begin"></a>開始之前
在開始設定之前，請確認您具備下列項目。

* 相容的 VPN 裝置 (以及能夠進行設定的人員)。 請參閱 [關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 如果不熟悉設定 VPN 裝置，或不熟悉位於內部部署網路組態的 IP 位址範圍，則您需要與能夠提供那些詳細資料的人協調。
* 您的 VPN 裝置對外開放的公用 IP 位址。 此 IP 位址不能位於 NAT 後方。
* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。
* 最新版的 Azure Resource Manager PowerShell Cmdlet。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。

## <a name="Login"></a>1.連線至您的訂用帳戶
請確定您切換為 PowerShell 模式以使用 Resource Manager Cmdlet。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。

開啟 PowerShell 主控台並連接到您的帳戶。 使用下列範例來協助您連接：

    Login-AzureRmAccount

檢查帳戶的訂用帳戶。

    Get-AzureRmSubscription

指定您要使用的訂用帳戶。

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="VNet"></a>2.建立虛擬網路和閘道器子網路
以下範例使用 /28 的閘道子網路。 雖然您可以建立小至 /29 的閘道子網路，我們建議您選取至少 /28 或 /27，建立包含更多位址的較大子網路。 這將允許足夠的位址，以容納您未來可能需要的其他組態。

如果已有具備 /29 或更大的閘道器子網路的虛擬網路，您可以往前跳至 [新增您的區域網路閘道](#localnet)。

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>建立虛擬網路和閘道器子網路
使用下列範例來建立虛擬網路和閘道子網路。 取代為您自己的值。

首先，建立資源群組：

    New-AzureRmResourceGroup -Name testrg -Location 'West US'

接著，建立您的虛擬網路。 請確認您指定的位址空間沒有與您在內部部署網路上所擁有的任何位址空間重疊。

下列範例會建立一個名為 testvnet 的虛擬網路和兩個子網路：一個名為 GatewaySubnet，另一個名為 Subnet1。 請務必建立一個特別命名為 GatewaySubnet 的子網路。 如果您將它命名為其他名稱，您的連線設定將會失敗。

設定變數。

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

建立 VNet。

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>將閘道器子網路加入至您已建立的虛擬網路
只有在您要將先前建立的閘道器子網路新增至 VNet 時才需要此步驟。

您可以使用下列範例來建立閘道子網路。 請務必將閘道器子網路命名為 'GatewaySubnet'。 如果您將其命名為其他名字，您會建立子網路，但 Azure 不會將它視為閘道器子網路。

設定變數。

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

建立閘道子網路。

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

設定組態。

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3.<a name="localnet"></a>新增區域網路閘道
在虛擬網路中，區域網路閘道通常是指您的內部部署位置。 您會指定該站台的名稱以供 Azure 參考，也會指定區域網路閘道的位址空間前置詞。

Azure 會使用您指定的 IP 位址前置詞來識別要傳送至內部部署位置的流量。 這表示您必須指定您要與區域網路閘道相關聯的每個位址前置詞。 如果您的內部部署網路變更，您可以輕鬆地更新這些前置詞。

使用 PowerShell 範例時，請注意下列各項：

* *GatewayIPAddress* 是內部部署 VPN 裝置的 IP 位址。 VPN 裝置不能位於 NAT 後方。
* *AddressPrefix* 是您的內部部署位址空間。

若要新增具有單一位址前置詞的區域網路閘道：

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

若要新增具有多個位址前置詞的區域網路閘道：

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>修改區域網路閘道的 IP 位址首碼
有時候您的區域網路閘道首碼會有所變更。 若要修改您的 IP 位址前置詞，採取的步驟取決於您是否已建立 VPN 閘道連線。 請參閱本文的 [修改區域網路閘道的 IP 位址首碼](#modify) 一節。

## <a name="PublicIP"></a>4.要求 VPN 閘道的公用 IP 位址
接下來，要求要配置給 Azure VNet VPN 閘道的公用 IP 位址。 這不是指派給 VPN 裝置的相同 IP 位址，而是指派給 Azure VPN 閘道本身的 IP 位址。 您無法指定想要使用的 IP 位址。 該 IP 位址會以動態方式配置給您的閘道。 在設定內部部署 VPN 裝置以連接到閘道時，您會使用此 IP 位址。

資源管理員部署模型的 Azure VPN 閘道目前使用動態配置方法，僅支援公用 IP 位址。 但是，這不代表 IP 位址會變更。 Azure VPN 閘道 IP 位址只會在刪除或重新建立閘道時變更。 閘道公用 IP 位址不會因為重新調整、重設或 Azure VPN 閘道的其他內部維護/升級而變更。

使用下列 PowerShell 範例：

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="GatewayIPConfig"></a>5.建立閘道器 IP 位址組態
閘道器組態定義要使用的子網路和公用 IP 位址。 使用下列範例來建立閘道組態。

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id

## <a name="CreateGateway"></a>6.建立虛擬網路閘道
在此步驟中，您會建立虛擬網路閘道。 建立閘道可能需要很長的時間才能完成。 通常 45 分鐘以上。

輸入下列值：

* 網站間組態的 -GatewayType 是 Vpn。 閘道器類型永遠是您實作的組態的特定類型。 例如，其他閘道器組態可能需要 -GatewayType ExpressRoute。
* -VpnType 可以是 RouteBased (在某些文件中稱為動態閘道器)，或 PolicyBased (在某些文件中稱為靜態閘道器)。 如需 VPN 閘道類型的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。
* -GatewaySku 可以是Basic、Standard 或 HighPerformance。     

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="ConfigureVPNDevice"></a>7.設定 VPN 裝置
此時，您需要虛擬網路閘道的公用 IP 位址，以便設定內部部署 VPN 裝置。 向您的裝置製造商取得特定的組態資訊。 您可以請參閱 [VPN 裝置](vpn-gateway-about-vpn-devices.md) 以取得詳細資訊。

若要尋找虛擬網路閘道的公用 IP 位址，請使用下面範例：

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="CreateConnection"></a>8.建立 VPN 連線
接下來，在虛擬網路閘道與 VPN 裝置之間建立網站間 VPN 連線。 請務必將值取代為您自己的值。 共用的金鑰必須符合您用於 VPN 裝置設定的值。 請注意，網站間的 `-ConnectionType` 為 IPsec。

設定變數。

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

建立連線。

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

過一會兒，連接將會建立。

## <a name="toverify"></a>驗證 VPN 連線
VPN 連線有幾種不同的驗證方式。

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>修改區域網路閘道的 IP 位址首碼
如果您需要變更區域網路閘道首碼，請使用下列指示。 所提供的指示有兩組。 要選擇哪組指示取決於您是否已建立閘道連線。

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>修改區域網路閘道的閘道 IP 位址
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>後續步驟
*  一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。
* 如需 BGP 的相關資訊，請參閱 [BGP 概觀](vpn-gateway-bgp-overview.md)和[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。

