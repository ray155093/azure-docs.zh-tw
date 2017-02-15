---
title: "針對在 Resource Manager 部署中刪除 Azure 儲存體帳戶、容器或 VHD 時所發生的錯誤進行疑難排解 | Microsoft Docs"
description: "針對在 Resource Manager 部署中刪除 Azure 儲存體帳戶、容器或 VHD 時所發生的錯誤進行疑難排解"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75d5e82e2f4e747747f24376239e23f6512f916a


---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>針對在 Resource Manager 部署中刪除 Azure 儲存體帳戶、容器或 VHD 時所發生的錯誤進行疑難排解
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

當您嘗試刪除 [Azure 入口網站](https://portal.azure.com)中的 Azure 儲存體帳戶、容器或虛擬硬碟 (VHD) 時，可能會收到錯誤。 本文提供可協助您解決 Azure Resource Manager 部署問題的疑難排解指引。

若本文未能解決您的 Azure 問題，請造訪 [MSDN 與 Stack Overflow](https://azure.microsoft.com/support/forums/) 上的 Azure 論壇。 您可以在這些論壇上張貼您的問題，或將問題貼到 Twitter 上的 @AzureSupport。 此外，您也可以選取 [Azure 支援](https://azure.microsoft.com/support/options/)網站上的 [取得支援]，來提出 Azure 支援要求。

## <a name="symptoms"></a>徵兆
### <a name="scenario-1"></a>案例 1
當您嘗試在 Resource Manager 部署的儲存體帳戶中刪除 VHD 時，您會收到下列錯誤訊息︰

**無法刪除 Blob 'vhds/BlobName.vhd'。錯誤：目前 blob 上沒有租用，且要求中沒有指定任何租用識別碼。**

虛擬機器 (VM) 在您嘗試刪除的 VHD 上有租用，所以會發生此問題。

### <a name="scenario-2"></a>案例 2
當您嘗試在 Resource Manager 部署的儲存體帳戶中刪除容器時，您會收到下列錯誤訊息︰

**無法刪除儲存體容器 'vhds'。錯誤：目前容器上沒有租用，且要求中沒有指定任何租用識別碼。**

容器中有 VHD 鎖定在租用狀態，所以會發生此問題。

### <a name="scenario-3"></a>案例 3
當您嘗試在 Resource Manager 部署中刪除儲存體帳戶時，您會收到下列錯誤訊息︰

**無法刪除儲存體帳戶 'StorageAccountName'。錯誤︰無法刪除儲存體帳戶，因為正在使用其構件。**

儲存體帳戶含有處於租用狀態的 VHD，所以會發生此問題。

## <a name="solution"></a>方案
若要解決這些問題，您必須識別造成錯誤的 VHD 和相關聯的 VM。 接著，將 VHD 與 VM 中斷連結 (若為資料磁碟)，或刪除使用 VHD 的 VM (若為 OS 磁碟)。 這會從 VHD 移除租用，讓 VHD 得以被刪除。

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>步驟 1︰識別有問題的 VHD 和相關聯的 VM
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [所有資源]。 移至您想要刪除的儲存體帳戶，然後選取 [Blob] > [VHD]。

    ![入口網站的螢幕擷取畫面，醒目提示儲存體帳戶與「vhds」容器](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)
3. 檢查容器中每個 VHD 的屬性。 找出處於 [已租用] 狀態的 VHD。 然後，判斷是哪個 VM 在使用 VHD。 通常來說，您可以藉由檢查 VHD 的名稱來判斷是哪個 VM 握有 VHD︰

   * OS 磁碟一般會遵循此命名慣例︰VMNameYYYYMMDDHHMMSS.vhd
   * 資料磁碟一般會遵循此命名慣例︰VMName-YYYYMMDD-HHMMSS.vhd

     ![入口網站中容器資訊的螢幕擷取畫面，醒目提示 VM 名稱、「已鎖定」租用狀態和「已租用」租用狀態](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>步驟 2︰從 VHD 移除租用
若要刪除使用 VHD 的 VM (若為 OS 磁碟)：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，選取 [虛擬機器]。
3. 選取在 VHD 上握有租用的 VM。
4. 確定沒有正在使用虛擬機器的項目，而且您不再需要虛擬機器。
5. 在 [VM 詳細資料] 刀鋒視窗頂端，選取 [刪除]，然後按一下 [是] 進行確認。
6. VM 應該會遭到刪除，但 VHD 應該會保留下來。 不過，VHD 上應該不會再有租用。 釋放租用可能需要幾分鐘的時間。 若要確認租用已遭到釋放，請移至 [所有資源] > [儲存體帳戶名稱] > [Blob] > [VHD]。 在 [Blob 屬性]] 窗格中，[租用狀態] 值應該是 [已解除鎖定]。

若要將 VHD 與使用 VHD 的 VM 中斷連結 (若為資料磁碟)：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，選取 [虛擬機器]。
3. 選取在 VHD 上握有租用的 VM。
4. 選取 [VM 詳細資料] 刀鋒視窗上的 [磁碟]。
5. 選取在 VHD 上握有租用的資料磁碟。 您可以藉由檢查 VHD 的 URL 來判斷磁碟中連接的 VHD。
6. 確實判斷沒有任何項目正在使用資料磁碟。
7. 按一下 [磁碟詳細資料] 刀鋒視窗上的 [中斷連結]。
8. 磁碟應該會立即與 VM 中斷連結，而且 VHD 上應該不會再有租用。 釋放租用可能需要幾分鐘的時間。 若要確認租用已遭到釋放，請移至 [所有資源] > [儲存體帳戶名稱] > [Blob] > [VHD]。 在 [Blob 屬性]] 窗格中，[租用狀態] 值應該是 [已解除鎖定]。

## <a name="what-is-a-lease"></a>租用是什麼？
租用是可用來控制 Blob (例如 VHD) 存取權的鎖定。 租用 Blob 時，只有租用的擁有者可以存取 Blob。 租用非常重要，原因如下︰

* 如果多名擁有者嘗試同時對 Blob 的相同部分寫入資料，租用可防止資料損毀。
* 租用可防止正在使用中的 Blob 遭到 (例如 VM) 刪除。
* 租用可防止正在使用中的儲存體帳戶遭到 (例如 VM) 刪除。

## <a name="next-steps"></a>後續步驟
* [刪除儲存體帳戶](storage-create-storage-account.md#delete-a-storage-account)
* [如何在 Microsoft Azure (PowerShell) 中止 blob 儲存體的鎖定租用](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)



<!--HONumber=Nov16_HO3-->


