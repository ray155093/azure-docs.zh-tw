---
title: "在新的或現有的應用程式閘道上設定 Web 應用程式防火牆 | Microsoft Docs"
description: "本文提供如何在現有的或新的應用程式閘道上開始使用 Web 應用程式防火牆的指引。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 486ed096bba7accfb3ca54aa97fb0b57b756d291
ms.openlocfilehash: ba7bff77a52c8e432b175d1db3d9dec66ec36a2b


---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>在新的或現有的應用程式閘道上設定 Web 應用程式防火牆

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-web-application-firewall-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-web-application-firewall-powershell.md)
> 
> 

Azure 應用程式閘道中的 Web 應用程式防火牆 (WAF) 可保護 Web 應用程式，不致遭受常見的 Web 型攻擊，例如 SQL 插入式攻擊、跨網站指令碼攻擊和工作階段攔截。

Azure 應用程式閘道是第 7 層負載平衡器。 不論是在雲端或內部部署中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。 應用程式提供許多應用程式傳遞控制器 (ADC) 功能，包括 HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、安全通訊端層 (SSL) 卸載、自訂健全狀態探查、多網站支援，以及許多其他功能。 若要尋找完整的支援功能清單，請瀏覽應用程式閘道概觀

下列文章說明如何[將 Web 應用程式防火牆新增至現有應用程式閘道](#add-web-application-firewall-to-an-existing-application-gateway)和[建立使用 Web 應用程式防火牆的應用程式閘道](#create-an-application-gateway-with-web-application-firewall)。

![案例影像][scenario]

## <a name="waf-configuration-differences"></a>WAF 組態差異

如果您已經讀過 [使用 PowerShell 建立應用程式閘道](application-gateway-create-gateway-arm.md)，您會了解建立應用程式閘道時要設定的 SKU 設定。 在應用程式閘道上設定 SKU 時，WAF 會提供其他可定義的設定。 您對應用程式閘道本身不會進行任何其他變更。

**SKU** - 沒有 WAF 的一般應用程式閘道支援 **Standard\_Small**、**Standard\_Medium** 和 **Standard\_Large** 大小。 WAF 推出後另外出現了兩個 SKU，分別是 **WAF\_Medium** 和 **WAF\_Large**。 小型應用程式閘道不支援 WAF。

**層級** - 可用的值為**標準**或 **WAF**。 使用 Web 應用程式防火牆時，必須選擇 **WAF** 。

**模式** - 此設定是 WAF 的模式。 允許的值為**偵測**和**防止**。 當 WAF 設定為偵測模式時，所有威脅會儲存在記錄檔中。 在防止模式時仍會記錄事件，但攻擊者會從應用程式閘道收到 403 未經授權的回應。

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>對現有應用程式閘道新增 Web 應用程式防火牆

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

### <a name="step-1"></a>步驟 1

登入您的 Azure 帳戶。

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>步驟 2

選取要用於此案例的訂用帳戶。

```powershell
Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>步驟 3

擷取要將 Web 應用程式防火牆新增到的閘道。

```powershell
$gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
```

### <a name="step-4"></a>步驟 4

設定 Web 應用程式防火牆 SKU。 可用大小為 **WAF\_Large** 和 **WAF\_Medium**。 使用 Web 應用程式防火牆時，必須為 **WAF** 層，設定 SKU 時必須確認容量。

```powershell
$gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
```

### <a name="step-5"></a>步驟 5

如下列範例所定義地設定 WAF 設定︰

**WafMode** 設定的可用值是「防止」和「偵測」。

```powershell
$gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
```

### <a name="step-6"></a>步驟 6

使用上一個步驟所定義的設定來更新應用程式閘道。

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

此命令會使用 Web 應用程式防火牆更新應用程式閘道。 建議您檢視 [應用程式閘道診斷](application-gateway-diagnostics.md) 來了解如何檢視應用程式閘道中的記錄檔。 由於 WAF 的安全性本質，您必須定期檢閱記錄檔，以了解 Web 應用程式的安全性狀態。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>使用 Web 應用程式防火牆來建立應用程式閘道

下列步驟會帶您從頭到尾完成使用 Web 應用程式防火牆來建立應用程式閘道的整個程序。

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
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-4"></a>步驟 4

建立資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager 需要所有的資源群組指定一個位置。 此位置用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 "appgw-RG" 的資源群組，且位置為美國西部 ("West US")。

> [!NOTE]
> 如果您需要為應用程式閘道設定自訂探查，請參閱 [使用 PowerShell 建立具有自訂探查的應用程式閘道](application-gateway-create-probe-ps.md)。 請參閱 [自訂探查和健全狀況監視](application-gateway-probe-overview.md) 以取得詳細資訊。
> 
> 

### <a name="step-5"></a>步驟 5

指派要用於應用程式閘道本身的子網路位址範圍。

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> 應用程式的子網路至少應有 28 個遮罩位元。 此值會讓應用程式閘道執行個體的子網路中留有 10 個可用位址。 若使用較小的子網路，您可能無法在日後為應用程式閘道新增更多執行個體。
> 
> 

### <a name="step-6"></a>步驟 6

指派要用於後端位址集區的位址範圍。

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-7"></a>步驟 7

使用 [建立資源群組](#create-the-resource-group)

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-8"></a>步驟 8

擷取要用於下列步驟的虛擬網路資源和子網路資源︰

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

### <a name="step-9"></a>步驟 9

建立要用於應用程式閘道的公用 IP 資源。 此公用 IP 位址會用於下列其中一個步驟：

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> 應用程式閘道不支援使用以定義之網域標籤建立的公用 IP 位址。 只支援具有動態建立之網域標籤的公用 IP 位址。 如果您需要讓應用程式閘道具有好記的 DNS 名稱，建議您使用 cname 記錄做為別名。
> 
> 

### <a name="step-10"></a>步驟 10

您必須先設定所有組態項目，再建立應用程式閘道。 下列步驟會建立應用程式閘道資源所需的組態項目。

建立應用程式閘道的 IP 組態，此組態會設定應用程式閘道所使用的子網路。 當「應用程式閘道」啟動時，它會從已設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-11"></a>步驟 11

使用後端 Web 伺服器的 IP 位址設定後端 IP 位址集區。 這些 IP 位址會接收來自前端 IP 端點的網路流量。 您可取代下列 IP 位址來新增自己的應用程式 IP 位址端點。

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

### <a name="step-12"></a>步驟 12

上傳要在已啟用 SSL 的後端集區資源上使用的憑證。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-13"></a>步驟 13

設定應用程式閘道的後端 http 設定。 將前述步驟中上傳的憑證指派給 http 設定。

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-14"></a>步驟 14

設定公用 IP 端點的前端 IP 連接埠。 此連接埠是供使用者連接到的連接埠。

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-15"></a>步驟 15

建立前端 IP 組態，此設定會將私人或公用 IP 位址對應到應用程式閘道的前端。 下一個步驟會將先前步驟中的公用 IP 位址關聯到前端 IP 組態。

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-16"></a>步驟 16

設定應用程式閘道憑證。 此憑證可用來解密和重新加密應用程式閘道上的流量。

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-17"></a>步驟 17

建立應用程式閘道的 HTTP 接聽程式。 指派要使用的前端 IP 組態、連接埠和 SSL 憑證。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-18"></a>步驟 18

建立會設定負載平衡器行為的負載平衡器路由規則。 在此範例中，會建立基本的循環配置資源規則。

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-19"></a>步驟 19

設定應用程式閘道的執行個體大小。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

> [!NOTE]
> 您可以選擇 **WAF\_Medium** 或 **WAF\_Large**，使用 WAF 時的層級永遠是 **WAF**。 容量是介於 1 到 10 之間的任何數字。
> 
> 

### <a name="step-20"></a>步驟 20

設定 WAF 的模式，可接受的值為**防止**和**偵測**。

```powershell
$config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"
```

### <a name="step-21"></a>步驟 21

利用上述步驟中的所有組態項目來建立應用程式閘道。 此範例中的應用程式閘道名為 "appgwtest"。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
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

請造訪 [應用程式閘道診斷](application-gateway-diagnostics.md)

[案例]: ./media/application-gateway-web-application-firewall-powershell/scenario.png



<!--HONumber=Nov16_HO3-->


