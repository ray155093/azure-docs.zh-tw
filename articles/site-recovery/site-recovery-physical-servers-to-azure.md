---
title: "將實體伺服器複寫至 Azure | Microsoft Docs"
description: "描述如何使用 Azure 入口網站部署 Azure Site Recovery，以協調內部部署 Windows/Linux 實體伺服器至 Azure 的複寫、容錯移轉和復原。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3396818cd177330b7123f3a346b1591a4bcb1e4e
ms.openlocfilehash: c9994e4e826bc35e1de439684ed5877db91fa069
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>使用 Azure 入口網站的 Azure Site Recovery 將實體機器複寫至 Azure


歡迎使用 Azure Site Recovery 服務！

Site Recovery 是一項有助於建立商務持續性和災害復原 (BCDR) 策略的 Azure 服務。 Site Recovery 會協調將內部部署實體伺服器與虛擬機器複寫至雲端 (Azure) 或次要資料中心的作業。 當您的主要位置發生故障時，您容錯移轉至次要位置，讓應用程式和工作負載保持可用。 當它恢復正常作業時，容錯回復至您的主要位置。 深入了解 [什麼是 Azure Site Recovery？](site-recovery-overview.md)

本文說明如何在 Azure 入口網站中使用 Azure Site Recovery，將內部部署 Windows/Linux 實體伺服器複寫至 Azure。

閱讀本文之後，請將任何意見張貼在底部的 Disqus 註解中。 請在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)提出技術問題。


## <a name="steps"></a>步驟

以下是您需要採取的動作：

1. 確認必要條件和限制。
2. 設定 Azure 網路和儲存體帳戶。
3. 準備您要部署為組態伺服器的內部部署電腦。
4. 建立復原服務保存庫。 保存庫包含組態設定，並協調複寫。
5. 指定來源、目標和複寫設定。
6. 將行動服務部署在您想要複寫的 VM 上。
7. 啟用 VM 複寫。
7. 執行測試容錯移轉，確定一切都沒問題

## <a name="prerequisites"></a>必要條件

