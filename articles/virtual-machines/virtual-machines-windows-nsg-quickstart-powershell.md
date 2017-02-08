---
title: "使用 PowerShell 對 VM 開啟連接埠 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 部署模式和 Azure PowerShell 對 Windows VM 開啟連接埠 / 建立端點"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 78336671467db146ca4a2c54c2b422cd1dce2865


---
# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>使用 PowerShell 對 Azure 中的 VM 開啟連接埠和端點
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>快速命令
若要建立網路安全性群組和 ACL 規則，您需要 [安裝最新版的 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 您也可以 [使用 Azure 入口網站來執行這些步驟](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

登入您的 Azure 帳戶：

```powershell
Login-AzureRmAccount
```

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`myNetworkSecurityGroup` 和 `myVnet`。

建立規則。 下列範例會建立名為 `myNetworkSecurityGroupRule` 的規則以允許連接埠 80 上的 TCP 流量︰

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

接著，依下列方式建立「網路安全性群組」並指派您剛才建立的 HTTP 規則。 下列範例會建立名為 `myNetworkSecurityGroup` 的網路安全性群組：

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

現在，讓我們將「網路安全性群組」指派給子網路。 下列範例會將名為 `myVnet` 的現有虛擬網路指派給變數 `$vnet`：

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

將「網路安全性群組」與子網路建立關聯。 下列範例會將名為 `mySubnet` 的子網路與您的網路安全性群組建立關聯：

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

最後，更新虛擬網路，以便讓變更生效︰

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>網路安全性群組的詳細資訊
這裡的快速命令可讓您使流向您 VM 的流量開始正常運作。 「網路安全性群組」提供許多絕佳的功能和細微性來控制對您資源的存取。 您可以深入了解 [建立網路安全性群組和 ACL 規則](../virtual-network/virtual-networks-create-nsg-arm-ps.md)。

您可以在 Azure Resource Manager 範本中定義網路安全性群組和 ACL 規則。 深入了解 [使用範本建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您需要使用連接埠轉送來將唯一的外部連接埠對應至您 VM 上的內部連接埠，請使用負載平衡器和「網路位址轉譯」(NAT) 規則。 例如，您可能會想要對外公開 TCP 連接埠 8080，然後讓流量導向到 VM 上的 TCP 連接埠 80。 您可以深入了解 [建立網際網路面向的負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-ps.md)。

## <a name="next-steps"></a>後續步驟
在此範例中，您建立了簡單的規則來允許 HTTP 流量。 您可以從下列文章中，找到有關建立更詳細環境的資訊︰

* [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)
* [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
* [負載平衡器的 Azure Resource Manager 概觀](../load-balancer/load-balancer-arm.md)




<!--HONumber=Dec16_HO2-->


