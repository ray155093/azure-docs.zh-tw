---
title: "用於複寫至次要站台的 Azure Site Recovery 支援矩陣 | Microsoft Docs"
description: "摘要說明 Azure Site Recovery 支援的作業系統和元件"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 0a2c3bec5fc6fb44b4baddc393d6f9387bbb5b94


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-customer-owned-secondary-site"></a>用於複寫至客戶擁有的次要站台的 Azure Site Recovery 支援矩陣

> [!div class="op_single_selector"]
> * [複寫至 Azure](site-recovery-support-matrix-to-azure.md)
> * [複寫至客戶擁有的次要站台](site-recovery-support-matrix-to-sec-site.md)

[這裡](site-recovery-best-practices.md) 提到的 Azure Site Recovery 必要條件清單及下文摘要說明複寫和復原至客戶擁有的次要站台時支援的 Azure Site Recovery 設定和元件。

## <a name="support-for-deployment-options"></a>支援部署選項

**部署** | **VMware/實體伺服器** | **Hyper-V (無 VMM)** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
**Azure 入口網站** | 內部部署 VMware VM 至次要 VMware 網站。<br/><br/> 下載說明指南](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) InMage Scout 使用者指南。 Azure 入口網站中未提供 | 不支援 | VMM 雲端中的內部部署 Hyper-V VM 至次要 VMM 雲端<br/><br/> 僅限標準 Hyper-V 複寫，不支援 SAN
**傳統入口網站** | 僅限使用維護模式。 無法建立新的保存庫。 | 不支援 | 僅限使用維護模式。
**PowerShell** | 不支援。 | 不支援 | 支援



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
**Hyper-V (無 VMM)** | 不是複寫至次要站台所支援的設定
**Hyper-V (含 VMM)** | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2<br/><br/> Windows Server 2016 主機應由 SCVMM 2016 所管理

## <a name="support-for-replicated-machine-machine-os-versions"></a>支援複寫機器的多種機器作業系統版本
下表摘要說明使用 Azure Site Recovery 時的各種部署案例中的作業系統支援。 這項支援適用於提到的作業系統上執行的**任何工作負載**。

**VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | [Hyper-V 支援的](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>**Linux 版本的儲存體支援**檔案系統 (EXT3、ETX4、ReiserFS、XFS) 多重路徑軟體裝置對應工具 (multipath) 磁碟區管理員：(LVM2) **不**支援使用 HP CCISS 控制器儲存體的實體伺服器。
>只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。

## <a name="support-for-network"></a>支援網路
下表摘要說明使用 Azure Site Recovery 複寫至 Azure 時的各種部署案例中的網路組態支援。

### <a name="host-network-configuration"></a>主機網路組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
NIC Teaming | 是 | 是
VLAN | 是 | 是
IPv4 | 是 | 是
IPv6 | 否 | 否

### <a name="guest-vm-network-configuration"></a>客體 VM 網路組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
NIC Teaming | 否 | 否
IPv4 | 是 | 是
IPv6 | 否 | 否
靜態 IP (Windows) | 是 | 是
靜態 IP (Linux) | 是 | 是
多個 NIC | 是 | 是


## <a name="support-for-storage"></a>儲存體的支援
下表摘要說明使用 Azure Site Recovery 複寫至 Azure 時的各種部署案例中的儲存體組態支援。

### <a name="host-storage-configuration"></a>主機儲存體組態

**儲存體 (主機)** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
NFS | 是 | NA
SMB 3.0 | NA | 是
SAN (ISCSI) | 是 | 是
多重路徑 (MPIO) | 是 | 是

### <a name="guest-physical-server-storage-configuration"></a>客體/實體伺服器儲存體組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
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

**動作** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否 | 否
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否 | 否

## <a name="support-for-provider-and-agent"></a>支援提供者和代理程式

**名稱** | **說明** | **最新版本** | **詳細資料**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供者** | 協調內部部署伺服器與 Azure 之間的通訊 <br/><br/> 安裝於內部部署 VMM 伺服器上，或 Hyper-V 伺服器上 (若沒有 VMM 伺服器) | 5.1.19 ([可從入口網站取得](http://aka.ms/downloaddra)) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**行動服務** | 協調內部部署 VMware 伺服器/實體伺服器和次要站台間複寫<br/><br/> 安裝於 VMware VM 上或您想要複寫的實體伺服器上  | NA (可從入口網站取得) | NA


## <a name="next-steps"></a>後續步驟
[準備部署](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


