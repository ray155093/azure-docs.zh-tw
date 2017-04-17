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
ms.date: 04/04/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d6f4caebeeced1286f24dd5fcb4f5fc7d8591785
ms.lasthandoff: 04/12/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>在 Azure 入口網站中建立站對站連線

網站間 (S2S) VPN 閘道連線是透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道建立的連線。 此類型的連線需要位於內部部署的 VPN 裝置，其具有指派的公用 IP 位址且不是位於 NAT 後方。 網站間連線可以用於跨單位與混合式組態。

![站對站 VPN 閘道跨單位連線圖表](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

本文逐步引導您使用 Azure Resource Manager 部署模型 和 Azure 入口網站，以建立虛擬網路及連至內部部署網路的網站間 VPN 閘道連線。 您也可以使用不同的部署工具來建立這個組態，或如果是傳統部署模型，從下列清單中選取不同選項來建立這個組態︰

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [傳統 - 傳統入口網站](vpn-gateway-site-to-site-create.md)
>
>


#### <a name="additional-configurations"></a>其他組態
如果您想要將 VNet 連接在一起，但不要建立對內部部署位置的連線，請參閱 [設定 VNet 對 VNet 連線](vpn-gateway-vnet-vnet-rm-ps.md)。 如果您想要網站間連接新增至已經有連接的 VNet，請參閱[將 S2S 連接新增至已有現有 VPN 閘道連接的 VNet](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

在開始設定之前，請確認您具備下列項目：

* 相容的 VPN 裝置 (以及能夠進行設定的人員)。 請參閱 [關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。
* 如果不熟悉位於內部部署網路的 IP 位址空間，您需要與能夠提供那些詳細資料的人協調。 站對站連線不能有重疊的位址空間。
* 您的 VPN 裝置對外開放的公用 IP 位址。 此 IP 位址不能位於 NAT 後方。
* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](http://azure.microsoft.com/pricing/free-trial)。

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
您必須為 VPN 閘道建立閘道子網路。 閘道子網路包含 VPN 閘道服務會使用的 IP 位址。 可能的話，最好使用 /28 或 /27 的 CIDR 區塊來建立閘道子網路。 這會確保您有足夠的 IP 位址來因應可能的未來閘道功能。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4.建立虛擬網路閘道

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5.建立區域網路閘道
區域網路閘道會參考您的內部部署位置。 您為區域網路閘道指定的設定會決定路由傳送至內部部署 VPN 裝置的位址空間。

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6.設定 VPN 裝置
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7.建立網站間 VPN 連線

在此步驟中，您會在虛擬網路閘道與內部部署 VPN 裝置之間建立站對站 VPN 連線。 開始這一節之前，請確認虛擬網路閘道與區域網路閘道已完成建立。

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8.驗證 VPN 連線

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>後續步驟
*  一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。
*  如需 BGP 的相關資訊，請參閱 [BGP 概觀](vpn-gateway-bgp-overview.md)和[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。


