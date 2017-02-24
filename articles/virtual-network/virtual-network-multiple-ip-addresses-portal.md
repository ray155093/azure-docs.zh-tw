---
title: "Azure 虛擬機器的多個 IP 位址 - 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站 / Resource Manager 對虛擬機器指派多個 IP 位址。"
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
ms.date: 11/30/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 394315f81cf694cc2bb3a28b45694361b11e0670
ms.openlocfilehash: 6e7eac6ae505c627ffa1d63aace76b9006d92c74


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>使用 Azure 入口網站將多個 IP 位址指派給虛擬機器

>[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
>
本文說明如何使用 Azure 入口網站透過 Azure Resource Manager 部署模型建立虛擬機器 (VM)。 無法將多個 IP 位址指派給透過傳統部署模型建立的資源。 若要深入了解 Azure 部署模型，請參閱[了解部署模型](../resource-manager-deployment-model.md)文章。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>建立有多個 IP 位址的 VM

如果您想要建立具有多重 IP 位址的 VM，您必須使用 PowerShell 或 Azure CLI 加以建立。 按一下本文頂端的 PowerShell 或 CLI 選項，即可了解作法。 您可以依照[建立 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 或[建立 Linux VM](../virtual-machines/virtual-machines-linux-quick-create-portal.md)文章中的步驟，透過入口網站來建立具有單一靜態私人 IP 位址及 (選擇性) 單一公用 IP 位址的 VM。 建立 VM 之後，您可以依照[將 IP 位址新增至 VM](#add)一節中的步驟，透過入口網站來變更 IP 位址類型並新增其他 IP 位址。

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>將 IP 位址新增至 VM

您可以完成後續步驟，將私人和公用 IP 位址新增至 NIC。 下列各節中的範例假設您已經有一個 VM，其具有本文中的[案例](#Scenario)所述的三項 IP 組態，您不需要進行設定。

### <a name="a-namecoreaddacore-steps"></a><a name="coreadd"></a>核心步驟

1. 登入並選取適當的訂用帳戶後，在 PowerShell 中執行下列命令來註冊預覽︰
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
    
2. 瀏覽至 Azure 入口網站 (https://portal.azure.com) 並視需要進行登入。
3. 在 Azure 入口網站中，按一下 [更多服務]，接著在篩選方塊中輸入「虛擬機器」，然後按一下 [虛擬機器]。
4. 在 [虛擬機器] 刀鋒視窗中，按一下您想要新增 IP 位址的 VM。 在顯示的 [虛擬機器] 刀鋒視窗中，按一下 [網路介面]，然後選取您想要新增 IP 位址的網路介面。 在下圖顯示的範例中，已選取名為 myVM 的 VM 中名為 myNIC 的 NIC︰

    ![網路介面](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

5. 在針對您所選 NIC 出現的刀鋒視窗中，按一下 [IP 組態]，如下圖所示︰

    ![IP 組態](./media/virtual-network-multiple-ip-addresses-portal/figure2.png)

根據您想要新增的 IP 位址類型，完成後續其中一節的步驟。

### <a name="add-a-private-ip-address"></a>**新增私人 IP 位址**

完成下列步驟，以新增私人 IP 位址：

1. 完成本文的[核心步驟](#coreadd)一節中的步驟。
2. 按一下 [新增] 。 在所顯示的 [新增 IP 組態] 刀鋒視窗中，建立名為 IPConfig-4 並以&10;.0.0.7 做為「靜態」私人 IP 位址的 IP 組態，然後按一下 [確定]，如下圖所示︰

    ![新增私人 IP](./media/virtual-network-multiple-ip-addresses-portal/figure3.png)

    > [!NOTE]
    > 新增靜態 IP 位址時，您必須在 NIC 所連接的子網路上指定未使用的有效位址。 如果您選取的位址無法使用，入口網站會針對 IP 位址顯示 X，而您必須選取不同的位址。

    如果您偏好的私人 IP 位址**配置方法**為「動態」，請改為選取它，而不需要指定 IP 位址。
3. 按一下 [確定] 後，刀鋒視窗會關閉，而您會看到新的 IP 組態列出，如下圖所示︰

    ![IP 組態](./media/virtual-network-multiple-ip-addresses-portal/figure4.png)

    按一下 [確定]，以關閉 [新增 IP 組態] 刀鋒視窗。
4. 您可以按一下 [新增] 來新增其他 IP 組態，或關閉所有開啟的刀鋒視窗以完成 IP 位址新增。
5. 完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中適用於您的作業系統的步驟，將私人 IP 位址新增至 VM 作業系統。

### <a name="add-a-public-ip-address"></a>新增公用 IP 位址

將公用 IP 位址與新的 IP 組態或現有的 IP 組態產生關聯，便可新增公用 IP 位址。

> [!NOTE]
> 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。 訂用帳戶中可使用的公用 IP 位址數目有限制。 若要深入了解限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章。
> 

### <a name="a-namecreate-public-ipacreate-a-public-ip-address-resource"></a><a name="create-public-ip">建立公用 IP 位址資源</a>

公用 IP 位址是公用 IP 位址資源的其中一項設定 如果您的公用 IP 位址資源目前並未關聯至您想要產生關聯的 IP 組態，請略過下列步驟，並視需要完成後續其中一節的步驟。 如果您沒有可用的公用 IP 位址資源，請完成下列步驟來建立一個︰

1. 瀏覽至 Azure 入口網站 (https://portal.azure.com) 並視需要進行登入。
3. 在入口網站中，按一下 [新增] > [網路] > [公用 IP 位址]。
4. 在顯示的 [建立公用 IP 位址]刀鋒視窗中，輸入 [名稱]，選取 [IP 位址指派] 類型、[訂用帳戶]、[資源群組] 和 [位置]，然後按一下 [建立]，如下圖所示︰

    ![建立公用 IP 位址資源](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. 完成後續其中一節的步驟，將公用 IP 位址資源與 IP 組態產生關聯。

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>將公用 IP 位址資源與新的 IP 組態產生關聯

1. 完成本文的[核心步驟](#coreadd)一節中的步驟。
2. 按一下 [新增] 。 在顯示的 [新增 IP 組態] 刀鋒視窗中，建立名為 IPConfig-4 的 IP 組態。 啟用 [公用 IP 位址]，然後從顯示的 [選擇公用 IP 位址] 刀鋒視窗中，選取現有的可用公用 IP 位址資源，如下圖所示︰

    ![新增 IP 組態](./media/virtual-network-multiple-ip-addresses-portal/figure6.png)

    選取公用 IP 位址資源後，請按一下 [確定]，刀鋒視窗便會關閉。 如果您沒有現有的公用 IP 位址，您可以完成[建立公用 IP 位址資源](#create-public-ip)一節中的步驟，加以建立。 

3. 檢閱新的 IP 組態，如下圖所示：

    ![IP 組態](./media/virtual-network-multiple-ip-addresses-portal/figure7.png)

    > [!NOTE]
    > 即使未明確指派私人 IP 位址，但已自動指派一個給 IP 組態，因為所有 IP 組態都必須有一個私人 IP 位址。
    >

4. 您可以按一下 [新增] 來新增其他 IP 組態，或關閉所有開啟的刀鋒視窗以完成 IP 位址新增。
5. 完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中適用於您的作業系統的步驟，將私人 IP 位址新增至 VM 作業系統。 請勿將公用 IP 位址新增至作業系統。

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>將公用 IP 位址資源與現有的 IP 組態產生關聯

1. 完成本文的[核心步驟](#coreadd)一節中的步驟。
2. 選取您要新增公用 IP 位址資源的 IP 組態，啟用公用 IP 位址，然後選取現有的可用公用 IP 位址資源。 在下圖所示的範例中，myPublicIp3 公用 IP 位址資源與 IPConfig&3; 相關聯。

    ![現有 IP 組態](./media/virtual-network-multiple-ip-addresses-portal/figure8.png)

    選取公用 IP 位址資源後，請按一下 [儲存]，刀鋒視窗便會關閉。 如果您沒有現有的公用 IP 位址，您可以完成[建立公用 IP 位址資源](#create-public-ip)一節中的步驟，加以建立。

3. 檢閱新的 IP 組態，如下圖所示：

    ![IP 組態](./media/virtual-network-multiple-ip-addresses-portal/figure9.png)

4. 您可以按一下 [新增] 來新增其他 IP 組態，或關閉所有開啟的刀鋒視窗以完成 IP 位址新增。 請勿將公用 IP 位址新增至作業系統。


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Feb17_HO2-->


