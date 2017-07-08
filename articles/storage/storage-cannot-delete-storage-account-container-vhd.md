---
title: "針對在傳統部署中刪除 Azure 儲存體帳戶、容器或 VHD 進行疑難排解 | Microsoft Docs"
description: "針對在傳統部署中刪除 Azure 儲存體帳戶、容器或 VHD 進行疑難排解"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: tysonn
tags: storage
ms.assetid: 0f7a8243-d8dc-432a-9d37-1272a0cb3a5c
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 9f3e824414ad6c1a0aba98a3d549ee63ddc7272f
ms.contentlocale: zh-tw
ms.lasthandoff: 06/29/2017


---
# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>針對在傳統部署中刪除 Azure 儲存體帳戶、容器或 VHD 進行疑難排解
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

當您嘗試刪除 [Azure 入口網站](https://portal.azure.com/)或 [Azure 傳統入口網站](https://manage.windowsazure.com/)中的 Azure 儲存體帳戶、容器或 VHD 時，可能會收到錯誤。 下列情況可能造成問題︰

* 當您刪除 VM 時，磁碟和 VHD 不會自動刪除。 這可能是儲存體帳戶刪除失敗的原因。 我們不會刪除磁碟，讓您可以使用該磁碟來裝載另一個 VM。
* 磁碟上仍有租用或有與磁碟相關聯的 blob。
* 仍有正在使用 Blob、容器或儲存體帳戶的 VM 映像。

若本文中未提及您的 Azure 問題，請造訪 [MSDN 及 Stack Overflow 上的 Azure 論壇](https://azure.microsoft.com/support/forums/)。 您可以在這些論壇上張貼您的問題，或將問題貼到 Twitter 上的 @AzureSupport。 此外，您也可以選取 [Azure 支援](https://azure.microsoft.com/support/options/)網站上的 [取得支援]，來提出 Azure 支援要求。

## <a name="symptoms"></a>徵兆
下列章節列出您在嘗試刪除 Azure 儲存體帳戶、容器或 VHD 時可能收到的常見錯誤。

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>案例 1：無法刪除儲存體帳戶
當您在 [Azure 入口網站](https://portal.azure.com/)中瀏覽至傳統儲存體帳戶，並選取 [刪除] 時，可能會出現妨礙刪除儲存體帳戶的物件清單︰

  ![刪除儲存體帳戶時發生錯誤的映像](./media/storage-cannot-delete-storage-account-container-vhd/newerror.png)

當您在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中瀏覽至儲存體帳戶，並選取 [刪除]時，您可能會看到下列其中一個錯誤︰

- 儲存體帳戶 StorageAccountName 包含 VM 映像。確認這些 VM 映像已移除之後再刪除此儲存體帳戶。

- *無法刪除儲存體帳戶 <vm-storage-account-name>。無法刪除儲存體帳戶 <vm-storage-account-name>：儲存體帳戶 <vm-storage-account-name> 有些作用中的映像及/或磁碟。確認這些映像及/或磁碟已移除之後再刪除此儲存體帳戶。」*

- *儲存體帳戶 <vm-storage-account-name> 有某些作用中的映像及/或磁碟，例如 xxxxxxxxx- xxxxxxxxx-O-209490240936090599。確認這些映像及/或磁碟已移除之後再刪除此儲存體帳戶。*

- *儲存體帳戶 <vm-storage-account-name> 已有 1 個具備作用中映像及/或磁碟成品的容器。請確定這些成品已從映像儲存機制移除之後再刪除此儲存體帳戶。*

- *提交失敗的儲存體帳戶 <vm-storage-account-name> 已有 1 個具備作用中映像及/或磁碟成品的容器。請確定這些成品已從映像儲存機制移除之後再刪除此儲存體帳戶。當您嘗試刪除儲存體帳戶，而且具有與其相關聯的磁碟仍在作用中時，您會看到一則訊息，告訴您有需要刪除的作用中磁碟*。

### <a name="scenario-2-unable-to-delete-a-container"></a>案例 2：無法刪除容器
當您嘗試刪除儲存體容器時，您可能會看到下列錯誤︰

無法刪除儲存體容器 <container name>。*錯誤：「目前容器上沒有租用，且要求中沒有指定任何租用識別碼*。

或

「下列虛擬機器磁碟使用這個容器中的 Blob，因此無法刪除容器: VirtualMachineDiskName1, VirtualMachineDiskName2, ...」

### <a name="scenario-3-unable-to-delete-a-vhd"></a>案例 3：無法刪除 VHD
在您刪除 VM，然後嘗試刪除相關聯的 blob 之後，您可能會收到下列訊息︰

*無法刪除 blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'。錯誤：「目前 blob 上沒有租用，且要求中沒有指定任何租用識別碼。*

或

「Blob 'BlobName.vhd' 正當做虛擬機器磁碟 'VirtualMachineDiskName' 使用，因此無法刪除。」

## <a name="solution"></a>方案
若要解決最常見的問題，請嘗試下列方法︰

### <a name="step-1-delete-any-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>步驟 1︰刪除任何會阻止您刪除儲存體帳戶、容器或 VHD 的磁碟
1. 切換至 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 選取 [虛擬機器] > [磁碟]。

    ![Azure 傳統入口網站上虛擬機器上的磁碟影像。](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)
3. 找出與您想要刪除的儲存體帳戶、容器或 VHD 相關聯的磁碟。 檢查磁碟的位置時，您會發現相關聯的儲存體帳戶、容器或 VHD。

    ![顯示 Azure 傳統入口網站上磁碟的位置資訊的影像](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)
4. 使用下列其中一種方法刪除磁碟：

  - 如果在磁碟的 [已連接到] 欄位上沒有列出 VM，您可以直接刪除磁碟。

  - 如果磁碟是資料磁碟，請遵循下列步驟：

    1. 檢查磁碟所連接之 VM 的名稱。
    2. 移至 [虛擬機器] > [執行個體]，然後尋找 VM。
    3. 確定沒有任何項目會主動使用磁碟。
    4. 選取入口網站底部的 [卸離磁碟] 以卸離磁碟。
    5. 移至 [虛擬機器] > [磁碟]，並等候 [已連接到] 欄位轉為空白。 這表示磁碟已成功從 VM 卸離。
    6. 選取 [虛擬機器] > [磁碟] 底部的 [刪除] 來刪除磁碟。

  - 如果磁碟是作業系統磁碟 ([包含作業系統] 欄位有值，例如 Windows) 並已連接到 VM，請遵循下列步驟來刪除 VM。 作業系統磁碟無法卸離，因此我們必須刪除 VM 才能釋放租用。

    1. 檢查 [資料磁碟] 所連接到的虛擬機器名稱。  
    2. 移至 [虛擬機器] > [執行個體]，然後選取磁碟所連接到的 VM。
    3. 確定沒有正在使用虛擬機器的項目，而且您不再需要虛擬機器。
    4. 選取磁碟所連接到的 VM，然後選取 [刪除] > [刪除已連接磁碟]。
    5. 移至 [虛擬機器] > [磁碟]，並等候磁碟消失。  這可能需要幾分鐘才會完成，且您可能需要重新整理頁面。
    6. 如果磁碟沒有消失，請等候 [已連接到] 欄位轉為空白。 這表示磁碟已完全從 VM 卸離。  接著，選取磁碟，然後選取頁面底部的 [刪除] 以刪除磁碟。


   > [!NOTE]
   > 如果磁碟連接到 VM，您將無法刪除它。 磁碟會以非同步的方式中斷連接已刪除的 VM。 刪除 VM 之後，可能需要幾分鐘，此欄位才能清除。
   >
   >


### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>步驟 2︰刪除任何會阻止您刪除儲存體帳戶或容器的 VM 映像
1. 切換至 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 選取 [虛擬機器] > [映像]，然後刪除與儲存體帳戶、容器或 VHD 相關聯的映像。

    接下來，嘗試再次刪除儲存體帳戶、容器或 VHD。

> [!WARNING]
> 請務必先備份您想要儲存的任何資料，再刪除帳戶。 刪除 VHD、blob、資料表、佇列或檔案就是永久刪除。 確定資源不在使用中。
>
>

## <a name="about-the-stopped-deallocated-status"></a>關於已停止 (已解除配置) 狀態
已在傳統部署模型中建立並已保留的 VM 將在 [Azure 入口網站](https://portal.azure.com/)或 [Azure 傳統入口網站](https://manage.windowsazure.com/)上具有 [已停止 (已解除配置)] 狀態。

**Azure 傳統入口網站**：

![Azure 入口網站上 VM 的已停止 (已解除配置) 狀態。](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

**Azure 入口網站**︰

![Azure 傳統入口網站上 VM 的已停止 (已解除配置) 狀態。](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

「已停止 (已解除配置)」狀態會釋出電腦資源，例如 CPU、記憶體和網路。 不過，磁碟仍會保留，以便您可在需要時快速重新建立 VM。 這些磁碟都建立在由 Azure 儲存體提供的 VHD 上。 儲存體帳戶具有這些 VHD，而磁碟有這些 VHD 上的租用。

## <a name="next-steps"></a>後續步驟
* [刪除儲存體帳戶](storage-create-storage-account.md#delete-a-storage-account)

