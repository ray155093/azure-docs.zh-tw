---
title: "將 ExpressRoute 線路和相關聯的虛擬網路從傳統移轉至 Resource Manager：Azure | Microsoft Docs"
description: "本頁面描述如何將傳統線路與相關聯的虛擬網路移轉至 Resource Manager。"
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9a3ad5be7cc12f1c82eab4a8b3089276a434c234
ms.lasthandoff: 03/29/2017


---
# <a name="migrate-expressroute-circuits-and-associated-virtual-networks-from-the-classic-to-the-resource-manager-deployment-model"></a>將 ExpressRoute 線路和相關聯的虛擬網路從傳統部署模型移轉至 Resource Manager 部署模型

本文說明如何將 Azure ExpressRoute 線路與相關聯的虛擬網路從傳統部署模型移轉至 Azure Resource Manager 部署模型。 


## <a name="before-you-begin"></a>開始之前
* 請確認您有最新版的 Azure PowerShell 模組。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 開始設定之前，請確定您已經檢閱過[必要條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)和[工作流程](expressroute-workflows.md)。
* 請檢閱[將 ExpressRoute 電路從傳統移至 Resource Manager](expressroute-move.md) 下提供的資訊。 請確定您已完整了解各項限制。
* 請確認電路在傳統部署模型中的運作完全正常。
* 請確定您擁有建立在 Resource Manager 部署模型中建立的資源群組。
* 檢閱下列資源移轉文件︰

    * [平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager)](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [常見問題集：平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager)](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [從傳統到 Azure Resource Manager 移轉的常見錯誤](../virtual-machines/virtual-machines-migration-errors.md)

## <a name="supported-and-unsupported-scenarios"></a>支援和不支援的案例

* ExpressRoute 線路可以從傳統移轉至 Resource Manager 環境，而不需要停機。 您可以將任何 ExpressRoute 線路從傳統移轉至 Resource Manager 環境，而不需要停機。 請遵循[使用 PowerShell 將 ExpressRoute 線路從傳統部署模型移至 Resource Manager 部署模型](expressroute-howto-move-arm.md)中的指示。 這是將所連接資源移至虛擬網路的必要條件。
* 虛擬網路、閘道，以及虛擬網路中連結至相同訂用帳戶中 ExpressRoute 線路的相關聯部署，都可以移轉至 Resource Manager 環境，而不需要停機。 您可以依照稍後描述的步驟來移轉資源，例如虛擬網路、閘道，以及虛擬網路中部署的虛擬機器。 您必須確保虛擬網路在移轉之前都已正確設定。 
* 虛擬網路、閘道，以及虛擬網路內與 ExpressRoute 線路位於不同訂用帳戶的相關聯部署，都需要一些停機時間，才能完成移轉。 本文件的最後一節描述移轉資源所需遵循的步驟。

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>將 ExpressRoute 線路從傳統移到 Resource Manager
嘗試移轉已連結至 ExpressRoute 線路的資源之前，您必須先將 ExpressRoute 線路從傳統移至 Resource Manager 環境。 若要完成此工作，請參閱下列文章：

* 請檢閱[將 ExpressRoute 電路從傳統移至 Resource Manager](expressroute-move.md) 下提供的資訊。
* [使用 Azure PowerShell 將戲路從傳統移至 Resource Manager](expressroute-howto-move-arm.md)。
* 使用 Azure 服務管理入口網站。 您可以依照工作流程來[建立新的 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md)並選取匯入選項。 

這項作業不需要停機。 進行移轉時，您可以繼續在內部部署與 Microsoft 之間傳輸資料。

## <a name="prepare-your-virtual-network-for-migration"></a>準備虛擬網路以便移轉
您必須確保所要移轉之虛擬網路的網路沒有不必要的構件。 若要下載您的虛擬網路組態並視需要加以更新，請執行下列 PowerShell Cmdlet：

    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName <VNET Subscription>
    Get-AzureVNetConfig -ExportToFile C:\virtualnetworkconfig.xml
      
您必須確保 <ConnectionsToLocalNetwork> 的所有參考都會從所要移轉的虛擬網路中移除。 下列程式碼片段顯示網路設定範例︰

    <VirtualNetworkSite name="MyVNet" Location="East US">
        <AddressSpace>
            <AddressPrefix>10.0.0.0/8</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="Subnet-1">
                <AddressPrefix>10.0.0.0/11</AddressPrefix>
            </Subnet>
            <Subnet name="GatewaySubnet">
                <AddressPrefix>10.32.0.0/28</AddressPrefix>
            </Subnet>
        </Subnets>
        <Gateway>
            <ConnectionsToLocalNetwork>
            </ConnectionsToLocalNetwork>
        </Gateway>
    </VirtualNetworkSite>
 
如果 <ConnectionsToLocalNetwork> 不是空的，請刪除其下的參考並重新提交您的網路組態。 您可以藉由執行下列 PowerShell Cmdlet 來這項作業：

    Set-AzureVNetConfig -ConfigurationPath c:\virtualnetworkconfig.xml

## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>移轉虛擬網路、閘道，以及與 ExpressRoute 線路位於相同訂用帳戶的相關聯部署。
這一節說明移轉虛擬網路、閘道，以及與 ExpressRoute 線路位於相同訂用帳戶的相關聯部署所需遵循的步驟。 這項移轉不需要停機。 您可以透過移轉程序繼續使用所有資源。 管理平面會在移轉進行時遭到封鎖。 

1. 確保 ExpressRoute 線路已從傳統移至 Resource Manager 環境。
2. 確保已為了移轉適當地準備虛擬網路。
3. 註冊您的訂用帳戶以便移轉資源。 若要註冊您的訂用帳戶以便移轉資源，請使用下列 PowerShell 程式碼片段︰ 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
4. 驗證、準備和移轉。 若要移轉虛擬網路，請使用下列 PowerShell 程式碼片段︰
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    您也可以執行下列 PowerShell Cmdlet 來中止移轉：
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ``` 
## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-a-different-subscription-from-that-of-the-expressroute-circuit"></a>移轉虛擬網路、閘道，以及與 ExpressRoute 線路位於不同訂用帳戶的相關聯部署。

1. 確保 ExpressRoute 線路已從傳統移至 Resource Manager 環境。
2. 確保已為了移轉適當地準備虛擬網路。
3. 確保 ExpressRoute 線路可以在傳統與 Resource Manager 環境中運作。 若要允許此線路同時使用於傳統和 Resource Manager 環境中，請使用下列 PowerShell 指令碼︰ 
    ```
    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName <My subscription>
    $circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    $circuit.AllowClassicOperations = $true
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    ```
4. 在 Resource Manager 環境中建立授權。 若要了解如何建立授權，請參閱[如何將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)。 若要建立授權，請使用下列 PowerShell 程式碼片段︰
    ```
    circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    $circuit = Get-AzureRmExpressRouteCircuit -Name MigrateCircuit -ResourceGroupName MigrateRGWest

    $id = $circuit.id 
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"

    $key=$auth1.AuthorizationKey 
    ```
    請記下線路識別碼與授權金鑰。 移轉完成之後，這些元素用於將線路連接到虛擬網路。
  
5. 刪除與虛擬網路相關聯的專用線路連結。 若要移除傳統環境中的線路連結，請使用下列 Cmdlet： 
    ```
    $skey = Get-AzureDedicatedCircuit | select ServiceKey
    Remove-AzureDedicatedCircuitLink -ServiceKey $skey -VNetName $vnetName
    ```  

6. 註冊您的訂用帳戶以便移轉資源。 若要註冊您的訂用帳戶以便移轉資源，請使用下列 PowerShell 程式碼片段︰ 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
7. 驗證、準備和移轉。 若要移轉虛擬網路，請使用下列 PowerShell 程式碼片段︰
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    您也可以執行下列 PowerShell Cmdlet 來中止移轉：
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ```
8. 將虛擬網路連回至 ExpressRoute 線路。 下列 PowerShell 程式碼片段會在虛擬網路建立所在的訂用帳戶內容中執行。 您不必在線路建立所在的訂用帳戶中執行此程式碼片段。 使用線路識別碼做為 PeerID 和步驟 4 中記下的授權金鑰。
    ```
    Select-AzureRMSubscription –SubscriptionName <customer subscription>  
    $gw = Get-AzureRmVirtualNetworkGateway -Name $vnetName-Default-Gateway -ResourceGroupName ($vnetName + "-Migrated")
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroup  ($vnetName + "-Migrated")  

    New-AzureRmVirtualNetworkGatewayConnection -Name  ($vnetName + "-GwConn") -ResourceGroupName ($vnetName + "-Migrated")  -Location $vnet.Location -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey $key
    ```
## <a name="next-steps"></a>後續步驟
* [平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager)](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [常見問題集：平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager)](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [從傳統到 Azure Resource Manager 移轉的常見錯誤](../virtual-machines/virtual-machines-migration-errors.md)

