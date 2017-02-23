---
title: "關於 Azure IaaS VM 磁碟的常見問題集 (FAQ) | Microsoft Docs"
description: "關於 Azure IaaS VM 磁碟和進階磁碟 (受控和非受控) 的常見問題集"
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: 0746c954e669bd739b8ecfcddaf287cb5172877f
ms.openlocfilehash: 95b627738726f3c108fff38bfeda413303b2c718


---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>關於 Azure IaaS VM 磁碟及受控和非受控進階磁碟的常見問題集

在本文中，我們將談論有關受控磁碟和進階儲存體的一些常見問題集。

## <a name="managed-disks"></a>受控磁碟

**何謂 Azure 受控磁碟？**

受控磁碟是可以為您管理儲存體帳戶而簡化 Azure IaaS VM 磁碟管理的一項功能。 如需詳細資訊，請參閱[受控磁碟概觀](storage-managed-disks-overview.md)。

**如果我從現有的 VHD (大小為 80 GB) 建立標準受控磁碟，需要多少費用？**

從 80 GB VHD 建立的標準受控磁碟會被視為下一個可用的進階磁碟大小 (S10 磁碟)。 我們將根據 S10 磁碟價格收費。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage)。

**標準受控磁碟有任何交易成本嗎？**

是，您需支付每一筆交易。 如需詳細資訊，請參閱 [價格頁面] (https://azure.microsoft.com/pricing/details/storage)。

**若是標準受控磁碟，收費是依據磁碟上的資料實際大小，還是磁碟的佈建容量？**

收費是依據磁碟的佈建容量。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage)。

**進階受控磁碟和非受控磁碟的價格有何不同？**

進階受控磁碟與進階非受控磁碟的價格相同。

**我可以變更受控磁碟的儲存體帳戶類型 (標準/進階) 嗎？**

是。 您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 變更受控磁碟的儲存體帳戶類型。

**是否有方法可以將受控磁碟複製或匯出至私人儲存體帳戶？**

是，您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 匯出受控磁碟。

**我可以使用 Azure 儲存體帳戶中的 VHD 檔案在不同的訂用帳戶中建立受控磁碟嗎？**

否。

**我可以使用 Azure 儲存體帳戶中的 VHD 檔案在不同的區域中建立受控磁碟嗎？**

否。

**客戶使用受控磁碟時有任何規模限制嗎？**

受控磁碟沒有儲存體帳戶方面的限制。 不過，依預設，每一訂用帳戶的受控磁碟數目限制為 2000 個。 您可以連絡支援人員來提高限制。

**我可以建立受控磁碟的增量快照集嗎？**

否。 目前的快照集功能會建立受控磁碟的完整複本。 不過，我們已規劃在未來支援增量快照集。

**可用性設定組中的 VM 可以由受控和非受控磁碟混合組成嗎？**

否，可用性設定組中的 VM 必須全部使用受控磁碟，或全部使用非受控磁碟。 在建立可用性設定組時，您可以選擇想要使用的磁碟類型。

**受控磁碟是 Azure 入口網站中的預設選項嗎？**

目前不是，但未來會變成預設值。

**我可以建立空的受控磁碟嗎？**

是，您可以建立空的磁碟。 受控磁碟可在 VM 外獨立建立，亦即不附加至 VM。

**使用受控磁碟的可用性設定組支援的容錯網域計數為何？**

使用受控磁碟的可用性設定組支援的容錯網域計數是 2 或 3，根據它所在的區域而定。

**如何設定診斷的標準儲存體帳戶？**

您需要為 VM 診斷設定私人儲存體帳戶。 在未來，我們規劃也將診斷切換至受控磁碟。

**受控磁碟有何種 RBAC 支援？**

受控磁碟支援三個主要預設角色︰

1.  擁有者：可以管理所有項目，包括存取。

2.  參與者：可以管理存取以外的所有項目。

3.  讀者：可以檢視所有項目，但是無法進行變更。

**是否有方法可以將受控磁碟複製或匯出至私人儲存體帳戶？**

您可以取得受控磁碟的唯讀共用存取簽章 (SAS) URI，並使用它將內容複製到私人儲存體帳戶或內部部署儲存體。

**您可以建立受控磁碟的複本嗎？**

客戶可以建立受控磁碟的快照集，然後使用快照集建立另一個受控磁碟。

**仍然支援非受控磁碟嗎？**

是，我們支援受控磁碟和非受控磁碟。 不過，我們建議您開始使用受控磁碟來處理新的工作負載，並將您目前的工作負載移轉至受控磁碟。

**如果我建立大小為 128 GB 的磁碟，然後將大小增加至 130 GB，我必須支付下一個磁碟大小嗎 (512 GB)？**

是。

**我可以建立 LRS、GRS 和 ZRS 受控磁碟嗎？**

Azure 受控磁碟目前僅支援本地備援儲存體 (LRS)。

## <a name="premium-disks--both-managed-and-unmanaged"></a>進階磁碟 – 受控和非受控

**如果 VM 使用的大小系列支援進階儲存體，例如 DSv2，我可以同時附加進階和標準資料磁碟嗎？** 

是。

**我可以同時將進階和標準資料磁碟附加至不支援進階儲存體的大小系列嗎？例如 D、Dv2、G 或 F 系列**

否。 只有當 VM 使用不支援進階儲存體的大小系列時，您才能將標準資料磁碟附加至 VM。

**如果我從現有的 VHD (大小 為 80 GB) 建立進階資料磁碟，需要多少費用？**

從 80 GB VHD 建立的進階資料磁碟會被視為下一個可用的進階磁碟大小 (P10 磁碟)。 我們將根據 P10 磁碟價格收費。

**使用進階儲存體時是否有交易成本？**

依 IOPS 和輸送量的特定限制而佈建的每個磁碟大小，都有固定成本。 其他成本包括輸出頻寬和快照集容量 (如果適用的話)。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage)。

**我可以從磁碟快取獲得的 IOPS 和輸送量有何限制？**

DS 系列的快取和本機 SSD 合併限制是每個核心 4000 IOPS，以及每個核心每秒 33 MB。 GS 系列提供每個核心 5000 IOPS，以及每個核心每秒 50 MB。

**受控磁碟 VM 支援本機 SSD 嗎？**

本機 SSD 是受控磁碟 VM 隨附的暫時儲存體。 暫時儲存體不需額外的成本。 建議您不要使用此本機 SSD 來儲存應用程式資料，因為它不會保存在 Azure Blob 儲存體中。

## <a name="what-if-my-question-isnt-answered-here"></a>如果這裡沒有解答我的問題該怎麼辦？

如果這裡未列出您的問題，請告訴我們，我們將協助您找到答案。 您可以在本文結尾處的意見欄，或在 MSDN [Azure 儲存體論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)，張貼有關本文的問題，與 Azure 儲存體小組和其他社群成員一起討論。

若要提出功能要求，請將要求和想法提交到 [Azure 儲存體意見反應論壇](https://feedback.azure.com/forums/217298-storage)。


<!--HONumber=Feb17_HO2-->


