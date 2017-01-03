---
title: "虛擬機器的多個 IP 位址 - 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站對虛擬機器指派多個 IP 位址。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 716046d5e23de015da42f25f8a1f674de228efac
ms.openlocfilehash: a04192ffde57ae38d901a78a74724a89f43caedb


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>對虛擬機器指派多個 IP 位址
> [!div class="op_single_selector"]
> * [Azure 入口網站](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)


Azure 虛擬機器 (VM) 可以附加一或多個網路介面 (NIC)。 任何 NIC 都可以有一或多個指派的公用或私人 IP 位址。 如果您不熟悉 Azure 中的 IP 位址，請參閱 [Azure 中的 IP 位址](virtual-network-ip-addresses-overview-arm.md) 文章以深入了解。 本文說明如何使用 Azure 入口網站將多個 IP 位址指派給 Azure Resource Manager 部署模型中的 VM。

將多個 IP 位址指派給 VM 可啟用下列功能：

* 在單一伺服器上，以不同 IP 位址和 SSL 憑證裝載多個網站或服務。
* 做為網路虛擬設備，例如防火牆或負載平衡器。
* 能夠將任何 NIC 的任何私人 IP 位址新增到 Azure Load Balancer 後端集區。 在過去，只能將主要 NIC 的主要 IP 位址新增到後端集區。

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

[本文](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)將逐步解說如何建立上述資源 (如果您尚未建立)。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>建立有多個 IP 位址的 VM
若要透過 Azure Preview 入口網站，建立上述案例中的多個 IP 組態，請依照下列步驟執行。

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 選取您想要新增 IP 組態的網路介面︰[虛擬機器] > [VM1] > [網路介面] > [VM1 NIC1]
3. 在 [網路介面] 刀鋒視窗中，選取 [IP 組態]。 您會看到現有 IP 組態的清單。
4. 若要建立關聯 **PIP1** 與 **ipconfig1**，請在 [IP 組態] 刀鋒視窗中，選取 [ipconfig1]。 在 [公用 IP 位址] 底下的 [ipconfig1] 刀鋒視窗中，選取 [啟用]。
5. 在 [IP 位址] 索引標籤中選取 [設定所需設定]，然後選取 [PIP1] 或您要與此主要 IP 組態產生關聯的公用 IP 位址，然後按一下 [儲存]。 您也可以建立新的公用 IP 位址以在這裡關聯。 然後，您會看到儲存網路介面的通知，一旦完成後，您會看見 **PIP1** 與 **ipconfig1** 相關聯。

    ![替代映像文字](media\\virtual-network-multiple-ip-addresses-portal\\01-portal.PNG)

    >[!NOTE] 
    > 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。

1. 接著，若要新增 IP 設定，在您網路介面的 **IP 組態**區段按一下 [+ 新增]。

   > [!NOTE]
   > 您最多可以對一個 NIC 指派 250 個私人 IP 位址。 訂用帳戶內可使用的公用 IP 位址數目有限制。 若要深入了解，請閱讀 [Azure 限制](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager) 文章。
   >
   >
2. 在 [新增 IP 組態] 刀鋒視窗中，命名您的 IP 設定。 就是這個，**IPConfig 2**。 選取 [配置] 的 [靜態] 並輸入您想要的 IP 位址。 在此案例中，指的是 10.0.0.5。 然後按一下 [確定] 。 一旦儲存組態後，您會看到清單中的 IP 設定。

    ![替代映像文字](media\\virtual-network-multiple-ip-addresses-portal\\02-portal.PNG)
3. 接下來，新增三個 IP 組態，方法為選取 [IP 組態] 刀鋒視窗中的 [+ 新增] ，然後填寫必要的詳細資訊，如下所示。 然後選取 [確定]。

    ![替代映像文字](media\\virtual-network-multiple-ip-addresses-portal\\03-portal.PNG)

    請注意，IPConfig-2 和 IPConfig 3 也可以與公用 IP 相關聯，方法為在 [新增 IP 組態]刀鋒視窗選取**公用 IP 位址**區段中的 [啟用]。 您可以建立新的公用 IP 或已建立的公用 IP 可以與 IP 組態相關聯。
4. 手動將所有私人 IP 位址 (包括主要位址) 新增到作業系統中的 TCP/IP 設定，如下所示。 要以手動方式新增 IP 位址，必須連接到您的 VM，然後依照下列步驟進行。

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

## <a name="a-nameaddaadd-ip-addresses-to-an-existing-vm"></a><a name="add"></a>將 IP 位址新增至現有的 VM
完成下列步驟，將其他 IP 位址新增至現有的 NIC：

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 在**網路介面**區段中選取您想要新增 IP 組態的網路介面。
3. 在 [網路介面] 刀鋒視窗中，選取 [IP 組態]。 您會看到現有 IP 組態的清單。
4. 接著，若要新增 IP 設定，在您網路介面的 IP 組態區段按一下 [+ 新增]。
5. 在 [新增 IP 組態] 刀鋒視窗中，命名您的 IP 組態。 在 [配置] 下選取您想要的 IP 位址類型︰靜態或動態。 如果您想要將 IP 組態與公用 IP 相關聯，請在 [公用 IP 位址] 下選取 [啟用]。 如果不是，請按一下 [停用]。 選取 [啟用] 可讓您另外將現有公用 IP 與您的組態相關聯，然後按一下 [確定]。 一旦儲存組態後，您會看到清單中的 IP 設定。



<!--HONumber=Nov16_HO3-->


