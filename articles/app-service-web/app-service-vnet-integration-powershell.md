---
title: 使用 PowerShell 將應用程式連接到虛擬網路
description: 如何使用 PowerShell 連接以及使用虛擬網路的相關指示
services: app-service
documentationcenter: ''
author: ccompy
manager: wpickett
editor: cephalin

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: ccompy

---
# 使用 PowerShell 將應用程式連接到虛擬網路
## Overview
在 Azure App Service 中，您可以將應用程式 (Web、行動或 API) 連接到您訂用帳戶中的虛擬網路 (VNet)。這項功能稱為 VNet 整合。VNet 整合功能不應該與 App Service 環境功能混淆，後者可讓您在虛擬網路中執行 Azure App Service 的執行個體。

VNet 整合功能在新的入口網站中有使用者介面 (UI)，可讓您與使用傳統部署模型或 Azure Resource Manager 部署模型所部署的虛擬網路整合。如果您想要深入了解此功能，請參閱[將您的應用程式與 Azure 虛擬網路整合](web-sites-integrate-with-vnet.md)。

本文不是關於如何使用 UI，而是如何使用 PowerShell 啟用整合。由於每種部署模型的命令都不同，所以本文中每種部署模型各有一節。

繼續閱讀本文之前，請確定您有︰

* 安裝最新版的 Azure PowerShell SDK。您可以使用 Web Platform Installer 來安裝。
* 在標準或進階 SKU 執行的 Azure App Service 中的應用程式。

## 傳統虛擬網路
本節將針對使用傳統部署模型的虛擬網路說明三項工作︰

1. 將應用程式連接到具有閘道器並已針對點對站連接進行設定的既存虛擬網路
2. 更新應用程式的虛擬網路整合資訊。
3. 中斷連接您的應用程式與虛擬網路。

### 將應用程式連接到傳統 VNet
若要將應用程式連接到虛擬網路，請遵循下列三個步驟：

1. 向 Web 應用程式宣告它將加入特定的虛擬網路。應用程式會產生憑證，該憑證會提供給虛擬網路以進行點對站連接。
2. 將 Web 應用程式憑證上傳至 虛擬網路，然後再擷取點對站 VPN 封裝 URI
3. 使用點對站封裝 URI 更新 Web 應用程式的虛擬網路連接。

第 1 和第 3 步驟完全可編寫指令碼，但是第 2 步驟需要透過入口網站的一次性手動動作，或存取以在虛擬網路 Azure Resource Manager 端點上執行 **PUT** 或 **PATCH** 動作。請連絡 Azure 支援啟用此功能。開始之前，請確定您的傳統虛擬網路已啟用點對站連接並已部署閘道器。若要建立閘道器並啟用點對站連接，您需要依照[建立 VPN 閘道][createvpngateway]所述使用入口網站。

傳統虛擬網路必須與 App Service 方案位於的相同訂用帳戶中，其中保留您整合的應用程式。

##### 設定 Azure PowerShell SDK
使用下列方式，開啟 PowerShell 視窗，並設定您的 Azure 帳戶和訂用帳戶︰

    Login-AzureRmAccount

這個命令將會開啟提示字元，以取得 Azure 認證。登入後，請使用下列命令來選取您要使用的訂用帳戶。請確定您使用您的虛擬網路和 App Service 方案所在的訂用帳戶。

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

或

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### 本文中使用的變數
為了簡化命令，我們會以特定組態設定 **$Configuration** PowerShell 變數。

在 PowerShell 中使用下列參數設定變數，如下所示：

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

應用程式位置應該是不包含任何空格的位置。例如，美國西部是 westus。

    $Configuration.WebAppLocation = "[Your web app Location]"

下一個項目是憑證應該寫入其中的位置。它應該是您的本機電腦上的可寫入路徑。請務必在結尾包含 .cer。

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

