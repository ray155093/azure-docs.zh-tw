---
title: 虛擬機器的多個 IP 位址 - PowerShell | Microsoft Docs
description: 了解如何使用 Azure PowerShell 對虛擬機器指派多個 IP 位址。
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/05/2016
ms.author: jdial;annahar

---
# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>對虛擬機器指派多個 IP 位址
Azure 虛擬機器 (VM) 可以附加一或多個網路介面 (NIC)。 每個 NIC 可以指派一或多個公用或私人 IP 位址。 如果您不熟悉 Azure 中的 IP 位址，請參閱 [Azure 中的 IP 位址](virtual-network-ip-addresses-overview-arm.md) 文章以深入了解。 本文說明如何使用 Azure PowerShell 對 Azure Resource Manager 部署模型中的 NIC 指派多個 IP 位址。

對 NIC 指派多個 IP 位址可讓 VM：

* 在單一伺服器上以不同 IP 位址和 SSL 憑證，裝載多個網站或服務。
* 做為網路虛擬設備，例如防火牆或負載平衡器。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

若要註冊以進行預覽，請傳送電子郵件給 [多個 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) ，其中包含您的訂用帳戶 ID 與用途。

## <a name="scenario"></a>案例
在本文中，您會將三個 IP 組態關聯到網路介面。
下列範例組態將會建立並指派至將指派三個私人 IP 位址和一個公用 IP 位址的 NIC：

