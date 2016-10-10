<properties
    pageTitle="使用 PowerShell 在 Resource Manager 中建立配置有 IPv6 的網際網路面向負載平衡器 | Microsoft Azure"
    description="了解如何使用 PowerShell 在 Resource Manager 中建立配置有 IPv6 的網際網路面向負載平衡器"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# 開始使用 PowerShell 在 Resource Manager 中建立配置有 IPv6 的網際網路面向負載平衡器

> [AZURE.SELECTOR]
- [PowerShell](load-balancer-IPv6-internet-ps.md)
- [Azure CLI](load-balancer-IPv6-internet-cli.md)
- [範本](load-balancer-IPv6-internet-template.md)

Azure 負載平衡器是第 4 層 (TCP、UDP) 負載平衡器。此負載平衡器可藉由在負載平衡器集合中，將連入流量分散於雲端服務或虛擬機器中狀況良好的服務執行個體之間，來提供高可用性。Azure Load Balancer 也會在多個連接埠、多個 IP 位址或兩者上顯示這些服務。

## 範例部署案例

下圖說明本文中部署的負載平衡解決方案。

![負載平衡器案例](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

在此案例中，您將建立下列 Azure 資源：

- 配置有 IPv4 和 IPv6 公用 IP 位址的網際網路面向負載平衡器
- 兩個負載平衡規則，用以對應公用 VIP 至私人端點
- 包含兩個 VM 的可用性設定組
- 兩部虛擬機器 (VM)
- 虛擬網路介面，用於每個已指派 IPv4 和 IPv6 位址的 VM

## 使用 Azure PowerShell 部署解決方案

下列步驟說明如何搭配 PowerShell 使用 Azure Resource Manager，來建立網際網路對向負載平衡器。使用 Azure Resource Manager 時，會個別建立並設定每項資源，然後放在一起來建立一項資源。

若要部署負載平衡器，請建立並設定下列物件：

- 前端 IP 組態 - 包含傳入網路流量的公用 IP 位址。
- 後端位址集區 - 包含虛擬機器的網路介面 (NIC)，可從負載平衡器接收網路流量。
- 負載平衡規則 - 包含將負載平衡器上的公用連接埠對應至後端位址集區中連接埠的規則。
- 輸入 NAT 規則 - 包含將負載平衡器上的公用連接埠對應至後端位址集區中特定虛擬機器之連接埠的規則。
- 探查 - 包含用來檢查後端位址集區中虛擬機器執行個體可用性的健全狀態探查。

如需詳細資料，請參閱 [Azure Resource Manager 的負載平衡器支援](load-balancer-arm.md)。

## 設定 PowerShell 以使用 Resource Manager

請確定您擁有適用於 PowerShell 的 Azure Resource Manager 模組最新生產版本。

1. 登入 Azure

        Login-AzureRmAccount

    出現提示時，請輸入您的認證。

2. 檢查帳戶的訂用帳戶

        Get-AzureRmSubscription

3. 選擇要使用哪一個 Azure 訂用帳戶。

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. 建立資源群組 (如果是使用現有的資源群組，請略過此步驟)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## 建立前端 IP 集區的虛擬網路和公用 IP 位址

1. 建立具有子網路的虛擬網路。

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. 建立前端 IP 位址集區的 Azure 公用 IP 位址 (PIP) 資源。

        $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
        $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6

    >[AZURE.IMPORTANT] 負載平衡器會使用公用 IP 的網域標籤做為其 FQDN 的前置詞。在此範例中，FQDN 是 *lbnrpipv4.westus.cloudapp.azure.com* 和 *lbnrpipv6.westus.cloudapp.azure.com*。

## 建立前端 IP 組態和後端位址集區

1. 建立會使用您所建立之公用 IP 位址的前端位址組態。

        $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
        $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6

2. 建立後端位址集區。

        $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
        $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"


## 建立 LB 規則、NAT 規則、探查及負載平衡器

此範例會建立下列項目：

- NAT 規則，用以將連接埠 443 上的所有傳入流量轉譯至連接埠 4443
- 一個可將連接埠 80 上所有傳入流量負載平衡至後端集區中位址上連接埠 80 的負載平衡器規則。
- 探查規則，用以檢查名為 *HealthProbe.aspx* 的頁面或連接埠 8080 的健全狀況
- 會使用上述所有物件的負載平衡器

1. 建立 NAT 規則。

        $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
        $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443

2. 建立健全狀況探查。有兩種方式可以設定探查：

    HTTP 探查

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    或 TCP 探查

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2

    在此範例中，我們使用 TCP 探查。

3. 建立負載平衡器規則。

        $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080

4. 使用先前建立的物件來建立負載平衡器。

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe -LoadBalancingRule $lbrule1v4,$lbrule1v6

## 建立後端 VM 的 NIC

1. 取得需要在其中建立 NIC 的虛擬網路和虛擬網路子網路。

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. 建立VM 的 IP 組態和 NIC。

        $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
        $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
        $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

        $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
        $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
        $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'

3. 建立 NIC，並將它們關聯至 NAT 規則和後端位址集區。

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

## 建立虛擬機器並指派新建立的 NIC

如需有關建立 VM 的詳細資訊，請參閱[使用 Resource Manager 和 Azure PowerShell 建立及預先設定 Windows 虛擬機器](..\virtual-machines\virtual-machines-windows-ps-create.md)。

1. 建立可用性設定組和儲存體帳戶

        New-AzureRmAvailabilitySet -Name $availabilitySetName -ResourceGroupName NRP-RG -location 'West US'
        $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
        New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name $vmStorageAccount -Location 'West US' -SkuName $LRS
        $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name $vmStorageAccount

2. 建立每個 VM 並指派先前建立的 NIC

        $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0 -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0 -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
        $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
        $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

        $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1 -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1 -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
        $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
        $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2

## 後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0928_2016-->