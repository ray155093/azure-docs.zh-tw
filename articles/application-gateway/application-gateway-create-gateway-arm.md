---
title: "建立和管理 Azure 應用程式閘道 - PowerShell | Microsoft Docs"
description: "本頁面提供使用 Azure 資源管理員建立、設定、啟動和刪除 Azure 應用程式閘道的指示。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 8a0eb841b1a41a14e443b6ce93e6b8fb8985a803
ms.contentlocale: zh-tw
ms.lasthandoff: 05/02/2017


---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>使用 Azure 資源管理員建立、啟動或刪除應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 範本](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 應用程式閘道是第 7 層負載平衡器。 不論是在雲端或內部部署中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。
應用程式閘道提供許多應用程式傳遞控制器 (ADC) 功能，包括 HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、安全通訊端層 (SSL) 卸載、自訂健全狀態探查、多網站支援，以及許多其他功能。

若要尋找完整的支援功能清單，請瀏覽 [應用程式閘道概觀](application-gateway-introduction.md)

本文將逐步引導您完成建立、設定、啟動及刪除應用程式閘道的步驟。

> [!IMPORTANT]
> 使用 Azure 資源之前，請務必了解 Azure 目前有「資源管理員」和「傳統」兩種部署模型。 在使用任何 Azure 資源之前，請先確認您了解 [部署模型和工具](../azure-classic-rm.md) 。 您可以按一下本文頂端的索引標籤，檢視不同工具的文件。 本文件會討論如何使用 Azure Resource Manager 建立應用程式閘道。 若要使用傳統的版本，請移至 [使用 PowerShell 建立應用程式閘道傳統部署](application-gateway-create-gateway.md)。

## <a name="before-you-begin"></a>開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。 您可以從 **下載頁面** 的 [Windows PowerShell](https://azure.microsoft.com/downloads/)區段下載並安裝最新版本。
1. 如果您有現有的虛擬網路，請選取現有的空白子網路，或在現有的虛擬網路中建立子網路，僅供應用程式閘道使用。 您無法將應用程式閘道部署到與您打算部署於應用程式閘道後方的資源不同的虛擬網路。
1. 您要設定來使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是已指派公用 IP/VIP。

## <a name="what-is-required-to-create-an-application-gateway"></a>建立應用程式閘道需要什麼？

* **後端伺服器集區：** 後端伺服器的 IP 位址清單、FQDNs 或 NIC。 如果使用 IP 位址，它們應屬於虛擬網路子網路或是公用 IP/VIP。
* **後端伺服器集區設定：** 每個集區都包括一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
* **前端連接埠：** 此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會到達此連接埠，然後重新導向至其中一個後端伺服器。
* **接聽程式：** 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，這些值都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
* **規則：** 規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。

## <a name="create-an-application-gateway"></a>建立應用程式閘道

使用「Azure 傳統」和「Azure Resource Manager」的差別，在於您建立應用程式閘道和需設定項目的順序。

透過 Resource Manager，組成應用程式閘道的所有項目會個別進行設定，然後一併建立應用程式閘道資源。

以下是建立應用程式閘道所需的步驟。

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源管理員的資源群組

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

### <a name="step-1"></a>步驟 1

登入 Azure

```powershell
Login-AzureRmAccount
```

系統會提示使用您的認證進行驗證。

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>步驟 3

選擇要使用哪一個 Azure 訂用帳戶。

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>步驟 4

建立資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager 需要所有的資源群組指定一個位置。 此位置用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 **appgw-RG** 的資源群組，且位置為 **West US** (美國西部)。

> [!NOTE]
> 如果您需要為應用程式閘道設定自訂探查，請造訪：[使用 PowerShell 建立具有自訂探查的應用程式閘道](application-gateway-create-probe-ps.md)。 請參閱 [自訂探查和健全狀況監視](application-gateway-probe-overview.md) 以取得詳細資訊。

## <a name="create-a-virtual-network-and-a-subnet"></a>建立虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路。 這個範例會建立應用程式閘道的 VNET。 應用程式閘道需要它自己的子網路，因此針對應用程式閘道建立的子網路會小於 VNET 位址空間。 使用允許其他資源的較小子網路，包括但不限於要在相同 VNET 中設定的 web 伺服器。

### <a name="step-1"></a>步驟 1

指派用於建立虛擬網路的位址範圍 10.0.0.0/24 給子網路變數。 這個步驟會建立用於下一個範例中之應用程式閘道的子網路組態物件。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>步驟 2

使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24，在美國西部 (West US) 區域的 **appgw-rg** 資源群組中建立名為 **appgwvnet** 的虛擬網路。 此步驟會使用可供應用程式閘道放置的單一子網路來完成 VNET 設定。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>步驟 3

指派下一個步驟的子網路變數，此變數會在未來的步驟中傳遞至 `New-AzureRMApplicationGateway` Cmdlet。

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address"></a>建立公用 IP 位址

在美國西部區域的 **appgw-rg** 資源群組中建立公用 IP 資源 **publicIP01**。 應用程式閘道可以使用公用 IP 位址、內部 IP 位址或兩者來接收進行負載平衡的要求。  此範例中僅使用公用 IP 位址。 在下列範例中，未設定 DNS 名稱以建立公用 IP 位址。  應用程式閘道在不支援公用 IP 位址上自訂 DNS 名稱。  如果公用端點需要自訂名稱，應該建立 CNAME 記錄以指向針對公用 IP 位址自動產生的 DNS 名稱。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> 在服務啟動時，系統會將 IP 位址指派至應用程式閘道。

## <a name="create-the-application-gateway-configuration-objects"></a>建立應用程式閘道組態物件

建立應用程式閘道之前，必須先設定所有組態項目。 下列步驟會建立應用程式閘道資源所需的組態項目。

### <a name="step-1"></a>步驟 1

建立名為 **gatewayIP01** 的應用程式閘道 IP 組態。 當「應用程式閘道」啟動時，它會從已設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>步驟 2

設定名為 **pool01** 的後端 IP 位址集區，其 IP 位址有 **pool1**。 這些 IP 位址會用來託管要受應用程式閘道保護的 web 應用程式資源。 這些後端集區成員都由探查 (無論是基本探查或自訂探查) 驗證為健康狀態。  當要求進入應用程式閘道時，流量便會路由至它們。 後端集區可在應用程式閘道內供多個規則使用，這表示一個後端集區可用於位在相同主機上的多個 web 應用程式。

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

此範例中有兩個後端集區，根據 URL 路徑路由傳送網路流量。 有一個集區會接收來自 URL 路徑 "/video" 的流量，而另一個集區會接收來自路徑 "/image" 的流量。 取代上述 IP 位址來新增自己的應用程式 IP 位址端點。

### <a name="step-3"></a>步驟 3

為後端集區中負載平衡的網路流量設定應用程式閘道設定 **poolsetting01**。 每個後端集區都可以有它自己的後端集區設定。  規則會使用後端 HTTP 設定，將流量路由到正確的後端集區成員。 後端的 HTTP 設定會決定將流量傳送到後端集區成員時使用的通訊協定和連接埠。 Cookie 型工作階段也是由後端 HTTP 設定決定。  啟用時，Cookie 型工作階段親和性會如每個封包的先前要求將流量至相同的後端。

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>步驟 4

設定應用程式閘道的前端連接埠。 接聽程式會使用前端連接埠組態物件來定義應用程式閘道會接聽哪個連接埠以取得接聽程式上的流量。

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>步驟 5

利用公用 IP 端點設定前端 IP。 接聽程式會使用前端 IP 組態物件將對外 IP 位址與接聽程式相關聯。

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>步驟 6

設定接聽程式。 這個步驟會針對用來接收連入網路流量的公用 IP 位址和連接埠設定接聽程式。 下列範例會採用先前設定的前端 IP 組態、前端連接埠組態及通訊協定 (http 或 https)，並設定接聽程式。 在此範例中，接聽程式會接聽稍早建立的公用 IP 位址上連接埠 80 的 HTTP 流量。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>步驟 7

建立名為 **rule01** 的負載平衡器路由規則，以設定負載平衡器的行為。 此規則由後端集區設定、接聽程式和先前步驟中建立的後端集區所組成。 根據定義的流量，準則會路由至適當的後端。

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting01 -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>步驟 8

設定執行個體數目和應用程式閘道的大小。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> **InstanceCount** 的預設值是 2，且最大值是 10。 GatewaySize  的預設值是 Medium。 您可以在 **Standard_Small**、**Standard_Medium** 及 **Standard_Large** 之間選擇。

## <a name="create-the-application-gateway"></a>建立應用程式閘道

利用上述步驟中的所有組態項目來建立應用程式閘道。 此範例中的應用程式閘道稱為 **appgwtest**。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

從附加至應用程式閘道的公用 IP 資源，擷取應用程式閘道的 DNS 和 VIP 詳細資料。

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-the-application-gateway"></a>刪除應用程式閘道

若要刪除應用程式閘道，請遵循下列步驟：

### <a name="step-1"></a>步驟 1

取得應用程式閘道物件，並關聯至變數 `$getgw`。

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>步驟 2

使用 `Stop-AzureRmApplicationGateway` 停止應用程式閘道。

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

當應用程式閘道處於已停止狀態之後，使用 `Remove-AzureRmApplicationGateway` Cmdlet 來移除服務。

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> **-force** 參數可用來隱藏移除確認訊息。

若要確認已移除服務，您可以使用 `Get-AzureRmApplicationGateway` Cmdlet。 這不是必要步驟。

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 [在 Azure 中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 若要尋找動態建立的 DNS 名稱，使用連結至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 應用程式閘道的 DNS 名稱應該用來建立將兩個 Web 應用程式指向此 DNS 名稱的 CNAME 記錄。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="delete-all-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請完成下列步驟︰

```powershell
Remove-AzureRmResourceGroup -Name appgw-RG
```

## <a name="next-steps"></a>後續步驟

如果您想要設定 SSL 卸載，請造訪：[設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想要將應用程式閘道設為與內部負載平衡器搭配使用，請造訪：[建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

如果您想進一步了解一般負載平衡選項，請造訪：

* [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)


