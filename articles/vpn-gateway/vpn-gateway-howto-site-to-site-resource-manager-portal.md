---
title: "將內部部署網路連接至 Azure 虛擬網路：站對站 VPN：入口網站 | Microsoft Docs"
description: "透過公用網際網路建立從內部部署網路至 Azure 虛擬網路之 IPsec 連線的步驟。 這些步驟可協助您使用入口網站建立跨單位的站對站 VPN 閘道連線。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: ae91d49bf4f715847bcef5d6b00e3798e6a02500
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>在 Azure 入口網站中建立站對站連線

本文說明如何使用 Azure 入口網站來建立從內部部署網路到 VNet 的站對站 VPN 閘道連線。 本文中的步驟適用於 Resource Manager 部署模型。 您也可從下列清單中選取不同的選項，以使用不同的部署工具或部署模型來建立此組態：

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [傳統 - 傳統入口網站](vpn-gateway-site-to-site-create.md)
> 
>

![站對站 VPN 閘道跨單位連線圖表](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

站對站 VPN 閘道連線可用來透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道，將內部部署網路連線到 Azure 虛擬網路。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 確認您想要使用 Resource Manager 部署模型。 [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* 相容的 VPN 裝置 (以及能夠進行設定的人員)。 如需相容 VPN 裝置和裝置組態的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。
* 您的 VPN 裝置對外開放的公用 IPv4 IP 位址。 此 IP 位址不能位於 NAT 後方。
* 如果您不熟悉位於內部部署網路組態的 IP 位址範圍，您需要與能夠提供那些詳細資料的人協調。 當您建立此組態時，您必須指定 IP 位址範圍的首碼，以供 Azure 路由傳送至您的內部部署位置。 內部部署網路的子網路皆不得與您所要連線的虛擬網路子網路重疊。 

### <a name="values"></a>範例值
練習這些步驟時，您可以使用下列範例值：

* **VNet 名稱︰**TestVNet1
* **位址空間：** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (對此練習是選擇性的)
* **子網路：**
  * FrontEnd：10.11.0.0/24
  * BackEnd：10.12.0.0/24 (對此練習是選擇性的)
  * GatewaySubnet：10.11.255.0/27
* **資源群組︰**TestRG1
* **位置：**美國東部
* **DNS 伺服器︰**DNS 伺服器的 IP 位址
* **虛擬網路閘道名稱：**VNet1GW
* **公用 IP：**VNet1GWIP
* **VPN 類型：**路由式
* **連線類型︰**網站間 (IPsec)
* **閘道類型：**VPN
* **區域網路閘道名稱：**Site2
* **連線名稱︰**VNet1toSite2

## <a name="CreatVNet"></a>1.建立虛擬網路

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2.指定 DNS 伺服器
站對站連線不需要 DNS。 不過，如果您想要對部署至虛擬網路的資源進行名稱解析，則應指定 DNS 伺服器。 此設定可讓您指定要用於此虛擬網路之名稱解析的 DNS 伺服器服務。 它不會建立 DNS 伺服器。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3.建立閘道子網路

虛擬網路閘道所使用的閘道子網路，會包含可供 VPN 閘道服務使用的 IP 位址。 當您建立閘道子網路時，請將它命名為「GatewaySubnet」。 如果您將它命名為其他名稱，您的連線設定會失敗。

您所要建立的 VPN 閘道組態會決定您需要為閘道子網路指定的大小。 雖然您可以建立小至 /29 的閘道子網路，但我們會建議您選取 /27 或 /28，以建立包含更多位址的較大子網路。 使用較大的閘道子網路可讓您擁有足夠的 IP 位址，以因應未來可能的組態。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]


## <a name="VNetGateway"></a>4.建立 VPN 閘道

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5.建立區域網路閘道

區域網路閘道通常是指您的內部部署位置。 請賦予網站可供 Azure 參考的名稱，然後指定您想要與其建立連線之內部部署 VPN 裝置的 IP 位址。 也請指定 IP 位址首碼，以供系統透過 VPN 閘道路由至 VPN 裝置。 您指定的位址首碼是位於內部部署網路上的首碼。 如果您的內部部署網路有所變更，您可以輕鬆地更新首碼。

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6.設定 VPN 裝置
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

若要使用 Azure 入口網站尋找 VPN 閘道的公用 IP 位址，請瀏覽至 [虛擬網路閘道]，然後按一下閘道名稱。

## <a name="CreateConnection"></a>7.建立 VPN 連線

您會在虛擬網路閘道與內部部署 VPN 裝置之間建立站對站 VPN 連線。

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8.驗證 VPN 連線

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]


## <a name="next-steps"></a>後續步驟

*  如需 BGP 的相關資訊，請參閱 [BGP 概觀](vpn-gateway-bgp-overview.md)和[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。
*  如需強制通道的相關資訊，請參閱[關於強制通道](vpn-gateway-forced-tunneling-rm.md)。
*  如需高可用性主動-主動連線的相關資訊，請參閱[高可用性跨單位和 VNet 對 VNet 連線能力](vpn-gateway-highlyavailable.md)。
