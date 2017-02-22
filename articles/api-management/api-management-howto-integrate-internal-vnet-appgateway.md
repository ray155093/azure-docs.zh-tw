---
title: "如何搭配使用虛擬網路中的 Azure API 管理與應用程式閘道 | Microsoft Docs"
description: "了解如何以應用程式閘道 (WAF) 做為前端在內部虛擬網路中安裝和設定 Azure API 管理"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2017
ms.author: sasolank
translationtype: Human Translation
ms.sourcegitcommit: a87349518f7494dda49e52ed160509a4ffeb7775
ms.openlocfilehash: a58ec5f6d62d2b48d6cf85d997b2deac95310505


---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>整合內部 VNET 中的 API 管理與應用程式閘道 

##<a name="overview"></a>概觀
 
API 管理服務可以內部模式設定於虛擬網路中，因此只能從虛擬網路中加以存取。 Azure 應用程式閘道是一項 PAAS 服務，可提供第&7; 層負載平衡器。 它可做為反向 Proxy 服務，並在其供應項目中提供 Web 應用程式防火牆 (WAF)。

結合使用內部 VNET 中佈建的 API 管理與應用程式閘道前端，可實現下列案例︰

* 使用相同的 API 管理資源供內部取用者和外部取用者取用。
* 使用單一 API 管理資源，並在 API 管理中定義一部分 API 供外部取用者使用。
* 提供周全的方法來開啟和關閉從公用網際網路對 API 管理的存取權。 

##<a name="scenario"></a>案例
本文將討論如何使用單一 API 管理服務供內部和外部取用者使用，並將其同時做為內部部署和雲端 API 的單一前端。 接著，您也可以選擇使用應用程式閘道中提供的 PathBasedRouting 功能，只公開這些 API 的一部分 (以綠色醒目提示) 供外部取用。

在此設定中，您所有的 API 只能從虛擬網路內部進行管理。 內部取用者 (以橘色醒目提示) 則可存取所有的內部和外部 API。 內部取用者會受益於流量絕不會離開前往網際網路，而享有透過 ExpressRoute 線路所提供的高速傳輸。

