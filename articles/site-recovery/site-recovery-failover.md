---
title: "Site Recovery 中的容錯移轉 | Microsoft Docs"
description: "Azure Site Recovery 可協調虛擬機器和實體伺服器的複寫、容錯移轉及復原作業。 了解如何容錯移轉到 Azure 或次要資料中心。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: pratshar
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 960fb84c309b18c7f9741bb60b52cfcc3753a07d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/02/2017


---
# <a name="failover-in-site-recovery"></a>Site Recovery 中的容錯移轉
本文說明如何容錯移轉 Site Recovery 所保護的虛擬機器和實體伺服器。

## <a name="prerequisites"></a>必要條件
1. 執行容錯移轉之前，請執行[測試容錯移轉](site-recovery-test-failover-to-azure.md)，以確保一切運作正常。
1. 執行容錯移轉之前，在目標位置[準備網路](site-recovery-network-design.md)。  


## <a name="run-a-failover"></a>執行容錯移轉
此程序說明如何針對[復原方案](site-recovery-create-recovery-plans.md)執行容錯移轉。 或者，您可以從 [複寫的項目] 頁面，針對單一虛擬機器或實體伺服器執行容錯移轉。


![容錯移轉](./media/site-recovery-failover/Failover.png)

1. 選取 [復原方案]  >  *recoveryplan_name*。 按一下 [容錯移轉]
2. 在 [容錯移轉]畫面上，選取要容錯移轉到的 [復原點]。 您可以使用下列其中一個選項：
    1.    **最新** (預設值)︰此選項會先處理已傳送到 Site Recovery 服務的所有資料，先為每個虛擬機器建立復原點後再進行容錯移轉。 此選項提供最低的 RPO (復原點目標)，因為在容錯移轉後建立的虛擬機器會具有觸發容錯移轉時已複寫到 Site Recovery 服務的所有資料。
    1.  **最新處理**︰這個選項會將復原方案的所有虛擬機器容錯移轉至 Site Recovery 服務已處理的最新復原點。 當您進行虛擬機器的測試容錯移轉時，也會顯示最新處理復原點的時間戳記。 如果您正在執行某個復原方案的容錯移轉，您可以移至個別的虛擬機器，並查看 [最新復原點] 圖格以取得此資訊。 因為沒有花時間處理未處理的資料，此選項提供低 RTO (復原時間目標) 容錯移轉選項。
    1.    **最新的應用程式一致**︰這個選項會將復原方案的所有虛擬機器容錯移轉至 Site Recovery 服務已處理的最新應用程式一致復原點。 當您進行虛擬機器的測試容錯移轉時，也會顯示最新應用程式一致復原點的時間戳記。 如果您正在執行某個復原方案的容錯移轉，您可以移至個別的虛擬機器，並查看 [最新復原點] 圖格以取得此資訊。
    1.    **自訂**︰如果您要執行虛擬機器的測試容錯移轉，您可以使用此選項以容錯移轉至特定復原點。

    > [!NOTE]
    > 只有在容錯移轉至 Azure 時，才有選項讓您選擇復原點。
    >
    >


1. 如果復原方案中有部分虛擬機器在前次執行時已容錯移轉，而現在這些虛擬機器在來源和目標位置上都在使用中，您可以使用 [變更方向] 選項，決定容錯移轉進行的方向。
1. 如果您要容錯移轉至 Azure，且已針對雲端啟用資料加密 (只有在您已從 VMM 伺服器保護 Hyper-v 虛擬機器時才適用)，請在 [加密金鑰] 中，選取您在 VMM 伺服器上安裝期間啟用資料加密時所發出的憑證。
1. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源虛擬機器關機，請選取 [先將機器關機再開始容錯移轉]。 即使關機失敗，仍會繼續容錯移轉。  

    > [!NOTE]
    > 對於 Hyper-v 虛擬機器，此選項也會在觸發容錯移轉之前，嘗試同步處理尚未傳送至服務的內部部署資料。
    >
    >

