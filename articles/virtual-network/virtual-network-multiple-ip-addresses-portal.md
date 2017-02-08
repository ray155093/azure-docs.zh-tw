---
title: "虛擬機器的多個 IP 位址 - 入口網站 | Microsoft Docs"
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
ms.sourcegitcommit: 7ce83952f0f16e01a4837ce2155571d223d7b551
ms.openlocfilehash: b1a2549e0f04dbc00a47a57ecc888ca36339c646


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>使用 Azure 入口網站將多個 IP 位址指派給虛擬機器

> [!div class="op_single_selector"]
> * [入口網站](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)
>

Azure 虛擬機器 (VM) 可附加一或多個網路介面 (NIC)。 任何 NIC 都可以獲派一或多個靜態或動態公用及私人 IP 位址。 將多個 IP 位址指派給 VM 可啟用下列功能：

* 在單一伺服器上，以不同 IP 位址和 SSL 憑證裝載多個網站或服務。
* 做為網路虛擬設備，例如防火牆或負載平衡器。
* 能夠將任何 NIC 的任何私人 IP 位址新增到 Azure Load Balancer 後端集區。 在過去，只能將主要 NIC 的主要 IP 位址新增到後端集區。 若要深入了解如何負載平衡多個 IP 設定，請參閱[負載平衡多個 IP 組態](../load-balancer/load-balancer-multiple-ip.md)文章。

連接到 VM 的每個 NIC 皆有一或多個 IP 組態與其相關聯。 每個組態會獲派一個靜態或動態私人 IP 位址。 每個組態可能也有一個相關聯的公用 IP 位址資源。 公用 IP 位址資源會獲派動態或靜態 IP 位址。 若要深入了解 Azure 中的 IP 位址，請閱讀 [Azure 中的 IP 位址](virtual-network-ip-addresses-overview-arm.md)文章。

本文說明如何使用 Azure 入口網站，將多個 IP 位址指派給透過 Azure Resource Manager 部署模型建立的 VM。 無法將多個 IP 位址指派給透過傳統部署模型建立的資源。 若要深入了解 Azure 部署模型，請參閱[了解部署模型](../resource-manager-deployment-model.md)文章。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>案例
建立具有單一 NIC 的 VM，並連接至虛擬網路。 VM 需要三個不同的「私人」IP 位址和兩個「公用」IP 位址。 IP 位址會指派給下列 IP 組態︰

* **IPConfig-1：**指派「動態」私人 IP 位址 (預設) 和「靜態」公用 IP 位址。
* **IPConfig-2：**指派「靜態」私人 IP 位址和「靜態」公用 IP 位址。
* **IPConfig-3：**指派「動態」私人 IP 位址，沒有公用 IP 位址。
  
    ![多個 IP 位址](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

建立 NIC 時，IP 組態會與 NIC 產生關聯，而建立 VM 時，NIC 會連結至 VM。 此案例使用的 IP 位址類型只是舉例說明。 您可以指派需要的任何 IP 位址和指派類型。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>建立有多個 IP 位址的 VM

如果您想要建立具有多重 IP 位址的 VM，您必須使用 PowerShell 或 Azure CLI 加以建立。 按一下本文頂端的 PowerShell 或 CLI 選項，即可了解作法。 您可以依照[建立 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 或[建立 Linux VM](../virtual-machines/virtual-machines-linux-quick-create-portal.md)文章中的步驟，透過入口網站來建立具有單一靜態私人 IP 位址及 (選擇性) 單一公用 IP 位址的 VM。 建立 VM 之後，您可以依照[將 IP 位址新增至 VM](#add)一節中的步驟，透過入口網站來變更 IP 位址類型並新增其他 IP 位址。

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>將 IP 位址新增至 VM

您可以完成後續步驟，將私人和公用 IP 位址新增至 NIC。 下列各節中的範例假設您已經有一個 VM，其具有本文中的[案例](#Scenario)所述的三項 IP 組態，您不需要進行設定。

> [!NOTE]
> 雖然本文會將所有 IP 組態指派給單一 NIC，您也可以指派多個 IP 組態給 VM 中的任何 NIC。 閱讀[建立具有多個 NIC 的 VM](virtual-network-deploy-multinic-arm-ps.md) 文章以了解如何建立具有多個 NIC 的 VM。

### <a name="a-namecoreaddacore-steps"></a><a name="coreadd"></a>核心步驟

1. 若要註冊以進行預覽，請將電子郵件傳送至[多個 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)，並註明您的訂用帳戶 ID 與用途。 請勿嘗試完成剩餘步驟︰
    - 除非收到電子郵件，通知已接受您進行預覽
    - 除非遵循您收到之電子郵件中的指示
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
2. 按一下 [新增] 。 在所顯示的 [新增 IP 組態] 刀鋒視窗中，建立名為 IPConfig-4 並以 10.0.0.7 做為「靜態」私人 IP 位址的 IP 組態，然後按一下 [確定]，如下圖所示︰

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
2. 選取您要新增公用 IP 位址資源的 IP 組態，啟用公用 IP 位址，然後選取現有的可用公用 IP 位址資源。 在下圖所示的範例中，myPublicIp3 公用 IP 位址資源與 IPConfig 3 相關聯。

    ![現有 IP 組態](./media/virtual-network-multiple-ip-addresses-portal/figure8.png)

    選取公用 IP 位址資源後，請按一下 [儲存]，刀鋒視窗便會關閉。 如果您沒有現有的公用 IP 位址，您可以完成[建立公用 IP 位址資源](#create-public-ip)一節中的步驟，加以建立。

3. 檢閱新的 IP 組態，如下圖所示：

    ![IP 組態](./media/virtual-network-multiple-ip-addresses-portal/figure9.png)

4. 您可以按一下 [新增] 來新增其他 IP 組態，或關閉所有開啟的刀鋒視窗以完成 IP 位址新增。 請勿將公用 IP 位址新增至作業系統。


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Dec16_HO2-->


