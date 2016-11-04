---
title: VM 的常用網路 PowerShell 命令 | Microsoft Docs
description: 常用 PowerShell 命令，可讓您開始為 VM 建立虛擬網路與其相關聯的資源。
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2016
ms.author: davidmu

---
# VM 的常用網路 Azure PowerShell 命令
如果您想要建立虛擬機器，您需要建立[虛擬網路](../virtual-network/virtual-networks-overview.md)或了解可以加入 VM 的現有虛擬網路。一般而言，當您建立 VM 時，您也需要考慮建立本文中所述的資源。

## 使用 Azure PowerShell 建立資源
如需如何安裝最新版 Azure PowerShell 的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)，並選取您要使用的訂用帳戶，然後登入您的 Azure 帳戶。

| 資源 | 命令 |
| --- | --- |
| 子網路 |$internetSubnet = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name internetSubnet -AddressPrefix 10.0.1.0/24<BR>$internalSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name internalSubnet -AddressPrefix 10.0.2.0/24<BR><BR>一般網路可能有適用於[網際網路面向負載平衡器](../load-balancer/load-balancer-internet-overview.md)的子網路，以及適用於[內部負載平衡器](../load-balancer/load-balancer-internal-overview.md)的個別子網路。 |
| 虛擬網路 |建立虛擬網路︰<BR><BR>$rgName = "[resource-group-name](../powershell-azure-resource-manager.md)"<BR>$locName = "[location-name](https://msdn.microsoft.com/library/azure/dn495177.aspx)"<BR>$vnetName = "virtual-network-name"<BR>$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $internetSubnet,$internalSubnet<BR><BR>取得虛擬網路清單︰<BR><BR>[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName $rgName &#124; 排序名稱 |
| 網域名稱標籤 |$domName = "domain-name"<BR>[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName $domName -Location $locName<BR><BR>您可以指定[公用 IP 資源](../virtual-network/virtual-network-ip-addresses-overview-arm.md)的 DNS 網域名稱標籤，以建立 domainnamelabel.location.cloudapp.azure.com 與 Azure 受管理 DNS 伺服器中的公用 IP 位址的對應。標籤只能包含字母、數字和連字號。第一個和最後一個字元必須是字母或數字，而網域名稱標籤必須是其 Azure 位置內唯一的。您一定要測試您的網域名稱標籤是否為全域唯一的。如果傳回 **True**，表示您提出的名稱是全域唯一的。 |
| 公用 IP 位址 |$ipName = "public-ip-address-name"<BR>$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name $ipName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic<BR><BR>公用 IP 位址會使用您先前建立並由負載平衡器前端組態使用的網域名稱標籤。 |
| 前端 IP 組態 |$feConfigName = "frontend-ip-config-name"<BR>$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name $feConfigName -PublicIpAddress $pip<BR><BR>前端組態包含您先前針對連入網路流量建立的公用 IP 位址。 |
| 後端位址集區 |$bePoolName = "back-end-pool-name"<BR>$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name $bePoolName<BR><BR>提供可透過網路介面存取的負載平衡器後端內部位址。 |
| 探查 |$probeName = "health-probe-name"<BR>$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name $probeName -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>包含健康情況探查，可用來檢查後端位址集區中虛擬機器執行個體的可用性。 |
| 負載平衡規則 |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>包含將負載平衡器上的公用連接埠對應至後端位址集區中的連接埠的規則。 |
| 傳入的 NAT 規則 |$ruleName = "NAT-rule-name"<BR>$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name $ruleName -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>包含將負載平衡器上的公用連接埠對應至後端位址集區中特定虛擬機器的連接埠的規則。 |
| 負載平衡器 |$lbName = "load-balancer-name"<BR>$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName $rgName -Name $lbName -Location $locName -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| 網路介面 |$vnet = [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -Name $vnetName -ResourceGroupName $rgName<BR>$internalSubnet = [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "internalSubnet" -VirtualNetwork $vnet<BR>$nicName = "network-interface-name"<BR>$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName $rgName -Name $nicName -Location $locName -PrivateIpAddress 10.0.2.6 -Subnet $internalSubnet -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>建立一個網路介面，其使用您先前建立的公用 IP 位址和虛擬網路子網路。 |

## 後續步驟
* 使用您剛才[建立 VM ](virtual-machines-windows-ps-create.md)時所建立的網路介面。
* 深入了解如何[建立具有多個網路介面的 VM](../virtual-network/virtual-networks-multiple-nics.md)。

<!---HONumber=AcomDC_0629_2016-->