1. 您可以 [作業] 頁面上追蹤容錯移轉進度。 即使在非計劃性容錯移轉期間發生錯誤，復原方案還是會執行到完成為止。
1. 容錯移轉之後，登入虛擬機器進行驗證。 如果您想要前往虛擬機器的另一個復原點，您可以使用 [變更復原點] 選項。
1. 一旦您滿意容錯移轉的虛擬機器，您可以 [認可] 容錯移轉。 這會刪除服務可用的所有復原點，而且無法再使用 [變更復原點] 選項。

## <a name="planned-failover"></a>計劃性容錯移轉
除了容錯移轉，使用 Site Recovery 保護的 Hyper-V 虛擬機器也支援**計劃性容錯移轉**。 這是完全不會遺失資料的容錯移轉選項。 觸發計劃性容錯移轉時，首先，來源虛擬機器會關機，尚未同步處理的資料會同步處理，然後觸發容錯移轉。

> [!NOTE]
> 當您在兩個內部部署網站之間容錯移轉 Hyper-v 虛擬機器時，若要回到主要內部部署網站，您必須先將虛擬機器**反向複寫**回主要網站，然後再觸發容錯移轉。 如果主要虛擬機器無法使用，則開始**反向複寫**之前，您必須從備份還原虛擬機器。   
>
>

## <a name="failover-job"></a>容錯移轉作業

![容錯移轉](./media/site-recovery-failover/FailoverJob.png)

觸發容錯移轉時會執行下列步驟︰

1. 先決條件檢查︰這個步驟可確保符合容錯移轉所需的所有條件
1. 容錯移轉︰這個步驟會處理並備妥資料，以用來建立 Azure 虛擬機器。 如果您已選擇 [最新] 復原點，此步驟會從已傳送至服務的資料建立復原點。
1. 開始︰這個步驟會使用上一個步驟中所處理的資料建立 Azure 虛擬機器。

> [!WARNING]
> **不要取消進行中的容錯移轉**︰容錯移轉開始之前，虛擬機器的複寫會停止。 如果您**取消**進行中的作業，容錯移轉會停止，但虛擬機器不會開始複寫。 無法重新開始複寫。
>
>

## <a name="time-taken-for-failover-to-azure"></a>容錯移轉至 Azure 所花費的時間

在某些情況下，虛擬機器的容錯移轉會需要其他中繼步驟，通常會費時大約 8 到 10 分鐘才能完成。 這些情況如下所示︰

* VMware 虛擬機器使用的行動服務版本早於 9.8 版
* 實體伺服器 
* VMware Linux 虛擬機器
* 如同實體伺服器般受到保護的 Hyper-V 虛擬機器
* 沒有以下驅動程式作為開機驅動程式的 VMware 虛擬機器 
    * storvsc 
    * vmbus 
    * storflt 
    * intelide 
    * atapi
* 沒有啟用 DHCP 服務的 VMware 虛擬機器，無論其是否正在使用 DHCP 或靜態 IP 位址

在其他所有情況下則不需要此中繼步驟，且容錯移轉所花費的時間非常少。 





## <a name="using-scripts-in-failover"></a>在容錯移轉中使用指令碼
您可能想要在容錯移轉時自動執行特定動作。 若要這樣做，您可以在[復原方案](site-recovery-create-recovery-plans.md)中使用指令碼或 [Azure 自動化 Runbook](site-recovery-runbook-automation.md)。

## <a name="other-considerations"></a>其他考量
* **磁碟機代號** - 若要在容錯移轉後保留虛擬機器上的磁碟機代號，您可以將虛擬機器的 [SAN 原則] 設定為 [OnlineAll]。 [閱讀更多資訊](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure)。



## <a name="next-steps"></a>後續步驟
當您容錯移轉虛擬機器之後，而且內部部署資料中心可用時，您應該將 VMware 虛擬機器移回內部部署資料中心來[**重新保護**](site-recovery-how-to-reprotect.md)。

使用[**計劃性容錯移轉**](site-recovery-failback-from-azure-to-hyper-v.md)選項，將 Hyper-v 虛擬機器從 Azure **容錯回復**回到內部部署。

如果您已將 Hyper-v 虛擬機器容錯回復至 VMM 伺服器所管理的另一個內部部署資料中心，而且主要資料中心可用，請使用 [反向複寫] 選項，開始複寫回到主要資料中心。

