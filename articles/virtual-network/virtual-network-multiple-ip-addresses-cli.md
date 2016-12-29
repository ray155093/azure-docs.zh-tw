---
title: "虛擬機器的多個 IP 位址 - Azure CLI | Microsoft Docs"
description: "了解如何使用 Azure CLI 將多個 IP 位址指派給虛擬機器 | Resource Manager。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: bf92cdc3f6adf1bfaffbcae4e8a0257d0682e33d
ms.openlocfilehash: 0bb3e5f74cb6d013a2b14b4e4c81f0936f90004a


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>使用 Azure CLI 將多個 IP 位址指派給虛擬機器

> [!div class="op_single_selector"]
> * [Azure 入口網站](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)

Azure 虛擬機器 (VM) 可附加一或多個網路介面 (NIC)。 任何 NIC 都可以獲派一或多個靜態或動態公用及私人 IP 位址。 將多個 IP 位址指派給 VM 可啟用下列功能：

* 在單一伺服器上，以不同 IP 位址和 SSL 憑證裝載多個網站或服務。
* 做為網路虛擬設備，例如防火牆或負載平衡器。
* 能夠將任何 NIC 的任何私人 IP 位址新增到 Azure Load Balancer 後端集區。 在過去，只能將主要 NIC 的主要 IP 位址新增到後端集區。 若要深入了解如何負載平衡多個 IP 設定，請參閱[負載平衡多個 IP 組態](../load-balancer/load-balancer-multiple-ip.md)文章。

連接到 VM 的每個 NIC 皆有一或多個 IP 組態與其相關聯。 每個組態會獲派一個靜態或動態私人 IP 位址。 每個組態可能也有一個相關聯的公用 IP 位址資源。 公用 IP 位址資源會獲派動態或靜態 IP 位址。 如果您不熟悉 Azure 中的 IP 位址，請參閱 [Azure 中的 IP 位址](virtual-network-ip-addresses-overview-arm.md) 文章以深入了解。

本文說明如何使用 PowerShell，將多個 IP 位址指派給透過 Azure Resource Manager 部署模型建立的 VM。 無法將多個 IP 位址指派給透過傳統部署模型建立的資源。 若要深入了解 Azure 部署模型，請參閱[了解部署模型](../resource-manager-deployment-model.md)文章。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>案例
建立具有單一 NIC 的 VM，並連接至虛擬網路。 VM 需要三個不同的「私人」IP 位址和兩個「公用」IP 位址。 IP 位址會指派給下列 IP 組態︰

