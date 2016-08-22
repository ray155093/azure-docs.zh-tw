<properties
   pageTitle="允許使用 PowerShell 從外部存取 VM | Microsoft Azure"
   description="了解如何開啟連接埠 / 使用 Resource Manager 部署模型和 Azure PowerShell 來建立允許從外部存取您 Windows VM 的端點"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="iainfou"/>

# 允許使用 PowerShell 從外部存取您的 VM
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## 快速命令
若要建立網路安全性群組和 ACL 規則，您需要[安裝最新版的 Azure PowerShell](../powershell-install-configure.md)。您也可以[使用 Azure 入口網站來執行這些步驟](virtual-machines-windows-nsg-quickstart-portal.md)。

首先，您必須建立規則以允許 TCP 連接埠 80 上的 HTTP 流量，其中請輸入您自己的名稱和描述︰

```
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name http-rule -Description "Allow HTTP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

接著，依下列方式建立「網路安全性群組」並指派您剛才建立的 HTTP 規則，其中請輸入您自己的資源群組名稱和位置︰

```
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `
    -Name "TestNSG" -SecurityRules $httprule
```

現在，讓我們將「網路安全性群組」指派給子網路。首先，選取虛擬網路：

```
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
```

將「網路安全性群組」與子網路建立關聯：

```
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet `
    -NetworkSecurityGroup $nsg
```

最後，更新虛擬網路，以便讓變更生效︰

```
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## 網路安全性群組的詳細資訊
這裡的快速命令可讓您使流向您 VM 的流量開始正常運作。「網路安全性群組」提供許多絕佳的功能和細微性來控制對您資源的存取。您可以深入了解[建立網路安全性群組和 ACL 規則](../virtual-network/virtual-networks-create-nsg-arm-ps.md)。

您可以在 Azure Resource Manager 範本中定義網路安全性群組和 ACL 規則。深入了解[使用範本建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您需要使用連接埠轉送，以將唯一的外部連接埠對應至您 VM 上的內部連接埠，您將需要使用負載平衡器和「網路位址轉譯」(NAT) 規則。例如，您可能會想要對外公開 TCP 連接埠 8080，然後讓流量導向到 VM 上的 TCP 連接埠 80。您可以深入了解[建立網際網路面向的負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-ps.md)。

## 後續步驟
在此範例中，您建立了簡單的規則來允許 HTTP 流量。您可以從下列文章中，找到有關建立更詳細環境的資訊︰

- [Azure 資源管理員概觀](../resource-group-overview.md)
- [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
- [負載平衡器的 Azure Resource Manager 概觀](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0810_2016-->