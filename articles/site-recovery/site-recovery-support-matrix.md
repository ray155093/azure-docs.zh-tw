---
title: "Azure Site Recovery 支援矩陣 | Microsoft Docs"
description: "摘要說明 Azure Site Recovery 支援的作業系統和元件"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1bbcc13c-ea21-4349-9ddf-0d7dfdcdcbfb
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 1a2ea0753d12915075c055f9983773618bf6777d
ms.openlocfilehash: 85c6a2296a4487ae549321d3ff07129bcbab5adf


---
# <a name="azure-site-recovery-support-matrix"></a>Azure Site Recovery 支援矩陣

本文摘要說明 Azure Site Recovery 支援的作業系統和元件。 針對每個部署案例的支援元件和必要條件清單將在相對應的個別部署文章中提供，本文件僅提供摘要說明。

## <a name="support-for-azure-replication-scenarios"></a>Azure 複寫案例的支援

**部署** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
**Azure 入口網站** | 內部部署 VMware VM 至 Azure 儲存體，使用 Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 為基礎或傳統 VM。 | 內部部署 Hyper-V VM (不在 VMM 雲端中) 至 Azure 儲存體，使用 Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 為基礎或傳統 VM。 | 內部部署 Hyper-V VM (在 VMM 雲端中) 至 Azure 儲存體，使用 Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 為基礎或傳統 VM。
**傳統入口網站** | 僅限使用維護模式。 無法建立新的保存庫。 | 僅限使用維護模式。 | 僅限使用維護模式。
**PowerShell** | 目前不支援。 | 支援 | 支援


## <a name="support-for-secondary-site-replication-scenarios"></a>次要網站複寫案例的支援

**部署** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
**Azure 入口網站** | 內部部署 VMware VM 至次要 VMware 網站。<br/><br/> 下載說明指南](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) InMage Scout 使用者指南。 Azure 入口網站中未提供 | 不支援 | VMM 雲端中的內部部署 Hyper-V VM 至次要 VMM 雲端<br/><br/> 僅限標準 Hyper-V 複寫，不支援 SAN
**傳統入口網站** | 僅限使用維護模式。 無法建立新的保存庫。 | 僅限使用維護模式。 | 僅限使用維護模式。
**PowerShell** | 不支援。 | NA | 支援



## <a name="support-for-virtualization-server-operating-systems"></a>支援虛擬化伺服器作業系統

### <a name="host-servers-replicate-to-azure"></a>主機伺服器 (複寫至 Azure)

**VMware VM/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | ---
vCenter 5.5 或 6.0 (僅支援 5.5 功能) <br/><br/> vSphere 6.0、 5.5 或 5.1 (含最新更新) | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2<br/><br/> 目前不支援混用執行 Windows Server 2016 和 2012 R2 之主機的 Hyper-V 網站。 | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2<br/><br/> Windows Server 2016 主機應由執行 System Center 2016 的 VMM 所管理。<br/><br/> 目前不支援混用 Windows Server 2016 和 2012 R2 主機的 VMM 2016 雲端。

### <a name="host-servers-replicate-to-secondary-site"></a>主機伺服器 (複寫至次要網站)

**VMware VM/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
vCenter 5.5 或 6.0 (僅支援 5.5 功能) <br/><br/> vSphere 6.0、 5.5 或 5.1 (含最新更新) | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2 或 Windows Server 2012。<br/><br/> Windows Server 2016 主機應由執行 System Center 2016 的 VMM 所管理。<br/><br/> 目前不支援混用 Windows Server 2016 和較舊主機的 VMM 2016 雲端。


## <a name="support-for-replicated-machines"></a>支援複寫的機器

### <a name="machines-replicate-to-azure"></a>機器 (複寫至 Azure)

虛擬機器必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。

