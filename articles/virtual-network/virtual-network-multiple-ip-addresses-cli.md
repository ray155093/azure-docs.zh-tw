---
title: "Azure 虛擬機器的多個 IP 位址 - Azure CLI | Microsoft Docs"
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
ms.sourcegitcommit: 394315f81cf694cc2bb3a28b45694361b11e0670
ms.openlocfilehash: 8c2441211f08e8bb22153ff16cbd98c85cb9cf3d


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>使用 Azure CLI 將多個 IP 位址指派給虛擬機器

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

本文說明如何使用 Azure CLI 透過 Azure Resource Manager 部署模型建立虛擬機器 (VM)。 無法將多個 IP 位址指派給透過傳統部署模型建立的資源。 若要深入了解 Azure 部署模型，請參閱[了解部署模型](../resource-manager-deployment-model.md)文章。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>建立有多個 IP 位址的 VM

後續步驟說明如何使用多個 IP 位址建立範例 VM，如案例中所述。 視您的實作而定，變更變數名稱和 IP 位址類型。

1. 依照[安裝和設定 Azure CLI](../xplat-cli-install.md) 文章中的步驟來安裝和設定 Azure CLI，並登入 Azure 帳戶。

2. 登入並選取適當的訂用帳戶後，在 PowerShell 中執行下列命令來註冊預覽︰
    ```
    Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

    Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
    執行 ```Get-AzureRmProviderFeature``` 命令時，請在看到下列輸出之後再嘗試完成剩餘步驟︰
        
    ```powershell
    FeatureName                            ProviderName      RegistrationState
    -----------                            ------------      -----------------      
    AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
    AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
    ```
        
    >[!NOTE] 
    >這可能需要幾分鐘的時間。

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
    > 如果您選取的位置不支援 VM 大小，則「建立 VM」文章中的步驟 6 會失敗。 執行下列命令來取得美國中西部的 VM 完整清單，例如：`azure vm sizes --location westcentralus`。可根據您的案例來變更此位置名稱。

    例如，若要將 VM 大小變更為標準 DS2 v2，只要在步驟 6 中將下列屬性 ```  --vm-size Standard_DS3_v2``` 新增至 ``` azure vm create ``` 命令即可。

7. 輸入下列命令，以檢視 NIC 和相關聯的 IP 設定︰

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```
8. 完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中適用於您的作業系統的步驟，將私人 IP 位址新增至 VM 作業系統。

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
     
9. 依照本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中的指示，將您新增至 NIC 的私人 IP 位址新增至 VM 作業系統。 請勿將公用 IP 位址新增至作業系統。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Feb17_HO2-->


