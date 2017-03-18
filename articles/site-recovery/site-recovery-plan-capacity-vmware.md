---
title: "規劃容量並調整 Azure 中的 VMware 複寫 | Microsoft Docs"
description: "將 VMware VM 複寫至 Azure 時，請使用本文規劃容量和調整"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/05/2017
ms.author: rayne
translationtype: Human Translation
ms.sourcegitcommit: 993449b7840f5077f23b3809439b89f27759e35d
ms.openlocfilehash: 1a991d1e4ac20019695fb557310e1981b5b491ec
ms.lasthandoff: 03/01/2017


---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>使用 Azure Site Recovery 規劃容量並調整 Azure 中的 VMware 複寫

使用這份文件找出在將內部部署 VMware VM 和實體伺服器複寫至 Azure 時，如何使用 [Azure Site Recovery](site-recovery-overview.md) 規劃容量和調整。

## <a name="how-do-i-start-capacity-planning"></a>如何開始容量規劃？

使用 [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) 收集複寫環境的相關資訊。 其中包括相容和不相容的虛擬機器數目、每個 VM 的磁碟數、每個磁碟的資料變換率、網路頻寬需求，以及成功複寫和測試容錯移轉/容錯移轉所需之 Azure 基礎結果的相關資訊。

## <a name="capacity-considerations"></a>容量考量

**元件** | **詳細資料** |
--- | --- | ---
**複寫** | **每日變更率上限** - 受保護的機器只能使用一部處理序伺服器，而且單一處理序伺服器可處理的每日變更率最多為 2 TB。 因此 2 TB 是針對受保護機器支援的每日資料變更率上限。<br/><br/> **最大輸送量**- 複寫的機器可以屬於 Azure 中的一個儲存體帳戶。 標準儲存體帳戶每秒可處理最多 20000 個要求，建議您將來源機器的 IOPS 數保持為 20000。 例如，如果您有一部具備 5 個磁碟的來源機器，並且在來源上的每個磁碟會產生 120 個 IOP (8K 大小)，則它會在 Azure 每個磁碟 IOPS 限制 500 之內。 儲存體帳戶所需的數目 = 來源 IOP 總計/20000。
**組態伺服器** | 組態伺服器應該要能夠處理在受保護機器上執行之所有工作負載的每日變更率容量，因此需要足夠頻寬以持續地將資料複寫到 Azure 儲存體。<br/><br/> 我們建議的最佳做法，是組態伺服器與您想要保護的機器位於相同網路與 LAN 區段上。 它可以位於不同的網路，但是您想要保護的機器應該具有 L3 網路可見性。<br/><br/> 下表摘要說明組態伺服器的大小建議。
**處理序伺服器** | 組態伺服器上會安裝第一部處理序伺服器。 您可以部署額外的處理序伺服器來調整您的環境。 請注意：<br/><br/> 處理序伺服器會從受保護的機器接收複寫資料，以快取最佳化、壓縮，並且在傳送至 Azure 之前加密。 處理序伺服器機器應該要有足夠的資源來執行這些工作。<br/><br/> 處理伺服器使用磁碟快取。 我們建議每個快取磁碟有 600 GB 以上的空間，以處理發生網路瓶頸或中斷時儲存的資料變更。

## <a name="size-recommendations-for-the-configuration-server"></a>組態伺服器的大小建議

**CPU** | **記憶體** | **快取磁碟大小** | **資料變更率** | **受保護的機器**
--- | --- | --- | --- | ---
8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz) | 16 GB | 300 GB | 500 GB 或更少 | 複寫少於 100 部機器。
12 個 vCPU (2 個通訊端 * 六核心 @ 2.5GHz) | 18 GB | 600 GB | 500 GB 至 1 TB | 複寫 100-150 部機器。
16 個 vCPU (2 個通訊端 * 八核心 @ 2.5GHz) | 32 GB | 1 TB | 1 TB 至 2 TB | 複寫 150-200 部機器。
部署另一個處理序伺服器 | | | > 2 TB | 如果您要複寫 200 部以上的機器，或如果每日資料變更率超過 2 TB，部署額外的處理序伺服器。

其中：

* 每個來源機器已設定各 100 GB 的 3 個磁碟。
* 我們使用具有 RAID 10 的 8 個 10 K RPM 的 SAS 磁碟機的效能評定儲存體以進行快取磁碟度量。

## <a name="size-recommendations-for-the-process-server"></a>處理序伺服器的大小建議

如果您要保護超過 200 部機器，或每日變更率大於 2 TB，您可以加入額外的處理伺服器來處理複寫負載。 若要擴充，您可以：

* 增加組態伺服器的數目。 例如，您可以使用兩部組態伺服器保護最多 400 部機器。
* 加入額外的處理伺服器並使用它們來處理流量，以取代 (或搭配) 組態伺服器。

下表描述案例，其中：

* 您不打算使用組態伺服器作為處理序伺服器。
* 您已設定額外的處理序伺服器。
* 您已設定受保護的虛擬機器，以使用額外的處理伺服器。
* 每個受保護的來源機器已設定各 100 GB 的 3 個磁碟。

