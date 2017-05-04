---
title: "教學課程 - 在 Azure VM 上建立高可用性應用程式 | Microsoft Docs"
description: "了解如何使用 Azure 中的負載平衡器，建立跨三部 Windows VM 且高度可用的安全應用程式"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/30/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 61fd0dc45cfa2d320713819be3db811be58ab77e
ms.lasthandoff: 04/27/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-windows-virtual-machines-in-azure"></a>在 Azure 中的 Windows 虛擬機器上，建置已負載平衡的高可用性應用程式

在本教學課程中，您可以建立高度可用且對維護事件具有彈性的應用程式。 應用程式會使用負載平衡器、可用性設定組和三部 Windows 虛擬機器 (VM)。 本教學課程會安裝 IIS，但您可以在本教學課程中使用相同高可用性元件和指導方針來部署不同的應用程式架構。 

## <a name="step-1---azure-prerequisites"></a>步驟 1 - Azure 必要條件

若要完成本教學課程，請確定您已安裝最新的 [Azure PowerShell](/powershell/azure/overview) 模組。

首先，使用 Login-AzureRmAccount 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Login-AzureRmAccount
```

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 您必須先使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 建立資源群組，才可以建立任何其他 Azure 資源。 下列範例會在 `westeurope` 區域建立名為 `myResourceGroup` 的資源群組： 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-2---create-availability-set"></a>步驟 2 - 建立可用性設定組

可以跨越邏輯容錯和更新網域建立虛擬機器。 每個邏輯網域都代表基礎 Azure 資料中心硬體的一部分。 當您建立兩部或多部 VM 時，您的計算和儲存體資源會分散於這些網域。 如果需要維護硬體元件，此分佈會維護您應用程式的可用性。 可用性設定組可讓您定義這些邏輯容錯和更新網域。

使用 [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) 建立可用性設定組。 下列範例會建立名為 `myAvailabilitySet` 的可用性設定組：

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroup `
  -Name myAvailabilitySet `
  -Location westeurope `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

## <a name="step-3---create-load-balancer"></a>步驟 3 - 建立負載平衡器

Azure Load Balancer 會使用負載平衡器規則，將流量分散於一組定義的 VM。 健康狀態探查會監視每部 VM 上指定的連接埠，而且只會將流量分散至操作 VM。

### <a name="create-public-ip-address"></a>建立公用 IP 位址

若要存取網際網路上您的應用程式，請將公用 IP 位址指派給負載平衡器。 使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 建立公用 IP 位址。 下列範例會建立名為 `myPublicIP` 的公用 IP 位址：

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-load-balancer"></a>建立負載平衡器

使用 [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) 建立前端 IP 位址。 下列範例會建立名為 `myFrontEndPool` 的前端 IP 位址： 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name myFrontEndPool -PublicIpAddress $pip
```

使用 [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) 建立後端位址集區。 下列範例會建立名為 `myBackEndPool` 的後端位址集區：

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

現在，使用 [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) 建立負載平衡器。 下列範例會使用 `myPublicIP` 位址建立名為 `myLoadBalancer` 的負載平衡器：

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroup `
  -Name myLoadBalancer `
  -Location westeurope `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-health-probe"></a>建立健全狀況探查

若要讓負載平衡器監視您應用程式的狀態，請使用健康狀態探查。 健康狀態探查會根據 VM 對健康狀態檢查的回應，以動態方式從負載平衡器輪替中新增或移除 VM。 根據預設，在 15 秒的間隔內連續發生兩次失敗後，VM 就會從負載平衡器分配中移除。

使用 [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) 建立健全狀況探查。 下列範例會建立名為 `myHealthProbe` 的健康狀態探查，以監視每部 VM：

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。

使用 [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) 建立負載平衡器規則。 下列範例會建立名為 `myLoadBalancerRule` 的負載平衡器規則，並平衡連接埠 `80` 上的流量：

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

使用 [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) 更新負載平衡器：

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="step-4---configure-networking"></a>步驟 4 - 設定網路功能

每部 VM 都有一或多個虛擬網路介面卡 (NIC) 可連接至虛擬網路。 此虛擬網路受到保護，可根據定義的存取規則來篩選流量。

### <a name="create-virtual-network"></a>建立虛擬網路

首先，使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 設定子網路。 下列範例會建立名為 `mySubnet`的子網路：

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

若要提供您 VM 的網路連線能力，請使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 建立虛擬網路。 下列範例會建立名為 `myVnet` 且包含 `mySubnet` 的虛擬網路：

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="configure-network-security"></a>設定網路安全性

