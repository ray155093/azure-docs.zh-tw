<properties
    pageTitle="透過應用程式閘道設定 SSL 原則和端對端 SSL | Microsoft Azure"
    description="本文說明如何使用 Azure Resource Manager PowerShell 以應用程式閘道設定端對端 SSL"
    services="application-gateway"
    documentationCenter="na"
    authors="georgewallace"
    manager="carmonmills"
    editor="tysonn"/>

<tags
    ms.service="application-gateway"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="gwallace"/>


# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>使用 PowerShell 透過應用程式閘道設定 SSL 原則和端對端 SSL

## <a name="overview"></a>Overview

應用程式閘道支援為流量進行端對端加密。 應用程式閘道用來進行此作業的方法是，在應用程式閘道終止 SSL 連線。 閘道接著會對流量套用路由規則、重新加密封包，並根據所定義的路由規則將封包轉送至適當的後端。 任何來自 Web 伺服器的回應都會經歷相同的程序而回到使用者端。

應用程式閘道支援的另一個功能是停用特定 SSL 通訊協定版本。 應用程式閘道支援停用下列通訊協定版本：TLSv1.0、TLSv1.1 和 TLSv1.2。

![案例影像][scenario]

## <a name="scenario"></a>案例

在此案例中，您將了解如何使用 PowerShell 來建立使用端對端 SSL 的應用程式閘道。

此案例將會：

- 建立名為「appgw-rg」的資源群組
- 建立名為「appgwvnet」且含有 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
- 建立名為「appgwsubnet」和「appsubnet」的兩個子網路。
- 建立支援端對端 SSL 加密並停用特定 SSL 通訊協定的小型應用程式閘道。

## <a name="before-you-begin"></a>開始之前

若要對應用程式閘道設定端對端 SSL，則需要閘道憑證和後端伺服器憑證。 閘道憑證可用來加密和解密透過 SSL 傳送過來的流量。 閘道憑證必須採用「個人資訊交換」(pfx) 格式。 此檔案格式可允許將私密金鑰匯出，而應用程式閘道需要這個匯出的金鑰來執行流量的加密和解密。 

若要加密端對端 SSL，後端必須已加入到應用程式閘道的白名單。 只要將後端的公開憑證上傳至應用程式閘道，即可完成此動作。 這可確保應用程式閘道只會與已知的後端執行個體通訊，進而保護端對端通訊。 下列步驟會說明此程序：

## <a name="create-the-resource-group"></a>建立資源群組

本節將引導您建立資源群組，其中包含應用程式閘道。

### <a name="step-1"></a>步驟 1

登入您的 Azure 帳戶。

    Login-AzureRmAccount

### <a name="step-2"></a>步驟 2

選取要用於此案例的訂用帳戶。

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>步驟 3

建立資源群組 (若使用現有的資源群組，請略過此步驟)。

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立應用程式閘道的虛擬網路和子網路

下列範例會建立虛擬網路和兩個子網路。 一個子網路用來承載應用程式閘道。 另一個子網路則用於裝載 Web 應用程式的後端伺服器。

### <a name="step-1"></a>步驟 1

指派要用於應用程式閘道本身的子網路位址範圍。

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>步驟 2

指派要用於後端位址集區的位址範圍。

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>步驟 3

建立具有上述步驟所定義子網路的虛擬網路。

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>步驟 4

擷取要用於下列步驟的虛擬網路資源和子網路資源︰

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

建立要用於應用程式閘道的公用 IP 資源。 此公用 IP 位址會用於下列步驟。

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] 應用程式閘道不支援使用以定義之網域標籤建立的公用 IP 位址。 只支援具有動態建立之網域標籤的公用 IP 位址。 如果您需要讓應用程式閘道具有好記的 DNS 名稱，建議您使用 cname 記錄做為別名。

## <a name="create-an-application-gateway-configuration-object"></a>建立應用程式閘道組態物件