若要查看您的設定，請輸入 **$Configuration**。

    > $Configuration

    Name                           Value
    ----                           -----
    GeneratedCertificatePath       C:\vnetcert.cer
    VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
    WebAppResourceGroup            vnetdemo-rg
    VnetResourceGroup              testase1-rg
    VnetName                       TestNetwork
    WebAppName                     vnetintdemoapp
    WebAppLocation                 centralus

這一節的其餘部分假設您已如上所述建立變數。

##### 向應用程式宣告虛擬網路
使用下列命令告訴應用程式，它會使用此特定虛擬網路。這會導致應用程式產生所需的憑證︰

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

如果此命令成功，**$vnet** 中應該有 **Properties** 變數。**Properties** 變數應該包含憑證指紋和憑證資料。

##### 將 Web 應用程式憑證上傳至虛擬網路
每個訂用帳戶和虛擬網路組合都需要執行手動一次性步驟。也就是說，如果您將訂用帳戶 A 中的應用程式連接到虛擬網路 A，您只需要執行此步驟一次，無論您設定多少應用程式。如果您將新的應用程式新增至另一個虛擬網路，需要再進行一次這個步驟。原因是因為憑證集是在 Azure App Service 的訂用帳戶層級產生，而且會針對應用程式將會連接的每個虛擬網路產生一次。

如果您遵循這些步驟，或者如果您使用入口網站與相同的虛擬網路整合，則憑證已設定。

第一個步驟是產生 .cer 檔案。第二個步驟是將 .cer 檔案上傳至您的虛擬網路。若要從先前步驟中的 API 呼叫產生 .cer 檔案，請執行下列命令。

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

憑證將位於 **$Configuration.GeneratedCertificatePath** 所指定的位置。

若要手動上傳憑證，請使用 [Azure 入口網站][azureportal]並 [瀏覽虛擬網路 (傳統)] –> [VPN 連接] –> [點對站] –> [管理憑證]。從這裡上傳您的憑證。

##### 取得點對站封裝
設定 Web 應用程式上的虛擬網路連接的下一步是取得點對站封裝，並將其提供給您的 Web 應用程式。

將下列範本儲存至您的電腦上某個位置中稱為 GetNetworkPackageUri.json 的檔案，例如：C:\\Azure\\Templates\\GetNetworkPackageUri.json。

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "certData": {
                "type": "string"
            },
            "certThumbprint": {
                "type": "string"
            },
            "networkName": {
                "type": "string"
            }
        },
        "variables": {
            "legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
            },
            "resources": [
            ],
        "outputs" : {
            "PackageUri" :
            {
            "value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
            }
        }
    }


設定輸入參數：

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

呼叫指令碼：

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


變數 **$output.Outputs.packageUri** 現在會包含要提供給您的 Web 應用程式的封裝 URI。

##### 將點對站封裝上傳至您的應用程式
最後一個步驟是將此封裝提供給應用程式。只要執行下一個命令︰

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

如果有訊息要求您確認您要覆寫現有的資源，請確定允許它。

此命令成功之後，您的應用程式現在應該連接至虛擬網路。若要確認成功，請移至應用程式主控台，然後輸入下列命令︰

    SET WEBSITE_

如果有名為 WEBSITE\_VNETNAME 的環境變數，其值符合目標虛擬網路名稱的值，則所有組態皆已成功。

### 更新傳統 VNet 整合資訊
若要更新或重新同步處理您的資訊，只需要重複您一開始建立整合時所遵循的步驟。這些步驟如下︰

1. 定義您的組態資訊。
2. 向應用程式宣告虛擬網路。
3. 取得點對站封裝。
4. 將點對站封裝上傳至您的應用程式。

### 中斷連接您的應用程式與傳統 VNet
若要中斷連接應用程式，您需要在虛擬網路整合期間設定的組態資訊。使用該資訊，則只有一個命令可中斷連接您的應用程式與虛擬網路。

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## Resource Manager 虛擬網路
Resource Manager 虛擬網路有 Azure Resource Manager API，可簡化某些處理程序 (相較於傳統虛擬網路)。我們有指令碼可協助您完成下列工作︰

