---
title: 使用 Azure Site Recovery 進行 Hyper-V 複寫 | Microsoft Docs
description: 使用本文來了解一些技術概念，協助您順利安裝、設定和管理 Azure Site Recovery。
services: site-recovery
documentationcenter: ''
author: Rajani-Janaki-Ram
manager: mkjain
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/12/2016
ms.author: rajanaki

---
# <a name="hyper-v-replication-with-azure-site-recovery"></a>使用 Azure Site Recovery 進行 Hyper-V 複寫
本文說明的技術概念，可協助您透過 Azure Site Recovery，順利設定和管理 Hyper-V 網站或 System Center Virtual Machine Manager (VMM) 網站至 Azure 的保護。

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>設定內部部署和 Azure 之間複寫的來源環境
在設定內部部署與 Azure 之間的災害復原時，請務必下載 Azure Site Recovery 提供者並安裝在 VMM 伺服器上。 在每個 Hyper-V 主機上安裝 Azure 復原服務代理程式。

![用於在內部部署和 Azure 之間複寫的 VMM 網站部署](media/site-recovery-understanding-site-to-azure-protection/image00.png)

在 Hyper-V 管理的基礎結構中設定來源環境，類似於在 VMM 管理的基礎結構中設定來源環境。 唯一的差別是提供者和代理程式安裝在 Hyper-V 主機本身。 在 VMM 環境中，提供者安裝在 VMM 伺服器上，而代理程式安裝在 Hyper-V 主機上 (如果是複寫至 Azure)。

## <a name="workflows"></a>工作流程
### <a name="enable-protection"></a>啟用保護。
從 Azure 入口網站或內部部署保護虛擬機器之後，將會啟動名為 **啟用保護** 的 Site Recovery 作業。 您可以在 [作業]  索引標籤下監視它。

![作業清單中的「啟用保護」作業](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

在叫用 **CreateReplicationRelationship** 方法之前， [啟用保護](https://msdn.microsoft.com/library/hh850036.aspx) 作業會檢查必要條件。 這個方法會使用在保護期間所設定的輸入來建立複寫至 Azure。

**啟用保護** 作業會叫用 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 方法，開始從內部部署進行初始複寫。 這個方法會將虛擬機器的虛擬磁碟傳送至 Azure。

![「啟用保護」作業的詳細資料](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>完成虛擬機器上的保護
系統會在初始複寫觸發時擷取 [Hyper-V VM 快照集](https://technet.microsoft.com/library/dd560637.aspx) 。 系統會逐一處理虛擬硬碟，直到所有磁碟都上傳至 Azure 為止。 視磁碟大小和頻寬而定，這項作業通常要經過一段時間才能完成。 若要將網路使用量最佳化，請參閱 [如何管理內部部署至 Azure 保護的網路頻寬使用量](https://support.microsoft.com/kb/3056159)。

當初始複寫完成之後， **完成虛擬機器上的保護** 作業會設定網路和複寫後設定。 在進行初始複寫時︰

* 追蹤磁碟的所有變更。 
* 快照集和 Hyper-V 複本記錄檔 (HRL) 都會耗用額外的磁碟儲存空間。

初始複寫完成時就會刪除 Hyper-V VM 快照集。 此刪除動作會導致將初始複寫之後的資料變更合併至父磁碟。

![作業清單中的「完成虛擬機器上的保護」作業](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>差異複寫
Hyper-V 複本複寫追蹤器是 Hyper-V 複本複寫引擎的一部分，會追蹤虛擬硬碟的變更，並記錄在 Hyper-V 複本記錄檔 (*.hrl) 中。 HRL 檔案與相關聯的磁碟位於相同的目錄中。

每個設定用於複寫的磁碟都會有相關聯的 HRL 檔案。 此記錄檔會在初始複寫完成後，傳送至客戶的儲存體帳戶。 當記錄檔傳輸至 Azure 時，將會在同一目錄內的另一個記錄檔中追蹤主要複本中的變更。

在初始複寫或差異複寫期間，您可以在 VM 檢視中監視 VM 複寫健全狀況，如 [監視虛擬機器的複寫健全狀況](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine)中所述。  

### <a name="resynchronization"></a>重新同步處理
當差異複寫失敗，且完整初始複寫會耗費大量網路頻寬或時間，系統就會標示虛擬機器以便重新同步處理。 例如，當 HRL 檔案大小累積達總磁碟大小的 50% 時，系統就會標示虛擬機器以便重新同步處理。 重新同步處理會計算來源和目標虛擬機器磁碟的總和檢查碼，並只傳送差異部分，藉此有效減少透過網路傳送的資料量。

重新同步處理完成之後，應會繼續進行正常的差異複寫。 若發生網路中斷或其他中斷情形，您可以繼續重新同步處理。

根據預設，系統會將自動排程的重新同步處理設定在非上班時間進行。 如果需要以手動方式重新同步處理虛擬機器，請從入口網站中選取虛擬機器，然後按一下 [重新同步處理] 。

![手動重新同步處理](media/site-recovery-understanding-site-to-azure-protection/image04.png)

重新同步處理會使用固定區塊的區塊處理演算法，將來源和目標檔案分成固定區塊。 系統會產生每個區塊的總和檢查碼並相互比較，以決定需要將來源中的哪些區塊套用至目標。

### <a name="retry-logic"></a>重試邏輯
發生複寫錯誤時會啟動內建的重試邏輯。 此邏輯可分為以下兩種類別：

| 類別 | 案例 |
| --- | --- |
| 無法復原的錯誤 |不嘗試重試。 虛擬機器複寫狀態為 **重大**，需要管理員介入處理。 範例包括： <ul><li>中斷的 VHD 鏈結</li><li>複本虛擬機器的狀態無效</li><li>網路驗證錯誤</li><li>授權錯誤</li><li>在獨立 Hyper-V 伺服器的情況下找不到虛擬機器</li></ul> |
| 可復原的錯誤 |在每個複寫間隔中進行重試，並採用指數倒退法，從第一次嘗試開始增加重試間隔 (1、2、4、8、10 分鐘)。 如果錯誤持續發生，會每隔 30 分鐘重試一次。 範例包括： <ul><li>網路錯誤</li><li>磁碟空間不足</li><li>記憶體不足的狀況</li></ul> |

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Hyper-V 虛擬機器保護和復原的生命週期
![Hyper-V 虛擬機器保護和復原的生命週期](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>其他參考資料
* [針對 VMware、VMM、Hyper-V 和實體網站的保護進行監視和疑難排解](site-recovery-monitoring-and-troubleshooting.md)
* [連絡 Microsoft 支援](site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
* [Azure Site Recovery 的常見錯誤和其解決方式](site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!--HONumber=Oct16_HO2-->