Azure [網路安全性群組](../../virtual-network/virtual-networks-nsg.md) (NSG) 控制一或多部虛擬機器的輸入和傳出流量。 網路安全性群組規則可允許或拒絕特定連接埠或連接埠範圍上的網路流量。 這些規則也可以包含來源位址首碼，所以只有在預先定義的來源產生的流量可以與虛擬機器通訊。

若要允許 Web 流量連到您的應用程式，請使用 [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) 建立網路安全性群組規則。 下列範例會建立名為 `myNetworkSecurityGroupRule` 的網路安全性群組規則：

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

使用 [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) 建立網路安全性群組。 下列範例會建立名為 `myNetworkSecurityGroup` 的 NSG：

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

使用 [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 將網路安全性群組新增至子網路：

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) 更新虛擬網路：

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-network-interface-cards"></a>建立虛擬網路介面卡

負載平衡器會搭配虛擬 NIC 資源運作，而不是實際的 VM。 虛擬 NIC 已連結至負載平衡器，然後連結至 VM。

使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 建立虛擬 NIC。 下列範例會建立三個虛擬 NIC。 (您在下列步驟中針對應用程式建立的每部 VM 都有一個虛擬 NIC)︰


```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroup `
     -Name myNic$i `
     -Location westeurope `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}

```

## <a name="step-5---create-virtual-machines"></a>步驟 5 - 建立虛擬機器

備妥所有基礎元件，您現在可以建立高度可用的 VM 來執行您的應用程式。 

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 取得虛擬機器上系統管理員帳戶所需的使用者名稱和密碼：

```powershell
$cred = Get-Credential
```

使用 [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig)、[Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem)、[Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage)、[Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk)、[Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) 和 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 來建立 VM。 下列範例會建立三個 VM：

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -StorageAccountType StandardLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
}

```

建立和設定這三部 VM 需要數分鐘的時間。 負載平衡器會自動偵測應用程式何時在每部 VM 上執行。 當應用程式正在執行時，負載平衡器規則就開始分配流量。

### <a name="install-the-app"></a>安裝應用程式 

Azure 虛擬機器擴充功能是用來自動化虛擬機器組態工作，例如安裝應用程式和設定作業系統。 [適用於 Windows 的自訂指令碼擴充功能](./../virtual-machines-windows-extensions-customscript.md)是用來在虛擬機器上執行任何 PowerShell 指令碼。 指令碼可以儲存在 Azure 儲存體、任何可存取的 HTTP 端點，或內嵌在自訂指令碼擴充功能組態。 使用自訂指令碼擴充功能時，Azure VM 代理程式會管理指令碼執行。

使用 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) 來安裝自訂指令碼擴充功能。 擴充功能會執行 `powershell Add-WindowsFeature Web-Server` 以安裝 IIS Web 伺服器：

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
     -Location westeurope
}
```

### <a name="test-your-app"></a>測試應用程式

使用 [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 取得負載平衡器的公用 IP 位址。 下列範例會取得稍早建立之 `myPublicIP` 的 IP 位址︰

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIP | select IpAddress
```

在 Web 瀏覽器中輸入公用 IP 位址。 備妥 NSG 規則，就會顯示預設 IIS 網站。 

![IIS 預設網站](media/load-balanced-iis-tutorial/iis.png)

## <a name="step-6--management-tasks"></a>步驟 6 - 管理工作

您可能需要在執行您應用程式的 VM 上執行維護，例如安裝 OS 更新。 若要處理您應用程式增加的流量，您可能需要新增額外的 VM。 本節說明如何在負載平衡器中移除或新增 VM。 

### <a name="remove-a-vm-from-the-load-balancer"></a>從負載平衡器移除 VM

重設網路介面卡的 LoadBalancerBackendAddressPools 屬性，以從後端位址集區移除 VM。

使用 [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) 取得網路介面卡：

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic2
``` 

將網路介面卡的 LoadBalancerBackendAddressPools 屬性設為 $null：

```powershell
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
```

更新網路介面卡：

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

### <a name="add-a-vm-to-the-load-balancer"></a>將 VM 新增至負載平衡器

在執行 VM 維護之後，或者如果需要擴充容量，請將 VM 的 NIC 新增至負載平衡器的後端位址集區。

取得負載平衡器：

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroup -Name myLoadBalancer 
```

將負載平衡器的後端位址集區新增至網路介面卡：

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
```

更新網路介面卡：

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>後續步驟

範例 – [Azure 虛擬機器 PowerShell 範例指令碼](./../virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