* **IPConfig-1：**指派「動態」私人 IP 位址 (預設) 和「靜態」公用 IP 位址。
* **IPConfig-2：**指派「靜態」私人 IP 位址和「靜態」公用 IP 位址。
* **IPConfig-3：**指派「動態」私人 IP 位址，沒有公用 IP 位址。
  
    ![多個 IP 位址](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

建立 NIC 時，IP 組態會與 NIC 產生關聯，而建立 VM 時，NIC 會連結至 VM。 此案例使用的 IP 位址類型只是舉例說明。 您可以指派需要的任何 IP 位址和指派類型。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>建立有多個 IP 位址的 VM

後續步驟說明如何使用多個 IP 位址建立範例 VM，如案例中所述。 視您的實作而定，變更變數名稱和 IP 位址類型。

1. 依照[安裝和設定 Azure CLI](../xplat-cli-install.md) 文章中的步驟來安裝和設定 Azure CLI，並登入 Azure 帳戶。

2. 若要註冊以進行預覽，請將電子郵件傳送至[多個 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)，並註明您的訂用帳戶 ID 與用途。 請勿嘗試完成剩餘步驟︰
    - 除非收到電子郵件，通知已接受您進行預覽
    - 除非遵循您收到之電子郵件中的指示
3. [建立資源群組](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations)，接著建立[虛擬網路和子網路](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet)。 將 ``` --address-prefixes ``` 和 ```--address-prefix``` 欄位變更為下列值，以遵循本文描述的確切案例：

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >上面引述的文章使用西歐做為位置來建立資源，但本文使用美國中西部。 適當地變更位置。

4. [建立儲存體帳戶](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account)以用於 VM。


5. 建立 NIC 和您要指派給 NIC 的 IP 設定。 您可以視需要新增、移除或變更組態。 案例中描述下列組態︰

    **IPConfig-1**

    輸入後續命令以建立︰

    - 使用靜態公用 IP 位址的公用 IP 位址資源
    - 使用公用 IP 位址資源和動態私人 IP 位址的 IP 組態

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。 訂用帳戶中可使用的公用 IP 位址數目有限制。 若要深入了解限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章。

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     輸入下列命令來建立具有靜態公用 IP 位址和靜態私人 IP 位址的新公用 IP 位址資源，以及新 IP 組態︰
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    輸入下列命令來建立具有動態私人 IP 位址與無公用 IP 位址的 IP 組態︰

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >雖然本文會將所有 IP 組態指派給單一 NIC，您也可以指派多個 IP 組態給 VM 中的任何 NIC。 若要了解如何建立具有多個 NIC 的 VM，請參閱「建立具有多個 NIC 的 VM」文章。

6. [建立 Linux VM](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms) 文章。 請務必移除 ```  --availset-name myAvailabilitySet \ ``` 屬性，此案例不需要它。 根據您的案例使用適當的位置。 

    >[!WARNING] 
    > 如果您選取的位置不支援 VM 大小，則「建立 VM」文章中的步驟 6 會失敗。 例如，執行下列命令來取得美國中西部的 VM 完整清單。 可根據您的案例來變更此位置名稱。
    > 
    >       azure vm sizes --location westcentralus

    例如，若要將 VM 大小變更為標準 DS2 v2，只要在步驟 6 中將下列屬性 ```  --vm-size Standard_DS3_v2``` 新增至 ``` azure vm create ``` 命令即可。

7. 輸入下列命令，以檢視 NIC 和相關聯的 IP 設定︰

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```

## <a name="a-nameosconfigaadd-ip-addresses-to-a-vm-operating-system"></a><a name="OsConfig"></a>將 IP 位址新增至 VM 作業系統

連接並登入您使用多個私人 IP 位址建立的 VM。 您必須手動新增您新增至 VM 的所有私人 IP 位址 (包括主要位址)。 對您的 VM 作業系統完成下列步驟：

### <a name="windows"></a>Windows

1. 從命令提示字元輸入 *ipconfig /all*。  您只會看到 *Primary* 私人 IP 位址 (透過 DHCP)。
2. 在命令提示字元中輸入 ncpa.cpl，以開啟 [網路連線]。
3. 開啟**區域連線**的內容。
4. 按兩下 [網際網路通訊協定第 4 版 (IPv4)]。
5. 選取 [使用下列 IP 位址]  並輸入下列值︰

    * **IP 位址**︰輸入 *Primary* 私人 IP 位址
    * **子網路遮罩**︰根據您的子網路設定。 例如，如果子網路為 /24 子網路，則子網路遮罩為 255.255.255.0。
    * **預設閘道**︰子網路中的第一個 IP 位址。 如果您的子網路為 10.0.0.0/24，則閘道 IP 位址為 10.0.0.1。
    * 按一下 [使用下列 DNS 伺服器位址]  並輸入下列值︰
        * **慣用 DNS 伺服器**︰如果您不是使用自己的 DNS 伺服器，請輸入 168.63.129.16。  如果您是使用自己的 DNS 伺服器，請輸入您的伺服器 IP 位址。
    * 按一下 [進階]  按鈕，然後新增其他 IP 位址。 使用為主要 IP 位址指定時相同的子網路，對 NIC 新增步驟 8 中列出的每個次要私人 IP 位址。
    * 按一下 [確定] 關閉 TCP/IP 設定，然後再按一次 [確定] 關閉介面卡設定。 您的 RDP 連接已重建。
6. 從命令提示字元輸入 *ipconfig /all*。 此時會顯示您加入的所有 IP 位址，而 DHCP 是關閉的。
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. 開啟終端機視窗。
2. 請確定您是 root 使用者。 如果不是，請輸入下列命令：

    ```bash
    sudo -i
    ```

3. 更新網路介面 (假設為 'eth0') 的組態檔。

    * 保留針對 dhcp 的現有行。 主要 IP 位址的設定仍然與先前一樣。
    * 使用下列命令，新增其他靜態 IP 位址的組態︰

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    您應該會看到一個 .cfg 檔案。
4. 開啟檔案︰vi *filename*。

    您應該會在檔案結尾看到下列這幾行：

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. 在此檔案已有的幾行後面加入下列這幾行︰

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. 使用下列命令儲存檔案︰

    ```bash
    :wq
    ```

7. 使用下列命令重設網路介面︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > 如果使用遠端連線，請在同一行中同時執行 ifdown 和 ifup。
    >

8. 使用下列命令確認 IP 位址已加入網路介面︰

    ```bash
    Ip addr list eth0
    ```

    您應該會在清單中看到您加入的 IP 位址。
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat、CentOS 以及其他)

1. 開啟終端機視窗。
2. 請確定您是 root 使用者。 如果不是，請輸入下列命令：

    ```bash
    sudo -i
    ```

3. 輸入您的密碼，並且依照提示的指示。 成為 root 使用者之後，使用下列命令瀏覽至網路指令碼資料夾︰

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. 使用下列命令列出相關的 ifcfg 檔案︰

    ```bash
    ls ifcfg-*
    ```

    您應該會看到其中一個檔案是 *ifcfg-eth0* 。

5. 使用下列命令複製 *ifcfg-eth0* 檔案並將它命名為 *ifcfg-eth0:0*︰

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. 使用下列命令編輯 *ifcfg-eth0:0* 檔案︰

    ```bash
    vi ifcfg-eth1
    ```

7. 使用下列命令，在檔案中將裝置變更為適當名稱，在此案例中為 *eth0:0* ︰

    ```bash
    DEVICE=eth0:0
    ```

8. 變更 *IPADDR = YourPrivateIPAddress* 行以反映 IP 位址。
9. 使用下列命令儲存檔案︰

    ```bash
    :wq
    ```

10. 執行下列命令重新啟動網路服務，並確定所做的變更都成功︰

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    您應該會在傳回的清單中看到您加入的 IP 位址 *eth0:0*。

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>將 IP 位址新增至 VM

您可以完成後續步驟，將其他私用和公用 IP 位址新增至現有的 NIC。 範例以本文章所述的[案例](#Scenario)為基礎。

1. 開啟 Azure CLI，並在單一的 CLI 工作階段內完成本章節的其餘步驟。 如果您尚未安裝和設定 Azure CLI，請完成[安裝和設定 Azure CLI](../xplat-cli-install.md) 文章中的步驟，並登入 Azure 帳戶。

2. 若要註冊以進行預覽，請將電子郵件傳送至[多個 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)，並註明您的訂用帳戶 ID 與用途。 請勿嘗試完成剩餘步驟︰
    - 除非收到電子郵件，通知已接受您進行預覽
    - 除非遵循您收到之電子郵件中的指示


3. 根據您的需求，完成下列其中一個章節中的步驟︰

    **新增私人 IP 位址**
    
    若要將私人 IP 位址新增至 NIC，您必須使用下列命令建立 IP 設定。  如果您想要新增動態私人 IP 位址，請在輸入命令之前移除 ```-PrivateIpAddress 10.0.0.7```。 指定靜態 IP 位址時，它必須是子網路未使用的位址。

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    使用唯一組態名稱和私人 IP 位址 (適用於具有靜態 IP 位址的組態)，視需要建立最多的組態。

    **新增公用 IP 位址**
    
    新增公用 IP 位址的方法是將它與新的 IP 組態或現有的 IP 組態產生關聯。 視需要完成後續其中一節的步驟。

    > [!NOTE]
    > 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。 訂用帳戶中可使用的公用 IP 位址數目有限制。 若要深入了解限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章。
    >

    **將資源與新的 IP 組態產生關聯**
    
    每當您在新的 IP 組態中新增公用 IP 位址時，也必須新增私人 IP 位址，因為所有的 IP 組態都必須有一個私人 IP 位址。 您可以新增現有的公用 IP 位址資源，或建立一個新的資源。 若要建立新的公用 IP 位址資源，請輸入下列命令：
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

    若要建立具有動態私人 IP 位址和相關聯的 *myPublicIP3* 公用 IP 位址資源的新 IP 組態，請輸入下列命令︰

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **將資源與現有的 IP 組態產生關聯**
    公用 IP 位址資源只能與尚未相關聯的 IP 組態產生關聯。 您可以輸入下列命令，判斷 IP組態是否有相關聯的公用 IP 位址︰

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    在傳回的輸出中，尋找類似接下來的這一行︰
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    由於 IpConfig-3 的 [公用 IP] 欄是空白，表示目前沒有相關聯的公用 IP 位址資源。 您可以將現有的公用 IP 位址資源新增至 IpConfig-3，或輸入下列命令以建立一個︰

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    輸入下列命令，將公用 IP 位址資源與名為 *IpConfig-3* 的現有 IP 組態產生關聯：
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```


7. 輸入下列命令，以檢視指派給 NIC 的私人 IP 位址和公用 IP 位址資源︰

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    您應該會看到如下所示的輸出： 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. 依照本文的[將 IP 位址新增至 VM 作業系統](#OsConfig)一節中的指示，將您新增至 NIC 的 IP 位址新增至 VM 作業系統。


<!--HONumber=Nov16_HO3-->