* 建立 Resource Manager 虛擬網路並您的應用程式整合。
* 建立閘道器，在既存的 Resource Manager 虛擬網路中設定點對站連接，然後與您的應用程式整合。
* 將您的應用程式與已啟用閘道器和點對站連接的既存 Resource Manager 虛擬網路整合。
* 中斷連接您的應用程式與虛擬網路。

### Resource Manager VNet App Service 整合指令碼
複製下列指令碼並儲存至檔案。如果您不想使用該指令碼，您可以從該指令碼學習，了解如何對 Resource Manager 虛擬網路進行設定。

    function ReadHostWithDefault($message, $default)
    {
        $result = Read-Host "$message [$default]"
        if($result -eq "")
        {
            $result = $default
        }
            return $result
        }

    function PromptCustom($title, $optionValues, $optionDescriptions)
    {
        Write-Host $title
        Write-Host
        $a = @()
        for($i = 0; $i -lt $optionValues.Length; $i++){
            Write-Host "$($i+1))" $optionDescriptions[$i]
        }
        Write-Host

        while($true)
        {
            Write-Host "Choose an option: "
            $option = Read-Host
            $option = $option -as [int]

            if($option -ge 1 -and $option -le $optionValues.Length)
            {
                return $optionValues[$option-1]
            }
        }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
        $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
        $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
        $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
        $result = $host.ui.PromptForChoice($title, $message, $options, $default)
        return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
        Write-Host "Creating a new VNET"
        $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
        New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
        $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

        Write-Host "Creating a public IP address for this VNET"
        $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

        Write-Host "Adding a root certificate to this VNET"
        $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

        Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
        New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
        Write-Host "Adding a new Vnet"
        Write-Host
        $vnetName = Read-Host "Specify a name for this Virtual Network"

        $vnetGatewayName="$($vnetName)-gateway"
        $vnetIpName="$($vnetName)-ip"
        $vnetIpConfigName="$($vnetName)-ipconf"

        # Virtual Network settings
        $vnetAddressSpace="10.0.0.0/8"
        $vnetGatewayAddressSpace="10.5.0.0/16"
        $vnetPointToSiteAddressSpace="172.16.0.0/16"

        $changeRequested = 0
        $resourceGroupName = $webAppResourceGroup

        while($changeRequested -eq 0)
        {
            Write-Host
            Write-Host "Currently, I will create a VNET with the following settings:"
            Write-Host
            Write-Host "Virtual Network Name: $vnetName"
            Write-Host "Resource Group Name:  $resourceGroupName"
            Write-Host "Gateway Name: $vnetGatewayName"
            Write-Host "Vnet IP name: $vnetIpName"
            Write-Host "Vnet IP config name:  $vnetIpConfigName"
            Write-Host "Address Space:$vnetAddressSpace"
            Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
            Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
            Write-Host
            $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

            if($changeRequested -eq 0)
            {
                $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
                $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
                $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
                $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
            }
        }

        $ErrorActionPreference = "Stop";

        # We create the virtual network and add it here. The way this works is:
        # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
        # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
        # 3) Get the VPN package from the gateway and pass it back to the App.

        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
        $location = $webApp.Location

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $ErrorActionPreference = "Stop";

        # At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
        Write-Host "Getting App information"
        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $location = $webApp.Location

        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"
        }

        # Display existing vnets
        $vnets = Get-AzureRmVirtualNetwork
        $vnetNames = @()
        foreach($vnet in $vnets)
        {
            $vnetNames += $vnet.Name
        }

        Write-Host
        $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

        # We need to check if this VNET is able to be joined to a App, based on following criteria
            # If there is no gateway, we can create one.
            # If there is a gateway:
                # It must be of type Vpn
                # It must be of VpnType RouteBased
                # If it doesn't have the right certificate, we will need to add it.
                # If it doesn't have a point-to-site range, we will need to add it.

        $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

        if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
        {
            $ErrorActionPreference = "Continue";
            # There is no gateway. We need to create one.
            Write-Host "This Virtual Network has no gateway. I will need to create one."

            $vnetName = $vnet.Name
            $vnetGatewayName="$($vnetName)-gateway"
            $vnetIpName="$($vnetName)-ip"
            $vnetIpConfigName="$($vnetName)-ipconf"

            # Virtual Network settings
            $vnetAddressSpace="10.0.0.0/8"
            $vnetGatewayAddressSpace="10.5.0.0/16"
            $vnetPointToSiteAddressSpace="172.16.0.0/16"

            $changeRequested = 0

            Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
            foreach($subnet in $vnet.Subnets)
            {
                Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
            }

            $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

            while($changeRequested -eq 0)
            {
                Write-Host
                Write-Host "Currently, I will create a VNET gateway with the following settings:"
                Write-Host
                Write-Host "Virtual Network Name: $vnetName"
                Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
                Write-Host "Gateway Name: $vnetGatewayName"
                Write-Host "Vnet IP name: $vnetIpName"
                Write-Host "Vnet IP config name:  $vnetIpConfigName"
                Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
                Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
                Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
                Write-Host
                $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

                if($changeRequested -eq 0)
                {
                    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
                }
            }

            $ErrorActionPreference = "Stop";

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # If there is no gateway subnet, we need to create one.
            if($gatewaySubnet -eq $null)
            {
                $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
                $vnet.Subnets.Add($gatewaySubnet);
                Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
            }

            CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
        }
        else
        {
            $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
            $gatewayResourceGroup = $uriParts[4]
            $gatewayName = $uriParts[8]

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

            # validate gateway types, etc.
            if($gateway.GatewayType -ne "Vpn")
            {
                Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
                return
            }

            if($gateway.VpnType -ne "RouteBased")
            {
                Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
                return
            }

            if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
            {
                Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
                $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
                Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
            }

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # We need to check if the certificate here exists in the gateway.
            $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

            $certFound = $false
            foreach($certificate in $certificates)
            {
                if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
                {
                    $certFound = $true
                    break
                }
            }

            if(-not $certFound)
            {
                Write-Host "Adding certificate"
                Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
            }
        }

        # Now finish joining by getting the VPN package and giving it to the App
        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

        Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"

            Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        }
            else
        {
            Write-Host "Not connected to a VNET."
        }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
        Write-Error "No subscriptions bound to this account."
        return
    }

    if($subs.Length -eq 1)
    {
        $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
        $subscriptionChoices = @()
        $subscriptionValues = @()

        foreach($subscription in $subs){
        $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
        $subscriptionValues += ($subscription.SubscriptionId);
        }

        $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
        AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
        AddExistingVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 2)
    {
        RemoveVnet $subscriptionId $resourceGroup $appName
    }

