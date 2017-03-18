---
title: "在傳統入口網站中，將 VMware VM 與實體伺服器複寫到 Azure | Microsoft Docs"
description: "本文描述如何部署 Azure Site Recovery，以協調內部部署 VMware 虛擬機器或 Windows/Linux 實體伺服器至 Azure 的複寫、容錯移轉和復原。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: a9022c1f-43c1-4d38-841f-52540025fb46
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 37291b16a966c17b684a4622f15d0393a5a5ce6d
ms.openlocfilehash: 0984ccc8163c94f56517e65919f8ea089a3e8b5f
ms.lasthandoff: 02/27/2017


---
# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure
> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-vmware-to-azure.md)
> * [傳統入口網站](site-recovery-vmware-to-azure-classic.md)
> * [傳統入口網站 (舊版)](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

Azure Site Recovery 服務可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原 (BCDR) 策略，為您的商務持續性與災害復原做出貢獻。 機器可以複寫至 Azure，或次要的內部部署資料中心。 如需快速概觀，請參閱[什麼是 Azure Site Recovery？](site-recovery-overview.md)。

## <a name="overview"></a>概觀
這篇文章說明如何：

* **將 VMware 虛擬機器複寫至 Azure**：部署 Site Recovery 以協調內部部署 VMware 虛擬機器對 Azure 儲存體的複寫、容錯移轉和復原。
* **將實體伺服器複寫至 Azure**：部署 Azure Site Recovery 以協調內部部署實體 Windows 和 Linux 伺服器至 Azure 的複寫、容錯移轉和復原。

> [!NOTE]
> 本文說明如何複寫至 Azure。 如果您想要將 VMware VM 或 Windows/Linux 實體伺服器複寫至次要資料中心，請參閱 [Site Recovery VMware 至 VMware](site-recovery-vmware-to-vmware.md)。
>
>

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## <a name="enhanced-deployment"></a>增強部署
本文包含 Azure 傳統入口網站中增強部署的指示。 建議您對所有新的部署使用這個版本。 如果您已經使用舊版來部署，建議您移轉到新的版本。 如需移轉的詳細資訊，請參閱 [Site Recovery VMware 至 Azure 的傳統舊版](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment)。

增強部署是一項重大更新。 以下是我們所做的改良功能的摘要：

* **Azure 中沒有基礎結構 VM**：資料會直接複寫到 Azure 儲存體帳戶。 此外，不必為複寫和容錯移轉來設定任何基礎結構 VM (例如組態伺服器或主要目標伺服器)，只有在舊版部署中才需要這樣做。  
* **整合安裝**：單一安裝可提供內部部署元件的簡單設定及延展性。
* **安全部署**：所有流量都會加密，且複寫管理通訊會透過 HTTPS 443 傳送。
* **復原點**：支援 Windows 和 Linux 環境中的當機和應用程式一致復原點，並同時支援單一 VM 和多個 VM 一致組態。
* **測試容錯移轉**：支援非干擾性測試容錯移轉至 Azure，而不會影響實際執行或暫停複寫。
* **非計劃性容錯移轉**：使用增強選項，在容錯移轉之前自動關閉 VM，支援非計劃性容錯移轉至 Azure。
* **容錯回復**：整合式容錯回復，僅將有差異的變更複寫回內部部署網站。
* **vSphere 6.0**：有限制支援 VMware vSphere 6.0 部署。

## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>Site Recovery 如何協助保護虛擬機器與實體伺服器？
* VMware 系統管理員可以設定異地保護，保護 Azure 不受 VMware 虛擬機器上執行的商務工作負載和應用程式所影響。 伺服器管理員可以將實體內部部署 Windows 和 Linux 伺服器複寫至 Azure。
* Azure Site Recovery 主控台提供單一位置以進行簡單的設定和複寫管理、容錯移轉及復原程序。
* 如果複寫由 vCenter 伺服器管理的 VMware 虛擬機器，Site Recovery 就可以自動探索這些 VM。 如果機器位於 ESXi 主機，Site Recovery 會探索主機上的 VM。
* 如果您執行從內部部署基礎結構至 Azure 的簡單容錯移轉，則可以從 Azure 容錯回復 (還原) 至內部部署網站中的 VMware VM 伺服器。
* 您可以設定復原方案，將分散於多部機器上的應用程式工作負載聚集起來。 如果您容錯移轉這些方案，Site Recovery 提供多 VM 一致性，以便執行相同工作負載的機器可以同時復原到一致的資料點。

## <a name="supported-operating-systems"></a>受支援的作業系統
### <a name="windows-64-bit-only"></a>Windows (僅&64; 位元)
* Windows Server 2008 R2 SP1+
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (僅&64; 位元)
* Red Hat Enterprise Linux 6.7、7.1 和 7.2
* CentOS 6.5、6.6、6.7、7.0、7.1 和 7.2
* Oracle Enterprise Linux 6.4 和 6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3)
* SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>案例架構
案例元件：

* **內部部署管理伺服器**：管理伺服器會執行 Site Recovery 元件：
  * **組態伺服器**：協調通訊和管理資料複寫與復原程序。
  * **處理序伺服器**：作為複寫閘道器。 從受保護的來源機器接收資料、以快取最佳化、壓縮和加密，以及將複寫資料傳送至 Azure 儲存體。 它還會處理用來保護機器的行動服務的推入安裝，並執行 VMWare VM 的自動探索。
  * **主要目標伺服器**：在從 Azure 容錯回復期間，處理複寫資料。
    您也可以部署管理伺服器，僅做為處理序伺服器，以調整您的部署。
* **行動服務**：此元件會部署在您想要複寫至 Azure 的每部機器上 (VMware VM 或實體伺服器)。 它會擷取在電腦上寫入的資料，並將它們轉送到處理序伺服器。
* **Azure**：您不需要建立任何 Azure VM 來處理複寫及容錯移轉。 Site Recovery 服務會處理資料管理，且資料會直接複寫至 Azure 儲存體。 複寫的 Azure VM 只會在容錯移轉至 Azure 時自動啟動。 不過，如果您想要從 Azure 容錯回復至內部部署網站，您必須設定 Azure VM 做為處理序伺服器。

下列圖表 (由 Henry Robalino 建立) 顯示這些元件的互動情形︰

