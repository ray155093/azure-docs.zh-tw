---
title: "在 Site Recovery 中將 Hyper-V 複寫至 Azure 如何運作？ | Microsoft Docs"
description: "這篇文章提供在 Azure Site Recovery 中 Hyper-V 複寫運作方式的概觀"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 9adf266c6a2ac00c3aaa34e2a29aefe34abe2871
ms.lasthandoff: 04/18/2017


---
# <a name="how-does-hyper-v-replication-to-azure-work"></a>將 Hyper-V 複寫至 Azure 如何運作？

閱讀本文以了解使用 [Azure Site Recovery](site-recovery-overview.md) 服務，進行 Hyper-V 複寫至 Azure 的架構和工作流程。

如有任何意見，請張貼於這篇文章下方或 [Azure 復原服務論壇 (英文)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 中。

您可以將下列項目複寫至 Azure：
- **具有 VMM 的 Hyper-V**：位於內部部署 Hyper-V 主機的 VM 是在 System Center Virtual MAchine Manager (VMM) 雲端進行管理。 主機可以執行任何[支援的作業系統](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)。 您可以複寫 Hyper-V VM，執行 [Hyper-V 和 Azure 支援](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)的任何客體作業系統。
- **沒有 VMM 的 Hyper-V**：位於 Hyper-V 主機的內部部署 VM，不是在 VMM 雲端進行管理。 主機可以執行任何[支援的作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)。 您可以複寫 Hyper-V VM，執行 [Hyper-V 和 Azure 支援](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)的任何客體作業系統。


## <a name="architectural-components"></a>架構元件

**領域** | **元件** | **詳細資料**
--- | --- | ---
**Azure** | 在 Azure 中，您需要 Microsoft Azure 帳戶、Azure 儲存體帳戶和 Azure 網路。 | 儲存體和網路可以是 Resource Manager 型帳戶或傳統帳戶。<br/><br/> 所複寫的資料會儲存在儲存體帳戶中，而在從內部部署網站進行容錯移轉時，便會以複寫的資料建立 Azure VM。<br/><br/> Azure VM 在建立後會連線到 Azure 虛擬網路。
**VMM 伺服器** | 位於 VMM 雲端中的 Hyper-V 主機 | 如果 Hyper-V 主機是在 VMM 雲端中進行管理，您是在復原服務保存庫中註冊 VMM 伺服器。<br/><br/> 在 VMM 伺服器上安裝 Site Recovery Provider，以協調與 Azure 的複寫。<br/><br/> 您需要邏輯和 VM 網路設定以設定網路對應。 VM 網路應該連結到與雲端相關聯的邏輯網路。
**Hyper-V 主機** | 可以部署包含或不包含 VMM 伺服器的 Hyper-V 伺服器。 | 如果沒有 VMM 伺服器，Site Recovery Provider 會安裝在主機上，以透過網際網路協調與 Site Recovery 的複寫。 如果有 VMM 伺服器，會在上面安裝 Provider，而不是在主機上安裝。<br/><br/> 復原服務代理程式安裝在主機上以處理資料複寫。<br/><br/> 來自提供者和代理程式的通訊都是安全且加密的。 Azure 儲存體中的複寫的資料也會加密。
**Hyper-V VM** | 您必須在 Hyper-V 主機伺服器上擁有一或多個 VM。 | 不需要明確地在 VM 上安裝任何項目

## <a name="deployment-steps"></a>部署步驟

1. **Azure**：您要設定 Azure 元件。 我們建議您先設定儲存體和網路帳戶，再開始 Site Recovery 部署。
2. **保存庫**︰您建立 Site Recovery 的復原服務保存庫，並設定保存庫設定，包括設定來源和目標設定、設定複寫原則，以及啟用複寫。
3. **來源與目標**：
    - **VMM 雲端中的 Hyper-V 主機**：做為指定來源設定的一部分，您在 VMM 伺服器上下載並安裝 Azure Site Recovery Provider，以及在每部 Hyper-V 主機上下載並安裝 Azure 復原服務代理程式。 來源將會是 VMM 伺服器。 目標是 Azure。
    - 沒有 VMM 的 Hyper-V 主機：當您指定來源設定時，您在每部 Hyper-V 主機上下載並安裝 Provider 和代理程式。 在部署期間您將主機蒐集到 Hyper-V 網站，並且指定此網站做為來源。 目標是 Azure。

    ![Hyper-V/VMM 複寫至 Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)
    ![Hyper-V 網站複寫至 Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


4. **複寫原則**：您要建立 Hyper-V 網站或 VMM 雲端的複寫原則。 此原則會套用至位於網站或雲端中之主機上的所有 VM。
5. **啟用複寫**：針對 Hyper-V VM 啟用複寫。 初始複寫會根據複寫原則設定來進行。 資料變更會受到追蹤，在初始複寫完成之後，就會開始將差異變更複寫到 Azure。 項目的追蹤變更會保存在 .hrl 檔案中。
6. **測試容錯移轉**：執行測試容錯移轉，確定一切都沒問題。

深入了解部署：
- [開始使用 Hyper-V VM 複寫至 Azure - 具有 VMM](site-recovery-vmm-to-azure.md)
- [開始使用 Hyper-V VM 複寫至 Azure - 沒有 VMM](site-recovery-hyper-v-site-to-azure.md)

## <a name="hyper-v-replication-workflow"></a>Hyper-V 複寫工作流程

### <a name="enable-protection"></a>啟用保護。

1. 您在 Azure 入口網站或內部部署針對 Hyper-V VM 啟用保護之後，**啟用保護**隨即啟動。
2. 作業會檢查符合必要條件的機器，然後叫用 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) 方法，以使用您進行的設定來設定複寫。
3. 作業會啟動初始複寫，方法是叫用 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 方法，以初始化完整的 VM 複寫，並且將 VM 的虛擬磁碟傳送至 Azure。
4. 您可以在 [作業] 索引標籤中監視作業。
        ![作業清單](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![啟用保護向下鑽研](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="initial-replication"></a>初始複寫

1. 系統會在初始複寫觸發時擷取 [Hyper-V VM 快照集](https://technet.microsoft.com/library/dd560637.aspx)。
2. 虛擬硬碟會逐一複寫，直到它們全部複製到 Azure 為止。 可能需要一些時間，取決於 VM 大小和網路頻寬。 若要將網路使用量最佳化，請參閱 [如何管理內部部署至 Azure 保護的網路頻寬使用量](https://support.microsoft.com/kb/3056159)。
3. 如果在初始複寫進行時發生磁碟變更，Hyper-V 複本複寫追蹤器會以 Hyper-V 複寫記錄檔 (.hrl) 的形式追蹤這些變更。 這些記錄檔位於與磁碟相同的資料夾中。 每個磁碟都有一個相關聯的.hrl 檔案，將會傳送至次要儲存體。
4. 當初始複寫正在進行時，快照和記錄檔會取用磁碟資源。
5. 初始複寫完成時，就會刪除 VM 快照集。 記錄中的差異磁碟變更會同步處理，並合併到父磁碟。


### <a name="finalize-protection"></a>完成保護

1. 初始複寫完成之後，**在虛擬機器上完成保護**作業會設定網路和其他複寫後設定，如此就能讓虛擬機器受到保護。
    ![完成保護作業](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. 如果您要複寫至 Azure，您可能需要調整虛擬機器的設定，使其準備好進行容錯移轉。 此時，您可以執行測試容錯移轉，以確認一切如預期般運作。

### <a name="delta-replication"></a>差異複寫

1. 在初始複寫之後，會根據複寫設定，開始進行差異同步處理。
2. Hyper-V 複本複寫追蹤器會以 .hrl 檔案格式追蹤虛擬硬碟的變更。 每個設定用於複寫的磁碟都會有相關聯的 .hrl 檔案。 此記錄檔會在初始複寫完成後，傳送至客戶的儲存體帳戶。 當記錄傳輸至 Azure 時，將會在同一目錄內的另一個記錄檔中追蹤主要磁碟中的變更。
3. 在初始和差異複寫期間，您可以在 VM 檢視中監視 VM。 [深入了解](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines)。  

### <a name="replication-synchronization"></a>複寫同步處理

1. 如果差異複寫失敗且完整複寫因為頻寬或時間需要大量成本，就會將 VM 標示為重新同步處理。 例如，如果 .hrl 檔案達到磁碟大小的 50%，系統就會標示 VM 以便重新同步處理。
2.  重新同步處理會計算來源和目標虛擬機器的總和檢查碼，並只傳送差異資料部分，藉此將傳送的資料量降至最低。 重新同步處理會使用固定區塊的區塊處理演算法，將來源和目標檔案分成固定區塊。 系統會產生每個區塊的總和檢查碼並相互比較，以決定需要將來源中的哪些區塊套用至目標。
3. 重新同步處理完成之後，應會繼續進行正常的差異複寫。 根據預設，重新同步處理會排程在上班時間以外的時間自動執行，但是您可以手動重新同步處理虛擬機器。 例如，若發生網路中斷或其他中斷情形，您可以繼續重新同步處理。 若要這樣做，請在入口網站中選取 VM > [重新同步處理]。

    ![手動重新同步處理](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retries"></a>重試

如果發生複寫錯誤，會有內建的重試。 此邏輯可分為以下兩種類別：

**類別** | **詳細資料**
--- | ---
**無法復原的錯誤** | 不嘗試重試。 VM 狀態為**重大**，需要管理員介入處理。 這些錯誤的範例包括︰中斷 VHD 鏈結；複本 VM 的狀態無效；網路驗證錯誤︰授權錯誤；找不到 VM 錯誤 (適用於獨立 Hyper-V 伺服器)
**可復原的錯誤** | 在每個複寫間隔中進行重試，並採用指數倒退法，從第一次嘗試開始增加重試間隔 (1、2、4、8、10 分鐘)。 如果錯誤持續發生，會每隔 30 分鐘重試一次。 範例包括：網路錯誤；磁碟空間不足錯誤；記憶體不足情況 |

## <a name="protection-and-recovery-lifecycle"></a>保護和復原生命週期

![工作流程](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>後續步驟

- [檢查部署必要條件](site-recovery-prereq.md)
- 疑難排解：
    - [針對保護進行監視和疑難排解](site-recovery-monitoring-and-troubleshooting.md)
    - [Microsoft 支援的說明](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
    - [常見錯誤和解決方案](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)