**支援需求** | **詳細資料**
--- | ---
**Azure** | 了解 [Azure 需求](site-recovery-prereq.md#azure-requirements)
**內部部署組態伺服器** | 執行 Windows Server 2012 R2 或更新版本的內部部署電腦。 您在 Site Recovery 部署期間設定此伺服器。<br/><br/> 根據預設，處理序伺服器與主要目標伺服器也會安裝在此 VM。 當您相應增加時，可能需要不同的處理序伺服器，它的需求與組態伺服器相同。<br/><br/> 在[這裡](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)深入了解這些元件
**內部部署 VM** | 您想要複寫的 VM 應該執行[支援的作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，也要符合 [Azure 必要條件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
**URL** | 組態伺服器需要存取這些 URL：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> 如果您有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。<br/></br> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br/></br> 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。<br/><br/> 允許使用此 URL 下載 MySQL：http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi。
**行動服務** | 安裝在每個複寫的 VM 上。


## <a name="site-recovery-in-the-azure-portal"></a>Azure 入口網站中的 Site Recovery
Azure 用來建立和處理資源的[部署模型](../azure-resource-manager/resource-manager-deployment-model.md)有二種 - Azure Resource Manager 和傳統。 Azure 也有兩個入口網站 – Azure 傳統入口網站和 Azure 入口網站。

本文說明如何在 Azure 入口網站中進行部署，以提供新功能和流暢的部署體驗。 傳統入口網站可用於維護現有的保存庫。 您無法使用傳統入口網站建立新的保存庫。


## <a name="set-up-azure"></a>設定 Azure

1. [設定 Azure 網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。
    - 在容錯移轉之後建立的 Azure VM 會置於這個網路。
    - 您可以在 [Resource Manager](../resource-manager-deployment-model.md) 或傳統模式中設定網路。

2. 為複寫的資料設定 [Azure 儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)。
    - 此帳戶可以是標準或[進階](../storage/storage-premium-storage.md)。
    - 您可以在 Resource Manager 或傳統模式中設定帳戶。

## <a name="prepare-the-configuration-server"></a>準備組態伺服器

1. 在內部部署伺服器上安裝 Windows Server 2012 R2 或更新版本。
2. 確定 VM 可存取[必要條件](#prerequisites)中列出的 URL。

**準備帳戶來推入行動服務**︰如果您要將行動服務推入 VM，則需要有一個可讓處理序伺服器存取 VM 的帳戶。 此帳戶僅用於推入安裝。 您可以使用網域或本機帳戶：

  - 在 Windows 上，如果您不使用網域帳戶，則必須停用本機電腦上的遠端使用者存取控制。 若要這樣做，請在登錄的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，新增 DWORD 項目 **LocalAccountTokenFilterPolicy**，值為 1。
    - 如果您想要從 CLI 新增適用於 Windows 的登錄項目，請輸入︰  ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
  - 在 Linux 上，帳戶應該是來源 Linux 伺服器上的根使用者。

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>選取保護目標

選取您要複寫的項目以及您要複寫到的位置。

1. 按一下 [復原服務保存庫] > 保存庫。
2. 在 [資源功能表] 中，按一下 [Site Recovery] > [步驟 1: 準備基礎結構] > [保護目標]。

    ![選擇目標](./media/site-recovery-vmware-to-azure/choose-goal-physical.PNG)
3. 在 [保護目標] 中選取 [至 Azure]，然後選取 [未虛擬化/其他]。


## <a name="set-up-the-source-environment"></a>設定來源環境

安裝組態伺服器、註冊在保存庫及探索 VM。

1. 按一下 [Site Recovery] > [步驟 1: 準備基礎結構] > [來源]。
2. 如果您沒有組態伺服器，請按一下 [+組態伺服器]。

    ![設定來源](./media/site-recovery-vmware-to-azure/set-source1.png)
3. 在 [新增伺服器] 中，檢查 [組態伺服器] 是否出現在 [伺服器類型] 中。
4. 下載 Site Recovery 統一安裝的安裝檔案。
5. 下載保存庫註冊金鑰。 您會在執行統一安裝時用到此金鑰。 該金鑰在產生後會維持 5 天有效。

   ![設定來源](./media/site-recovery-vmware-to-azure/set-source2.png)
6. 在組態伺服器 VM 上，請確定系統時鐘與[時間伺服器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步，然後執行「整合安裝」來安裝組態伺服器、處理序伺服器和主要目標伺服器。

## <a name="run-site-recovery-unified-setup"></a>執行 Site Recovery 統一安裝

開始之前：

- 確定 VM 上的時間與當地時區的時間相同。 應該相符。 如果快慢誤差 15 分鐘，安裝可能會失敗。
- 在組態伺服器 VM 上以本機系統管理員身分執行安裝程式。
- 確定 VM 上已啟用 TLS 1.0。

然後在組態伺服器上執行「整合安裝」安裝檔案。


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 您可以透過命令列安裝組態伺服器。 如需詳細資訊，請參閱[使用命令列工具安裝組態伺服器](http://aka.ms/installconfigsrv)。

## <a name="set-up-the-target"></a>設定目標

設定目標環境之前，請檢查您有 [Azure 儲存體帳戶和網路](#set-up-azure)

1. 按一下 [準備基礎結構] > [目標]，然後選取您要使用的 Azure 訂用帳戶。
2. 指定目標部署模型是以 Resource Manager 為基礎或傳統。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

   ![目標](./media/site-recovery-vmware-to-azure/gs-target.png)
4. 如果您尚未建立儲存體帳戶或網路，請按一下 [+儲存體帳戶] 或[+網路]，以建立 Resource Manager 帳戶或網路內嵌。

## <a name="set-up-replication-settings"></a>設定複寫設定

1. 若要建立新的複寫原則，請按一下 [Site Recovery 基礎結構] > [複寫原則] > [+複寫原則]。
2. 在 [建立複寫原則] 中，指定原則名稱。
3. 在 [RPO 臨界值] 中，指定 RPO 限制。 這個值指定資料復原點的建立頻率。 連續複寫超過此限制時會產生警示。
4. 在 [復原點保留] 中，指定每個復原點的保留週期長度 (以小時為單位)。 複寫的 VM 可以還原至一個週期內的任何時間點。 複寫至進階儲存體的電腦支援最長保留 24 小時，標準儲存體則是 72 小時。
5. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (以分鐘為單位)。 按一下 [確定]  以建立原則。

    ![複寫原則](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. 當您建立新的原則時，該原則會自動與組態伺服器產生關聯。 依預設會自動建立容錯回復的比對原則。 例如，如果複寫原則是 **rep-policy**，容錯回復原則便會是 **rep-policy-failback**。 從 Azure 起始容錯回復時才會使用此原則。  


## <a name="plan-capacity"></a>規劃容量

1. 您現已設定您的基本基礎結構，您可以思考容量規劃並找出您是否需要額外的資源。 [深入了解](site-recovery-plan-capacity-vmware.md)。
2. 當您完成容量規劃時，請在 [已完成容量計劃了嗎?] 中選取 [是]

   ![容量規劃](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>準備 VM 進行複寫

您想要複寫的所有電腦都必須安裝行動服務。 安裝行動服務有許多方式︰

1. 從處理伺服器使用推入安裝來安裝。 您需要準備 VM 才能使用這個方法。
2. 使用 System Center Configuration Manager 或 Azure Automation DSC 之類的部署工具來安裝。
3.  手動安裝。

[深入了解](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>啟用複寫

開始之前：

- 當您新增或修改 VM 時，可能需要 15 分鐘或更久，變更才會生效，也才會出現在入口網站中。
- 您可以在 [組態伺服器] > [上次連絡時間] 中，查看上次探索 VM 的時間。
- 若要新增 VM 而不等候已排定的探索，請醒目提示組態伺服器 (不要按一下)，然後按一下 [重新整理]。
* 如果已準備好 VM 進行推入安裝，當您啟用複寫時，處理序伺服器會自動安裝行動服務。


### <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

依預設會複寫電腦上的所有磁碟。 您可以從複寫排除磁碟。 例如，您可能不想要複寫具有暫存資料的磁碟，或是每次機器或應用程式重新啟動時便重新整理的資料 (例如 pagefile.sys 或 SQL Server tempdb)。

### <a name="replicate-vms"></a>複寫 VM

1. 按一下 [步驟 2: 複寫應用程式]  >  [來源]。
2. 在 [來源] 中，選取 [內部部署]
3. 在 [來源位置] 中，選取組態伺服器名稱
4. 在 [機器類型] 中，選取 [實體機器]。
4. 在 [vCenter/vSphere Hypervisor] 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。
5. 在 [處理序伺服器] 中，選擇處理序伺服器。 如果您尚未建立任何額外的處理序伺服器，則這會成為組態伺服器。 然後按一下 [確定] 。

    ![啟用複寫](./media/site-recovery-physical-to-azure/chooseVM.png)

6. 在 [目標] 中，選取您想要在其中建立容錯移轉 VM 的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或資源管理) 中，針對容錯移轉 VM 使用的部署模型。

7. 選取您要用來複寫資料的 Azure 儲存體帳戶。 如果您不想要使用已設定的帳戶，您可以建立新的帳戶。

8. 選取 Azure VM 在容錯移轉後啟動時所要建立的 Azure 網路和子網路。 選取 [立即設定選取的機器]，將網路設定套用至您選取要進行保護的所有機器。 選取 [稍後設定] 以選取每部機器的 Azure 網路。 如果您不想使用現有的網路，您可以建立網路。

    ![啟用複寫](./media/site-recovery-physical-to-azure/targetsettings.png)
9. 在 [實體機器] 中，按一下 [+] 按鈕，輸入 [名稱]、[IP 位址]，然後選擇您想要複寫之電腦的作業系統類型。
  ![啟用複寫](./media/site-recovery-physical-to-azure/machineselect.png) 必須等候幾分鐘，才會探索到電腦並顯示在清單中。

10. 在 [名稱]  > 中，選取處理序伺服器將用來在機器上自動安裝行動服務的帳戶。
11. 依預設會複寫所有磁碟。 按一下 [所有磁碟]  ，然後將任何您不想要複寫的磁碟取消選取。 然後按一下 [確定] 。 您可以稍後再設定其他 VM 屬性。

    ![啟用複寫](./media/site-recovery-physical-to-azure/configprop.png)
11. 在 [複寫設定] > [設定複寫設定] 中，確認已選取正確的複寫原則。 如果您修改原則，變更會套用至複寫電腦及新的電腦。
12. 如果您想要將機器聚集成一個複寫群組，請啟用 [多部 VM 一致性]  ，並指定群組的名稱。 然後按一下 [確定] 。 請注意：

    * 複寫群組中的電腦會一起複寫，並且在容錯移轉時會有共用的損毀一致和應用程式一致的復原點。
    * 我們建議您將 VM 與實體伺服器一起收集，讓它們鏡像您的工作負載。 啟用多部 VM 一致性可能會影響工作負載的效能，應該只用於機器正在執行相同工作負載，且您需要一致性的情況。
    ![啟用複寫](./media/site-recovery-physical-to-azure/policy.png)

13. 按一下 [啟用複寫] 。 您可以在 [設定]  >  [作業]  >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 執行 [完成保護]  作業之後，機器即準備好進行容錯移轉。

啟用複寫後，如果您設定推入安裝，將會安裝行動服務。 在 VM 上推入安裝行動服務之後，保護作業會啟動且失敗。 在失敗之後，您需要手動重新啟動每一部機器。 然後，保護作業會再次啟動，並進行初始複寫。


### <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

我們建議您確認 VM 屬性，並進行任何需要的變更。

1. 按一下 [複寫的項目]，然後選取電腦。 [程式集]  刀鋒視窗會顯示機器設定與狀態的相關資訊。
2. 在 [屬性] 中，您可以檢視 VM 的複寫和容錯移轉資訊。
3. 在 [計算和網路] > [計算屬性] 中，您可以指定 Azure VM 名稱和目標大小。 視需要修改名稱以符合 [Azure 需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) 。
4. 修改目標網路、子網路以及將指派給 Azure VM 的 IP 位址等設定：

   - 您可以設定目標 IP 位址。

    - 如果您未提供地址，則容錯移轉的機器會使用 DHCP。
    - 如果您設定的位址在容錯移轉時無法使用，則容錯移轉會失敗。
    - 如果相同的目標 IP 位址在測試容錯移轉網路中可用，則此位址可用於測試容錯移轉。

   - 網路介面卡的數目會視您指定給目標虛擬機器的大小而有所不同：

     - 如果來源電腦上的網路介面卡數目等於或小於針對目標電腦大小所允許的介面卡數目，則目標將具備與來源相同的介面卡數目。
     - 如果來源虛擬機器的介面卡數目超過針對目標大小所允許的數目，則將使用目標大小的最大值。
     - 例如，如果來源機器具有兩張網路介面卡，而目標機器大小支援四張，則目標機器將會有兩張介面卡。 如果來源機器具有兩張介面卡，但支援的目標大小僅支援一張，則目標機器將只會有一張介面卡。     
   - 如果虛擬機器有多張網路介面卡，則全部會連接至相同的網路。
   - 如果虛擬機器具有多個網路介面卡，則清單中顯示的第一個會變成 Azure 虛擬機器中的*預設*網路介面卡。
5. 在 [磁碟] 中，您可以看見 VM 作業系統和將要複寫的資料磁碟。

## <a name="run-a-test-failover"></a>執行測試容錯移轉

一切就緒後，執行測試容錯移轉，確定一切如預期般運作。


1. 若要容錯移轉單一機器，請在 [設定]  >  [複寫的項目] 中，按一下 VM > [+測試容錯移轉] 圖示。

    ![測試容錯移轉](./media/site-recovery-vmware-to-azure/TestFailover.png)

1. 若要容錯移轉復原方案，請在 [設定]  >  [復原方案] 中，以滑鼠右鍵按一下方案 > [測試容錯移轉]。 若要建立復原方案，請[遵循這些指示](site-recovery-create-recovery-plans.md)。  

1. 在 [測試容錯移轉] 中，選取 Azure VM 在容錯移轉之後要連接的 Azure 網路。

1. 按一下 [確定]  即可開始容錯移轉。 您可以按一下 VM 以開啟其屬性，或在保存庫名稱 > [設定]  >  [作業]  >  [Site Recovery 作業] 中的 [測試容錯移轉] 作業上按一下，來追蹤進度。

1. 容錯移轉完成之後，您應該也會看到複本 Azure 機器出現在 Azure 入口網站 > [虛擬機器]中。 您應該確定 VM 為適當的大小、已連接到適當的網路，而且正在執行中。

1. 如果您[已準備好容錯移轉後的連線](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)，您應該能夠連接到 Azure VM。

1. 完成後，在復原方案上按一下 [清除測試容錯移轉]。 在 [記事]  中，記錄並儲存關於測試容錯移轉的任何觀察。 這將刪除在測試容錯移轉期間所建立的虛擬機器。

如需詳細資訊，請參閱[測試容錯移轉至 Azure](site-recovery-test-failover-to-azure.md) 文件。

## <a name="next-steps"></a>後續步驟

複寫開始正常執行之後，運作中斷時就會容錯移轉至 Azure，而且會從複寫的資料建立 Azure VM。 然後，您可以在 Azure 中存取工作負載和應用程式，直到恢復正常運作時容錯回復至主要位置。

- [深入了解](site-recovery-failover.md)不同類型的容錯移轉及如何執行。
- 如果您要移轉電腦而不是複寫和容錯回復，請[深入了解](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers)。
- 當複寫實體機器，您可以只容錯回復到 VMWare 環境。 [了解容錯回復](site-recovery-failback-azure-to-vmware.md)。

## <a name="third-party-software-notices-and-information"></a>第三方廠商軟體注意事項和資訊
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

