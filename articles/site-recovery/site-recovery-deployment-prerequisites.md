---
title: "Azure Site Recovery 部署必要條件 | Microsoft Docs"
description: "本文說明使用 Azure Site Recovery 設定複寫的必要條件。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 92abadf68e4acbd9daae9e15dcfdce53563f6eb2
ms.openlocfilehash: a407f5ebe096db74820ff93354a636bc63894b1d

---

# <a name="site-recovery-deployment-prerequisites"></a>Site Recovery 部署必要條件

組織需要 BCDR 策略，以決定應用程式、工作負載和資料如何在規劃與未規劃停機期間維持運作，並儘速復原到正常運作的情況。 Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。 當您的主要位置發生故障時，您容錯移轉至次要位置，讓應用程式和工作負載保持可用。 當它恢復正常作業時，容錯回復至您的主要位置。 深入了解 [什麼是 Site Recovery？](site-recovery-overview.md)

本文摘要說明 Site Recovery 複寫案例的部署必要條件。  

在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。



## <a name="azure-deployment-models"></a>Azure 部署模型

Azure 有兩種不同的[部署模型](../azure-resource-manager/resource-manager-deployment-model.md) 來建立和使用資源：Azure Resource Manager 模型和傳統模型。 Azure 也有兩個入口網站 – 支援傳統部署模型的 [Azure 傳統入口網站](https://manage.windowsazure.com/)，以及同時支援兩種部署模型的 [Azure 入口網站](https://ms.portal.azure.com/)。

新的站台復原案例應該部署在 [Azure 入口網站](https://portal.azure.com)中。 此入口網站提供新的功能和增強的部署經驗。 保存庫可保存在傳統入口網站，但無法建立新的保存庫。


## <a name="azure-account-requirements"></a>Azure 帳戶需求

**需求** | **詳細資料**
--- | --- 
**Azure 帳戶** | [Microsoft Azure](http://azure.microsoft.com/) 帳戶。<br/><br/> 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。


## <a name="azure-storage-requirements"></a>Azure 儲存體需求

複寫的資料會儲存在 Azure 儲存體，而在發生容錯移轉時會建立 Azure VM。

**需求** | **詳細資料**
--- | --- 
[Azure 儲存體帳戶](../storage/storage-introduction.md) | 您可以使用 [GRS](../storage/storage-redundancy.md#geo-redundant-storage) 或 LRS 儲存體。<br/><br/> 我們建議使用 GRS，以便在發生區域性停電或無法復原主要區域時，能夠恢復資料。 [深入了解](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy)
**Azure 入口網站** | 在 Azure 入口網站中，您可以使用 Resource Manager 儲存體，或傳統儲存體帳戶。
**進階儲存體** | 如果您使用 Azure 入口網站將 VMware VM 或實體伺服器複寫至 Azure，則支援[進階儲存體](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)。<br/><br/> 進階儲存體通常是用於需要持續高 IO 效能和低延遲性以裝載 IO 密集型工作負載的虛擬機器。<br/><br/> 如果使用進階儲存體，您也需要標準儲存體帳戶來儲存複寫記錄檔，這些記錄檔會擷取內部部署資料的進行中變更。
**儲存體限制** | 傳統的入口網站只支援 GRS。<br/><br/> 在 Azure 入口網站中建立的儲存體帳戶不能跨越資源群組移動。<br/><br/> 印度中部與印度南部目前不支援對進階儲存體帳戶執行複寫。

## <a name="azure-network-requirements"></a>Azure 網路需求

您需要 Azure 網路，以便在容錯移轉後建立的 Azure VM 進行連接。

**需求** | **詳細資料**
--- | ---
**網路區域** | 此網路必須位於與保存庫相同的區域中。
**Azure 入口網站** | 在 Azure 入口網站中，您可以使用 Resource Manager 網路或傳統網路。
**網路對應** | 如果您從 VMM 複寫至 Azure，您將設定 VMM VM 網路與 Azure 網路之間的[網路對應](site-recovery-network-mapping.md)，以確保 Azure VM 在容錯移轉之後連接到適當的網路。


## <a name="vmware-replication-requirements-to-azure"></a>VMware 複寫需求 (至 Azure)

**元件** | **需求**
--- | ---
**組態伺服器** | 執行 Windows Server 2012 R2 的內部部署實體或虛擬機器。 此伺服器上會安裝所有的內部部署 Site Recovery 元件。<br/><br/>針對 VMware VM 複寫，我們建議您將伺服器部署為高可用的 VMware VM。 <br/><br/>如果伺服器是 VMware VM，網路介面卡類型必須是 VMXNET3。 如果您是使用不同類型的網路介面卡，請在 vSphere 5.5 伺服器上安裝 [VMware 更新](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)。<br/><br/>該伺服器必須具有靜態 IP 位址。<br/><br/>該伺服器不能是網域控制站。<br/><br/>伺服器的主機名稱應包含少於或等於 15 個字元。<br/><br/>作業系統只能是英文版。<br/><br/> 安裝 VMware vSphere PowerCLI 6.0。 伺服器。<br/><br/>組態伺服器需要網際網路存取。 輸出存取需要下列條件：<br/><br/>於 Site Recovery 元件設定期間需要在 HTTP 80 上取得暫時存取 (以下載 MySQL)<br/><br/>需要在 HTTPS 443 上針對複寫管理取得持續的輸出存取<br/><br/>需要在 HTTPS 9443 上針對複寫流量取得持續的輸出存取 (可修改此連接埠)<br/><br/> 允許下列 URL 以便下載 MySQL：``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> 伺服器也需要存取[這些 URL](#requirements-for-url-access)
**處理序伺服器** | 預設會安裝在組態伺服器上<br/><br/> 做為複寫閘道。 從受保護的來源機器接收複寫資料、以快取最佳化、壓縮和加密，以及將資料傳送至 Azure 儲存體。 它還會處理用來保護機器的行動服務的推入安裝，並執行 VMWare VM 的自動探索。 隨著部署規模擴大，您可以新增更多個別的專用處理序伺服器，以處理日益增加的複寫流量。
**主要目標伺服器** | 預設會安裝在組態伺服器上。 在從 Azure 容錯回復期間，處理複寫資料。
**vSphere 主機** | 一個或多個 VMware vSphere Hypervisor。<br/><br/>Hypervisor 應該執行 vSphere 6.0、5.5 或 5.1 版 (含最新更新)。<br/><br/>我們建議您讓 vSphere 主機和 vCenter 伺服器位於與處理序伺服器相同的網路 (除非您已設定專用的處理序伺服器，否則這是組態伺服器所在的網路)。 
**vCenter 伺服器** | 建議您部署 VMware vCenter 伺服器來管理您的 vSphere 主機。 它應該執行 vCenter 6.0 或 5.5 版 (含最新更新)。<br/><br/>**限制**：Site Recovery 不支援新的 vCenter 和 vSphere 6.0 功能，例如跨 vCenter vMotion、虛擬磁碟區和儲存體 DRS。 Site Recovery 支援僅限於 5.5 版中也可用的功能

### <a name="vmware-vm-requirements-to-azure"></a>VMware VM 需求 (至 Azure)

**元件** | **需求**
--- | ---
**VMware VM** | 複寫的 VM 應該安裝並執行 VMware 工具。<br/><br/> VM 應該要符合建立 Azure VM 的 [Azure 必要條件](site-recovery-support-matrix.md#support-for-azure-vms)。<br/><br/>受保護機器上的個別磁碟容量不可超過 1023 GB。 VM 可以有多達 64 個磁碟 (因此多達 64 TB)。 <br/><br/>安裝磁碟機上必須至少有 2 GB 的可用空間來進行元件安裝。<br/><br/>**限制**：不支援使用加密的磁碟來保護 VM。<br/><br/>**限制**：不支援共用磁碟客體叢集。<br/><br/>如果您想要啟用多部 VM 一致性，應該將 VM 本機防火牆上的**連接埠 20004** 開啟。<br/><br/>不支援具有「整合可延伸韌體介面」(UEFI)/「可延伸韌體介面」(EFI) 開機的機器。<br/><br/>機器名稱應包含介於 1 到 63 個字元 (字母、數字和連字號)。 名稱必須以字母或數字開頭，並以字母或數字結尾。 您可以在為機器啟用複寫後修改 Azure 名稱。<br/><br/>如果來源 VM 具有 NIC 小組，它將會在容錯移轉至 Azure 後轉換成單一 NIC。<br/><br/>如果受保護的虛擬機器具有 iSCSI 磁碟，則 Site Recovery 會在 VM 容錯移轉至 Azure 時，將受保護的 VM iSCSI 磁碟轉換成 VHD 檔案。 如果 Azure VM 可以觸及 iSCSI 目標，則它會連接到 iSCSI 目標，並且基本上查看兩個磁碟 – Azure VM 上的 VHD 磁碟和來源 iSCSI 磁碟。 在此情況下，您必須將出現在 Azure VM 的 iSCSI 目標中斷連線。
**執行 Windows 的 VM** | Windows 電腦應該執行[支援的](site-recovery-support-matrix.md#support-for-replicated-machines) 64 位元作業系統。<br/><br/> 作業系統應該安裝在 C:\ 磁碟機上。<br/><br/> OS 磁碟應該是 Windows 基本磁碟而非動態磁碟。 資料磁碟可以為動態。<br/><br/> Site Recovery 支援具有 RDM 磁碟的 VM。 在容錯回復期間，如果原始來源 VM 和 RDM 磁碟可用，則 Site Recovery 會重複使用 RDM 磁碟。 如果它們都無法使用，在容錯回復期間，Site Recovery 會為每個磁碟建立新的 VMDK 檔案。
**執行 Linux 的 VM** | Linux 電腦應該執行[支援的作業系統](site-recovery-support-matrix.md#support-for-replicated-machines)。<br/><br/> 受保護機器上的 /etc/hosts 檔案應該包含將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。<br/><br/> 主機名稱、掛接點、裝置名稱和 Linux 系統路徑和檔案名稱 (eg /etc/; /usr) 僅可使用英文。<br/><br/> 應該[支援](site-recovery-support-matrix.md#support-for-replicated-machines)儲存體<br/><br/> Site Recovery 支援具有 RDM 磁碟的 VM。  在 Linux 的容錯回復期間，Site Recovery 不會重複使用 RDM 磁碟。 而是會針對每個對應的 RDM 磁碟建立新的 VMDK 檔案。<br/><br/> 請確定您在 VMware 中將 VM 的組態參數設定設為 disk.enableUUID=true。 如果不存在，請建立該項目。 這樣才能提供一致的 UUID 給 VMDK，使其可正確掛接。 加入這項設定也可確保在容錯回復期間只將差異變更傳輸回到內部部署，而不是傳輸完整的複寫。



## <a name="physical-servers-replication-requirements-to-azure"></a>實體伺服器複寫需求 (至 Azure)

**元件** | **需求**
--- | ---
**組態伺服器** | 執行 Windows Server 2012 R2 的內部部署實體或虛擬機器。 此伺服器上會安裝所有的內部部署 Site Recovery 元件。<br/><br/> 若要複寫實體機器，則機器可以是實體伺服器。<br/><br/>  組態伺服器需要網際網路存取。 輸出存取需要下列條件：<br/><br/> 於 Site Recovery 元件設定期間需要在 HTTP 80 上取得暫時存取 (以下載 MySQL)<br/><br/>需要在 HTTPS 443 上針對複寫管理取得持續的輸出存取<br/><br/>需要在 HTTPS 9443 上針對複寫流量取得持續的輸出存取 (可修改此連接埠)<br/><br/> 允許下列 URL 以便下載 MySQL：``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> 伺服器也需要存取[這些 URL](#requirements-for-url-access)
**容錯回復** | 即使您已複寫實體伺服器，仍一律會從 Azure 容錯回復到 VMware VM。 如果您並未將組態伺服器部署為 VMware VM，則必須設定 VMware VM 以接收容錯回復流量，然後才能進行容錯回復。

### <a name="physical-machine-requirements-to-azure"></a>實體機器需求 (至 Azure)

**元件** | **需求**
--- | ---
**執行 Windows 的實體伺服器** | Windows 電腦應該執行[支援的](site-recovery-support-matrix.md#support-for-replicated-machines) 64 位元作業系統。<br/><br/> 作業系統應該安裝在 C:\ 磁碟機上。<br/><br/> OS 磁碟應該是 Windows 基本磁碟而非動態磁碟。 資料磁碟可以為動態。<br/><br/> Site Recovery 支援具有 RDM 磁碟的 VM。 在容錯回復期間，如果原始來源 VM 和 RDM 磁碟可用，則 Site Recovery 會重複使用 RDM 磁碟。 如果它們都無法使用，在容錯回復期間，Site Recovery 會為每個磁碟建立新的 VMDK 檔案。
**執行 Linux 的實體伺服器** | Linux 電腦應該執行[支援的作業系統](site-recovery-support-matrix.md#support-for-replicated-machines)。<br/><br/> 受保護機器上的 /etc/hosts 檔案應該包含將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。<br/><br/> 主機名稱、掛接點、裝置名稱和 Linux 系統路徑和檔案名稱 (eg /etc/; /usr) 僅可使用英文。<br/><br/> 應該[支援](site-recovery-support-matrix.md#support-for-replicated-machines)儲存體。

## <a name="hyper-v-replication-requirements-to-azure"></a>Hyper-V 複寫需求 (至 Azure)

**元件** | **需求**
--- | ---
**VMM (選用)** | 您可以選擇性地複寫 VMM 雲端中受管理 Hyper-V 主機上的 VM。<br/><br/> 如果您未使用 VMM，可將一或多個 Hyper-V 主機或叢集蒐集到 Hyper-V 網站中，並設定網站的複寫。<br/><br/> 如果您使用 VMM，它應在 System Center 2012 R2 上執行。<br/><br/> 如果您使用 VMM，則每部 VMM 伺服器應設定一或多個雲端。 雲端應包含一或多個 VMM 主機群組，而每個主機群組中各有一或多個 Hyper-V 主機伺服器或叢集。<br/><br/> VMM 伺服器應連接到網際網路，並可存取[必要的 URL](#requirements-for-url-access)。<br/><br/> 如果您在 VMM 伺服器上有以 IP 位址為基礎的防火牆規則，請檢查這些規則以允許對 Azure 的通訊。<br/><br/> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br/><br/> 允許訂用帳戶的 Azure 區域和美國西部的 IP 位址範圍。
**HYPER-V 主機** | Hyper-V 主機伺服器必須至少執行 **Windows Server 2012 R2** (具有 Hyper-V 角色) 或 **Microsoft Hyper-V Server 2012 R2** 且已安裝最新的更新。<br/><br/> Hyper-V 伺服器應該包含一或多部 VM。<br/><br/>Hyper-V 伺服器應該直接或透過 Proxy 連接到網際網路。<br/><br/>Hyper-V 伺服器應該已安裝 [2961977](https://support.microsoft.com/kb/2961977) 文章中提及的修正程式。<br/><br/>Hyper-V 主機伺服器需要存取網際網路，才能將資料複寫至 Azure (包含存取[必要的 URL](#requirements-for-url-access))。 此外，允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br/><br/> 允許訂用帳戶的 Azure 區域和美國西部的 IP 位址範圍。<br/><br/> 如果 Hyper-V 主機位於 VMM 雲端，請務必安裝 [KB 2961977](https://support.microsoft.com/en-us/kb/2961977) 中所述的修正

### <a name="hyper-v-vm-requirements-to-azure"></a>Hyper-V VM 需求 (至 Azure)

**元件** | **需求**
--- | ---
**VM 法規遵循** |將 VM 容錯移轉之前，確定指派給 Azure VM 的名稱符合 [Azure 必要條件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。 您可以在啟用 VM 的複寫後修改此名稱。
**磁碟** | 受保護機器上的個別磁碟容量不可超過 1023 GB。 VM 可以有多達 64 個磁碟 (因此多達 64 TB)。<br/><br/> 不支援共用磁碟客體叢集。
**UEFI** | 不支援整合可延伸韌體介面 (UEFI)/可延伸韌體介面 (EFI) 開機。
**NIC Teaming** | 如果來源 VM 具有 NIC 小組，它將會在容錯移轉至 Azure 後轉換成單一 NIC。
**Linux 靜態** | 不支援保護使用靜態 IP 位址執行 Linux 的 Hyper-V VM。



## <a name="vmwarephysical-servers-replication-requirements-secondary-site"></a>VMware/實體伺服器複寫需求 (次要網站)

此案例是由 InMage 產品處理，當您選擇要將 VMware VM 或實體伺服器複寫至次要網站時，可以從 Site Recovery 保存庫下載該產品。 請注意，此案例不適用於 Azure 入口網站。

**元件** | **需求**
--- | ---
**主要網站** | 您需要處理序伺服器來處理快取、壓縮和資料最佳化。 此伺服器也會處理您想要複寫的每部電腦上所需整合代理程式的推入安裝。<br/><br/> 如果您想要複寫 VMware VM，您需要一或多部 VMware ESX/ESXi 伺服器，以及一部 VMware vCenter 伺服器 (選擇性)。
**次要網站** | 您需要在次要網站中設定組態伺服器，以便設定及管理您的環境。<br/><br/> 主要目標伺服器已安裝於次要網站中，用以保存複寫的資料。<br/><br/> vContinuum 伺服器預設安裝於主要目標伺服器中，並提供主控台來管理和監視您的環境。

### <a name="vmware-vmphysical-machine-requirements-secondary-site"></a>VMware VM/實體機器需求 (次要網站)

確認 [InMage 支援矩陣](https://aka.ms/asr-scout-cm)中的需求。


## <a name="hyper-v-replication-requirements-secondary-site"></a>Hyper-V 複寫需求 (次要網站)

**元件** | **需求**
--- | ---
**VMM** | 若要將 Hyper-V VM 複寫至次要網站，Hyper-V 主機必須在 System Center VMM 雲端中進行管理。 <br/><br/> VMM 必須至少執行含有最新更新的 System Center 2012 SP1。<br/><br/> 建議在主要網站與次要網站中各部署一部 VMM 伺服器。 您可以在[相同 VMM 伺服器上的不同雲端](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-single-vmm)之間進行複寫，但此案例需要進行一些手動設定。<br/><br/> VMM 伺服器應連接到網際網路，並可存取[必要的 URL](#requirements-for-url-access)。<br/><br/> 如果您在 VMM 伺服器上有以 IP 位址為基礎的防火牆規則，請檢查這些規則以允許對 Azure 的通訊。<br/><br/> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br/><br/> 允許訂用帳戶的 Azure 區域和美國西部的 IP 位址範圍。
**Hyper-V** | Hyper-V 伺服器必須至少執行具備 Hyper-V 角色並已安裝最新更新的 Windows Server 2012。<br/><br/> Hyper-V 伺服器應該包含一或多部 VM<br/><br/> Hyper-V 主機應該位於主要和次要 VMM 伺服器的主機群組中。<br/><br/> 如果您在 Windows Server 2012 R2 上的叢集中執行 Hyper-V，則應該安裝更新 [2961977](https://support.microsoft.com/kb/2961977)。 如果您的 Hyper-V 叢集位於 Windows Server 2012，當您的叢集是靜態 IP 位址型叢集時，並不會自動建立叢集訊息代理程式。 [深入了解](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)如何手動進行設定。

### <a name="hyper-v-vm-requirements-secondary-site"></a>Hyper-V VM 需求 (次要網站)

**元件** | **詳細資料**
--- | ---
**VMM 雲端** | VM 必須位於 VMM 雲端中的 Hyper-V 主機上。






## <a name="url-access-requirements"></a>URL 存取需求

這些 URL 應可透過 VMware、VMM 和 Hyper-V 主機伺服器提供。

**URL** | **VMM 至 VMM** | **VMM 至 Azure** | **Hyper-V 至 Azure** | **VMware 至 Azure** 
--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | 允許 | 允許 | 允許 | 允許
``*.backup.windowsazure.com`` | 不需要 | 允許 | 允許 | 允許
``*.hypervrecoverymanager.windowsazure.com`` | 允許 | 允許 | 允許 | 允許
``*.store.core.windows.net`` | 允許 | 允許 | 允許 | 允許
``*.blob.core.windows.net`` | 不需要 | 允許 | 允許 | 允許
``https://www.msftncsi.com/ncsi.txt`` | 允許 | 允許 | 允許 | 允許
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 不需要 | 不需要 | 不需要 | 允許 SQL 下載
``time.windows.com`` | 允許 | 允許 | 允許 | 允許
``time.nist.gov`` | 允許 | 允許 | 允許 | 允許

## <a name="failover-requirements"></a>容錯移轉需求

**案例** | **Test** | **已計劃** | **未計劃**
--- | --- | --- | ---
VMware 至 Azure | 支援 | 不支援 | 支援
實體至 Azure | 支援 | 不支援 | 支援
Hyper-V (VMM) 至 Azure | 支援 | 支援 | 支援
Hyper-V (無 VMM) 至 Azure | 支援 | 支援 | 不支援
Hyper-V (主要 VMM/雲端) 至次要 | 支援 | 支援 | 支援

## <a name="failback-requirements"></a>容錯回復需求

**案例** | **Test** | **已計劃** | **未計劃**
--- | --- | --- | ---
**Azure 至 VMware** | 不支援 | 不支援 | 支援
**Azure 至實體** | 不支援 | 不支援 | 僅支援複寫至 VMware
**Azure 至 VMM** | 不支援 | 支援 | 不支援
**Azure 至 Hyper-V (無 VMM)** | 不支援 | 支援 | 不支援
**Hyper-V (次要 VMM/雲端) 至主要** | 支援 | 支援 | 支援

### <a name="failback-from-azure-to-vmware-vms"></a>從 Azure 容錯回復至 VMware VM

**元件** | **詳細資料**
--- | ---
**Azure 中的暫存處理序伺服器**。 | 如果您想要在容錯移轉後從 Azure 容錯回復，您必須將 Azure VM 設定為處理序伺服器，以處理來自 Azure 的複寫。 容錯回復完成後，您可以刪除此 VM。
**VPN 連線** | 如需容錯回復，您需要設定從 Azure 網路到內部部署網站的 VPN 連線 (或 Azure ExpressRoute)。
**個別內部部署主要目標伺服器** | 內部部署主要目標伺服器會處理容錯回復。 主要目標伺服器預設會安裝在管理伺服器上，但如果要容錯回復大量資料，您應該就此目的設定個別的內部部署主要目標伺服器。



### <a name="failback-from-azure-to-physical-machines"></a>從 Azure 容錯回復至實體機器

**元件** | **詳細資料**
--- | ---
**實體對實體容錯回復** | 不支援此做法。 這表示如果您將實體伺服器容錯移轉至 Azure，然後想要容錯回復，則必須容錯回復至 VMware VM。 您無法容錯回復到實體伺服器。
**VM** | 您需要可供容錯回復的內部部署 VMware VM。
**組態伺服器** | 如果您並未將內部部署組態伺服器部署為 VMware VM，則必須將個別的主要目標伺服器設定為 VMware VM。 之所以需要如此，是因為主要目標伺服器會與 VMware 儲存體互動並與其連接，以將磁碟還原至 VMware VM。



## <a name="deployment-optimization"></a>部署最佳化

使用下列秘訣可最佳化並調整您的部署。

- **作業系統磁碟區大小**：當您將虛擬機器複寫到 Azure 時，作業系統磁碟區必須小於 1TB。 如果磁碟區大小超過此值，您可以在開始部署之前，手動將磁碟區移動至不同的磁碟。
- **資料磁碟大小**：如果複寫至 Azure，您可以在虛擬機器上擁有最多 64 個資料磁碟，每個資料磁碟的上限為 1 TB。 您可以有效地複寫及容錯移轉最多 64 TB 的虛擬機器。
- 復原方案限制：Site Recovery 可擴充至數千個虛擬機器。 復原方案是設計做為應用程式的模型，而由於應用程式應該一起進行容錯移轉，因此我們將一個復原方案中的機器數目限制為 50。
- **Azure 服務限制**：每個 Azure 訂用帳戶均隨附一組對核心、雲端服務等的預設限制。我們建議您執行容錯移轉測試，以驗證您訂用帳戶中的資源可用性。 您可以透過 Azure 支援修改這些限制。
- **容量規劃**：深入了解 Site Recovery 的 [容量規劃](site-recovery-capacity-planner.md) 。
- 複寫頻寬：如果您的複寫頻寬不足，請注意：
- **ExpressRoute**：Site Recovery 可搭配 Azure ExpressRoute 和 WAN 最佳化工具，例如 Riverbed。 [深入了解](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) ExpressRoute。
- 複寫流量：Site Recovery 只會使用資料區塊 (而非整個 VHD) 來執行智慧型初始複寫。 只會在複寫進行期間複寫變更。
- 網路流量：您可以根據目的地 IP 位址和連接埠，以原則設定 [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) ，藉此控制用於複寫的網路流量。  此外，如果您使用 Azure Backup Agent 來複寫至 Azure Site Recovery，您可以為該代理程式設定節流。 [閱讀更多資訊](https://support.microsoft.com/kb/3056159)。
- **RTO**：若要測量使用 Site Recovery 時可預期的復原時間目標 (RTO)，建議您執行測試容錯移轉並檢視 Site Recovery 作業，以分析完成作業所需的時間。 如果您容錯移轉至 Azure，我們建議您藉由整合 Azure 自動化和復原方案，將所有的手動操作自動化，來取得最佳的 RTO。
- **RPO**：當您複寫至 Azure 時，Site Recovery 支援幾乎同步的復原點目標 (RPO)。 這是假設您的資料中心與 Azure 之間有足夠的頻寬。



## <a name="next-steps"></a>後續步驟
檢閱一般部署需求後，請閱讀詳細的必要條件和部署案例。

* [將 VMWare 虛擬機器複寫至 Azure](site-recovery-vmware-to-azure.md)
* [將實體伺服器複寫到 Azure](site-recovery-vmware-to-azure.md)
* [將 VMM 雲端中的 Hyper-V 伺服器複寫至 Azure](site-recovery-vmm-to-azure.md)
* [將 Hyper-V 虛擬機器 (不使用 VMM) 複寫至 Azure](site-recovery-hyper-v-site-to-azure.md)
* [將 Hyper-V VM 複寫至次要站台](site-recovery-vmm-to-vmm.md)
* [利用 SAN 將 Hyper-V VM 複寫至次要站台](site-recovery-vmm-san.md)
* [利用單一 VMM 伺服器複寫 Hyper-V VM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