您必須先設定所有組態項目，再建立應用程式閘道。 下列步驟會建立應用程式閘道資源所需的組態項目。

### <a name="step-1"></a>步驟 1

建立應用程式閘道的 IP 組態，此設定會設定應用程式閘道所使用的子網路。 當應用程式閘道啟動時，它會從已設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>步驟 2

建立前端 IP 組態，此設定會將私人或公用 IP 位址對應到應用程式閘道的前端。 下一個步驟會將先前步驟中的公用 IP 位址關聯到前端 IP 組態。

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>步驟 3

使用後端 Web 伺服器的 IP 位址設定後端 IP 位址集區。 這些 IP 位址會接收來自前端 IP 端點的網路流量。 您可取代下列 IP 位址來新增自己的應用程式 IP 位址端點。

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] 完整網域名稱 (FQDN) 也是可取代後端伺服器 IP 位址的有效值。

### <a name="step-4"></a>步驟 4

設定公用 IP 端點的前端 IP 連接埠。 此連接埠是供使用者連接到的連接埠。

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>步驟 5

設定應用程式閘道憑證。 此憑證可用來解密和重新加密應用程式閘道上的流量。

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] 此範例會設定 SSL 連接所使用的憑證。 憑證必須是 .pfx 格式，而密碼則必須介於 4 到 12 個字元。

### <a name="step-6"></a>步驟 6

建立應用程式閘道的 HTTP 接聽程式。 指派要使用的前端 IP 組態、連接埠和 SSL 憑證。

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>步驟 7

上傳要在已啟用 SSL 的後端集區資源上使用的憑證。

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] 在此步驟中所提供的憑證應該是在後端中出示之 pfx 憑證的公開金鑰。 此步驟會將後端加入到應用程式閘道的白名單。 

### <a name="step-8"></a>步驟 8

設定應用程式閘道的後端 http 設定。 將前述步驟中上傳的憑證指派給 http 設定。

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>步驟 9

建立會設定負載平衡器行為的負載平衡器路由規則。 在此範例中，會建立基本的循環配置資源規則。

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>步驟 10

設定應用程式閘道的執行個體大小。  可用大小是 **Standard\_Small**、**Standard\_Medium** 和 **Standard\_Large**。  容量的可用值為 1 到 10。

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] 若要進行測試，可以選擇執行個體計數 1。 請務必了解 SLA 不涵蓋任何低於兩個執行個體的執行個體計數，因此不建議使用。 小型閘道適用於開發測試，不適合在生產環境中使用。

### <a name="step-11"></a>步驟 11

設定要在應用程式閘道上使用的 SSL 原則。 應用程式閘道支援停用特定 SSL 通訊協定版本的功能。

下列值是可以停用的通訊協定版本清單。

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

下列範例會停用 TLSv1\_0 和 TLSv1\_1

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1

## <a name="create-the-application-gateway"></a>建立應用程式閘道

使用上述所有步驟建立應用程式閘道。 建立閘道是很漫長的程序。

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>停用現有應用程式閘道上的 SSL 通訊協定版本

上述步驟會引導您建立具有端對端 SSL 的應用程式並停用特定的 SSL 通訊協定版本。 下列範例會停用現有應用程式閘道上的特定 SSL 原則。

### <a name="step-1"></a>步驟 1

擷取要更新的應用程式閘道。

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>步驟 2

定義 SSL 原則。 在下列範例中，會停用 TLSv1.0。

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0 -ApplicationGateway $gw

### <a name="step-3"></a>步驟 3

最後，更新閘道。 請務必注意，此最後一個步驟是漫長的工作。 完成時，應用程式閘道上便已設定端對端 SSL。

    $gw | Set-AzureRmApplicationGateway

## <a name="next-steps"></a>後續步驟

瀏覽 [Web 應用程式防火牆概觀](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png


<!--HONumber=Oct16_HO2-->


