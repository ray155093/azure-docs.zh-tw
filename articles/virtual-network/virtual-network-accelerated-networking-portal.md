---
title: "虛擬機器的加速網路 - 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站設定 Azure 虛擬機器的加速網路。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: af4515c6-4377-4d4a-a104-18fe1348252c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 90dc3b45f470bfac03c34c1a20a7b54b9acbc281
ms.lasthandoff: 03/31/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-the-azure-portal"></a>使用 Azure 入口網站設定虛擬機器的加速網路
> [!div class="op_single_selector"]
> * [Azure 入口網站](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

加速網路可以對虛擬機器 (VM) 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升網路效能。 這個高效能路徑會略過資料路徑的主機而減少延遲、抖動和 CPU 使用率，供支援的 VM 類型上最嚴苛的網路工作負載使用。 本文說明如何使用 Azure 入口網站設定 Azure Resource Manager 部署模型中的加速網路。 您也可以使用 Azure 入口網站建立有加速網路的 VM。 若要了解做法，請按一下這篇文章頂端的 [PowerShell] 方塊。

下圖顯示有和沒有加速網路的兩部虛擬機器 (VM) 之間的通訊︰

![比較](./media/virtual-network-accelerated-networking-portal/image1.png)

如果沒有加速網路，進出 VM 的所有網路流量都必須周遊主機和虛擬交換器。 虛擬交換器對網路流量提供所有原則強制執行，例如網路安全性群組、存取控制清單、隔離性以及其他網路虛擬化服務。 若要深入了解，請閱讀 [Hyper-V Network Virtualization and Virtual Switch (Hyper-V 網路虛擬化和虛擬交換器)](https://technet.microsoft.com/library/jj945275.aspx) 文章。

如果使用加速網路，網路流量會送達網路卡 (NIC)，然後轉送到 VM。 虛擬交換器在不使用加速網路時套用的所有網路原則會卸載並在硬體中套用。 在硬體中套用原則會讓 NIC 略過主機和虛擬交換器，同時在主機中維護套用的所有原則，直接將網路流量轉送到 VM。

加速網路的優點只適用於已啟用此功能的 VM。 為了獲得最佳結果，最好在至少兩部連線到相同 VNet 的 VM 上啟用此功能。 當透過 VNet 通訊或連線內部部署時，此功能對整體延遲的影響可以降到最低。

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
1. 請傳送電子郵件給 [加速網路訂用帳戶](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)，其中包含您的訂用帳戶 ID 與用途，即可註冊以進行預覽。 直到您收到電子郵件通知您已被接受到預覽中，再完成其餘步驟。
2. 登入 Azure 入口網站，網址是 http://portal.azure.com。
3. 完成[建立 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)文章中的步驟並選取下列選項以建立 VM︰
   
   * 選取本文章＜限制＞段落中列出的作業系統。
   * 選取本文章＜限制＞段落中列出的位置 (區域)。
   * 選取本文章＜限制＞段落中列出的 VM 大小。 如果其中一個支援的大小並未列出，請按一下 [選擇大小] 刀鋒視窗中的 [檢視全部]，以從展開的清單中選取大小。
   * 在 [設定] 刀鋒視窗中，按一下 [加速網路] 中的 [啟用]，如下圖所示︰
     
       ![設定](./media/virtual-network-accelerated-networking-portal/image3.png)
     
     > [!NOTE]
     > 只有當您符合以下條件，才會顯示 [加速網路] 選項︰
     > 
     > * 您已被接受到預覽中
     > * 您已選取本文章＜限制＞段落中提及的作業系統、位置和 VM 大小。
     > 
     > 
4. VM 建立好之後，請下載 [加速網路驅動程式](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)、連線並登入 VM，並在 VM 內執行驅動程式安裝程式。
5. 以滑鼠右鍵按一下 Windows 按鈕，然後按一下 [裝置管理員]。 確認 [網路] 選項在展開時，下面出現 **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**，如下圖所示︰
   
    ![裝置管理員](./media/virtual-network-accelerated-networking-portal/image2.png)


