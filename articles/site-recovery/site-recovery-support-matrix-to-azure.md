---
title: "用於複寫至 Azure 的 Azure Site Recovery 支援矩陣 | Microsoft Docs"
description: "摘要說明 Azure Site Recovery 支援的作業系統和元件"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/25/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 3b9912ac45627f799ef3ca20eccf8d11f9ae52f0


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>用於複寫至 Azure 的 Azure Site Recovery 支援矩陣

> [!div class="op_single_selector"]
> * [複寫至 Azure](site-recovery-support-matrix-to-azure.md)
> * [複寫至客戶擁有的次要站台](site-recovery-support-matrix-to-sec-site.md)


[這裡](site-recovery-best-practices.md) 提到的 Azure Site Recovery 必要條件清單及下文摘要說明複寫和復原至 Azure 時支援的 Azure Site Recovery 設定和元件。


## <a name="support-for-deployment-options"></a>支援部署選項

**部署** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
**Azure 入口網站** | 內部部署 VMware VM 至 Azure 儲存體，使用 Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 為基礎或傳統 VM。 | 內部部署 Hyper-V VM (不在 VMM 雲端中) 至 Azure 儲存體，使用 Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 為基礎或傳統 VM。 | 內部部署 Hyper-V VM (在 VMM 雲端中) 至 Azure 儲存體，使用 Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 為基礎或傳統 VM。
**傳統入口網站** | 僅限使用維護模式。 無法建立新的保存庫。 | 僅限使用維護模式。 | 僅限使用維護模式。
**PowerShell** | 目前不支援。 | 支援 | 支援


## <a name="support-for-datacenter-management-servers"></a>支援資料中心管理伺服器

### <a name="virtualization-management-entities"></a>虛擬化管理實體

**部署** | **支援**
--- | ---
**VMware VM/實體伺服器** | vSphere 6.0、5.5 或 5.1 (含最新更新)
**Hyper-V (含 VMM)** | SCVMM 2016 & SCVMM 2012 R2

  >[!Note]
  > 目前不支援混用 Windows Server 2016 和 2012 R2 主機的 SCVMM 2016 雲端。

### <a name="host-servers"></a>主機伺服器

**部署** | **支援**
--- | ---
**VMware VM/實體伺服器** | vCenter 5.5 或 6.0 (僅支援 5.5 功能)
**Hyper-V (無 VMM)** | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2
**Hyper-V (含 VMM)** | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2<br/><br/> Windows Server 2016 主機應由 SCVMM 2016 所管理

  >[!Note]
  >目前不支援混用執行 Windows Server 2016 和 2012 R2 之主機的 Hyper-V 網站。

## <a name="support-for-replicated-machine-os-versions"></a>支援多種複寫機器作業系統版本

