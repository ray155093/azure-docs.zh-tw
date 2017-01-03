---
title: "在資源管理員中使用 PowerShell 建立應用程式閘道的自訂探查 | Microsoft Docs"
description: "了解如何在資源管理員中使用 PowerShell 建立應用程式閘道的自訂探查"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: d883cdc007beaf17118c6b6ddbc8345c3bfb5ef2
ms.openlocfilehash: c766763e4633c4905595ae15aca0679b5ecaf5bd


---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>使用 Azure 資源管理員的 PowerShell 建立 Azure 應用程式閘道的自訂探查

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 傳統 PowerShell](application-gateway-create-probe-classic-ps.md)
> 
> 

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

> [!NOTE]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。  本文涵蓋之內容包括使用 Resource Manager 部署模型，Microsoft 建議大部分的新部署使用此模型，而不是[傳統部署模型](application-gateway-create-probe-classic-ps.md)。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>步驟 1

使用 Login-AzureRmAccount 進行驗證。

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>步驟 3

選擇要使用哪一個 Azure 訂用帳戶。 <BR>

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

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立應用程式閘道的虛擬網路和子網路

下列步驟會建立應用程式閘道的虛擬網路和子網路。

### <a name="step-1"></a>步驟 1

將位址範圍 10.0.0.0/24 指派給用於建立虛擬網路的子網路變數。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>步驟 2

使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24，在美國西部 (West US) 區域的 **appgw-rg** 資源群組中建立名為 **appgwvnet** 的虛擬網路。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>步驟 3

針對建立應用程式閘道的後續步驟，指派子網路變數。

```powershell
$subnet = $vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

在美國西部區域的 **appgw-rg** 資源群組中建立公用 IP 資源 **publicIP01**。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>使用自訂探查建立應用程式閘道組態物件

您先設定所有組態項目，再建立應用程式閘道。 下列步驟會建立應用程式閘道資源所需的組態項目。

### <a name="step-1"></a>步驟 1

建立名為 **gatewayIP01** 的應用程式閘道 IP 組態。 當「應用程式閘道」啟動時，它會從已設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>步驟 2

設定名為 **pool01** 的後端 IP 位址集區，其 IP 位址有 **134.170.185.46、134.170.188.221、134.170.185.50**。 這些值為 IP 位址，可接收來自前端 IP 端點的網路流量。 您需取代上述 IP 位址來新增自己的應用程式 IP 位址端點。

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

### <a name="step-3"></a>步驟 3

此步驟會設定自訂探查。

所使用的參數如下：

* **Interval** - 以秒為單位設定探查間隔檢查。
* **Timeout** - 定義 HTTP 回應檢查的探查逾時。
* **Hostname 和 path** - 應用程式閘道所叫用以判斷執行個體健全狀態的完整 URL 路徑。 例如，若您擁有網站 **http://contoso.com/**，則可以為 **http://contoso.com/path/custompath.htm** 設定自訂探查，以便讓探查檢查有成功的 HTTP 回應。
* **UnhealthyThreshold** - 要將後端執行個體標記為「狀況不良」所需的失敗 HTTP 回應次數。

```powershell
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-4"></a>步驟 4

設定後端集區中流量的應用程式閘道設定 **poolsetting01**。 此步驟中也有適用於應用程式閘道要求之後端集區回應的逾時組態。 當後端回應達到逾時限制時，「應用程式閘道」就會取消要求。 這個值與僅適用於探查檢查之後端回應的探查逾時不同。

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80
```

### <a name="step-5"></a>步驟 5

為公用 IP 端點設定前端 IP 連接埠 **frontendport01**。

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80
```

### <a name="step-6"></a>步驟 6

建立名為 **fipconfig01** 的前端 IP 組態，並將公用 IP 位址與前端 IP 組態產生關聯。

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-7"></a>步驟 7

建立名為 **listener01** 的接聽程式，並將前端連接埠與前端 IP 組態產生關聯。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-8"></a>步驟 8

建立名為 **rule01** 的負載平衡器路由規則，以設定負載平衡器的行為。

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-9"></a>步驟 9

設定應用程式閘道的執行個體大小。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```


> [!NOTE]
> **InstanceCount** 的預設值是 2，且最大值是 10。 GatewaySize  的預設值是 Medium。 您可以在 **Standard_Small**、**Standard_Medium** 及 **Standard_Large** 之間選擇。
> 
> 

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>使用 New-AzureRmApplicationGateway 建立應用程式閘道

以上述步驟中的所有組態項目建立應用程式閘道。 此範例中的應用程式閘道稱為 **appgwtest**。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>將探查新增至現有應用程式閘道

要將自訂探查新增至現有應用程式閘道需要四個步驟。

### <a name="step-1"></a>步驟 1

使用 `Get-AzureRmApplicationGateway` 將應用程式閘道資源載入至 PowerShell 變數。

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>步驟 2

將探查新增至現有閘道組態。

```powershell
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

範例中會將自訂探查設定為每 30 秒檢查一次 URL 路徑 contoso.com/path/custompath.htm。 所設定的逾時臨界值是 120 秒，最多只能有 8 個失敗的探查要求。

### <a name="step-3"></a>步驟 3

使用 `Set-AzureRmApplicationGatewayBackendHttpSettings` 將探查新增至後端集區設定組態和逾時。

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120
```

### <a name="step-4"></a>步驟 4

使用 `Set-AzureRmApplicationGateway` 將組態儲存至應用程式閘道。

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>從現有應用程式閘道中移除探查

以下是從現有應用程式閘道中移除自訂探查的步驟。

### <a name="step-1"></a>步驟 1

使用 `Get-AzureRmApplicationGateway` 將應用程式閘道資源載入至 PowerShell 變數。

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```


### <a name="step-2"></a>步驟 2

使用 `Remove-AzureRmApplicationGatewayProbeConfig` 移除應用程式閘道中的探查組態。

```powershell
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name
```

### <a name="step-3"></a>步驟 3

使用 `Set-AzureRmApplicationGatewayBackendHttpSettings` 更新用來移除探查和逾時設定的後端集區設定。

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled
```

### <a name="step-4"></a>步驟 4

使用 `Set-AzureRmApplicationGateway` 將組態儲存至應用程式閘道。 

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 [在 Azure 中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 做法是使用連接至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 應用程式閘道的 DNS 名稱應該用來建立將兩個 Web 應用程式指向此 DNS 名稱的 CNAME 記錄。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。

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

## <a name="next-steps"></a>後續步驟

請瀏覽 [設定 SSL 卸載](application-gateway-ssl-arm.md)




<!--HONumber=Nov16_HO4-->


