---
title: "將內部部署網路連線至 Azure 虛擬網路：站對站 VPN：CLI | Microsoft Docs"
description: "透過公用網際網路建立從內部部署網路至 Azure 虛擬網路之 IPsec 連線的步驟。 這些步驟可協助您使用 CLI 建立跨單位的站對站 VPN 閘道連線。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c3563f3a3fa46d40ba02fe97b3b0ebe3c45caddd
ms.lasthandoff: 04/26/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>使用 CLI 建立具有站對站 VPN 連線的虛擬網路

本文說明如何使用 Azure CLI 來建立從內部部署網路到 VNet 的站對站 VPN 閘道連線。 本文中的步驟適用於 Resource Manager 部署模型。 您也可從下列清單中選取不同的選項，以使用不同的部署工具或部署模型來建立此組態：

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [傳統 - 傳統入口網站](vpn-gateway-site-to-site-create.md)
> 
>


![站對站 VPN 閘道跨單位連線圖表](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

站對站 VPN 閘道連線可用來透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道，將內部部署網路連線到 Azure 虛擬網路。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 確認您想要使用 Resource Manager 部署模型。 [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* 相容的 VPN 裝置 (以及能夠進行設定的人員)。 如需相容 VPN 裝置和裝置組態的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。
* 您的 VPN 裝置對外開放的公用 IPv4 位址。 此 IP 位址不能位於 NAT 後方。
* 如果您不熟悉位於內部部署網路組態的 IP 位址範圍，您需要與能夠提供那些詳細資料的人協調。 當您建立此組態時，您必須指定 IP 位址範圍的首碼，以供 Azure 路由傳送至您的內部部署位置。 內部部署網路的子網路皆不得與您所要連線的虛擬網路子網路重疊。 
* CLI 命令的最新版本 (2.0 版或更新版本)。 如需關於安裝 CLI 命令的資訊，請參閱[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="example-values"></a>值的範例

您可以使用下列值來建立測試環境，或參考這些值來進一步了解本文中的範例：

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1.登入 Azure

使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli
az login
```

如果您有多個 Azure 訂用帳戶，系統會列出帳戶的所有訂用帳戶。

```azurecli
Az account list --all
```

指定您要使用的訂用帳戶。

```azurecli
Az account set --subscription <replace_with_your_subscription_id>
```

## <a name="2-create-a-resource-group"></a>2.建立資源群組

下列範例會在「eastus」位置建立名為「TestRG1」的資源群組。 如果您在該區域中已有想要用來建立 VNet 的資源群組，您可以改為使用該資源群組。

```azurecli
az group create -n TestRG1 -l eastus
```

## <a name="VNet"></a>3.建立虛擬網路

如果您還沒有虛擬網路，請建立一個。 在建立虛擬網路時，請確定您指定的位址空間沒有與您在內部部署網路上所擁有的任何位址空間重疊。 

下列範例會建立名為「TestVNet1」的虛擬網路和「Subnet1」子網路。

```azurecli
az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.12.0.0/16 -l eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4.<a name="gwsub"></a>建立閘道子網路

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

對於此組態，您也需要有閘道子網路。 虛擬網路閘道所使用的閘道子網路，會包含可供 VPN 閘道服務使用的 IP 位址。 當您建立閘道子網路時，請將它命名為「GatewaySubnet」。 如果您將其命名為其他名字，您會建立子網路，但 Azure 不會將它視為閘道器子網路。

您所要建立的 VPN 閘道組態會決定您需要為閘道子網路指定的大小。 雖然您可以建立小至 /29 的閘道子網路，但我們會建議您選取 /27 或 /28，以建立包含更多位址的較大子網路。 使用較大的閘道子網路可讓您擁有足夠的 IP 位址，以因應未來可能的組態。


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 -n GatewaySubnet -g TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5.建立區域網路閘道

區域網路閘道通常是指您的內部部署位置。 請賦予網站可供 Azure 參考的名稱，然後指定您想要與其建立連線之內部部署 VPN 裝置的 IP 位址。 也請指定 IP 位址首碼，以供系統透過 VPN 閘道路由至 VPN 裝置。 您指定的位址首碼是位於內部部署網路上的首碼。 如果您的內部部署網路有所變更，您可以輕鬆地更新首碼。

輸入下列值：

* --gateway-ip-address 是內部部署 VPN 裝置的 IP 位址。 VPN 裝置不能位於 NAT 後方。
* --local-address-prefixes 是內部部署位址空間。

下列範例示範如何新增具有多個位址首碼的區域網路閘道：

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6.要求公用 IP 位址

請要求公用 IP 位址，以配置給虛擬網路 VPN 閘道。 這是您的 VPN 裝置所設定要連線的 IP 位址。

Resource Manager 部署模型的虛擬網路閘道目前僅支援使用動態配置方法的公用 IP 位址。 但是，這不代表 IP 位址會變更。 VPN 閘道 IP 位址只會在刪除或重新建立閘道時變更。 虛擬網路閘道公用 IP 位址不會因為重新調整、重設或 VPN 閘道的其他內部維護/升級而變更。 

```azurecli
az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7.建立 VPN 閘道

建立虛擬網路 VPN 閘道。 建立 VPN 閘道可能需要 45 分鐘以上的時間才能完成。

輸入下列值：

* 網站間組態的 --gateway-type 是 Vpn。 閘道器類型永遠是您實作的組態的特定類型。 如需詳細資訊，請參閱[閘道類型](vpn-gateway-about-vpn-gateway-settings.md#gwtype)
* --vpn-type 可以是 RouteBased (在某些文件中稱為動態閘道器)，或 PolicyBased (在某些文件中稱為靜態閘道器)。 這是您所連線裝置之需求的特有設定。 如需 VPN 閘道類型的詳細資訊，請參閱[關於 VPN 閘道組態設定](vpn-gateway-about-vpn-gateway-settings.md#vpntype)。
* --sku 可以是 Basic、Standard 或 HighPerformance。 某些 SKU 有組態限制。 如需詳細資訊，請參閱[閘道 SKU](vpn-gateway-about-vpngateways.md#gateway-skus)。

執行此命令之後，您不會看到任何意見反應或輸出。 大約需要 45 分鐘的時間來建立閘道。

```azurecli
az network vnet-gateway create -n VNet1GW --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8.設定 VPN 裝置

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
若要尋找虛擬網路閘道的公用 IP 位址，請使用下面範例，將這些值替換為自己的值。 為方便閱讀，系統會將輸出格式化為以資料表格式顯示公用 IP 的清單。

  ```azurecli
  az network public-ip list -g TestRG1 -o table
  ```

## <a name="CreateConnection"></a>9.建立 VPN 連線

您會在虛擬網路閘道與內部部署 VPN 裝置之間建立站對站 VPN 連線。 請特別注意共用金鑰值，此值必須符合您的 VPN 裝置所設定的共用金鑰值。

```azurecli
az network vpn-connection create -n VNet1toSite2 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

過一會兒，連接將會建立。

## <a name="toverify"></a>10.驗證 VPN 連線

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

如果您想要使用其他方法來確認您的連線，請參閱[確認 VPN 閘道連線](vpn-gateway-verify-connection-resource-manager.md)。

## <a name="common-tasks"></a>常見工作

### <a name="to-view-local-network-gateways"></a>檢視區域網路閘道

```azurecli
az network local-gateway list --resource-group TestRG1
```

### <a name="modify"></a>修改區域網路閘道的 IP 位址首碼
如果您需要變更區域網路閘道首碼，請使用下列指示。 每次進行變更時，您都必須指定完整的首碼清單，而不只是指定您想要變更的首碼。

- **如果您已指定連線**，請使用下列範例。 指定由現有首碼以及您想要新增之首碼所組成的完整首碼清單。 在此範例中，10.0.0.0/24 和 20.0.0.0/24 已存在。 我們要新增首碼 30.0.0.0/24 和 40.0.0.0/24。

  ```azurecli
  az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
  ```

- **如果您未指定連線**，請使用您在建立區域網路閘道時所使用的同一個命令。 您也可以使用此命令來更新 VPN 裝置的閘道 IP 位址。 請在您還未擁有連線時，才使用這個命令。 在此範例中，10.0.0.0/24、20.0.0.0/24、30.0.0.0/24 和 40.0.0.0/24 已存在。 我們只指定我們想要保留的首碼。 在此案例中為 10.0.0.0/24 和 20.0.0.0/24。

  ```azurecli
  az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
  ```

### <a name="modifygwipaddress"></a>修改區域網路閘道的閘道 IP 位址

此組態中的 IP 位址是您要連線之 VPN 裝置的 IP 位址。 如果 VPN 裝置的 IP 位址有所變更，您可以修改此值。 即使已有閘道連線，您仍可變更 IP 位址。

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 -n Site2 -g TestRG1
```

在檢視結果時，請確認其中已包括 IP 位址首碼。

  ```azurecli
  "localNetworkAddressSpace": { 
    "addressPrefixes": [ 
      "10.0.0.0/24", 
      "20.0.0.0/24", 
      "30.0.0.0/24" 
    ] 
  }, 
  "location": "eastus", 
  "name": "Site2", 
  "provisioningState": "Succeeded",  
  ```

### <a name="to-view-the-virtual-network-gateway-public-ip-address"></a>檢視虛擬網路閘道的公用 IP 位址

若要尋找虛擬網路閘道的公用 IP 位址，請使用下列範例。 為方便閱讀，系統會將輸出格式化為以資料表格式顯示公用 IP 的清單。

```azurecli
az network public-ip list -g TestRG1 -o table
```

### <a name="to-verify-the-shared-key-values"></a>確認共用金鑰值

請確認共用金鑰值和您用於 VPN 裝置組態的值相同。 如果不同，請使用裝置的值再次執行連線，或是以傳回的值將裝置更新。 這兩個值必須相符。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 -g TestRG1
```

## <a name="next-steps"></a>後續步驟

*  一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。
* 如需 BGP 的相關資訊，請參閱 [BGP 概觀](vpn-gateway-bgp-overview.md)和[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。
* 如需強制通道的相關資訊，請參閱[設定強制通道](vpn-gateway-forced-tunneling-rm.md)。
* 如需 Azure CLI 網路命令的清單，請參閱 [Azure CLI](https://docs.microsoft.com/cli/azure/network)。
