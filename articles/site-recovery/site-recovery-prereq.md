---
title: "使用 Azure Site Recovery 複寫至 Azure 的必要條件 | Microsoft Docs"
description: "本文摘要說明使用 Azure Site Recovery 服務將 VM 和實體機器複寫至 Azure 的必要條件。"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/11/2016
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 46ea97f5c13b7befc78466fae92a737107be8f83
ms.openlocfilehash: 31cae0cf625552c0e9547d0e514c6017c6a179ef

---

#  <a name="prerequisites-for-replication-to-azure-using-azure-site-recovery"></a>使用 Azure Site Recovery 複寫至 Azure 的必要條件

 Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。 當主要位置運作中斷時，您可以容錯移轉至次要位置，讓應用程式和工作負載保持可用。 當主要位置恢復正常作業時，就可以容錯回復至該位置。 深入了解 [什麼是 Site Recovery？](site-recovery-overview.md)

本文摘要說明開始 Site Recovery 至 Azure 複寫的必要條件。

在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。


## <a name="azure-requirements"></a>Azure 需求

**需求** | **詳細資料**
--- | ---
**Azure 帳戶** | [Microsoft Azure](http://azure.microsoft.com/) 帳戶。<br/><br/> 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。
**Site Recovery 服務** | [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。 |
**Azure 儲存體** | 您需要 Azure 儲存體帳戶來儲存複寫的資料，而且它與復原服務保存庫必須位於相同區域中。 複寫的資料會儲存在 Azure 儲存體，而在發生容錯移轉時會建立 Azure VM。<br/><br/> 視您想要針對容錯移轉的 Azure VM 使用的資源模型而定，您可以在 [Resource Manager 模型](../storage/storage-create-storage-account.md)或[傳統模型](../storage/storage-create-storage-account-classic-portal.md)中設定帳戶。<br></br>您可以使用 [GRS](../storage/storage-redundancy.md#geo-redundant-storage) 或 LRS 儲存體。 我們建議使用 GRS，以便在發生區域性停電或無法復原主要區域時，能夠恢復資料。<br/><br/> 如果您在 Azure 入口網站中複寫 VMware VM 或實體伺服器，則支援進階儲存體。 [進階儲存體](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)通常是用於需要持續高 IO 效能和低延遲性以裝載 IO 密集型工作負載的虛擬機器。 如果使用進階儲存體存放複寫資料，您也需要標準儲存體帳戶來儲存複寫記錄檔，這些記錄檔會擷取內部部署資料進行中的變更。<br/><br/>
**儲存體限制** | 不論是跨資源群組、在訂用帳戶之內，還是跨訂用帳戶，您都不能移動 Site Recovery 中使用的儲存體帳戶。<br/><br/> 印度中部與印度南部目前不支援複寫至進階儲存體帳戶。
**Azure 網路** | 您需要 Azure 網路供 Azure VM 在容錯移轉之後連接，而且它與復原服務保存庫必須位於相同區域中。<br/><br/> 在 Azure 入口網站中，您可以在 [Resource Manager 模型](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)或[傳統模型](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)中建立網路。<br/><br/> 如果您從 VMM 複寫至 Azure，您將設定 VMM VM 網路與 Azure 網路之間的[網路對應](site-recovery-network-mapping.md)，以確保 Azure VM 在容錯移轉之後連接到適當的網路。
**網路限制** | 不論是跨資源群組、在訂用帳戶之內，還是跨訂用帳戶，您都不能移動 Site Recovery 中使用的網路帳戶。
**網路對應** | 如果您在 VMM 雲端中複寫 Hyper-V VM，則需要設定[網域對應](site-recovery-network-mapping.md)，以便容錯移轉之後，讓 Azure VM 在建立時連接至適當的網路。

>[!NOTE]
>以下各節說明客戶環境中各種元件的必要條件。 如需有關支援特定設定的詳細資訊，請參閱[支援矩陣](site-recovery-support-matrix.md)。
>

## <a name="disaster-recovery-of-vmware-virtual-machines-or-physical-windowslinux-servers-to-azure"></a>VMware 虛擬機器或實體 Windows/Linux 伺服器至 Azure 的災害復原
除了[上述](#Azure requirements)元件，以下是 VMware 虛擬機器或實體 Windows/Linux 伺服器災害復原所需的元件。


1. **設定伺服器或其他處理伺服器**︰您需要設定一台內部部署電腦作為設定伺服器，以協調內部部署網站和 Azure 之間的通訊，以及管理資料複寫。 <br></br>

2. **VMware vCenter/vSphere 主機**

| **元件** | **需求** |
| --- | --- |
| **vSphere** | 一個或多個 VMware vSphere Hypervisor。<br/><br/>Hypervisor 應該執行 vSphere 6.0、5.5 或 5.1 版 (含最新更新)。<br/><br/>我們建議您讓 vSphere 主機和 vCenter 伺服器位於與處理序伺服器相同的網路 (除非您已設定專用的處理序伺服器，否則這是組態伺服器所在的網路)。 |
| **vCenter** | 建議您部署 VMware vCenter 伺服器來管理您的 vSphere 主機。 它應該執行 vCenter 6.0 或 5.5 版 (含最新更新)。<br/><br/>**限制**：Site Recovery 不支援新的 vCenter 和 vSphere 6.0 功能，例如跨 vCenter vMotion、虛擬磁碟區和儲存體 DRS。 Site Recovery 支援僅限於 5.5 版中可用的功能。||

3.**複寫電腦的必要條件**


| **元件** | **需求** |
| --- | --- |
| **內部部署 (VMware VM)** | 複寫的 VM 應該安裝並執行 VMware 工具。<br/><br/> VM 應該要符合建立 Azure VM 的 [Azure 必要條件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。<br/><br/>受保護機器上的個別磁碟容量不可超過 1023 GB。 <br/><br/>安裝磁碟機上必須至少有 2 GB 的可用空間來進行元件安裝。<br/><br/>如果您想要啟用多部 VM 一致性，應該將 VM 本機防火牆上的**連接埠 20004** 開啟。<br/><br/>機器名稱應包含介於 1 到 63 個字元 (字母、數字和連字號)。 名稱必須以字母或數字開頭，並以字母或數字結尾。 您可以在為機器啟用複寫後修改 Azure 名稱。<br/><br/> |
| **Windows 機器 (實體或 VMware)** | 機器應該執行受支援的 64 位元作業系統：Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 (至少為 SP1)。<br/><br/> 作業系統應該安裝在 C:\ 磁碟機上。 OS 磁碟應該是 Windows 基本磁碟而非動態磁碟。 資料磁碟可以為動態。<br/><br/>|
| **Linux 機器** (實體或 VMware) | 您需要受支援的 64 位元作業系統：Red Hat Enterprise Linux 6.7、7.1、7.2；Centos 6.5、6.6、6.7、7.0、7.1、7.2；Oracle Enterprise Linux 6.4、6.5 (執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3))、SUSE Linux Enterprise Server 11 SP3。<br/><br/>受保護機器上的 /etc/hosts 檔案應該包含將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。<br/><br/>如果您想要在容錯轉移之後使用安全殼層用戶端 (ssh) 連線到執行 Linux 的 Azure 虛擬機器，請確認受保護機器上的安全殼層服務已設為在系統開機時自動啟動，且防火牆規則允許 ssh 與其連線。<br/><br/>主機名稱、掛接點、裝置名稱和 Linux 系統路徑和檔案名稱 (eg /etc/; /usr) 僅可使用英文。<br/><br/>

## <a name="disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-vmm"></a>Hyper-V 虛擬機器至 Azure (無 VMM) 的災害復原

除了[上述](#Azure requirements)元件，以下是 VMM 雲端中 Hyper-V 虛擬機器災害復原所需的元件。

| **必要條件** | **詳細資料** |
| --- | --- |
| **Hyper-V 主機** |一或多部執行 Windows Server 2012 R2 (含最新更新且已啟用 Hyper-V 角色) 或 Microsoft Hyper-V Server 2012 R2 的內部部署伺服器。<br></br>Hyper-V 伺服器應該包含一或多部虛擬機器。<br></br>Hyper-V 伺服器應該直接或透過 Proxy 連接到網際網路。<br></br>Hyper-V 伺服器應該已安裝 [KB2961977](https://support.microsoft.com/en-us/kb/2961977) 中提及的修正程式。
|**Provider 和代理程式**| 在 Azure Site Recovery 部署期間，您會安裝 Azure Site Recovery Provider。 Provider 安裝也會在每部執行要保護之虛擬機器的 Hyper-V 伺服器上安裝 Azure 復原服務代理程式。 <br></br>Site Recovery 保存庫中的所有 Hyper-V 伺服器應該有相同版本的提供者和代理程式。<br></br>提供者必須透過網際網路連接到 Azure Site Recovery。 可以直接或透過 Proxy 傳送流量。 請注意，不支援 HTTPS 型 Proxy。 Proxy 伺服器應該要允許對下列位置的存取：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>如果您在伺服器上有以 IP 位址為基礎的防火牆規則，請檢查這些規則以允許對 Azure 的通訊。<br></br> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br></br> 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>VMM 雲端中的 Hyper-V 虛擬機器至 Azure 的災害復原

除了[上述](#Azure requirements)元件，以下是 VMM 雲端中 Hyper-V 虛擬機器災害復原所需的元件。

| **必要條件** | **詳細資料** |
| --- | --- |
| **VMM** |在 **System Center 2012 R2 或更高版本**上執行的一或多部 VMM 伺服器。 每部 VMM 伺服器應設定一或多個雲端。 <br></br>雲端應該包含<br>- 一或多個 VMM 主機群組。<br/>- 每個主機群組中的一或多個 Hyper-V 主機伺服器或叢集。<br/><br/>[深入了解](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx)設定 VMM 雲端。 |
| **Hyper-V** |Hyper-V 主機伺服器必須至少執行 **Windows Server 2012 R2** (具有 Hyper-V 角色) 或 **Microsoft Hyper-V Server 2012 R2** 且已安裝最新的更新。<br/><br/> Hyper-V 伺服器應該包含一或多部 VM。<br/><br/> 包含您想要複寫之 VM 的 Hyper-V 主機伺服器或叢集必須在 VMM 雲端中管理。<br/><br/>Hyper-V 伺服器應該直接或透過 Proxy 連接到網際網路。<br/><br/>Hyper-V 伺服器應該已安裝 [2961977](https://support.microsoft.com/kb/2961977) 文章中提及的修正程式。<br/><br/>Hyper-V 主機伺服器需要網際網路存取權以將資料複寫至 Azure。 |
| **Provider 和代理程式** |在 Azure Site Recovery 部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery Provider 以及在 Hyper-V 主機上安裝復原服務代理程式。 Provider 和代理程式必須透過網際網路直接連接，或透過 Proxy 連接到 Azure Site Recovery。 不支援 HTTPS 型 Proxy。 VMM 伺服器上的 Proxy 伺服器和 Hyper-V 主機應該允許存取︰ <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>如果您在 VMM 伺服器上有以 IP 位址為基礎的防火牆規則，請檢查這些規則以允許對 Azure 的通訊。<br/><br/> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br/><br/> 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>複寫之機器的必要條件
| **元件** | **詳細資料** |
| --- | --- |
| **受保護的 VM** | Site Recovery 支援 [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) 支援的所有作業系統。<br></br>VM 應該要符合建立 Azure VM 的 [Azure 必要條件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。 機器名稱應包含介於 1 到 63 個字元 (字母、數字和連字號)。 名稱必須以字母或數字開頭，並以字母或數字結尾。 <br></br>您可以在啟用 VM 的複寫後修改此名稱。 <br/><br/> 受保護機器上的個別磁碟容量不可超過 1023 GB。 VM 最多可以有 16 個磁碟 (因此最多可達 16 TB)。<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-a-customer-owned-site"></a>VMM 雲端中的 Hyper-V 虛擬機器至客戶自有網站的災害復原

除了[上述](#Azure requirements)元件，以下是 VMM 雲端中的 Hyper-V 虛擬機器至客戶自有網站災害復原所需的元件。

| **元件** | **詳細資料** |
| --- | --- |
| **VMM** |  建議您在主要站台與次要站台各部署一部 VMM 伺服器。<br/><br/> 您可以[在單一 VMM 伺服器上的雲端之間進行複寫](site-recovery-single-vmm.md)。 若要這樣做，您必須在 VMM 伺服器上至少設定兩個雲端。<br/><br/> VMM 伺服器至少應執行含有最新更新的 **System Center 2012 SP1**。<br/><br/> 每個 VMM 伺服器至少必須有一或多個雲端。 所有的雲端都必須設定 Hyper-V 容量設定檔。 <br/><br/>雲端必須包含一或多個 VMM 主機群組。 [深入了解](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)設定 VMM 雲端。<br/><br/> VMM 伺服器需要網際網路存取。 |
| **Hyper-V** | Hyper-V 伺服器必須執行**至少具備 Hyper-V 角色的 Windows Server 2012** 且已安裝最新更新。<br/><br/> Hyper-V 伺服器應該包含一或多部 VM。<br/><br/>  Hyper-V 主機伺服器應該位於主要和次要 VMM 雲端的主機群組中。<br/><br/> 如果您在 Windows Server 2012 R2 上的叢集中執行 Hyper-V，則應該安裝[更新 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> 如果您是在 Windows Server 2012 上的叢集中執行 Hyper-V，請注意，當您的叢集是靜態 IP 位址型叢集時，並不會自動建立叢集代理。 您必須手動設定叢集代理人。 [閱讀更多資訊](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。 |
| **提供者** | 在 Site Recovery 部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。 Provider 會透過 HTTPS 443 與 Site Recovery 通訊來協調複寫。 資料複寫是透過 LAN 或 VPN 連線在主要和次要 Hyper-V 伺服器之間進行。<br/><br/> VMM 伺服器上執行的 Provider 需要存取這些 URL：<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>允許從 VMM 伺服器到 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)的防火牆通訊，並允許 HTTPS (443) 通訊協定。 |



<!--HONumber=Jan17_HO2-->