![元件架構](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

## <a name="capacity-planning"></a>容量規劃
當您規劃容量時，以下是需要考慮的項目：

* **來源環境**：容量規劃或 VMware 基礎結構和來源機器需求。
* **管理伺服器**：針對執行 Site Recovery 元件的內部部署管理伺服器做規劃。
* **從來源到目標的網路頻寬**：針對來源與 Azure 之間複寫所需的網路頻寬做規劃

### <a name="source-environment-considerations"></a>來源環境考量
* **每日變更率上限**︰受保護的機器只能使用一個處理序伺服器。 單一處理序伺服器每天最多可處理 2 TB 的資料變更。 因此，2 TB 是針對受保護機器支援的每日資料變更率上限。
* **最大輸送量**：複寫的機器可以屬於 Azure 中的一個儲存體帳戶。 標準儲存體帳戶每秒可處理最多 20000 個要求，建議您將來源機器的 IOPS 數保持為 20000。 例如，如果您有一部具備 5 個磁碟的來源機器，並且在來源上的每個磁碟會產生 120 個 IOP (8 KB 大小)，則它會在 Azure 每個磁碟 IOPS 限制 500 之內。 儲存體帳戶所需的數目 = 來源 IOP 總計/20,000。

### <a name="management-server-considerations"></a>管理伺服器考量
管理伺服器會執行 Site Recovery 元件，這些元件會處理資料最佳化、複寫及管理。 它應該能夠處理受保護機器上執行的所有工作負載的每日變更率容量，並且有足夠頻寬可以持續將資料複寫到 Azure 儲存體。 具體而言：

* 處理序伺服器會從受保護的機器接收複寫資料，以快取最佳化、壓縮，並且在傳送至 Azure 之前加密。 管理伺服器應該有足夠的資源來執行這些工作。
* 處理伺服器使用磁碟快取。 我們建議每個快取磁碟有 600 GB 以上的空間，以處理發生網路瓶頸或中斷時儲存的資料變更。 在部署期間，您可以在具有至少 5 GB 的可用儲存體的任何磁碟機上設定快取，但是 600 GB 是最低的建議。
* 我們建議的最佳做法是管理伺服器位於與您想要保護的機器的相同網路與 LAN 區段上。 它可以位於不同的網路，但是您想要保護的機器應該具有 L3 網路可見性。

下表摘要說明管理伺服器的大小建議：

| **管理伺服器 CPU** | **記憶體** | **快取磁碟大小** | **資料變更率** | **受保護的機器** |
| --- | --- | --- | --- | --- |
| 8 個 vCPU (2 個插槽 * 4 核心 @ 2.5GHz) |16 GB |300 GB |500 GB 或更少 |以這些設定部署管理伺服器，以複寫少於 100 部的機器。 |
| 12 個 vCPU (2 個插槽 * 6 核心 @ 2.5GHz) |18 GB |600 GB |500 GB 至 1 TB |以這些設定部署管理伺服器，以複寫少於 100-150 部的機器。 |
| 16 個 vCPU (2 個插槽 * 8 核心 @ 2.5GHz) |32 GB |1 TB |1 TB 至 2 TB |以這些設定部署管理伺服器，以複寫少於 150-200 部的機器。 |
| 部署另一個處理序伺服器 | | |> 2 TB |如果您要複寫 200 部以上的機器，或如果每日資料變更率超過 2 TB，部署額外的處理序伺服器。 |

其中：

* 每個來源機器已設定各 100 GB 的 3 個磁碟。
* 我們使用具有 RAID 10 的 8 個 10,000 RPM 的 SAS 磁碟機的效能評定儲存體以進行快取磁碟度量。

### <a name="network-bandwidth-from-source-to-target"></a>從來源到目標的網路頻寬
請確定您是使用[容量規劃工具](site-recovery-capacity-planner.md)來計算初始複寫和差異複寫所需的頻寬。

#### <a name="throttling-bandwidth-used-for-replication"></a>用於複寫的節流頻寬
複寫到 Azure 的 VMware 流量會通過特定的處理序伺服器。 您可以節流可用於該伺服器上 Site Recovery 複寫的頻寬，如下所示：

1. 開啟主要管理伺服器上或執行其他佈建處理序伺服器的管理伺服器上的 Microsoft Azure 備份 MMC 嵌入式管理單元。 根據預設，桌面上會建立 Microsoft Azure 備份的捷徑。 您也可以在 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中找到它。
2. 在嵌入式管理單元中，按一下 [變更屬性]。

    ![節流頻寬變更屬性](./media/site-recovery-vmware-to-azure-classic/throttle1.png)
3. 在 [節流] 索引標籤中，指定可用於 Site Recovery 複寫的頻寬，以及適用的排程。

    ![節流頻寬複寫](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

您也可以使用 PowerShell 設定節流。 以下是範例：

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>最大化頻寬使用量
若要增加用於 Azure Site Recovery 複寫的頻寬，您必須變更登錄機碼。

下列機碼可控制複寫時每個複寫磁碟使用的執行緒數目：

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 在過度佈建的網路中，必須變更此登錄機碼的預設值。 我們支援最多 32 個。  

如需容量規劃的詳細資訊，請參閱 [Site Recovery Capacity Planner](site-recovery-capacity-planner.md)。

### <a name="additional-process-servers"></a>額外處理序伺服器
如果您要保護超過 200 部機器，或每日變更率大於 2 TB，您可以新增額外的伺服器以處理負載。 若要擴充，您可以：

* 增加管理伺服器的數目。 例如，您可以使用兩個管理伺服器保護最多 400 部機器。
* 新增額外的處理序伺服器和使用這些項目來處理流量，而不是管理伺服器。

下表描述案例，其中：

* 您設定原始管理伺服器僅做為組態伺服器。
* 您設定額外的處理序伺服器。
* 您設定受保護的虛擬機器以使用額外的處理序伺服器。
* 每個受保護的來源機器已設定各 100 GB 的 3 個磁碟。

| **原始管理伺服器**<br/><br/>(組態伺服器) | **額外處理序伺服器** | **快取磁碟大小** | **資料變更率** | **受保護的機器** |
| --- | --- | --- | --- | --- |
| 8 個 vCPU (2 個插槽 * 4 核心 @ 2.5GHz)，16 GB RAM |4 個 vCPU (2 個插槽 * 2 核心 @ 2.5GHz)，8 GB RAM |300 GB |250 GB 或更少 |您可以複寫 85 部或更少的機器。 |
| 8 個 vCPU (2 個插槽 * 4 核心 @ 2.5GHz)，16 GB RAM |8 個 vCPU (2 個插槽 * 4 核心 @ 2.5GHz)，12 GB RAM |600 GB |250 GB 至 1 TB |您可以複寫 85-150 部機器。 |
| 12 個 vCPU (2 個插槽 * 6 核心 @ 2.5GHz)，18 GB RAM |12 個 vCPU (2 個插槽 * 6 核心 @ 2.5GHz)，24 GB RAM |1 TB |1 TB 至 2 TB |您可以複寫 150-225 部機器。 |

如何調整伺服器取決於相應增加或相應放大模型的喜好設定。 您部署幾個高階管理和處理序伺服器以相應增加，或使用較少的資源部署更多伺服器以相應放大。 例如，如果您需要保護 220 部機器，您可以執行下列任一項：

* 設定具有 12 個 vCPU 和 18 GB RAM 的原始管理伺服器。 設定具有 12 個 vCPU 和 24 GB RAM 的額外處理序伺服器。 將受保護機器設定為只使用額外的處理序伺服器。
* 設定兩部管理伺服器 (2 x 8 個 vCPU，16 GB RAM) 和兩個部額外的處理序伺服器 (1 x 8 個 vCPU 和 4vCPUs x 1，以處理 135 + 85 (220) 部機器)。 將受保護機器設定為只使用額外的處理序伺服器。

依照[部署額外處理序伺服器](#deploy-additional-process-servers)中的指示，設定額外的處理序伺服器。

## <a name="before-you-start-deployment"></a>開始部署之前
下列資料表摘要說明部署此案例的必要條件。

### <a name="azure-prerequisites"></a>Azure 必要條件
| **必要條件** | **詳細資料** |
| --- | --- |
| **Azure 帳戶** |您需要 [Microsoft Azure](https://azure.microsoft.com/) 帳戶。 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 如需 Site Recovery 價格的詳細資訊，請參閱 [Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)。 |
| **Azure 儲存體** |您需要 Azure 儲存體帳戶來儲存複寫的資料。 複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。 <br/><br/>您需要一個[標準異地備援儲存體帳戶](../storage/storage-redundancy.md#geo-redundant-storage)。 此帳戶應與 Site Recovery 服務位於相同的區域，且與相同的訂用帳戶相關聯。 目前不支援複寫到進階儲存體帳戶，請勿使用。<br/><br/>我們不支援在資源群組之間移動使用 [Azure 入口網站](../storage/storage-create-storage-account.md)所建立的儲存體帳戶。 如需詳細資訊，請參閱 [Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)。<br/><br/> |
| **Azure 網路** |容錯移轉發生時，您需要 Azure VM 會連線的 Azure 虛擬網路。 Azure 虛擬網路必須位於與 Site Recovery 保存庫相同的區域中。<br/><br/>若要在容錯移轉之後容錯回復至 Azure，您需要設定從 Azure 網路到內部部署網站的 VPN 連線 (或 Azure ExpressRoute)。 |

### <a name="on-premises-prerequisites"></a>內部部署必要條件
| **必要條件** | **詳細資料** |
| --- | --- |
| **管理伺服器** |您需要在虛擬機器或實體伺服器上執行的內部部署 Windows 2012 R2 伺服器。 所有的內部部署 Site Recovery 元件都會安裝在此管理伺服器上。<br/><br/> 我們建議您將伺服器部署為高可用的 VMware VM。 從 Azure 容錯回復到內部部署網站時一律是容錯回復到 VMware VM，無論您是容錯移轉 VM 還是實體伺服器。 如果您不將管理伺服器設定為 VMware VM，則必須設定個別的主要目標伺服器來做為 VMware VM，以接收容錯回復流量。<br/><br/>該伺服器不能是網域控制站。<br/><br/>該伺服器必須具有靜態 IP 位址。<br/><br/>伺服器的主機名稱必須少於或等於 15 個字元。<br/><br/>作業系統地區設定只能是英文版。<br/><br/>管理伺服器需要網際網路存取。<br/><br/>您需要下列來自伺服器的輸出存取：於 Site Recovery 元件設定期間需要在 HTTP 80 上取得暫時存取 (以下載 MySQL)、需要在 HTTPS 443 上針對複寫管理取得持續的輸出存取、需要在 HTTPS 9443 上針對複寫流量取得持續的輸出存取 (可修改此連接埠)。<br/><br/> 確定可以從管理伺服器存取這些 URL： <br/>- \*.hypervrecoverymanager.windowsazure.com<br/>- \*.accesscontrol.windows.net<br/>- \*.backup.windowsazure.com<br/>- \*.blob.core.windows.net<br/>- \*.store.core.windows.net<br/>- https://www.msftncsi.com/ncsi.txt<br/>- [ https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>如果您在伺服器上有以 IP 位址為基礎的防火牆規則，請檢查這些規則以允許對 Azure 的通訊。 您需要允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)和 HTTPS (443) 連接埠。 您也必須將訂用帳戶的 Azure 區域和美國西部的 IP 位址範圍列於允許清單。 [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") 是用來下載 MySQL 的 URL。 |
| **VMware vCenter/ESXi 主機** |您需要一或多個 VMware vSphere ESX/ESXi Hypervisor 來管理您 VMware 虛擬機器，這些 Hypervisor 需執行具有最新更新項目的 6.0、5.5 或 5.1 版 ESX/ESXi。<br/><br/> 建議您部署 VMware vCenter 伺服器來管理您的 ESXi 主機。 它應該執行 vCenter 6.0 或 5.5 版 (含最新更新)。<br/><br/>請注意，Site Recovery 不支援新的 vCenter 和 vSphere 6.0 功能，例如跨 vCenter vMotion、虛擬磁碟區和儲存體 DRS。 Site Recovery 支援僅限於 5.5 版中可用的功能。 |
| **受保護的機器** |**Azure**<br/><br/>您想要保護的機器應該要符合建立 Azure VM 的 [Azure 必要條件](site-recovery-prereq.md)。<br><br/>如果您想要在容錯移轉之後連線至 Azure VM，您必須在本機防火牆上啟用遠端桌面連線。<br/><br/>受保護機器上的個別磁碟容量不可超過 1023 GB。 VM 可以有多達 64 個磁碟 (因此多達 64 TB)。 如果您有容量大於 1 TB 的磁碟，請考慮使用資料庫複寫，例如 SQL Server Always On 或 Oracle Data Guard。<br/><br/>安裝磁碟機上必須至少有 2 GB 的可用空間來進行元件安裝。<br/><br/>不支援共用磁碟客體叢集。 如果您有叢集部署，請考慮使用資料庫複寫，例如 SQL Server Always On 或 Oracle Data Guard。<br/><br/>不支援整合可延伸韌體介面 (UEFI)/可延伸韌體介面 (EFI) 開機。<br/><br/>機器名稱應包含介於 1 到 63 個字元 (字母、數字和連字號)。 名稱必須以字母或數字開頭，並以字母或數字結尾。 機器受到保護之後，您可以修改 Azure 的名稱。<br/><br/>**VMware VM**<br/><br>您需要安裝 VMware vSphere PowerCLI 6.0。 管理伺服器上 (組態伺服器)。<br/><br/>您想要保護的 VMware VM 應該安裝並執行 VMware 工具。<br/><br/>如果來源 VM 具有 NIC 小組，它將會在容錯移轉至 Azure 後轉換成單一 NIC。<br/><br/>如果受保護的 VM 具有 iSCSI 磁碟，Site Recovery 會在 VM 容錯移轉至 Azure 後將受保護的 VM iSCSI 磁碟轉換成 VHD 檔案。 如果 Azure VM 可以觸達 iSCSI 目標，則它會連線到 iSCSI 目標，並且基本上查看兩個磁碟 – Azure VM 上的 VHD 磁碟和來源 iSCSI 磁碟。 在此情況下，您必須將出現在容錯移轉的 Azure VM 上的 iSCSI 目標中斷連線。<br/><br/>如需 Site Recovery 需要的 VMware 使用者權限的詳細資訊，請參閱 [vCenter 存取的 VMware 權限](#vmware-permissions-for-vcenter-access)。<br/><br/> **Windows Server 機器 (位於 VMware VM 或實體伺服器上)**<br/><br/>伺服器應該執行受支援的 64 位元作業系統：Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 (至少為 SP1)。<br/><br/>作業系統應安裝在 C 磁碟機上，OS 磁碟應該是 Windows 基本磁碟  (OS 應該安裝在 Windows 動態磁碟上)。<br/><br/>針對 Windows Server 2008 R2 機器，您需要安裝 .NET Framework 3.5.1。<br/><br/>您必須提供系統管理員帳戶 (必須是 Windows 機器上的本機系統管理員)，以在 Windows 伺服器上推入安裝行動服務。 如果提供的帳戶是非網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。 如需詳細資訊，請參閱[使用推入安裝來安裝行動服務](#install-the-mobility-service-with-push-installation)。<br/><br/>Site Recovery 支援具有 RDM 磁碟的 VM。 在容錯回復期間，如果原始來源 VM 和 RDM 磁碟可用，則 Site Recovery 會重複使用 RDM 磁碟。 如果它們都無法使用，在容錯回復期間，Site Recovery 會為每個磁碟建立新的 VMDK 檔案。<br/><br/>**Linux 機器**<br/><br/>您需要受支援的 64 位元作業系統：Red Hat Enterprise Linux 6.7；Centos 6.5、6.6 或 6.7；Oracle Enterprise Linux 6.4 或 6.5 (執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3))；SUSE Linux Enterprise Server 11 SP3。<br/><br/>受保護機器上的 /etc/hosts 檔案應該包含將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。 <br/><br/>如果您想要在容錯轉移之後使用安全殼層用戶端 (ssh) 連線到執行 Linux 的 Azure 虛擬機器，請確認受保護機器上的安全殼層服務已設為在系統開機時自動啟動，且防火牆規則允許 ssh 與其連線。<br/><br/>保護只會針對具有下列儲存體的 Linux 機器啟用：檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。 不支援使用 HP CCISS 控制站儲存體的實體伺服器。 只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。<br/><br/>Site Recovery 支援具有 RDM 磁碟的 VM。 在 Linux 的容錯回復期間，Site Recovery 不會重複使用 RDM 磁碟。 而是會針對每個對應的 RDM 磁碟建立新的 VMDK 檔案。 |

僅適用於 Linux VM：確定您在 VMware 中將 VM 的組態參數設定設為 disk.enableUUID=true。 如果該資料列不存在，請新增一個。 這樣才能提供一致的 UUID 給 VMDK，使其可正確掛接。 如果沒有此設定，即使 VM 位於內部部署，容錯回復仍會造成完整的下載。 新增此設定會確保只有差異變更才會在容錯回復期間傳送回來。

## <a name="step-1-create-a-vault"></a>步驟 1：建立保存庫
1. 登入 [Azure 入口網站](https://manage.windowsazure.com/)。
2. 展開 [資料服務]  >  [復原服務]，然後按一下 [Site Recovery 保存庫]。
3. 按一下 [新建]  >  [快速建立]。
4. 在 [ **名稱**] 中，輸入保存庫的易記識別名稱。
5. 在 [地區] 中，選取保存庫的地理區域。 若要查看支援的地區，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 按一下 [建立保存庫] 。
    ![建立保存庫](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

檢查狀態列，以確認是否順利建立保存庫。 保存庫在主要 [復原服務] 頁面上會列為 [使用中]。

## <a name="step-2-set-up-an-azure-network"></a>步驟 2：設定 Azure 網路
設定 Azure 網路，讓 Azure VM 在容錯移轉之後連接到網路，以便容錯回復到內部部署網站可以正常運作。

1. 在 Azure 入口網站中，選取 [建立虛擬網路]，並指定網路名稱、IP 位址範圍和子網路名稱。
2. 如果您需要進行容錯回復，請將 VPN/ExpressRoute 新增至網路。 即使在容錯移轉之後，VPN/ExpressRoute 也可以新增至網路。

如需 Azure 網路的詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

> [!NOTE]
> 對於用於部署 Site Recovery 的網路，不支援跨相同訂用帳戶內的資源群組或跨訂用帳戶[移轉網路](../azure-resource-manager/resource-group-move-resources.md)。
>
>

## <a name="step-3-install-the-vmware-components"></a>步驟 3：安裝 VMware 元件
如果您想要複寫 VMware 虛擬機器，請在管理伺服器上遵循下列步驟：

1. [下載](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) 並安裝 VMware vSphere PowerCLI 6.0。
2. 重新啟動伺服器。

## <a name="step-4-download-a-vault-registration-key"></a>步驟 4：下載保存庫註冊金鑰
1. 從管理伺服器，在 Azure 中開啟 Site Recovery 主控台。 在 [復原服務] 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。 您也可以隨時可以按一下此圖示，開啟 [快速啟動] 頁面。

    ![快速啟動圖示](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)
2. 在 [快速啟動] 頁面中，按一下 [準備目標資源]  > [下載註冊金鑰]。 註冊檔案會隨即自動產生。 該金鑰在產生後會維持五天有效。

## <a name="step-5-install-the-management-server"></a>步驟 5：安裝管理伺服器
> [!TIP]
> 確定可以從管理伺服器存取這些 URL：
>
> * *.hypervrecoverymanager.windowsazure.com
> * *.accesscontrol.windows.net
> * *.backup.windowsazure.com
> * *.blob.core.windows.net
> * *.store.core.windows.net
> * https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
> * https://www.msftncsi.com/ncsi.txt
>
>



>[!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Setup-Registration/player]



1. 在 [快速啟動] 頁面中，將整合安裝檔案下載至伺服器。
2. 執行安裝檔案以在 **Site Recovery 整合安裝**精靈啟動安裝程式。
3. 在 [開始之前] 選取 [安裝組態伺服器和處理序伺服器]。

   ![開始之前](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. 在 [協力廠商軟體授權] 中，按一下 [我接受] 來下載並安裝 MySQL。

    ![協力廠商軟體](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)
5. 在 [註冊] 中，瀏覽並選取您從保存庫下載的註冊金鑰。

    ![註冊](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)
6. 在 [網際網路設定] 中，指定在組態伺服器上執行的 Provider 將如何透過網際網路連線到 Azure Site Recovery。

   * 如果您想要使用機器上目前設定的 Proxy 來連線，請選取 [以現有的 Proxy 設定連線]。
   * 如果您想要讓提供者直接連線，請選取 [不使用 Proxy 直接連線]。
   * 如果現有的 Proxy 需要驗證，或是您想要讓提供者使用自訂 Proxy 來連線，請選取 [以自訂 Proxy 設定連線]。
     * 如果您使用自訂 proxy，您必須指定位址、連接埠以及認證。
     * 如果您使用 Proxy，您應該已經允許下列 URL：
       * *.hypervrecoverymanager.windowsazure.com    
       * *.accesscontrol.windows.net
       * *.backup.windowsazure.com
       * *.blob.core.windows.net
       * *.store.core.windows.net

    ![防火牆](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

1. 在 [必要條件檢查] 中，安裝程式會執行檢查來確定可以執行安裝。

    ![必要條件](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     如果出現有關「通用時間同步處理檢查」的警告，請確認系統時鐘上的時間 ([日期和時間] 設定) 與時區相同。

     ![時間同步問題](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

1. 在 [MySQL 組態] 中，建立認證來登入將要安裝的 MySQL 伺服器執行個體。

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)
2. 在 [環境詳細資料] 中，選取您是否要複寫 VMware VM。 如果是的話，安裝程式會檢查是否已安裝 PowerCLI 6.0。

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)
3. 在 [安裝位置] 中，選取您要安裝二進位檔及儲存快取的位置。 您可以選取至少有 5 GB 可用磁碟空間的磁碟機，但我們建議快取磁碟機至少有 600 GB 的可用空間。

   ![安裝位置](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)
4. 在 [網路選取] 中，指定組態伺服器將用來傳送和接收複寫資料的接聽程式 (網路介面卡和 SSL 連接埠)。 您可以修改預設連接埠 (9443)。 除了此連接埠之外，網頁伺服器將會使用連接埠 443 來協調複寫作業。 請勿使用 443 來接收複寫流量。

    ![網路選擇](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



1. 在 [摘要] 中檢閱資訊，然後按一下 [安裝]。 安裝完成時，會產生複雜密碼。 在您啟用複寫時會需要它，所以請將它複製並保存在安全的位置。

   ![摘要](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)


> [!WARNING]
> 必須安裝 Microsoft Azure 復原服務代理程式 Proxy。
> 安裝完成之後，從 Windows [開始] 功能表啟動 Microsoft Azure 復原服務殼層。 在開啟的命令視窗中，執行下列命令組來設定 Proxy 伺服器設定。
>
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
>


### <a name="run-setup-from-the-command-line"></a>從命令列執行安裝程式
您也可以從命令列執行整合精靈，如下所示：

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

其中：

* / ServerMode：必要。 指定安裝是否應該安裝組態和處理序伺服器或僅安裝處理序伺服器 (用來安裝額外的處理序伺服器)。 輸入值：CS、PS
* InstallDrive：必要。 指定要安裝元件的資料夾。
* /MySQLCredFilePath。 必要。 指定儲存 MySQL 伺服器認證的檔案的路徑。 取得範本以建立檔案。
* /VaultCredFilePath。 必要。 保存庫認證檔的位置。
* /EnvType。 必要。 安裝類型。 值：VMware、NonVMware。
* /PSIP 和 /CSIP。 必要。 處理序伺服器和組態伺服器 IP 位址。
* /PassphraseFilePath。 必要。 複雜密碼檔案的位置。
* /ByPassProxy。 選用。 指定不使用 Proxy 而連線至 Azure 的管理伺服器。
* /ProxySettingsFilePath。 選用。 指定自訂 Proxy 的設定 (伺服器上需要驗證的預設 Proxy，或是自訂的 Proxy)。

## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>步驟 6：設定 vCenter 伺服器的認證
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Discovery/player]
>
>

處理序伺服器可以自動探索 vCenter 伺服器所管理的 VMware VM。 對於自動探索，Site Recovery 需要可以存取 vCenter 伺服器的帳戶和認證。 如果您只是複寫實體伺服器，則這無關緊要。

若要設定帳戶和認證︰

1. 在 vCenter 伺服器上，建立擁有[必要權限](#vmware-permissions-for-vcenter-access)的 vCenter 層級角色 (**Azure_Site_Recovery**)。
2. 將 **Azure_Site_Recovery** 角色指派給 vCenter 使用者。

   > [!NOTE]
   > VCenter 使用者帳戶具有唯讀角色，可以執行容錯移轉，而不需要關閉受保護的來源機器。 如果您想要關閉這些機器，您需要 Azure_Site_Recovery 角色。 如果您只是從 VMware 將 VM 移轉至 Azure，且不需要容錯回復，則唯讀角色已足夠。
   >
   >
3. 若要新增帳戶，請開啟 **cspsconfigtool**。 它會在桌面上的捷徑，位於 [安裝位置]\home\svsystems\bin 資料夾。
4. 在 [管理帳戶] 索引標籤中，按一下 [新增帳戶]。

    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic/credentials1.png)
5. 在 [帳戶詳細資料] 中，新增可用來存取 vCenter 伺服器的認證。 可能要超過 15 分鐘，帳戶名稱才會出現在入口網站。 若要立即更新，請按一下 [組態伺服器] 索引標籤上的 [重新整理]。

    ![詳細資料](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>步驟 7：新增 vCenter 伺服器和 ESXi 主機
如果您是複寫 VMware VM，則需要新增 vCenter 伺服器 (或 ESXi 主機)。

1. 在 [伺服器] > [組態伺服器] 索引標籤上，選取 [新增 vCenter 伺服器]。

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)
2. 新增 vCenter 伺服器或 ESXi 主機詳細資料、您在上一個步驟中指定來存取 vCenter 伺服器的帳戶的名稱，以及將用來探索由 vCenter 伺服器管理的 VMware VM 的處理序伺服器。 vCenter 伺服器或 ESXi 主機應位於與安裝處理序伺服器所在的伺服器相同的網路中。

   > [!NOTE]
   > 如果您要以沒有 vCenter 或主機伺服器的系統管理員權限的帳戶新增 vCenter 伺服器或 ESXi 主機，請確認 vCenter 或 ESXi 帳戶已啟用這些權限：資料中心、資料存放區、資料夾、Jost、網路、資源、虛擬機器，vSphere 分散式切換。 vCenter 伺服器需要已啟用儲存體檢視權限。
   >
   >

    ![新增 vCenter 伺服器](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)
3. 探索完成之後，vCenter 伺服器將會列在 [組態伺服器] 索引標籤上。

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)

## <a name="step-8-create-a-protection-group"></a>步驟 8：建立保護群組
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Protection/player]
>
>

保護群組是虛擬機器或實體伺服器的邏輯群組，您想要使用相同的保護設定來保護這類群組。 您只要將保護設定套用至保護群組，這些設定就會套用至您新增至該群組中的所有機器 (虛擬或實體)。

1. 移至 [受保護的項目] > [保護群組]，然後按一下圖示以新增保護群組。

    ![建立保護群組](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)
2. 在 [指定保護群組設定] 頁面上，指定群組的名稱。 在 [從] 下拉式清單中，選取您要建立群組的組態伺服器。 [目標] 是 Microsoft Azure。

    ![保護群組設定](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)
3. 在 [指定複寫設定] 頁面上，設定將用於群組中所有機器的複寫設定。

    ![保護群組複寫](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

   * **多部 VM 一致性**：如果您開啟這項功能，則會在保護群組中的機器之間，建立共用的應用程式一致復原點。 當保護群組中的所有機器都執行相同的工作負載時，這項設定就可以發揮最高的重要性。 所有機器都將復原到相同的資料點。 無論您是複寫 VMware VM 或實體伺服器 (Windows/Linux)，這都可以使用。
   * **RPO 臨界值**：設定 RPO。 當連續資料保護複寫超過設定的 RPO 臨界值時，就會產生警示。
   * **復原點保留期**：指定保留週期。 受保護的機器可以復原到這個週期內的任意點。
   * **應用程式一致的快照頻率**：指定每隔多久建立包含應用程式一致快照集的復原點。

選取此核取記號會以您指定的名稱建立保護群組。 此外，會以名稱 *protection-group-name*-Failback 建立第二個保護群組。 如果您在容錯移轉至 Azure 之後容錯回復到內部部署網站，會使用此保護群組。 您可以監視保護群組，因為它們是在 [受保護的項目]  頁面上建立的。

## <a name="step-9-install-the-mobility-service"></a>步驟 9：安裝行動服務
啟用虛擬機器和實體伺服器保護的第一個步驟是安裝行動服務。 您可以使用兩種方式執行此動作：

* 從處理序伺服器自動推入並在每一部機器上安裝服務。 當您將機器新增至已在執行適當版本行動服務的保護群組時，就不會發生推入安裝。 您也可以使用企業推入方法，例如 WSUS 或 System Center Configuration Manager，以自動安裝服務。 請確定您在執行這項操作之前已設定管理伺服器。
* 在您想要保護的每部機器上手動安裝服務。 請確定您在執行這項操作之前已設定管理伺服器。

### <a name="install-the-mobility-service-with-push-installation"></a>使用推入安裝來安裝行動服務
當您將機器加入保護群組時，行動服務會自動由處理序伺服器推入並安裝於每部機器上。

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>準備在 Windows 機器上自動推入
準備 Windows 機器，讓處理序伺服器可以自動安裝行動服務：

1. 建立可供處理序伺服器存取機器的帳戶。 帳戶應該具有系統管理員權限 (本機或網域)。 這些認證只用於行動服務的推入安裝。

   > [!NOTE]
   > 如果您未使用網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。 若要執行此動作，請在登錄的 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下，新增 DWORD 登錄 LocalAccountTokenFilterPolicy，值為 1 以下。 若要透過 CLI 開啟命令或使用 PowerShell 來新增登錄項目，請輸入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。
   >
   >
2. 對於您想要保護之機器的 Windows 防火牆，選取 [允許應用程式或功能通過防火牆]，並啟用 [檔案及印表機共用] 和 [Windows Management Instrumentation]。 針對隸屬於網域中的機器，您可以利用 GPO 設定防火牆原則。

   ![防火牆設定](./media/site-recovery-vmware-to-azure-classic/mobility1.png)
3. 新增您所建立的帳戶：

   * 開啟 **cspsconfigtool**。 它會在桌面上的捷徑，位於 [安裝位置]\home\svsystems\bin 資料夾。
   * 在 [管理帳戶] 索引標籤中，按一下 [新增帳戶]。
   * 新增您所建立的帳戶。 新增帳戶之後，當您將機器加入保護群組時，您必須提供這些認證。

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>準備在 Linux 伺服器上自動推入
1. 確定您要保護的 Linux 機器受到支援，如 [內部部署必要條件](#on-premises-prerequisites)中所述。 請確保您想要保護的電腦與執行處理序伺服器的管理伺服器之間的網路連線能力。
2. 建立可供處理序伺服器存取機器的帳戶。 帳戶應該是來源 Linux 伺服器上的根使用者。 這些認證只用於行動服務的推入安裝。

   * 開啟 **cspsconfigtool**。 它會在桌面上的捷徑，位於 [安裝位置]\home\svsystems\bin 資料夾。
   * 在 [管理帳戶] 索引標籤中，按一下 [新增帳戶]。
   * 新增您所建立的帳戶。 新增帳戶之後，當您將機器加入保護群組時，您必須提供這些認證。
3. 檢查來源 Linux 伺服器上的 /etc/hosts 檔案包含將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。
4. 在您想要保護的電腦上安裝最新的 openssh、openssh-server、openssl 套件。
5. 請確定 SSH 已啟用且正在連接埠 22 上執行。
6. 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證，如下所示：

   * 以 root 的身分登入。
   * 在 /etc/ssh/sshd_config 檔案中，尋找以 PasswordAuthentication 開頭的行。
   * 取消該行的註解並將值從 **no** 變更為 **yes**。
   * 尋找以 **Subsystem** 為開頭的行並取消其註解。

     ![沒有子系統的 Linux 覆寫預設值](./media/site-recovery-vmware-to-azure-classic/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>手動安裝行動服務
安裝程式位於 C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository。

| 來源作業系統 | 行動服務安裝檔案 |
| --- | --- |
| Windows Server (僅限&64; 位元) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4、6.5、6.6 (僅限 64 位元) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (僅限 64 位元) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-the-mobility-service-manually-on-a-windows-server"></a>在 Windows 伺服器上手動安裝行動服務
1. 下載並執行相關安裝程式。
2. 在 [開始之前] 中，選取 [行動服務]。

    ![行動服務安裝](./media/site-recovery-vmware-to-azure-classic/mobility3.png)
3. 在 [組態伺服器詳細資料] 中，指定管理伺服器的 IP 位址，以及您安裝管理伺服器元件時所產生的複雜密碼。 您可以在管理伺服器上執行 **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n**，以取得複雜密碼。

    ![行動服務](./media/site-recovery-vmware-to-azure-classic/mobility6.png)
4. 在 [安裝位置] 中，保留預設位置，然後按 [下一步] 開始安裝。
5. 在 [安裝進度] 中檢查安裝，並在系統提示時重新啟動機器。

您也可以在命令列輸入下列文字來安裝︰

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]

在上述命令中︰

* /Role：必要。 指定是否應該要安裝行動服務。
* /InstallLocation：必要。 指定安裝服務的位置。
* /PassphraseFilePath：必要。 指定組態伺服器複雜密碼。
* /LogFilePath：必要。 指定記錄安裝檔案位置。

#### <a name="uninstall-the-mobility-service-manually"></a>手動解除安裝行動服務
您可以使用控制台的 [解除安裝或變更程式]，或使用命令列，以解除安裝行動服務。

使用命令列解除安裝行動服務的命令是：

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="change-the-ip-address-of-the-management-server"></a>變更管理伺服器的 IP 位址
在執行精靈之後，您可以變更管理伺服器的 IP 位址，如下所示：

1. 開啟檔案 hostconfig.exe (位於桌面上)。
2. 在 [全域] 索引標籤中，您可以變更管理伺服器的 IP 位址。

   > [!NOTE]
   > 只變更管理伺服器的 IP 位址。 管理伺服器通訊的連接埠號碼必須是 443，且 [使用 HTTPS] 必須保持啟用。 請勿變更複雜密碼。
   >
   >

    ![管理伺服器 IP 位址](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-the-mobility-service-manually-on-a-linux-server"></a>在 Linux 伺服器上手動安裝行動服務
1. 將適當的 tar 封存檔複製到您要保護的 Linux 機器。 請參閱[手動安裝行動服務](#install-the-mobility-service-manually)下的資料表，以決定應該使用的 tar 封存檔。
2. 執行下列命令來開啟殼層程式，並將壓縮的 tar 封存檔解壓縮到本機路徑： `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. 在您解壓縮 tar 封存檔內容的本機目錄中，建立名為 passphrase.txt 的檔案。 若要執行這項操作，請在管理伺服器上從 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 複製複雜密碼，然後在殼層中執行 *`echo <passphrase> >passphrase.txt`*，將它儲存在 passphrase.txt 中。
4. 輸入下列命令來安裝行動服務：*`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*
5. 指定管理伺服器的內部 IP 位址，並確定已選取連接埠 443。

#### <a name="install-the-mobility-service-from-the-command-line"></a>從命令列安裝行動服務

在管理伺服器上從 C:\Program Files (x86)\InMage Systems\private\connectio 複製複雜密碼，並且在管理伺服器上將它儲存為 "passphrase.txt"。 然後執行下列命令。 在我們的範例中，管理伺服器的 IP 位址是 104.40.75.37，HTTPS 連接埠是 443：

若要在實際執行伺服器上安裝：

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

若要在主要目標伺服器上安裝：

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>步驟 10：對機器啟用保護
若要啟用保護，請將虛擬機器和實體伺服器新增至保護群組。 在開始之前，如果您要保護 VMware 虛擬機器，請注意下列事項：

* 系統會每隔 15 分鐘探索 VMware VM 一次，而且在探索之後，可能需要 15 分鐘以上，VMware VM 才會出現在 Site Recovery 入口網站中。
* 虛擬機器上的環境變更 (例如 VMware 工具安裝) 也可能需要超過 15 分鐘的時間，才能在 Site Recovery 中更新。
* 您可以在 [組態伺服器] 索引標籤上 vCenter 伺服器/ESXi 主機的 [上次連絡時間] 欄位中，查看 VMware VM 的上次探索時間。
* 如果您在建立保護群組之後新增 vCenter 伺服器或 ESXi 主機，可能需要 15 分鐘以上，Azure Site Recovery 入口網站才會重新整理，[將機器新增至保護群組] 對話方塊中也才會列出虛擬機器。
* 如果您想要立即繼續將機器新增至保護群組，而不想等候排程的探索，請醒目提示組態伺服器 (不要按它)，然後按一下 [重新整理]。

此外：

* 建議您架構保護群組，使其反映您的工作負載。 例如，將執行特定應用程式的機器新增至相同群組。
* 當您將機器加入至保護群組時，如果尚未安裝行動服務，則處理序伺服器會自動推入並安裝行動服務。 您必須備妥上一個步驟中所述的推入機制。

### <a name="add-machines-to-a-protection-group"></a>將機器加入保護群組

1. 移至 [受保護的項目] > [保護群組]  > [機器] > [新增機器]。
2. 如果您要保護 VMware 虛擬機器，請在 [選取虛擬機器] 中，選取負責管理虛擬機器的 vCenter 伺服器 (或執行這些機器的 EXSi 主機)，然後選取機器。

    ![對虛擬機器啟用保護](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)
3. 如果您要保護實體伺服器，請在 [選取虛擬機器] 中，開啟 [新增實體機器] 精靈，並提供 IP 位址和易記名稱。 然後選取作業系統系列。

   ![啟用保護實體伺服器](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)
4. 在 [指定目標資源] 中，選取要用於複寫的儲存體帳戶，並選取是否應將設定用於所有工作負載。 目前不支援進階儲存體帳戶。

   > [!NOTE]
   > 我們不支援在資源群組之間移動使用 [Azure 入口網站](../storage/storage-create-storage-account.md)所建立的儲存體帳戶。                           
   > 對於用於部署 Site Recovery 的儲存體帳戶，不支援跨相同訂用帳戶內的資源群組或跨訂用帳戶[移轉儲存體帳戶](../azure-resource-manager/resource-group-move-resources.md)。
   >
   >

    ![進行目標設定](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)
5. 在 [指定帳戶] 中，選取您[已設定](#install-the-mobility-service-with-push-installation)用來自動安裝行動服務的帳戶。

    ![指定帳戶](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)
6. 按一下核取記號，以完成將機器加入保護群組，並啟動每部機器的初始複寫。

   > [!NOTE]
   > 如果已備妥推入安裝，則行動服務會在機器加入至保護群組時自動安裝在沒有該服務的機器上。 服務安裝之後，保護作業會開始，並且會失敗。 在失敗之後，您必須手動重新啟動已安裝行動服務的每一部機器。 重新啟動之後，保護作業再次開始，並且會發生初始複寫。
   >
   >

您可以在 [工作]  頁面上監視狀態。

![在 [作業] 頁面上監視狀態](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

您也可以在 [受保護的項目] > 「保護群組名稱」 > [虛擬機器] 中監視保護狀態。 初始複寫完成並且同步處理資料之後，機器狀態就會變更為 [受保護]。

![在 [受保護的項目] 中監視狀態](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)

## <a name="step-11-set-protected-machine-properties"></a>步驟 11：設定受保護機器屬性
1. 在機器進入 [受保護] 狀態之後，您可以設定其容錯移轉屬性。 在保護群組詳細資料中，選取機器並開啟 [設定] 索引標籤。
2. Site Recovery 會自動建議 Azure VM 的屬性，並偵測內部部署網路設定。

    ![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)
3. 您可以變更這些設定：

   * **Azure VM 名稱**：這是將在容錯移轉之後提供給 Azure 中機器的名稱。 名稱必須符合 Azure 需求。
   * **Azure VM 大小**：網路介面卡的數目取決於您為目標虛擬機器指定的大小。 如需大小和介面卡的詳細資訊，請參閱[大小資料表](../virtual-machines/virtual-machines-linux-sizes.md#size-tables)。 請注意：

     * 在您修改虛擬機器的大小並儲存設定之後，下次您開啟 [設定] 索引標籤時，網路介面卡的數目將會改變。 目標虛擬機器上的網路介面卡最小數目，等於來源虛擬機器上的網路介面卡最小數目。 網路介面卡的最大數目取決於虛擬機器的大小。
       * 如果來源電腦上的網路介面卡數目小於或等於針對目標機器大小所允許的介面卡數目，則目標將具備與來源相同的介面卡數目。
       * 如果來源虛擬機器的介面卡數目超過針對目標大小所允許的數目，則將使用目標大小的最大值。
        例如，如果來源機器具有兩張網路介面卡，而目標機器大小支援四張，則目標機器將會有兩張介面卡。 如果來源機器具有兩張介面卡，但支援的目標大小僅支援一張介面卡，則目標機器將只會有一張介面卡。
     * 如果虛擬機器具有多張網路介面卡，則所有的介面卡都應該連線到同一個 Azure 網路。
   * **Azure 網路**：您必須指定 Azure VM 在容錯移轉之後將會連接的 Azure 網路。 如果您未指定一個網路，則 Azure VM 不會連線到任何網路。 此外，如果您想要從 Azure 容錯回復至內部部署網站，您必須指定 Azure 網路。 容錯回復需要 Azure 網路與內部部署網路之間的 VPN 連線。
   * **Azure IP 位址/子網路**：針對每個網路介面卡，選取 Azure VM 應該連線的子網路。 請注意，如果來源機器的網路介面卡設定為使用靜態 IP 位址，您可以為 Azure VM 指定靜態 IP 位址。 如果您未指定靜態 IP 位址，則會配置任何可用的 IP 位址。 如果已指定目標 IP 位址，但是已由 Azure 中的另一個 VM 使用，容錯移轉將會失敗。 如果來源機器的網路介面卡設定為使用 DHCP，則您將具有 DHCP 做為 Azure 的設定。      

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>步驟 12：建立復原計畫並執行容錯移轉
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failover/player]
>
>

您可以執行單一機器的容錯移轉，或容錯移轉執行相同工作或執行相同工作負載的多部虛擬機器。 若要同時容錯移轉多部機器，將它們新增至復原方案。

建立復原方案：

1. 在 [復原方案] 頁面上，按一下 [新增復原方案] 並新增復原方案。 指定計畫的詳細資料並選取 **Azure** 做為目標。

 ![設定復原計畫](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)
2. 在 [選取虛擬機器] 中，選取保護群組，然後在群組中選取要新增至復原方案的機器。

 ![新增虛擬機器](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

您可以自訂計畫以建立群組並決定機器在復原計劃中容錯移轉的順序。 您也可以加入進行手動動作的指令碼和提示。 您可以手動建立指令碼，或使用 [Azure 自動化 Runbook](site-recovery-runbook-automation.md) 來建立指令碼。 如需有關自訂復原方案的詳細資訊，請參閱[建立復原方案](site-recovery-create-recovery-plans.md)。

## <a name="run-a-failover"></a>執行容錯移轉
在執行容錯移轉之前：

* 請確定管理伺服器正在執行並且可以使用。 否則容錯移轉會失敗。
* 如果執行非計劃性容錯移轉：

  * 如果可能的話，您應該在執行非計劃性容錯移轉之前關閉主要機器。 這可確保您不需要同時執行來源和複本機器。 如果您是複寫 VMware VM，則當您執行非計劃性容錯移轉時，您可以指定 Site Recovery 應該嘗試關閉來源機器。 這是否有作用，取決於主要網站的狀態。 如果您是複寫實體伺服器，則 Site Recovery 不提供此選項。
  * 非計劃性容錯移轉會停止從主要機器的資料複寫，讓任何資料差異不會在開始非計劃性容錯移轉之後傳送。
  * 如果您要在容錯移轉之後連線到 Azure 中的複本虛擬機器，請先在來源機器上啟用遠端桌面連線，再執行測試容錯移轉。 然後允許 RDP 連線通過防火牆。 您也需要在容錯移轉之後，在 Azure 虛擬機器的公用端點上允許 RDP。 請依照[最佳作法](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)進行，以確保 RDP 在容錯移轉之後能夠運作。

> [!NOTE]
> 若要在執行容錯移轉至 Azure 時獲得最佳效能，請確保您已經在受保護的機器上安裝 Azure 代理程式。 這有助於加速電腦開機，也有助於診斷問題。 Azure 代理程式有 [Linux](https://github.com/Azure/WALinuxAgent) 和 [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) 版本。
>
>

### <a name="run-a-test-failover"></a>執行測試容錯移轉
執行測試容錯移轉，以在不會影響生產環境的隔離網路中模擬您的容錯移轉和復原程序，並讓一般複寫如往常繼續。 測試容錯移轉會在來源上啟動，您可以用數種方式執行：

* **不指定 Azure 網路**：如果您在沒有網路的情況下執行測試容錯移轉，該測試將會檢查虛擬機器是否啟動，以及是否在 Azure 中正確顯示。 在容錯移轉之後，虛擬機器不會連線到 Azure 網路。
* **指定 Azure 網路**：這種類型的容錯移轉會檢查整個複寫環境是否如預期般運作，以及 Azure 虛擬機器是否連線到指定的網路。

若要執行測試容錯移轉：

1. 在 [復原方案] 頁面中，選取方案並按一下 [測試容錯移轉]。

 ![選取方案](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)
2. 在 [確認測試容錯移轉] 中，選取 [無]，以表示您不想要使用 Azure 網路來進行測試容錯移轉，或選取測試 VM 在容錯移轉之後將連線的網路。 按一下核取記號以啟動容錯移轉。

 ![選取](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)
3. 在 [作業]  索引標籤上監視容錯移轉進度。

 ![監視進度](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)
4. 容錯移轉完成之後，您應該也會看到複本 Azure 機器出現在 Azure 入口網站的 [虛擬機器] 中。 如果您想要起始 RDP 連線至 Azure VM，您必須在 VM 端點上開啟連接埠 3389。
5. 完成之後，在容錯移轉到達**完成**測試階段時，請按一下 [完成測試] 來完成。 在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。
6. 按一下 [測試容錯移轉已完成]  ，即可自動清除測試環境。 此動作完成之後，測試容錯移轉將會顯示 [完成] 狀態。 任何在測試容錯移轉期間自動建立的元素或 VM 都將刪除。 如果測試容錯移轉持續兩週以上，則會被強制完成。

### <a name="run-an-unplanned-failover"></a>執行非計劃性容錯移轉
非計劃性容錯移轉是從 Azure 起始，即使主要網站無法使用也可以執行。

1. 在 [復原方案] 頁面上，選取方案並按一下 [容錯移轉] > [非計劃性容錯移轉]。

 ![選取方案](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)
2. 如果您是複寫 VMware 虛擬機器，您可以嘗試關閉內部部署 VM。 這已是最好的行動，無論成功與否，容錯移轉都會繼續。 如果不成功，[作業] 索引標籤的 [非計劃性容錯移轉作業] 下會顯示錯誤詳細資料。

 ![內部部署 VM 的關閉選項](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

 > [!NOTE]
 > 如果您是複寫實體伺服器，則這個選項無法使用。 您必須盡可能嘗試手動關閉。
 >
 >

3. 在 [確認容錯移轉] 中，確認容錯移轉方向 (至 Azure)，然後選取您要用於容錯移轉的復原點。 如果您啟用多個 VM，您在設定複寫屬性時可以復原到最新的應用程式或當機時保持一致復原點。 您也可以選取 [自訂復原點]  來復原至較早的時間點。 按一下核取記號以啟動容錯移轉。

 ![確認容錯移轉方向](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)
4. 等候非計劃性容錯移轉作業完成。 您可以在 [作業]  索引標籤上監視容錯移轉進度。 即使在非計劃性容錯移轉期間發生錯誤，復原方案還是會執行到完成為止。 您也應該會可以看到複本 Azure 機器出現在 Azure 入口網站的 [虛擬機器] 中。

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>在容錯移轉之後連接到複寫的 Azure 虛擬機器
若要在容錯移轉之後連線至 Azure 中複寫的虛擬機器，您需要：

- 在主要機器上啟用遠端桌面連線。
- 主要機器上的 Windows 防火牆設為允許 RDP。
- 將 RDP 新增至 Azure 虛擬機器的公用端點。

如需此設定的相關資訊，請參閱[使用 ASR 容錯移轉之後針對遠端桌面連線進行疑難排解](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

## <a name="deploy-additional-process-servers"></a>部署額外處理序伺服器
如果您必須相應放大您的部署超過 200 部來源機器，或每日變換率總計超過 2 TB，您將需要額外的處理序伺服器來處理流量。 若要設定額外處理序伺服器，請查看[額外處理序伺服器](#additional-process-servers)中的需求，然後依照下列指示來設定處理序伺服器。 設定伺服器之後，您可以設定來源機器來使用它。

### <a name="set-up-an-additional-process-server"></a>設定額外處理序伺服器
設定額外處理序伺服器，如下所示：

* 執行整合精靈，設定管理伺服器僅做為處理序伺服器。
* 如果您想要僅使用新的處理序伺服器來管理資料複寫，您必須移轉受保護的機器。

### <a name="install-the-process-server"></a>安裝處理序伺服器
1. 在 [快速啟動] 頁面，下載整合安裝檔案以進行 Site Recovery 元件安裝。 執行安裝程式。
2. 在 [開始之前] 中，選取 [新增額外處理序伺服器以相應放大部署]。

 ![新增處理序伺服器](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)
3. 就像在[設定](#step-5-install-the-management-server)第一部管理伺服器時一樣，完成精靈。
4. 在 [組態伺服器詳細資料] 中，輸入您安裝組態伺服器之原始管理伺服器的 IP 位址，然後指定複雜密碼。 如果您沒有複雜密碼，請在原始管理伺服器上執行 **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n**，以擷取複雜密碼。

 ![組態伺服器詳細資料](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>移轉機器以使用新的處理序伺服器
1. 開啟 [組態伺服器] > [伺服器] > 「原始管理伺服器的名稱」 > [伺服器詳細資料]。

 ![伺服器詳細資料](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)
2. 在 [處理序伺服器] 清單中，在您要修改的伺服器旁邊選取 [變更處理序伺服器]。

 ![更新處理序伺服器](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)
3. 依序選取 [變更處理序伺服器]、[目標處理序伺服器] 和新的管理伺服器。 然後，選取新的處理序伺服器會處理的虛擬機器。 按一下資訊圖示以取得伺服器的相關資訊。 隨即顯示將每個選取的虛擬機器複寫到新的處理序伺服器所需的平均空間，協助您進行負載的判斷。 按一下核取記號以開始複寫到新處理序伺服器。

 ![變更處理序伺服器](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)

## <a name="vmware-permissions-for-vcenter-access"></a>vCenter 存取的 VMware 權限
處理序伺服器可以自動探索 vCenter 伺服器上的 VM。 若要執行自動探索，您必須在 vCenter 層級定義角色 (Azure_Site_Recovery)，以允許 Site Recovery 存取 vCenter 伺服器。 如果您只需要將 VMware 機器移轉至 Azure，而不需要從 Azure 容錯回復，您可以定義唯讀角色，這個角色已足夠。 設定權限，如[步驟 6︰設定 vCenter 伺服器的認證](#step-6-set-up-credentials-for-the-vcenter-server)中所述。 下表摘要說明角色權限：

| **角色** | **詳細資料** | **權限** |
| --- | --- | --- |
| Azure_Site_Recovery 角色 |VMware VM 探索 |針對 v-Center 伺服器指派這些權限：<br/><br/>資料存放區：配置空間、瀏覽資料存放區、低階檔案作業、移除檔案、更新虛擬機器檔案<br/><br/>網路：網路指派<br/><br/>資源：指派虛擬機器至資源集區、移轉已關閉的虛擬機器、移轉已啟動的虛擬機器<br/><br/>工作：建立工作、更新工作<br/><br/>虛擬機器  > 組態<br/><br/>虛擬機器 > 互動 > 回答問題、裝置連接、設定 CD 媒體、設定磁碟片媒體、關閉電源、開啟電源、VMware 工具安裝<br/><br/>虛擬機器 > 清查 > 建立、註冊、取消註冊<br/><br/>虛擬機器 > 佈建 > 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/>虛擬機器 > 快照集 > 移除快照集 |
| vCenter 使用者角色 |VMware VM 探索/容錯移轉而不關閉來源 VM |針對 v-Center 伺服器指派這些權限：<br/><br/>資料中心物件 > 傳播至子物件、role=Read-only <br/><br/>由於是在資料中心層級指派使用者，因此使用者能夠存取資料中心內的所有物件。 如果您想要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (ESX 主機、資料存放區、VM 及網路)。 |
| vCenter 使用者角色 |容錯移轉和容錯回復 |針對 v-Center 伺服器指派這些權限：<br/><br/>資料中心物件 –> 傳播至子物件、role=Azure_Site_Recovery<br/><br/>由於是在資料中心層級指派使用者，因此使用者能夠存取資料中心內的所有物件。  如果您想要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (ESX 主機、資料存放區、VM 及網路)。 |

## <a name="third-party-software-notices-and-information"></a>第三方廠商軟體注意事項和資訊
<!--Do Not Translate or Localize-->

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## <a name="next-steps"></a>後續步驟
[深入了解容錯回復](site-recovery-failback-azure-to-vmware-classic.md)，以便將在 Azure 中容錯移轉的機器回復到您的內部部署環境。