複寫至 Azure 時，受保護的虛擬機器必須符合[ Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。
下表摘要說明使用 Azure Site Recovery 時的各種部署案例中的複寫作業系統支援。 這項支援適用於提到的作業系統上執行的**任何工作負載**。

 **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | ---
64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | 任何 [Hyper-V 支援的](https://technet.microsoft.com/library/cc794868.aspx)的客體 OS | 任何 [Hyper-V 支援的](https://technet.microsoft.com/library/cc794868.aspx)的客體 OS


>[!Note]
>**Linux 版本的儲存體支援**檔案系統 (EXT3、ETX4、ReiserFS、XFS) 多重路徑軟體裝置對應工具 (multipath) 磁碟區管理員：(LVM2) **不**支援使用 HP CCISS 控制器儲存體的實體伺服器。
>只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。

## <a name="support-for-network"></a>支援網路
下表摘要說明使用 Azure Site Recovery 複寫至 Azure 時的各種部署案例中的網路組態支援。

### <a name="host-network-configuration"></a>主機網路組態

**組態** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
NIC Teaming | 是<br/><br/>在實體機器中不支援| 是 | 是
VLAN | 是 | 是 | 是
IPv4 | 是 | 是 | 是
IPv6 | 否 | 否 | 否

### <a name="guest-vm-network-configuration"></a>客體 VM 網路組態

**組態** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
NIC Teaming | 否 | 否 | 否
IPv4 | 是 | 是 | 是
IPv6 | 否 | 否 | 否
靜態 IP (Windows) | 是 | 是 | 是
靜態 IP (Linux) | 否 | 否 | 否
多個 NIC | 是 | 是 | 是

### <a name="failed-over-azure-vm-network-configuration"></a>容錯移轉的 Azure VM 網路組態

**Azure 網路功能** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
ExpressRoute | 是 | 是 | 是
ILB | 是 | 是 | 是
ELB | 是 | 是 | 是
流量管理員 | 是 | 是 | 是
多個 NIC | 是 | 是 | 是
保留的 IP | 是 | 是 | 是
IPv4 | 是 | 是 | 是
保留來源 IP | 是 | 是 | 是


## <a name="support-for-storage"></a>儲存體的支援
下表摘要說明使用 Azure Site Recovery 複寫至 Azure 時的各種部署案例中的儲存體組態支援。

### <a name="host-storage-configuration"></a>主機儲存體組態

**組態** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
NFS | VMware 為是<br/><br/> 實體伺服器為否 | NA | NA
SMB 3.0 | NA | 是 | 是
SAN (ISCSI) | 是 | 是 | 是
多重路徑 (MPIO)<br></br>測試標的︰Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON | VMware 為是<br/><br/> | 是 | 是

### <a name="guest-physical-server-storage-configuration"></a>客體/實體伺服器儲存體組態

**組態** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
VMDK | 是 | NA | NA
VHD/VHDX | NA | 是 | 是
第 2 代 VM | NA | 是 | 是
EFI/UEFI| 否 | 是 | 是
共用叢集磁碟 | VMware 為是<br/><br/> 實體伺服器為 NA | 否 | 否
已加密磁碟 | 否 | 否 | 否
NFS | 否 | NA | NA
SMB 3.0 | 否 | 否 | 否
RDM | 是<br/><br/> 實體伺服器為 NA | NA | NA
磁碟 > 1 TB | 否 | 否 | 否
使用等量磁碟的磁碟區 > 1 TB<br/><br/> LVM 邏輯磁碟區管理 | 是 | 是 | 是
儲存空間 | 否 | 是 | 是
熱新增/移除磁碟 | 否 | 否 | 否
排除磁碟 | 是 | 是 | 是
多重路徑 (MPIO) | NA | 是 | 是

**Azure 儲存體** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
LRS | 是 | 是 | 是
GRS | 是 | 是 | 是
非經常性儲存體 | 否 | 否 | 否
經常性存取儲存體| 否 | 否 | 否
待用加密 (SSE)| 是 | 是 | 是
進階儲存體 | 是 | 否 | 否
匯入/匯出服務 | 否 | 否 | 否


## <a name="support-for-azure-compute-configuration"></a>支援 Azure 計算設定

**計算功能** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
可用性集合 | 否 | 否 | 否
中樞 | 是 | 是 | 是

## <a name="failed-over-azure-vm-requirements"></a>容錯移轉的 Azure VM 需求

您可以部署 Site Recovery 以複寫執行受 Azure 支援的任何作業系統的虛擬機器和實體伺服器。 這包括大部分的 Windows 和 Linux 版本。 複寫至 Azure 時，您想要複寫的內部部署 VM 必須符合下列 Azure 需求。

**實體** | **需求** | **詳細資料**
--- | --- | ---
**客體作業系統** | 從 Hyper-V 複寫到 Azure：Site Recovery 支援 [Azure 支援](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有作業系統。 <br/><br/> 針對 VMware 和實體伺服器複寫：請檢查 Windows 和 Linux [必要條件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | 如果不支援，則先決條件檢查會失敗。
**客體作業系統架構** | 64 位元 | 如果不支援，則先決條件檢查會失敗
**作業系統磁碟大小** | 最多 1023 GB | 如果不支援，則先決條件檢查會失敗
**作業系統磁碟計數** | 1 | 如果不支援，則先決條件檢查會失敗。
**資料磁碟計數** | 16 或更少 (最大值是所建立之虛擬機器大小的函數。 16 = XL) | 如果不支援，則先決條件檢查會失敗
**資料磁碟 VHD 大小** | 最多 1023 GB | 如果不支援，則先決條件檢查會失敗
**網路介面卡** | 支援多個介面卡 |
**共用 VHD** | 不支援 | 如果不支援，則先決條件檢查會失敗
**FC 磁碟** | 不支援 | 如果不支援，則先決條件檢查會失敗
**硬碟格式** | VHD  <br/><br/> VHDX | 雖然 Azure 目前不支援 VHDX，但 Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。 當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
**Bitlocker** | 不支援 | 必須停用 Bitlocker，才能保護虛擬機器。
**VM 名稱** | 介於 1 到 63 個字元。 只能使用字母、數字和連字號。 應該以字母或數字為開頭和結尾 | 更新 Site Recovery 中虛擬機器內容的值
**VM type** | 第 1 代<br/><br/> 第 2 代 - Windows | OS 磁碟基本類型的第 2 代 VM，其中包含一或兩個格式化為支援 VHDX 的資料磁碟區且支援低於 300 GB。<br></br>不支援 Linux 第 2 代 VM。 [深入了解](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>支援復原服務保存庫動作

**動作** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否 | 否 | 否
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否 | 否 | 否


## <a name="support-for-provider-and-agent"></a>支援提供者和代理程式

**名稱** | **說明** | **最新版本** | **詳細資料**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供者** | 協調內部部署伺服器與 Azure 之間的通訊 <br/><br/> 安裝於內部部署 VMM 伺服器上，或 Hyper-V 伺服器上 (若沒有 VMM 伺服器) | 5.1.19 ([可從入口網站取得](http://aka.ms/downloaddra)) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 整合安裝 (VMware 到 Azure)** | 協調內部部署 VMware 伺服器與 Azure 之間的通訊  <br/><br/> 安裝在內部部署 VMware 伺服器上 | 9.3.4246.1 (可從入口網站取得) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**行動服務** | 協調內部部署 VMware 伺服器/實體伺服器和 Azure/次要站台間複寫<br/><br/> 安裝於 VMware VM 上或您想要複寫的實體伺服器上  | NA (可從入口網站取得) | NA
**Microsoft Azure 復原服務 (MARS) 代理程式** | 協調 HYPER-V VM 與 Azure 之間的複寫<br/><br/> 安裝在內部部署 HYPER-V 伺服器上 (無論是否有 VMM 伺服器) | 最新的代理程式 ([可從入口網站取得](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>後續步驟
[準備部署](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