![URL 路由](./media/api-management-using-with-vnet/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a>開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。 您可以從 **下載頁面** 的 [Windows PowerShell](https://azure.microsoft.com/downloads/)區段下載並安裝最新版本。
2. 建立虛擬網路，並為 API 管理和應用程式閘道建立個別子網路。 
3. 如果您想要為虛擬網路建立自訂 DNS 伺服器，請在開始部署之前建立，然後確定在虛擬網路之新子網路中所建立的虛擬機器可以解析所有 Azure 服務端點。

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>在 API 管理和應用程式閘道之間建立整合的所需條件為何？

* **後端伺服器集區︰**這是 API 管理服務的內部虛擬 IP 位址。
* **後端伺服器集區設定：** 每個集區都包括一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
* **前端連接埠：** 此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會到達此連接埠，然後重新導向至其中一個後端伺服器。
* **接聽程式：** 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，這些值都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
* **規則：** 規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。
* **自訂健全狀況探查︰**應用程式閘道預設會使用 IP 位址型探查，來找出 BackendAddressPool 中有哪些伺服器正在作用中。 API 管理服務只會回應具有正確主機標頭的要求，因此預設探查會失敗。 我們必須定義自訂的健全狀況探查，讓應用程式閘道知道服務正在執行，因此它應該轉送要求。
* **自訂網域憑證︰**若要從網際網路存取 API 管理，您需要進行主機名稱和應用程式閘道前端 DNS 名稱的 CNAME 對應。 相同的主機名稱標頭和憑證也會套用至 API 管理，因此當要求是透過應用程式閘道到來時，API 管理會將其視為有效要求並做出回應。
## <a name="overview-steps"></a>整合 API 管理和應用程式閘道的所需步驟 

1. 建立資源管理員的資源群組。
2. 建立應用程式閘道的虛擬網路、子網路和公用 IP。 為 API 管理建立其他子網路。
3. 在上面所建立的 VNET 子網路內，以內部 VNET 模式建立 API 管理服務。
4. 以自訂網域名稱更新 API 管理服務。
5. 建立應用程式閘道組態物件。
6. 建立應用程式閘道資源。
7. 將 API 管理 Proxy 主機名稱 Cname 到應用程式閘道資源的公用 DNS 名稱。

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源管理員的資源群組

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

### <a name="step-1"></a>步驟 1

登入 Azure

```powershell
Login-AzureRmAccount
```

系統會提示使用您的認證進行驗證。<BR>

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶並加以選取。

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>步驟 3

建立資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
New-AzureRmResourceGroup -Name apim-appGw-RG -Location "West US"
```
Azure Resource Manager 需要所有的資源群組指定一個位置。 這用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立應用程式閘道的虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路。

### <a name="step-1"></a>步驟 1

對要在建立虛擬網路時用於應用程式閘道的子網路變數指派位址範圍 10.0.0.0/24。

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgateway01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>步驟 2

對要在建立虛擬網路時用於 API 管理的子網路變數指派位址範圍 10.0.1.0/24。

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name apim01 -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>步驟 3

使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24 和 10.0.1.0/24，在美國西部 (West US) 區域的 **apim-appGw-RG** 資源群組中建立名為 **appgwvnet** 的虛擬網路。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName apim-appGw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>步驟 4

指派子網路變數以供後續步驟使用

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-in-internal-vnet-mode"></a>以內部 VNET 模式建立 API 管理服務

下列範例示範如何以內部 VNET 建立 API 管理服務

### <a name="step-1"></a>步驟 1
使用上面所建立的子網路 $apimsubnetdata 建立 API 管理虛擬網路物件。

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>步驟 2
在虛擬網路內建立 API 管理服務

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization Contoso -AdminEmail admin@contoso.com -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Premium"
```
上述命令執行成功之後，請參閱 [要存取內部 VNET API 管理服務所需的 DNS 組態][api-management-using-with-internal-vnet.md#apim-dns-configuration] 來存取它。

## <a name="update-api-management-service-with-custom-domain-name"></a>以自訂網域名稱更新 API 管理服務

現在，我們會將自訂網域名稱套用至我們想要從網際網路存取的 API 管理服務端點 Proxy。

### <a name="step-1"></a>步驟 1
將具有會顯示授權單位之私密金鑰的憑證上傳，以在網域中設定自訂網域，例如 `*.contoso.net`。 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file>
```

### <a name="step-2"></a>步驟 2
憑證上傳後，我們可以使用主機名稱 `api.contoso.net` 建立 Proxy 的主機名稱組態物件，因為憑證已顯示我們的授權單位在 `*.contoso.net` 網域上。 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" –PortalHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

在美國西部區域的 **apim-appGw-RG** 資源群組中建立公用 IP 資源 **publicIP01**。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

在服務啟動時，系統會將 IP 位址指派至應用程式閘道。

## <a name="create-application-gateway-configuration"></a>建立應用程式閘道組態

建立應用程式閘道之前，必須先設定所有組態項目。 下列步驟會建立應用程式閘道資源所需的組態項目。

### <a name="step-1"></a>步驟 1

建立名為 **gatewayIP01** 的應用程式閘道 IP 組態。 當「應用程式閘道」啟動時，它會從已設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>步驟 2

設定公用 IP 端點的前端 IP 連接埠。 此連接埠是供使用者連接到的連接埠。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```
### <a name="step-3"></a>步驟 3

利用公用 IP 端點設定前端 IP。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>步驟 4

設定應用程式閘道憑證。 此憑證可用來解密和重新加密應用程式閘道上的流量。

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>步驟 5

建立應用程式閘道的 HTTP 接聽程式。 指派要使用的前端 IP 組態、連接埠和 SSL 憑證。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>步驟 6

這裡我們要建立 API 管理服務 `ContosoApi` Proxy 網域端點的自訂探查。 `/status-0123456789abcdef` 路徑是裝載於所有 API 管理服務上的預設健全狀況端點。 主機名稱 `contosoapi.azure-api.net` 則是服務 `contosoapi` 在公用 Azure 中建立時所設定的預設 Proxy 主機名稱。

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name apimproxyprobe -Protocol Https -HostName "contosoapi.azure-api.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>步驟 7

上傳要在已啟用 SSL 的後端集區資源上使用的憑證。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>步驟 8

設定後端集區中流量的應用程式閘道設定 **apimPoolSetting**。 此步驟中也有適用於應用程式閘道要求之後端集區回應的逾時組態。 當後端回應達到逾時限制時，「應用程式閘道」就會取消要求。 這個值與僅適用於探查檢查之後端回應的探查逾時不同。

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name apimPoolSetting -Port 443 -Protocol Https -CookieBasedAffinity Disabled -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>步驟 9

以上面建立的 API 管理服務內部虛擬 IP 位址設定名為 **apimbackend** 的後端 IP 位址集區。

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name apimbackend -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>步驟 10
設定後端集區的 URL 規則路徑。 我們可以在 API 管理中設定多個 API，例如 `Echo API (/echo/), Calculator API (/calc/) etc.`，而且我們可以選擇從網際網路只允許存取 `Echo API`。 

下列範例會為將流量路由傳送至後端「apimProxyBackendPool」的「/echo/」路徑建立簡單的規則。

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting

$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule -DefaultBackendAddressPool $apimProxyBackendPool -DefaultBackendHttpSettings $apimPoolSetting
```

上面的步驟可確保只有「"/echo"」路徑的要求可以通過應用程式閘道。 對 API 管理中所設定之其他 API 的要求，則會在從網際網路存取時，從應用程式閘道擲回 404。 

### <a name="step-11"></a>步驟 11

建立規則設定。 這個步驟會設定應用程式閘道來使用 URL 路徑型路由。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-12"></a>步驟 12

設定執行個體數目和應用程式閘道的大小。 這裡我們使用 [WAF Sku][../application-gateway/application-gateway-webapplicationfirewall-overview.md] 來提升 API 管理資源的安全性。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

### <a name="step-13"></a>步驟 13

我們將 WAF 的模式設定為「防止」模式
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>建立應用程式閘道

利用上述步驟中的所有組態物件來建立應用程式閘道。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName apim-appGw-RG -Location "West US" -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-public-dns-name-of-application-gateway-resource"></a>將 API 管理 Proxy 主機名稱 Cname 到應用程式閘道資源的公用 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 若要設定前端 IP CNAME 記錄，使用連接至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 您應該使用應用程式閘道的 DNS 名稱來建立 CNAME 記錄，將我們在上面所設定的 Proxy 主機名稱 `api.contoso.net` 指向此 DNS 名稱。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -Name publicIP01
```

##<a name="summary"></a>摘要
VNET 中的 Azure API 管理服務提供了單一閘道介面，供您管理所有 API 的存取，不論它們是裝載在內部部署環境或雲端中。 您可以深入了解 API 的使用者和使用方式。 整合應用程式閘道與 API 管理可讓您更加彈性地設定要賦予網際網路存取權的 API，並提供 Web 應用程式防火牆來做為 API 管理執行個體的前端。

##<a name="next-steps"></a>後續步驟
* 深入了解 Azure 應用程式閘道
  * [應用程式閘道概觀](../application-gateway/application-gateway-introduction.md)
  * [應用程式閘道 Web 應用程式防火牆](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
* 深入了解 VNET 中的 API 管理
  * [在 VNET 中使用 API 管理](api-management-using-with-vnet.md)





<!--HONumber=Feb17_HO1-->


