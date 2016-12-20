---
title: "VM 的常用網路 PowerShell 命令 | Microsoft Docs"
description: "常用 PowerShell 命令，可讓您開始為 VM 建立虛擬網路與其相關聯的資源。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 9f97aee1d257c97d6ff4f1b6bf941599eade2376


---
# <a name="common-network-azure-powershell-commands-for-vms"></a>VM 的常用網路 Azure PowerShell 命令
如果您想要建立虛擬機器，您需要建立 [虛擬網路](../virtual-network/virtual-networks-overview.md) 或了解可以加入 VM 的現有虛擬網路。 一般而言，當您建立 VM 時，您也需要考慮建立本文中所述的資源。

如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## <a name="create-network-resources"></a>建立網路資源
| Task | 命令 |
| --- | --- |
| 建立子網路組態 |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name "subnet_name" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "subnet_name" -AddressPrefix XX.X.X.X/XX<BR><BR>一般網路可能有[網際網路面向負載平衡器](../load-balancer/load-balancer-internet-overview.md)的子網路和[內部負載平衡器](../load-balancer/load-balancer-internal-overview.md)的不同子網路。 |
| 建立虛擬網路 |$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name "virtual_network_name" -ResourceGroupName "resource_group_name" -Location "location_name" -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| 唯一網域名稱的測試 |[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName "domain_name" -Location "location_name"<BR><BR>您可以指定[公用 IP 資源](../virtual-network/virtual-network-ip-addresses-overview-arm.md)的 DNS 網域名稱，以建立 domainname.location.cloudapp.azure.com 與 Azure 受管理 DNS 伺服器中的公用 IP 位址的對應。 名稱只能包含字母、數字和連字號。 第一個和最後一個字元必須是字母或數字，而網域名稱必須是其 Azure 位置內唯一的。 如果傳回 **True** ，表示您提出的名稱是全域唯一的。 |
| 建立公用 IP 位址 |$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name "ip_address_name" -ResourceGroupName "resource_group_name" -DomainNameLabel "domain_name" -Location "location_name" -AllocationMethod Dynamic<BR><BR>公用 IP 位址會使用您先前測試並由負載平衡器前端組態使用的網域名稱。 |
| 建立前端 IP 組態 |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name "frontend_ip_name" -PublicIpAddress $pip<BR><BR>前端組態包含您先前針對連入網路流量建立的公用 IP 位址。 |
| 建立後端位址集區 |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name "backend_pool_name"<BR><BR>提供可透過網路介面存取的負載平衡器後端內部位址。 |
| 建立探查 |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name "probe_name" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>包含健康情況探查，可用來檢查後端位址集區中虛擬機器執行個體的可用性。 |
| 建立負載平衡規則 |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>包含將負載平衡器上的公用連接埠指派至後端位址集區中的連接埠的規則。 |
| 建立輸入 NAT 規則 |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name "rule_name" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>包含將負載平衡器上的公用連接埠對應至後端位址集區中特定虛擬機器的連接埠的規則。 |
| 建立負載平衡器 |$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName "resource_group_name" -Name "load_balancer_name" -Location "location_name" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| 建立網路介面 |$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName "resource_group_name" -Name "network_interface_name" -Location "location_name" -PrivateIpAddress XX.X.X.X -Subnet subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>建立一個網路介面，其使用您先前建立的公用 IP 位址和虛擬網路子網路。 |

## <a name="get-information-about-network-resources"></a>取得關於網路資源的資訊
| Task | 命令 |
| --- | --- |
| 列出虛擬網路 |[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName "resource_group_name"<BR><BR>列出資源群組中的所有虛擬網路。 |
| 取得虛擬網路的相關資訊 |Get-AzureRmVirtualNetwork -Name "virtual_network_name" -ResourceGroupName "resource_group_name" |
| 列出虛擬網路中的子網路 |Get-AzureRmVirtualNetwork -Name "virtual_network_name" -ResourceGroupName "resource_group_name" &#124; Select Subnets |
| 取得子網路的相關資訊 |[Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "subnet_name" -VirtualNetwork $vnet<BR><BR>取得指定虛擬網路中的子網路相關資訊。 $Vnet 值代表 Get-AzureRmVirtualNetwork 所傳回的物件。 |
| 列出 IP 位址 |[Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) -ResourceGroupName "resource_group_name"<BR><BR>列出資源群組中的公用 IP 位址。 |
| 列出負載平衡器 |[Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) -ResourceGroupName "resource_group_name"<BR><BR>列出資源群組中的所有負載平衡器。 |
| 列出網路介面 |[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) -ResourceGroupName "resource_group_name"<BR><BR>列出資源群組中的所有網路介面。 |
| 取得網路介面的相關資訊 |Get-AzureRmNetworkInterface -Name "network_interface_name" -ResourceGroupName "resource_group_name"<BR><BR>取得特定網路介面的相關資訊。 |
| 取得網路介面的 IP 組態 |[Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -Name "ipconfiguration_name" -NetworkInterface $nic<BR><BR>取得指定網路介面的 IP 組態相關資訊。 $nic 值代表 Get-AzureRmNetworkInterface 所傳回的物件。 |

## <a name="manage-network-resources"></a>管理網路資源
| Task | 命令 |
| --- | --- |
| 將子網路新增至虛擬網路 |[Add-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) -AddressPrefix XX.X.X.X/XX -Name "subnet_name" -VirtualNetwork $vnet<BR><BR>可將子網路新增現有虛擬網路。 $Vnet 值代表 Get-AzureRmVirtualNetwork 所傳回的物件。 |
| 刪除虛擬網路 |[Remove-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -Name "virtual_network_name" -ResourceGroupName "resource_group_name"<BR><BR>從資源群組移除指定的虛擬網路。 |
| 刪除網路介面 |[Remove-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -Name "network_interface_name" -ResourceGroupName "resource_group_name"<BR><BR>從資源群組移除指定的網路介面。 |
| 刪除負載平衡器 |[Remove-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -Name "load_balancer_name" -ResourceGroupName "resource_group_name"<BR><BR>從資源群組移除指定的負載平衡器。 |
| 刪除公用 IP 位址 |[Remove-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-Name "ip_address_name" -ResourceGroupName "resource_group_name"<BR><BR>從資源群組移除指定的公用 IP 位址。 |

## <a name="next-steps"></a>後續步驟
* 使用您剛才[建立 VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 時所建立的網路介面。
* 深入了解如何 [建立具有多個網路介面的 VM](../virtual-network/virtual-networks-multiple-nics.md)。




<!--HONumber=Nov16_HO3-->


