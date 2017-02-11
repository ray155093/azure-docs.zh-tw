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
ms.date: 10/18/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a50ac3b7fe76dd44887fb197f685c1311d9dc04c


---
# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>針對在傳統部署中刪除 Azure 儲存體帳戶、容器或 VHD 進行疑難排解
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

當您嘗試刪除 [Azure 入口網站](https://portal.azure.com/)或 [Azure 傳統入口網站](https://manage.windowsazure.com/)中的 Azure 儲存體帳戶、容器或 VHD 時，可能會收到錯誤。 下列情況可能造成問題︰

* 當您刪除 VM 時，磁碟和 VHD 不會自動刪除。 這可能是儲存體帳戶刪除失敗的原因。 我們不會刪除磁碟，所以您可以使用磁碟來裝載另一個 VM。
* 磁碟上仍有租用或有與磁碟相關聯的 blob。

若本文中未提及您的 Azure 問題，請造訪 [MSDN 及 Stack Overflow 上的 Azure 論壇](https://azure.microsoft.com/support/forums/)。 您可以在這些論壇上張貼您的問題，或將問題貼到 Twitter 上的 @AzureSupport。 此外，您也可以選取 [Azure 支援](https://azure.microsoft.com/support/options/)網站上的 [取得支援]，來提出 Azure 支援要求。

## <a name="symptoms"></a>徵兆
下列章節列出您在嘗試刪除 Azure 儲存體帳戶、容器或 VHD 時可能收到的常見錯誤。

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>案例 1：無法刪除儲存體帳戶
當您瀏覽至 [Azure 入口網站](https://portal.azure.com/)或 [Azure 傳統入口網站](https://manage.windowsazure.com/)中的儲存體帳戶，並選取 [刪除] 時，您可能會看到下列錯誤訊息︰

儲存體帳戶 StorageAccountName 包含 VM 映像。確認這些 VM 映像已移除之後再刪除此儲存體帳戶。

您也可能會看到此錯誤︰

**在 Azure 入口網站上**：

*無法刪除儲存體帳戶 <vm-storage-account-name>。無法刪除儲存體帳戶 <vm-storage-account-name>：儲存體帳戶 <vm-storage-account-name> 有些作用中的映像及/或磁碟。確認這些映像及/或磁碟已移除之後再刪除此儲存體帳戶。」*

**在 Azure 傳統入口網站上**：

*儲存體帳戶 <vm-storage-account-name> 有某些作用中的映像及/或磁碟，例如 xxxxxxxxx- xxxxxxxxx-O-209490240936090599。確認這些映像及/或磁碟已移除之後再刪除此儲存體帳戶。*

或

**在 Azure 入口網站上**：

*儲存體帳戶 <vm-storage-account-name> 已有 1 個具備作用中映像及/或磁碟成品的容器。請確定這些成品已從映像儲存機制移除之後再刪除此儲存體帳戶。*

**在 Azure 傳統入口網站上**：

*提交失敗的儲存體帳戶 <vm-storage-account-name> 已有 1 個具備作用中映像及/或磁碟成品的容器。請確定這些成品已從映像儲存機制移除之後再刪除此儲存體帳戶。當您嘗試刪除儲存體帳戶，而且具有與其相關聯的磁碟仍在作用中時，您會看到一則訊息，告訴您有需要刪除的作用中磁碟*。

### <a name="scenario-2-unable-to-delete-a-container"></a>案例 2：無法刪除容器
當您嘗試刪除儲存體容器時，您可能會看到下列錯誤︰

無法刪除儲存體容器 <container name>。*錯誤：「目前容器上沒有租用，且要求中沒有指定任何租用識別碼*。

### <a name="scenario-3-unable-to-delete-a-vhd"></a>案例 3：無法刪除 VHD
在您刪除 VM，然後嘗試刪除相關聯的 blob 之後，您可能會收到下列訊息︰

*無法刪除 blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'。錯誤：「目前 blob 上沒有租用，且要求中沒有指定任何租用識別碼。*

## <a name="solution"></a>方案
若要解決最常見的問題，請嘗試下列方法︰

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>步驟 1︰刪除任何會阻止您刪除儲存體帳戶、容器或 VHD 的 OS 磁碟
1. 切換至 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 選取 [虛擬機器] > [磁碟]。
   
    ![Azure 傳統入口網站上虛擬機器上的磁碟影像。](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)
3. 找出與您想要刪除的儲存體帳戶、容器或 VHD 相關聯的磁碟。 檢查磁碟的位置時，您會發現相關聯的儲存體帳戶、容器或 VHD。
   
    ![顯示 Azure 傳統入口網站上磁碟的位置資訊的影像](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)
4. 確認磁碟的 [連接到]  欄位上沒有列出 VM，然後再刪除磁碟。
   
   > [!NOTE]
   > 如果磁碟連接到 VM，您將無法刪除它。 磁碟會以非同步的方式中斷連接已刪除的 VM。 刪除 VM 之後，可能需要幾分鐘，此欄位才能清除。
   > 
   > 

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>步驟 2︰刪除任何會阻止您刪除儲存體帳戶或容器的 VM 映像
1. 切換至 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 選取 [虛擬機器] > [映像]，然後刪除與儲存體帳戶、容器或 VHD 相關聯的映像。
   
    接下來，嘗試再次刪除儲存體帳戶、容器或 VHD。

> [!WARNING]
> 請務必先備份您想要儲存的任何資料，再刪除帳戶。 您無法還原已刪除的儲存體帳戶，也無法擷取刪除之前所包含的任何內容。 這也適用於帳戶中的任何資源：一旦刪除 VHD、Blob、資料表、佇列或檔案，就是永久刪除。 確定資源不在使用中。
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
* [如何在 Microsoft Azure (PowerShell) 中止 blob 儲存體的鎖定租用](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)




<!--HONumber=Nov16_HO3-->


