---
title: "什麼是 Site Recovery？ | Microsoft Docs"
description: "提供 Azure Site Recovery 服務的概觀，並摘要說明部署案例。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 276d7949e01b8936804514c5fe8bd26af438bd08
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017


---
# <a name="what-is-site-recovery"></a>什麼是 Site Recovery？

歡迎使用 Azure Site Recovery 服務！ 本文提供此服務的快速概觀。

中斷是因為正常事件與操作失敗所造成。 您的組織需要商務持續性和災害復原 (BCDR) 策略，以便在計劃性停機與非計劃性停機期間，資料可保持安全、應用程式仍可供使用，且業務會儘速復原到正常運作情況。

Azure 復原服務可參與 BCDR 策略。 [Azure 備份](https://docs.microsoft.com/en-us/azure/backup/)服務可讓您的資料保持安全且可供復原。 Site Recovery 會複寫、容錯移轉及復原工作負載，以便在發生失敗時仍能使用它們。

## <a name="what-does-site-recovery-provide"></a>Site Recovery 可以提供什麼功能？

- **雲端災害復原** - 您可以使用 Azure 至 Azure 災害復原解決方案，來複寫和保護 Azure 上所執行的 VM。 您可以將 VM 和實體伺服器上執行的工作負載複寫到 Azure，而不是複寫到次要站台。 這可排除維護次要資料中心的成本和複雜度。
- **針對混合式環境提供彈性複寫** - 您可以複寫 Azure VM、內部部署 Hyper-V VM、VMware VM 和 Windows/Linux 實體伺服器上所支援的任何工作負載。
- **移轉** - 您可以使用 Site Recovery，將內部部署和 AWS 執行個體移轉到 Azure VM，或者在 Azure 區域之間移轉 Azure VM。
- **簡化 BCDR** - 您可以從 Azure 入口網站中的單一位置部署複寫。  您可以針對單一和多部機器執行簡單的容錯移轉和容錯回復。
- **恢復功能** - Site Recovery 會協調複寫和容錯移轉，而不需攔截應用程式資料。 複寫的資料會儲存在 Azure 儲存體中，並具備 Azure 儲存體所提供的恢復功能。 進行容錯移轉時，會根據複寫的資料來建立 Azuer VM。
- **複寫效能** - Site Recovery 為 Azure VM 和 VMware VM 提供了連續複寫功能，並為 Hyper-V 提供最低 30 秒的複寫頻率。 您可以透過 Site Recovery 的自動復原程序，並與 [Azure 流量管理員](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)整合，來減少復原時間目標 (RTO)
- **應用程式一致性** - 您可以為復原點設定應用程式一致快照。 除了擷取磁碟資料，應用程式一致快照還會擷取記憶體中的所有資料和處理序中的所有交易。
- **不需中斷即可進行測試** - 您可以輕鬆地執行測試用容錯移轉，既能支援災害復原演練，又不會影響生產環境和後續的複寫作業。
- **彈性的容錯移轉和復原** - 您可以執行計劃性容錯移轉，因為是預期的中斷，所以不會遺失任何資料；或是執行非計劃性容錯移轉，以在發生非未預期的災害時將資料損失減到最少 (取決於複寫頻率)。 您可以在主要站台再次可用時，輕鬆地容錯回復到該站台。
- **自訂復原方案** - 復原方案可讓您建立模型，並自訂容錯移轉和復原分散在多個 VM 的多層式應用程式。 您要排序計劃內的群組，並新增指令碼和手動動作。 復原計劃可以與 Azure 自動化 Runbook 整合。
- **多層式應用程式** - 您可以建立復原計劃，以對多層式應用程式進行循序容錯移轉和復原。 您可以將復原計劃內不同層級 (例如資料庫、Web、應用程式) 中的機器群組在一起，並自訂每個群組的容錯移轉和啟動方式。
* **與現有 BCDR 技術整合** - Site Recovery 能夠與其他 BCDR 技術整合。 例如，您可以使用 Site Recovery 保護公司工作負載的 SQL Server 後端，包括原生支援 SQL Server AlwaysOn 以便管理可用性群組的容錯移轉。
* **與自動化程式庫整合** - 豐富的 Azure 自動化程式庫會提供已可用於生產環境，且可下載並已經與 Site Recovery 整合的應用程式特定指令碼。
* **簡易網路管理** - Site Recovery 和 Azure 中的進階網路管理可簡化應用程式網路需求，包括保留 IP 位址、設定負載平衡器和整合 Azure 流量管理員以進行有效率的網路轉換。


## <a name="whats-supported"></a>支援的項目？

**支援** | **詳細資料**
--- | ---
**Site Recovery 支援哪些區域？** | [支援區域](https://azure.microsoft.com/en-us/regions/services/) |
**我可以複寫哪些項目？** | Azure VM (預覽版)、內部部署 VMware VM、Hyper-V VM、Windows 和 Linux 實體伺服器。
**複寫的機器需要哪些作業系統？** | Azure VM 的[支援作業系統](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)<br></br>適用於 VMware VM 的[支援作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> 如果是 Hyper-V VM，支援 Azure 和 Hyper-V 支援的所有[客體 OS](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)。<br/><br/> 適用於實體伺服器的[作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**我可以複寫到何處？** | 若為 Azure VM，您可以複寫其他 Azure 區域。 <br></br> 若為內部部署機器，您可以複寫到 Azure 儲存體或次要資料中心。<br/><br/> 

>[!NOTE]
>
> 如果 Hyper-V，只有 System Center VMM 雲端中所管理之 Hyper-V 主機上的 VM 可以複寫到次要資料中心。

**我需要哪些 VMware 伺服器/主機？** | 您想要複寫的 VMware VM 可以由[支援的 vSphere 主機/vCenter 伺服器](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)所管理
**可以複寫哪些工作負載？** | 您可以複寫在支援的複寫機器上執行的所有工作負載。 此外，Site Recovery 小組已經針對[應用程式數目](site-recovery-workload.md#workload-summary)執行應用程式專屬測試。


## <a name="which-azure-portal"></a>哪個 Azure 入口網站？

* Site Recovery 可以部署在 [Azure 入口網站](https://portal.azure.com)中。
* 在 Azure 傳統入口網站中，您可以使用傳統服務管理模型來管理 Site Recovery。
- 傳統的入口網站只能用來維護現有的 Site Recovery 部署。 您無法在傳統入口網站中建立新的保存庫。

## <a name="next-steps"></a>後續步驟
* 深入了解 [複寫 Azure 虛擬機器] (site-recovery-azure-to-azure.md)
* 深入了解[工作負載支援](site-recovery-workload.md)
* 深入了解 [Site Recovery 架構和元件](site-recovery-components.md)