**組態伺服器** | **額外處理序伺服器** | **快取磁碟大小** | **資料變更率** | **受保護的機器**
--- | --- | --- | --- | ---
8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz)，16 GB 記憶體 | 4 個 vCPU (2 個通訊端 * 雙核心 @ 2.5GHz)，8 GB 記憶體 | 300 GB | 250 GB 或更少 | 複寫 85 部或更少的機器。
8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz)，16 GB 記憶體 | 8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz)，12 GB 記憶體 | 600 GB | 250 GB 至 1 TB | 複寫 85-150 部機器。
12 個 vCPU (2 個通訊端 * 六核心 @ 2.5GHz)，18 GB 記憶體 | 12 個 vCPU (2 個通訊端 * 六核心 @ 2.5GHz)，24 GB 記憶體 | 1 TB | 1 TB 至 2 TB | 複寫 150-225 部機器。

您調整伺服器的方式取決於相應增加或相應放大模型的喜好設定。  您部署幾個高階組態和處理序伺服器以相應增加，或使用較少的資源部署更多伺服器以相應放大。 例如，如果您需要保護 220 部機器，您可以執行下列任一項：

* 設定具有 12 個 vCPU、18 GB 記憶體的組態伺服器、具有 12 個 vCPU、24 GB 記憶體的額外處理序伺服器，並將受保護的機器設定為僅使用額外的處理序伺服器。
* 或者，您也可以設定兩部組態伺服器 (2 x 8vCPU、16 GB RAM) 和兩部額外的處理序伺服器 (1 x 8vCPU 和 4vCPU x 1 以處理 135 + 85 (220) 部機器)，並將受保護的機器設定為僅使用額外的處理序伺服器。


## <a name="control-network-bandwidth"></a>控制網路頻寬

您可以使用[部署規劃工具](https://aka.ms/asr-deployment-planner-doc)來計算複寫 (初始複寫，而後是差異複寫) 所需的頻寬 。 若要控制複寫所用的頻寬數量，您有幾個選項可用︰

* **節流頻寬**︰複寫至 Azure 的 VMware 流量會經過特定的處理序伺服器。 您可在執行作為處理序伺服器的機器上進行頻寬節流。
* **影響頻寬**︰您可以使用幾個登錄機碼來影響用於複寫的頻寬：
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** 登錄值可指定用於磁碟資料傳輸 (初始或差異複寫) 的執行緒數目。 較高的值可增加複寫所用的網路頻寬。
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** 可指定在容錯回復期間用於資料傳輸的執行緒數目。

### <a name="throttle-bandwidth"></a>節流頻寬
1. 在作為處理序伺服器的機器上開啟 Microsoft Azure 備份 MMC 嵌入式管理單元。 根據預設，Microsoft Azure 備份的捷徑位於桌面上或在 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中。
2. 在嵌入式管理單元中，按一下 [變更屬性] 。

    ![節流頻寬](./media/site-recovery-vmware-to-azure/throttle1.png)
3. 在 [節流] 索引標籤上，選取 [啟用備份操作的網際網路頻寬使用節流設定]，然後設定工作和非工作時數的限制。 有效範圍是每秒 512 Kbps 到 102 Mbps。

    ![節流頻寬](./media/site-recovery-vmware-to-azure/throttle2.png)

您也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) Cmdlet 來設定節流。 以下是一個範例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要節流。

#### <a name="influence-network-bandwidth-for-a-vm"></a>影響 VM 的網路頻寬

1. 在 VM 的登錄中，瀏覽至 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**。
   * 若要影響複製磁碟上的頻寬流量，請修改 **UploadThreadsPerVM**的值，如果不存在則請建立機碼。
   * 若要影響從 Azure 容錯回復流量的頻寬，請修改 **DownloadThreadsPerVM**的值。
2. 預設值為 4。 在 “overprovisioned” 網路中，這些登錄機碼必須變更自其預設值。 最大值為 32。 監視流量，將此值最佳化。


## <a name="deploy-additional-process-servers"></a>部署額外處理序伺服器

如果您必須相應放大您的部署超過 200 部來源機器，或每日變換率總計超過 2 TB，您將需要額外的處理序伺服器來處理流量。 遵循這些指示以設定處理序伺服器。 設定伺服器之後，請移轉來源機器以便使用它。

1. 在 [Site Recovery 伺服器] 中，按一下 [設定伺服器] > [處理序伺服器]。

    ![新增處理序伺服器](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. 在 [伺服器類型] 中，按一下 [處理伺服器 (內部部署)]。

    ![新增處理序伺服器](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. 下載 Site Recovery 統一安裝檔案，然後執行它以安裝處理序伺服器，並在保存庫中註冊。
4. 在 [開始之前] 中，選取 [新增額外處理序伺服器以相應放大部署]。
5. 以您 [設定](#step-2-set-up-the-source-environment) 組態伺服器時的相同方式完成精靈。

    ![新增處理序伺服器](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. 在 [組態伺服器詳細資料] 中，指定組態伺服器的 IP 位址，以及複雜密碼。 若要取得複雜密碼，請在組態伺服器上執行 **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n**。

    ![新增處理序伺服器](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>移轉機器以使用新的處理序伺服器
1. 在 [設定] > [Site Recovery 伺服器] 中，按一下組態伺服器，然後展開 [處理伺服器]。

    ![更新處理序伺服器](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. 以滑鼠右鍵按一下目前使用中的處理序伺服器，然後按一下 [切換]。

    ![更新處理序伺服器](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. 在 [選取目標處理序伺服器] 中，選取您要使用的新處理序伺服器，然後選取新處理序伺服器將處理的虛擬機器。 按一下資訊圖示以取得伺服器的相關資訊。 為了協助您進行負載的判斷，會顯示將每個選取的虛擬機器複寫到新的處理序伺服器所需的平均空間。 按一下核取記號以開始複寫到新處理序伺服器。