* IPConfig-1：一個動態私人 IP 位址 (預設值) 以及一個來自名為 PIP1 的公用 IP 位址資源的公用 IP 位址。
* IPConfig-2︰一個靜態私人 IP 位址，沒有公用 IP 位址。
* IPConfig-3︰一個動態私人 IP 位址，沒有公用 IP 位址。
  
    ![替代映像文字](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

此案例假設您有稱為 *RG1* 的資源群組，其中有稱為 *VNet1* 的 VNet 和稱為 *Subnet1* 的子網路。 此外，也假設您有稱為 *VM1* 的 VM 、稱為 *VM1 NIC1* 且與它相關聯的網路介面，以及稱為 *PIP1* 的公用 IP 位址。

[本文](../virtual-machines/virtual-machines-windows-ps-create.md)將逐步解說如何建立上述資源 (如果您尚未建立)。

## <a name="<a-name-=-"create"></a>create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>建立有多個 IP 位址的 VM
1. 開啟 PowerShell 命令提示字元，在單一 PowerShell 工作階段內完成本節中其餘的步驟。 如果您尚未安裝和設定 PowerShell，請先完成 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 文章中的步驟。
2. 將下列 $Variables 的「值」變更為您的虛擬網路所在的 Azure [位置](https://azure.microsoft.com/regions)、[資源群組](../resource-group-overview.md#resource-groups)的名稱、資源群組內的 VNet、NIC 要連線的目標子網路和 NIC 的名稱。
   
        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"
   
    如果您不知道現有 Azure 位置或資源群組的名稱，請輸入下列命令︰
   
        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName
   
    <a name="subnet"></a>NIC 必須連線到現有 Azure 虛擬網路 (VNet) 內的子網路。 三個元件︰NIC、子網路和 VNet，必須全部存在於相同的區域和[訂用帳戶](../azure-glossary-cloud-terminology.md#subscription)中。  如果您不熟悉 VNet，請閱讀[虛擬網路概觀](virtual-networks-overview.md)文章深入了解，或閱讀[建立 VNet](virtual-networks-create-vnet-arm-ps.md) 文章了解如何建立一個。
   
    如果您不知道現有的 VNet 名稱，請輸入下列命令並將先前變數中的 *VNet1* 取代為 VNet 的名稱︰
   
        Get-AzureRmVirtualNetwork | Format-Table Name
   
    如果傳回的清單是空的，您必須建立 VNet。 若要了解如何建立，請閱讀 [建立虛擬網路](virtual-networks-create-vnet-arm-ps.md) 文章。
   
    輸入下列命令來取得 VNet 內的現有子網路名稱，並將上面的 *Subnet1* 取代為子網路的名稱︰
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix
3. 輸入下列命令來擷取子網路，並將它指派給變數。
   
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }
4. <a name="ipconfigs"></a>定義您想要對 NIC 指派的 IP 組態。 每個組態只能有一個靜態或動態私人 IP 位址，以及一個與靜態或動態位址相關聯的公用 IP 位址資源。
   
    依據您要將多少個 IP 位址與 NIC 關聯，以及您要設定的設定，新增或移除下面任意數目的組態。
   
    **IPConfig-1**
   
    將 *PIP1* 值變更為您要建立 NIC 的位置中存在且目前沒有與另一個 NIC 相關聯的現有公用 IP 位址資源名稱。 將 *RG1* 變更為公用 IP 位址資源所在的資源群組名稱。 將 *IPConfig-1* 變更為您想要提供給第一個 IP 組態的名稱。 輸入下列命令：
   
        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName
   
        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary
   
    請注意 *-Primary* 參數。 當您對 NIC 指派多個 IP 組態時，必須將一個組態指派為 *Primary*。 如果您不知道現有公用 IP 位址資源的名稱，請輸入下列命令︰
   
        Get-AzureRMPublicIPAddress |Format-Table Name, Location, IPAddress, IpConfiguration
   
    如果傳回的輸出中 **IPConfiguration** 欄沒有任何值，公用 IP 位址資源就與現有的 NIC 無關聯，可以使用。 如果清單空白，或沒有可用的公用 IP 位址資源，您可以使用 **New-AzureRmPublicIPAddress** 命令建立一個。
   
   > [!NOTE]
   > 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。
   > 
   > 
   
    **IPConfig-2**
   
    將 *IPConfig-2* 變更為您想要提供給第二個 IP 組態的名稱，並將 *10.0.0.5* 變更為您要將 NIC 指派至有效而未使用的子網路 IP 位址。 輸入下列命令：
   
        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5
   
        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress
   
    如果您不知道指派至子網路的 IP 位址範圍，請輸入下列命令︰
   
        $VNet.Subnets | Format-Table Name, AddressPrefix
   
    **IPConfig-3**
   
    將 *IPConfig-3* 變更為您想要提供給第三個 IP 組態的名稱，並輸入下列命令︰
   
        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
   
   > [!NOTE]
   > 您最多可以對一個 NIC 指派 250 個私人 IP 位址。 訂用帳戶內可使用的公用 IP 位址數目有限制。 若要深入了解，請閱讀 [Azure 限制](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) 文章。
   > 
   > 
5. 使用上一個步驟中定義的 IP 組態建立 NIC。
   
        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
6. 依照 [建立 VM](../virtual-machines/virtual-machines-windows-ps-create.md) 文章中的步驟建立 VM 時，附加 NIC。 雖然該篇文章建立的是執行 Windows Server 的 VM，但是步驟也適用於 Linux VM，差別只在於選取不同的作業系統。 完成文章的步驟 1-3。 略過＜建立 VM＞文章中的步驟 4 和 5，然後完成步驟 6。
   
   > [!WARNING]
   > 如果您在本文中的步驟 6 將名為 $nic 的變數變更為其他內容，或是尚未完成本文的先前步驟，＜建立 VM＞文章中的步驟 6 將會失敗。
   > 
   > 
7. 輸入下列命令，檢視 Azure DHCP 指派給 NIC 的私人 IP 位址，以及指派給 NIC 的公用 IP 位址資源︰
   
        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
8. <a name="os"></a>手動將所有次要私人 IP 位址 (在上一個步驟中，在輸出的 *Primary* 欄中值為 **False** 的 IP 位址) 新增到作業系統中的 TCP/IP 設定。 指派至 *IPConfig-1* 的私人 IP 位址在步驟 5 會透過 Azure DHCP 自動指派至作業系統，因為它是 *Primary* 組態。

**Windows**

1. 從命令提示字元輸入 *ipconfig /all*。  您只會看到 *Primary* 私人 IP 位址 (透過 DHCP)。
2. 接著在命令提示字元視窗中輸入 *ncpa.cpl*。 這會開啟新的視窗。
3. 開啟**區域連線**的內容。
4. 連按兩下 [網際網路通訊協定第 4 版 (IPv4)]
5. 選取 [使用下列 IP 位址]  並輸入下列值︰
   
   * **IP 位址**︰輸入 *Primary* 私人 IP 位址
   * **子網路遮罩**︰根據您的子網路設定。 例如，如果子網路為 /24 子網路，則子網路遮罩為 255.255.255.0。
   * **預設閘道**︰子網路中的第一個 IP 位址。 如果您的子網路為 10.0.0.0/24，則閘道 IP 位址為 10.0.0.1。
   * 按一下 [使用下列 DNS 伺服器位址]  並輸入下列值︰
     * **慣用 DNS 伺服器** ︰如果您不是使用自己的 DNS 伺服器，請輸入 168.63.129.16。  如果您是，請輸入您的 DNS 伺服器的 IP 位址。
   * 按一下 [進階]  按鈕，然後新增其他 IP 位址。 使用為主要 IP 位址指定時相同的子網路，對 NIC 新增步驟 8 中列出的每個次要私人 IP 位址。
   * 按一下 [確定] 關閉 TCP/IP 設定，然後再按一次 [確定] 關閉介面卡設定。 這會接著重新建立 RDP 連線。
6. 從命令提示字元輸入 *ipconfig /all*。 此時會顯示您加入的所有 IP 位址，而 DHCP 是關閉的。

**Linux (Ubuntu)**

1. 開啟終端機視窗。
2. 請確定您是 root 使用者。 如果不是，您可以使用下列命令來達成目的：
   
            sudo -i
3. 更新網路介面 (假設為 'eth0') 的組態檔。
   
   * 保留針對 dhcp 的現有行。 這會如早先所做的設定主要 IP 位址。
   * 使用下列命令，新增其他靜態 IP 位址的組態︰
     
               cd /etc/network/interfaces.d/
               ls
     
       您應該會看到一個 .cfg 檔案。
4. 開啟檔案︰vi *filename*。
   
    您應該會在檔案結尾看到下列這幾行：
   
            auto eth0
            iface eth0 inet dhcp
5. 在此檔案已有的幾行後面加入下列這幾行︰
   
            iface eth0 inet static
            address <your private IP address here>
6. 使用下列命令儲存檔案︰
   
            :wq
7. 使用下列命令重設網路介面︰
   
           sudo ifdown eth0 && sudo ifup eth0
   
   > [!IMPORTANT]
   > 如果使用遠端連線，請在同一行中同時執行 ifdown 和 ifup。
   > 
   > 
8. 使用下列命令確認 IP 位址已加入網路介面︰
   
            ip addr list eth0
   
    您應該會在清單中看到您加入的 IP 位址。

**Linux (Redhat、CentOS 以及其他)**

1. 開啟終端機視窗。
2. 請確定您是 root 使用者。 如果不是，您可以使用下列命令來達成目的：
   
            sudo -i
3. 輸入您的密碼，並且依照提示的指示。 成為 root 使用者之後，使用下列命令瀏覽至網路指令碼資料夾︰
   
            cd /etc/sysconfig/network-scripts
4. 使用下列命令列出相關的 ifcfg 檔案︰
   
            ls ifcfg-*
   
    您應該會看到其中一個檔案是 *ifcfg-eth0* 。
5. 使用下列命令複製 *ifcfg-eth0* 檔案並將它命名為 *ifcfg-eth0:0*︰
   
            cp ifcfg-eth0 ifcfg-eth0:0
6. 使用下列命令編輯 *ifcfg-eth0:0* 檔案︰
   
            vi ifcfg-eth1
7. 使用下列命令，在檔案中將裝置變更為適當名稱，在此案例中為 *eth0:0* ︰
   
            DEVICE=eth0:0
8. 變更 *IPADDR = YourPrivateIPAddress* 行以反映 IP 位址。
9. 使用下列命令儲存檔案︰
   
            :wq
10. 執行下列命令重新啟動網路服務，並確定所做的變更都成功︰
    
            /etc/init.d/network restart
            Ipconfig
    
    您應該會在傳回的清單中看到您加入的 IP 位址 *eth0:0*。

## <a name="<a-name="add"></a>add-ip-addresses-to-an-existing-vm"></a><a name="add"></a>將 IP 位址新增至現有的 VM
完成下列步驟，將其他 IP 位址新增至現有的 NIC：

1. 開啟 PowerShell 命令提示字元，在單一 PowerShell 工作階段內完成本節中其餘的步驟。 如果您尚未安裝和設定 PowerShell，請先完成 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 文章中的步驟。
2. 將下列「$變數」的 "值" 變更為您想要對其新增 IP 位址的 NIC 名稱，以及 NIC 所在的資源群組和位置︰
   
        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"
   
    如果您不知道要變更的 NIC 名稱，請輸入下列命令，然後變更先前變數的值︰
   
        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
3. 輸入下列命令建立變數，並將它設定為現有 NIC︰
   
        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName
4. 完成本文＜建立有多個 IP 位址的 VM＞一節的 [步驟 3](#subnet) ，擷取 NIC 連線到的子網路識別碼。
5. 依照本文＜建立有多個 IP 位址的 VM＞一節的 [步驟 5](#ipconfigs) 中的指示，建立您想要新增到網路的 IP 組態。
6. 將 *$IPConfigName4* 變更為您在上一個步驟中建立的 IP 組態名稱。 若要新增組態，請輸入下列命令︰
   
        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1
7. 若使用 IP 組態設定 NIC，請輸入下列命令︰
   
        Set-AzureRmNetworkInterface -NetworkInterface $nic
8. 依照本文＜建立有多個 IP 位址的 VM＞一節的 [步驟 9](#os) 中的指示，將您新增至 NIC 的 IP 位址新增至 VM 作業系統。

<!--HONumber=Oct16_HO2-->


