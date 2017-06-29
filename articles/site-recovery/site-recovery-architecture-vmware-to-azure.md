---
title: "在 Azure Site Recovery 中將 VMware 複寫至 Azure 如何運作？ | Microsoft Docs"
description: "本文提供使用 Azure Site Recovery 服務將內部部署 VMware VM 和實體伺服器複寫至 Azure 時所用元件和架構的概觀"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: vmware-walkthrough-architecture
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 1ebf6e562c99c5113cc33bc8cb87416c663029c6
ms.contentlocale: zh-tw
ms.lasthandoff: 06/15/2017

---



# <a name="how-does-vmware-replication-to-azure-work-in-site-recovery"></a>在 Site Recovery 中將 VMware 複寫至 Azure 如何運作？

本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署 VMware 虛擬機器和 Windows/Linux 實體伺服器複寫至 Azure 時的相關元件和程序。

當您將實體內部部署伺服器複寫至 Azure 時，複寫作業也會使用與 VMware VM 複寫相同的元件和程序，但有下列差異︰

- 您可以針對組態伺服器使用實體伺服器，而不是 VMware VM。
- 您需要內部部署的 VMware 基礎結構以供進行容錯回復。 您無法容錯回復到實體機器。

請在本文下方張貼意見，或在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提出問題。


## <a name="architectural-components"></a>架構元件

將 VMware VM 和實體伺服器複寫至 Azure 時，涉及許多元件。

**元件** | **Location** | **詳細資料**
--- | --- | ---
**Azure** | 在 Azure 中，您需要 Azure 帳戶、Azure 儲存體帳戶和 Azure 網路。 | 所複寫的資料會儲存在儲存體帳戶中，而在從內部部署網站進行容錯移轉時，便會以複寫的資料建立 Azure VM。 Azure VM 在建立後會連線到 Azure 虛擬網路。
**組態伺服器** | 單一內部部署管理伺服器 (VMWare VM)，它會執行部署所需的所有內部部署元件，包括組態伺服器、處理序伺服器、主要目標伺服器 | 組態伺服器元件會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
 **處理序伺服器**：  | 預設會安裝在組態伺服器上。 | 會做為複寫閘道器。 接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。<br/><br/> 處理序伺服器還會處理用來保護機器的行動服務的推入安裝，並執行 VMWare VM 的自動探索。<br/><br/> 隨著部署規模擴大，您可以新增更多個別的專用處理序伺服器，以處理日益增加的複寫流量。
 **主要目標伺服器** | 預設會安裝在內部部署組態伺服器上。 | 在從 Azure 容錯回復期間，處理複寫資料。<br/><br/> 如果容錯回復的流量很高，您可以部署個別的主要目標伺服器來供容錯回復使用。
**VMware 伺服器** | VMware VM 裝載在 vSphere ESXi 伺服器上，我們建議使用 vCenter 伺服器來管理主機。 | 您可以將 VMware 伺服器新增至您的復原服務保存庫。
**複寫的機器** | 行動服務將會安裝在您要複寫的每部 VMware VM 上。 您可以手動將它安裝在每部電腦上，或是從處理序伺服器進行推入安裝。

了解[支援矩陣](site-recovery-support-matrix-to-azure.md)中每個元件的部署必要條件和需求。

**圖 1：VMware 到 Azure 的元件**

![元件](./media/site-recovery-components/arch-enhanced.png)

## <a name="replication-process"></a>複寫程序

1. 您要設定部署 (包括 Azure 元件) 和復原服務保存庫。 在保存庫中指定複寫來源和目標、設定組態伺服器、新增 VMware 伺服器、建立複寫原則、部署行動服務、啟用複寫，以及執行測試容錯移轉。
2.  機器根據複寫原則開始複寫，並將資料的初始複本複寫到 Azure 儲存體。
4. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 機器的追蹤變更會保存在 .hrl 檔案中。
    - 複寫機器會在輸入連接埠 HTTPS 443 上與組態伺服器進行通訊，以管理複寫。
    - 複寫機器會在輸入連接埠 HTTPS 9443 (可加以設定) 上將複寫資料傳送至處理序伺服器。
    - 組態伺服器會透過輸出連接埠 HTTPS 443 與 Azure 協調複寫管理。
    - 處理序伺服器會透過輸出連接埠 443，接收來源機器所傳來的資料、將其最佳化並加密，再將它傳送至 Azure 儲存體。
    - 如果您啟用多部 VM 一致性，則複寫群組中的機器會透過連接埠 20004 彼此通訊。 如果您將多部機器群組為幾個共用當機時保持一致復原點和應用程式一致復原點的複寫群組，當這些群組在進行容錯移轉時，便會使用多部 VM。 如果機器執行的是相同的工作負載，且需要保持一致，此功能就很實用。
5. 流量透過網際網路複寫到 Azure 儲存體的公用端點。 或者，您可以使用 Azure ExpressRoute [公用對等](../expressroute/expressroute-circuit-peerings.md#public-peering)。 不支援從內部部署網站透過站台對站台 VPN 將流量複寫至 Azure。

**圖 2：VMware 到 Azure 的複寫**

![增強](./media/site-recovery-components/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

1. 確認測試容錯移轉如預期般運作之後，您可以視需要執行至 Azure 的未計劃容錯移轉。 不支援有計劃的容錯移轉。
2. 您可以容錯移轉單一機器，或建立[復原計劃](site-recovery-create-recovery-plans.md)，來容錯移轉多部 VM。
3. 當您執行容錯移轉時，會在 Azure 中建立複本 VM。 您要認可讓容錯移轉開始存取來自複本 Azure VM 的工作負載。
4. 當主要的內部部署網站恢復可用狀態時，您就可以容錯回復。 您要設定容錯回復基礎結構、開始將機器從次要網站複寫到主要網站，以及從次要網站執行非計劃性容錯移轉。 在認可此容錯移轉後，資料會回到內部部署網站，而您必須再次啟用複寫至 Azure 的功能。 [深入了解](site-recovery-failback-azure-to-vmware.md)

容錯回復有以下幾項需求︰


- **Azure 中的暫存處理序伺服器**︰如果您想要在容錯移轉後從 Azure 容錯回復，您必須將 Azure VM 設定為處理序伺服器，以處理來自 Azure 的複寫。 容錯回復完成後，您可以刪除此 VM。
- **VPN 連線**：如需容錯回復，您需要設定從 Azure 網路到內部部署網站的 VPN 連線 (或 Azure ExpressRoute)。
- **個別內部部署主要目標伺服器**︰內部部署主要目標伺服器會處理容錯回復。 主要目標伺服器預設會安裝在管理伺服器上，但如果要容錯回復大量資料，您應該就此目的設定個別的內部部署主要目標伺服器。
- **容錯回復原則**︰若要複寫回到內部部署網站，您需要容錯回復原則。 此原則會在您建立複寫原則時自動建立。
- **VMware 基礎結構**：您必須容錯回復到內部部署 VMware VM。 這表示您需要內部部署 VMware 基礎結構，即使是將內部部署實體伺服器複寫到 Azure。

**圖 3：VMware/實體容錯回復**

![容錯回復](./media/site-recovery-components/enhanced-failback.png)


## <a name="next-steps"></a>後續步驟

檢閱[支援矩陣](site-recovery-support-matrix-to-azure.md)

