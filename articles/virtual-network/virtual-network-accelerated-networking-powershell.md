---
title: "虛擬機器的加速網路 - PowerShell | Microsoft Docs"
description: "了解如何使用 PowerShell 設定 Azure 虛擬機器的加速網路。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a0b63599-c23b-40b5-a8ab-23af8b07dded
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5edba79e5c65f0e9411a418ac1a121fd938feff2
ms.lasthandoff: 03/31/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-powershell"></a>使用 PowerShell 設定虛擬機器的加速網路
> [!div class="op_single_selector"]
> * [Azure 入口網站](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

加速網路可以對虛擬機器 (VM) 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升網路效能。 這個高效能路徑會略過資料路徑的主機而減少延遲、抖動和 CPU 使用率，供支援的 VM 類型上最嚴苛的網路工作負載使用。 本文說明如何使用 Azure PowerShell 設定 Azure Resource Manager 部署模型中的加速網路。 您也可以使用 Azure 入口網站建立有加速網路的 VM。 若要了解做法，請按一下這篇文章頂端的 [Azure 入口網站] 方塊。

下圖顯示有和沒有加速網路的兩部虛擬機器 (VM) 之間的通訊︰

![比較](./media/virtual-network-accelerated-networking-powershell/image1.png)

如果沒有加速網路，進出 VM 的所有網路流量都必須周遊主機和虛擬交換器。 虛擬交換器對網路流量提供所有原則強制執行，例如網路安全性群組、存取控制清單、隔離性以及其他網路虛擬化服務。 若要深入了解，請閱讀 [Hyper-V Network Virtualization and Virtual Switch (Hyper-V 網路虛擬化和虛擬交換器)](https://technet.microsoft.com/library/jj945275.aspx) 文章。

如果使用加速網路，網路流量會送達網路卡 (NIC)，然後轉送到 VM。 虛擬交換器在不使用加速網路時套用的所有網路原則會卸載並在硬體中套用。 在硬體中套用原則會讓 NIC 略過主機和虛擬交換器，同時在主機中維護套用的所有原則，直接將網路流量轉送到 VM。

加速網路的優點只適用於已啟用此功能的 VM。 為了獲得最佳結果，最好在至少兩部連線到相同 VNet 的 VM 上啟用此功能。  當透過 VNet 通訊或連線內部部署時，此功能對整體延遲的影響可以降到最低。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>優點
* **較低的延遲 / 較高的每秒封包數目 (pps)：** 從資料路徑移除虛擬交換器會減少主機中封包在處理原則時所花的時間，並增加 VM 內可處理的封包數目。
* **減少抖動︰** 虛擬交換器處理視需要套用的原則數量和正在進行處理的 CPU 工作負載而定。 將原則強制執行卸載到硬體透過將封包直接傳遞到 VM、移除主機到 VM 的通訊，以及所有軟體插斷和環境切換，而減少變化。
* **降低 CPU 使用率︰** 略過主機中的虛擬交換器可減少處理網路流量的 CPU 使用率。

## <a name="limitations"></a>限制
使用這項功能時，有下列限制︰

* **網路介面建立︰** 只能對新的網路介面啟用加速網路。  無法在現有的網路介面上啟用。
* **VM 建立：** 啟用加速網路的網路介面只能在 VM 建立之後附加至 VM。 網路介面無法附加至現有的 VM。
* **區域︰** 只在美國中西部和西歐的 Azure 區域中提供。 未來將延伸區域範圍。
* **支援的作業系統︰** Microsoft Windows Server 2012 R2 和 Windows Server 2016 Technical Preview 5。 即將加入 Linux 和 Windows Server 2012 的支援。
* **VM 大小︰**只支援 Standard_D15_v2 和 Standard_DS15_v2 的 VM 執行個體大小。 如需詳細資訊，請參閱 [Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 文章。 未來將增加支援的 VM 執行個體大小。

這些限制如有變更將會透過 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/accelerated-networking-in-preview)頁面發佈。

## <a name="create-a-windows-vm-with-accelerated-networking"></a>建立使用加速網路的 Windows VM
1. 開啟 PowerShell 命令提示字元，在單一 PowerShell 工作階段內完成本節中其餘的步驟。 如果您尚未安裝和設定 PowerShell，請先完成 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 文章中的步驟。
2. 若要註冊以進行預覽，請傳送電子郵件給 [加速網路訂用帳戶](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) ，其中包含您的訂用帳戶 ID 與用途。 直到您收到電子郵件通知您已被接受到預覽中，再完成其餘步驟。
3. 輸入下列命令，使用您的訂用帳戶註冊功能︰
   
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
4. 將 *westcentralus* 取代為本文 [限制](#limitations) 一節中列出此功能支援的另一個位置名稱。 輸入下列命令，設定位置的變數︰
   
        $locName = "westcentralus"
5. 將 *RG1* 取代為將包含新網路介面的資源群組名稱，並輸入下列命令來建立名稱︰
   
        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
6. 將 *VM1-NIC1* 變更為想要為網路介面命名的名稱，然後輸入下列命令︰
   
        $NICName = "VM1-NIC1"
7. 網路介面必須連線到與網路介面相同位置和 [訂用帳戶](../azure-glossary-cloud-terminology.md#subscription) 中現有 Azure 虛擬網路 (VNet) 內的子網路。 如果您不熟悉上述內容，請閱讀 [虛擬網路概觀](virtual-networks-overview.md) 深入了解 VNet。 若要建立 VNet，請完成 [建立 VNet](virtual-networks-create-vnet-arm-ps.md) 文章中的步驟。 將下列「$變數」的 "值" 變更為 VNet 和您想要將網路介面連線的 VNet 名稱和子網路。
   
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
   
    如果您不知道您在步驟 3 所選擇位置中現有的 VNet 名稱，請輸入下列命令︰
   
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
   
    如果傳回的清單是空的，您必須在該位置中建立 VNet。 若要建立 VNet，請完成 [建立虛擬網路](virtual-networks-create-vnet-arm-ps.md) 文章中的步驟。
   
    若要取得 VNet 內的子網路名稱，請輸入下列命令並將上面的 *Subnet1* 取代為子網路的名稱︰
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix
8. 輸入下列命令來擷取 VNet 和子網路，並將它們指派給變數。
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }
9. 找出可與網路介面相關的現有公用 IP 位址資源，以便您可以透過網際網路連線。 如果您不想使用網路介面透過網際網路存取 VM，可以略過此步驟。 如果沒有公用 IP 位址，您就必須從另一部連線到相同 VNet 的 VM 連線到 VM。 
   
    將 *PIP1* 變更為您要建立網路介面的位置中存在且目前沒有與另一個網路介面相關聯的現有公用 IP 位址資源名稱。 如有必要，將 $rgName 變更為公用 IP 位址資源所在的資源群組名稱，並輸入下列命令︰
   
        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName
   
    如果您不知道現有公用 IP 位址資源的名稱，請輸入下列命令︰
   
        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration
   
    如果傳回的輸出中 **IPConfiguration** 欄沒有任何值，公用 IP 位址資源就與現有的網路介面無關聯，可以使用。 如果清單空白，或沒有可用的公用 IP 位址資源，您可以使用 New-AzureRmPublicIPAddress 命令建立一個。
   
   > [!NOTE]
   > 公用 IP 位址需要少許費用。 如需深入了解定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。
   > 
   > 
10. 如果您選擇不將公用 IP 位址資源加入介面，請移除下面命令結尾的 *-PublicIPAddress $PIP1* 。 輸入下列命令建立有加速網路的網路介面︰
    
        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 
11. 依照 [建立 VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 文章的步驟 3 和 6 中的指示建立 VM 時，對 VM 指派網路介面。 在步驟 6-2 中，將 *Standard_A1* 取代為本文[限制](#limitations)一節列出的其中一個 VM 大小。
    
    > [!NOTE]
    > 如果您變更了本文中 $locName、$rgName 或 $nic 變數的「名稱」，＜建立 VM＞一文中的步驟 6 將會失敗。 不過，您可以變更變數的「值」  。
    > 
    > 
12. VM 建立好之後，請下載 [加速網路驅動程式](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)、連線到 VM，並在 VM 內執行驅動程式安裝程式。
13. 以滑鼠右鍵按一下 Windows 按鈕，然後按一下 [裝置管理員]。 確認 [網路] 選項在展開時，下面出現 **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**，如下圖所示︰
    
    ![裝置管理員](./media/virtual-network-accelerated-networking-powershell/image2.png)


