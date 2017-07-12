---
title: "建立 Azure 虛擬網路 (傳統)︰傳統入口網站 | Microsoft Docs"
description: "了解如何在 Azure 傳統入口網站中使用 netcfg 檔案來建立虛擬網路 (傳統)。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 69894a0b-8050-451e-8a25-c513e1bd271e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: d365f7137527d60eb509b4f431295de2218ea706
ms.openlocfilehash: 5ff91cccb711d61ed120e4a4e820d6a5dfc5e4a6
ms.contentlocale: zh-tw
ms.lasthandoff: 01/31/2017


---

<a id="create-a-virtual-network-classic-with-a-netcfg-file-using-the-azure-classic-portal" class="xliff"></a>

# 使用 Azure 傳統入口網站以 netcfg 檔案建立虛擬網路 (傳統)
[!INCLUDE [virtual-networks-create-vnet-selectors-classic-include](../../includes/virtual-networks-create-vnet-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文說明如何使用 Azure 傳統入口網站，透過傳統部署模型以 netcfg 檔案建立虛擬網路。 您也可以[透過傳統部署模型建立虛擬網路而不使用 netcfg 檔案](virtual-networks-create-vnet-classic-pportal.md)，或使用 Azure 入口網站[透過 Azure Resource Manager 部署模型建立虛擬網路](virtual-networks-create-vnet-arm-pportal.md)。

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

<a id="how-to-create-a-vnet-with-a-network-config-file-in-the-microsoft-azure-classic-portal" class="xliff"></a>

## 如何在 Microsoft Azure 傳統入口網站中使用網路組態檔來建立 VNet
Azure 會使用 xml 檔案定義訂用帳戶所有可用的 VNet。 您可以下載這個檔案並加以編輯，以透過傳統部署模型建立 VNet，或是修改或刪除現有的 VNet。 本文說明如何下載此檔案 (稱為網路組態檔或 netcfg 檔案)、將 VNet 新增至此檔案，以及上傳此檔案來建立 VNet。 若要深入了解網路組態檔，請檢閱 [Azure 虛擬網路組態結構描述 (英文)](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

若要透過 Azure 傳統入口網站使用 netcfg 檔案來建立 VNet，請完成下列步驟：

1. 透過瀏覽器瀏覽至 http://manage.windowsazure.com，並視需要使用您的 Azure 帳戶登入。
2. 向下捲動服務清單，按一下 [網路]，然後按一下 [匯出]，如下圖所示︰

    ![Azure 虛擬網路](./media/virtual-networks-create-vnet-classic-portal/networks.png)
3. 在 [匯出網路組態] 對話方塊中，選取您要從中匯出虛擬網路組態的訂用帳戶，然後按一下方塊右下角的打勾記號按鈕。
4. 依照瀏覽器中的指示，儲存 **NetworkConfig.xml** 檔案。 請務必記下儲存檔案的位置。
5. 使用任何 XML 或文字編輯器應用程式來開啟您在步驟 4 中儲存的檔案，然後尋找 `<VirtualNetworkConfiguration>` 元素內的 `<VirtualNetworkSites>` 元素。 如果您有任何現有的 VNet，則每個 VNet 都會列在自己的 `<VirtualNetworkSite>` 元素中。 如果檔案的 `<VirtualNetworkConfiguration>` 元素內未包含 `<VirtualNetworkSites>` 元素，請建立一個。
6. 如果您現有的 NetworkConfig 檔案中沒有任何 VNet，在您將此案例所述的 VNet 新增至檔案中後，您的 NetworkConfig.xml 檔案將會類似以下範例：

    ```xml
    <NetworkConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="TestVNet" Location="Central US">
            <AddressSpace>
              <AddressPrefix>192.168.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>192.168.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>192.168.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    ```
7. 儲存網路組態檔。
8. 在 Azure 傳統入口網站中，依序按一下 [新增]、[網絡服務]、[虛擬網路]，然後按一下 [匯入組態]，如下圖所示：

    ![匯入組態](./media/virtual-networks-create-vnet-classic-portal/import.png)
10. 在 [匯入網路組態檔] 對話方塊中，按一下 [瀏覽檔案]，瀏覽至您在步驟 7 中儲存檔案的資料夾，選取該檔案，然後按一下 [開啟]，如下圖所示：

    ![匯入網路組態檔頁面](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure4.png)

    在方塊右下角，按一下箭頭按鈕以移至下一個步驟。

9. 在 [建立您的網路] 對話方塊中，注意新 VNet 的項目，如下圖所示：

    ![建置您的網路頁面](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure5.png)
10. 若要建立 VNet，請按一下上圖中方塊右下角的打勾記號。 幾秒鐘後，VNet 就會顯示在可用的 VNet 清單中，如下圖所示：

    ![新的虛擬網路](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure6.png)