儲存指令碼的複本。在本文中，其名稱為 V2VnetAllinOne.ps1，但您可以使用其他名稱。此指令碼沒有引數。您只需要執行它。指令碼執行的第一件事是提示您登入。登入後，指令碼會取得您的帳戶詳細資料，並傳回一份訂用帳戶清單。如果要求您的認證不算在內，初始指令碼執行如下所示︰

    PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
    Please Login

    Environment           : AzureCloud
    Account               : ccompy@microsoft.com
    TenantId              : 722278f-fef1-499f-91ab-2323d011db47
    SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
    CurrentStorageAccount :

    Choose a subscription

    1) Demo Subscription (af5358e1-acac-2c90-a9eb-722190abf47a)
    2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
    3) Purple Demo Subscription (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

    Choose an option:
    3

    Account      : ccompy@microsoft.com
    Environment  : AzureCloud
    Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d
    Tenant       : 722278f-fef1-499f-91ab-2323d011db47

    Please enter the Resource Group of your App: hcdemo-rg
    Please enter the Name of your App: v2vnetpowershell
    What do you want to do?

    1) Add a NEW Virtual Network to an App
    2) Add an EXISTING Virtual Network to an App
    3) Remove a Virtual Network from an App

本節的其餘部分將說明每個選項。

### 建立 Resource Manager VNet 並與其整合
若要建立採用 Resource Manager 部署模型的新虛擬網路，並且與您的應用程式整合，請選取 **1) 將新的虛擬網路新增至應用程式**。這將會提示您輸入虛擬網路的名稱。在我的案例中，如您在下列設定中所見，我使用了 v2pshell 這個名稱。