**需求** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
複寫項目 | 在 Windows 或 Linux VM 上的任何工作負載 | 任何工作負載 | 任何工作負載
主機作業系統 | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> 所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。 不支援使用 HP CCISS 控制站儲存體的實體伺服器。 只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。 | 任何 [Hyper-V 支援的](https://technet.microsoft.com/library/cc794868.aspx)的客體 OS | 任何 [Hyper-V 支援的](https://technet.microsoft.com/library/cc794868.aspx)的客體 OS


### <a name="machines-replicate-to-secondary-site"></a>機器 (複寫至次要網站)

**需求** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
複寫項目 | 在 Windows 或 Linux VM 上的任何工作負載 | 任何工作負載 | 任何工作負載
主機作業系統 | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> 所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。<br/><br/> 不支援使用 HP CCISS 控制站儲存體的實體伺服器。 只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。 | Hyper-V 所支援的任何客體 OS](https://technet.microsoft.com/library/mt126277.aspx)


## <a name="support-for-provider-and-agent"></a>支援提供者和代理程式

**名稱** | **說明** | **最新版本** | **詳細資料**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供者** | 協調內部部署伺服器與 Azure/次要站台間通訊  <br/><br/> 安裝於內部部署 VMM 伺服器上，或 Hyper-V 伺服器上 (若沒有 VMM 伺服器) | 5.1.1700 (可從入口網站取得) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 整合安裝 (VMware 到 Azure)** | 協調內部部署 VMware 伺服器與 Azure 之間的通訊  <br/><br/> 安裝在內部部署 VMware 伺服器上 | 9.3.4246.1 (可從入口網站取得) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**行動服務** | 協調內部部署 VMware 伺服器/實體伺服器和 Azure/次要站台間複寫<br/><br/> 安裝於 VMware VM 上或您想要複寫的實體伺服器上  | NA (可從入口網站取得) | 。
**Microsoft Azure 復原服務 (MARS) 代理程式** | 協調 HYPER-V VM 與 Azure 之間的複寫<br/><br/> 安裝在內部部署 HYPER-V 伺服器上 (無論是否有 VMM 伺服器) | |

## <a name="support-for-networking"></a>支援網路功能

### <a name="networking-replicate-to-azure"></a>網路功能 (複寫至 Azure)

**主機網路功能** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
NIC Teaming | 是<br/><br/>在實體機器中不支援| 是 | 是
VLAN | 是 | 是 | 是
IPv4 | 是 | 是 | 是
IPv6 | 否 | 否 | 否

**來賓 VM 網路功能** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
NIC Teaming | 否 | 否 | 否
IPv4 | 是 | 是 | 是
IPv6 | 否 | 否 | 否
靜態 IP (Windows) | 是 | 是 | 是
靜態 IP (Linux) | 否 | 否 | 否
多個 NIC | 是 | 是 | 是

**Azure 網路功能** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
ExpressRoute | 是 | 是 | 是
ILB | 是 | 是 | 是
ELB | 是 |  |
流量管理員 | 是 | 是 | 是
多個 NIC | 是 | 是 | 是
保留的 IP | 是 | 是 | 是
IPv4 | 是 | 是 | 是
保留來源 IP | 是 | 是 | 是

### <a name="networking-replicate-to-secondary-site"></a>網路功能 (複寫至次要網站)

**主機網路功能** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
NIC Teaming | 是 | 是
VLAN | 是 | 是
IPv4 | 是 | 是
IPv6 | 否 | 否

**來賓 VM 網路功能** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
NIC Teaming | 否 | 否
IPv4 | 是 | 是
IPv6 | 否 | 否
靜態 IP (Windows) | 是 | 是
靜態 IP (Linux) | 是 | 是
多個 NIC | 是 | 是


## <a name="support-for-storage"></a>儲存體的支援

### <a name="storage-replicate-to-azure"></a>儲存體 (複寫至 Azure)

**儲存體 (主機)** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
NFS | VMware 為是<br/><br/> 實體伺服器為否 | NA | NA
SMB 3.0 | NA | 是 | 是
SAN (ISCSI) | 是 | 是 | 是
多重路徑 (MPIO) | VMware 為是<br/><br/> 實體伺服器為 NA | 是 | 是

**儲存體 (來賓 VM/實體伺服器)** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
VMDK | 是 | NA | NA
VHD/VHDX | NA | 是 | 是
第 2 代 VM | NA | 是 | 是
共用叢集磁碟 | VMware 為是<br/><br/> 實體伺服器為 NA | 否 | 否
已加密磁碟 | 否 | 否 | 否
EFI/UEFI| 否 | 是 | 是
NFS | 否 | 否 | 否
SMB 3.0 | 否 | 否 | 否
RDM | 是<br/><br/> 實體伺服器為 NA | NA | NA
磁碟 > 1 TB | 否 | 否 | 否
使用等量磁碟的磁碟區 > 1 TB<br/><br/> LVM | 是 | 是 | 是
儲存空間 | 否 | 是 | 是
熱新增/移除磁碟 | 否 | 否 | 否
排除磁碟 | 是 | 否 | 否
多重路徑 (MPIO) | NA | 是 | 是

**Azure 儲存體** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
LRS | 是 | 是 | 是
GRS | 是 | 是 | 是
非經常性儲存體 | 否 | 否 | 否
經常性存取儲存體| 否 | 否 | 否
待用加密 | 是 | 是 | 是
進階儲存體 | 是 | 否 | 否
匯入/匯出服務 | 否 | 否 | 否


### <a name="storage-replicate-to-secondary-site"></a>儲存體 (複寫至次要網站)

**儲存體 (主機)** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
NFS | 是 | NA
SMB 3.0 | NA | 是
SAN (ISCSI) | 是 | 是
多重路徑 (MPIO) | 是 | 是

**儲存體 (來賓 VM/實體伺服器)** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
VMDK | 是 | NA
VHD/VHDX | NA | 是 (最多 16 個磁碟)
第 2 代 VM | NA | 是
共用叢集磁碟 | 是  | 否
已加密磁碟 | 否 | 否
UEFI| 否 | NA
NFS | 否 | 否
SMB 3.0 | 否 | 否
RDM | 是 | NA
磁碟 > 1 TB | 否 | 是
使用等量磁碟的磁碟區 > 1 TB<br/><br/> LVM | 是 | 是
儲存空間 | 否 | 是
熱新增/移除磁碟 | 否 | 否
排除磁碟 | 否 | 否
多重路徑 (MPIO) | NA | 是

## <a name="support-for-recovery-services-vault-actions"></a>支援復原服務保存庫動作

### <a name="vaults-replicate-to-azure"></a>保存庫 (複寫至 Azure)

**動作** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否 | 否 | 否
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否 | 否 | 否

### <a name="vaults-replicate-to-secondary-site"></a>保存庫 (複寫至次要網站)

**動作** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否 | 否
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否 | 否


## <a name="support-for-azure-compute-replicate-to-azure"></a>支援 Azure 計算 (複寫至 Azure)

**計算功能** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
共用磁碟客體叢集 | 否 | 否 | 否
可用性設定組 | 否 | 否 | 否
中樞 | 是 | 是 | 是

## <a name="support-for-azure-vms"></a>Azure VM 的支援

您可以部署 Site Recovery 以複寫執行受 Azure 支援的任何作業系統的虛擬機器和實體伺服器。 這包括大部分的 Windows 和 Linux 版本。 您想要複寫的內部部署 VM 必須符合 Azure 需求。

**功能** | **需求** | **詳細資料**
--- | --- | ---
**Hyper-V 主機** | 應該執行 Windows Server 2012 R2 或更新版本 | 如果不支援作業系統，則先決條件檢查會失敗
**VMware hypervisor** | 支援的作業系統 | [檢查需求](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**客體作業系統** | 從 Hyper-V 複寫到 Azure：Site Recovery 支援 [Azure 支援](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有作業系統。 <br/><br/> 針對 VMware 和實體伺服器複寫：請檢查 Windows 和 Linux [必要條件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | 如果不支援，則先決條件檢查會失敗。
**客體作業系統架構** | 64 位元 | 如果不支援，則先決條件檢查會失敗
**作業系統磁碟大小** | 最多 1023 GB | 如果不支援，則先決條件檢查會失敗
**作業系統磁碟計數** | 1 | 如果不支援，則先決條件檢查會失敗。
**資料磁碟計數** | 16 或更少 (最大值是所建立之虛擬機器大小的函數。 16 = XL) | 如果不支援，則先決條件檢查會失敗
**資料磁碟 VHD 大小** | 最多 1023 GB | 如果不支援，則先決條件檢查會失敗
**網路介面卡** | 支援多個介面卡 |
**靜態 IP 位址** | 支援 | 如果主要虛擬機器使用靜態 IP 位址，您可以為將在 Azure 中建立的虛擬機器指定靜態 IP 位址。<br/><br/> 不支援**在 Hyper-V 上執行的 Linux VM** 之靜態 IP 位址。
**iSCSI 磁碟** | 不支援 | 如果不支援，則先決條件檢查會失敗
**共用 VHD** | 不支援 | 如果不支援，則先決條件檢查會失敗
**FC 磁碟** | 不支援 | 如果不支援，則先決條件檢查會失敗
**硬碟格式** | VHD  <br/><br/> VHDX | 雖然 Azure 目前不支援 VHDX，但 Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。 當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
**Bitlocker** | 不支援 | 必須停用 Bitlocker，才能保護虛擬機器。
**VM 名稱** | 介於 1 到 63 個字元。 只能使用字母、數字和連字號。 應該以字母或數字為開頭和結尾 | 更新 Site Recovery 中虛擬機器內容的值
**VM type** | 第 1 代<br/><br/> 第 2 代 - Windows | OS 磁碟基本類型的第 2 代 VM，其中包含一或兩個格式化為支援 VHDX 的資料磁碟區且支援低於 300 GB。<br/><br/>。 不支援 Linux 第 2 代 VM。 [深入了解](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |








## <a name="next-steps"></a>後續步驟
[準備部署](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO2-->