指令碼會提供有關所建立的虛擬網路的詳細資料。如果我想要，我可以變更任何值。在此範例執行中，我建立的虛擬網路具有下列設定︰

    Virtual Network Name:         v2pshell
    Resource Group Name:          hcdemo-rg
    Gateway Name:                 v2pshell-gateway
    Vnet IP name:                 v2pshell-ip
    Vnet IP config name:          v2pshell-ipconf
    Address Space:                10.0.0.0/8
    Gateway Address Space:        10.5.0.0/16
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):

如果您想要變更任何值，則輸入 **Y**，然後進行變更。當您滿意虛擬網路設定時，則輸入 **N**，或在系統提示您變更設定時按 Enter 鍵。從該處開始直到完成，指令碼會告訴您它正在執行什麼動作，直到開始建立虛擬網路閘道器為止。該步驟需要最多一個小時。這個階段沒有任何進度指示器，但是指令碼會讓您知道何時建立閘道器。

指令碼完成時，它會顯示 [完成]。此時，您會有一個 Resource Manager 虛擬網路，其具有您所選取的名稱和設定。這個新的虛擬網路也會與您的應用程式整合。

### 將您的應用程式與既存的 Resource Manager VNet 整合
與既存的虛擬網路整合時，如果您提供的 Resource Manager 虛擬網路沒有閘道器或點對站連接，則指令碼會加以設定。如果 VNET 已設定這些項目，指令碼就會直接進入應用程式整合。若要啟動此程序，只需選取 **2) 將現有的虛擬網路新增至應用程式**。

此選項僅適用於您有既存的 Resource Manager 虛擬網路，其位於與您的應用程式相同的訂用帳戶中。選取此選項之後，將看到一份 Resource Manager 虛擬網路清單。

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    Choose an option:
    5

只要選取您想要整合的虛擬網路。如果您已經有啟用點對站連接的閘道器，則指令碼只會將您的應用程式與虛擬網路整合。如果您沒有閘道器，您必須指定閘道器子網路。您的閘道器子網路必須位於您的虛擬網路位址空間，而且它不能在任何其他子網路中。如果您的虛擬網路沒有閘道器且您執行此步驟，則結果如下所示︰

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

在此範例中，我建立的虛擬網路閘道器具有下列設定︰

    Virtual Network Name:         v2pshell2
    Resource Group Name:          vnetdemo-rg
    Gateway Name:                 v2pshell2-gateway
    Vnet IP name:                 v2pshell2-ip
    Vnet IP config name:          v2pshell2-ipconf
    Address Space:                172.16.0.0/16
    Gateway Address Space:        172.16.1.0/26
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):
    Creating App association to VNET

如果您想要變更上述任何設定，可以這麼做。否則按下 Enter 鍵，指令碼會建立您的閘道器，並將您的應用程式附加至虛擬網路。閘道器建立時間仍然是一小時，請謹記在心。一切完成後，指令碼會顯示 [完成]。

### 中斷連接您的應用程式與 Resource Manager VNet
中斷連接您的應用程式與虛擬網路不會關閉閘道器或停用點對站連接。畢竟，您可能將它使用於其他項目。也不會中斷連接它與您提供的應用程式以外的任何其他應用程式。若要執行此動作，請選取 **3) 從應用程式移除虛擬網路**。在您這麼做時，您會看到如下的畫面：

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

雖然指令碼表示刪除，但不會刪除虛擬網路。它只是移除整合。在您確認這是您想要執行的動作之後，命令會快速、安靜地進行處理，然後在完成時傳回 **True**。

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

<!---HONumber=AcomDC_0831_2016-->