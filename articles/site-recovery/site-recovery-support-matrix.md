<properties
	pageTitle="Azure Site Recovery 支援矩陣 | Microsoft Azure"
	description="摘要說明 Azure Site Recovery 支援的作業系統和元件"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/07/2016"
	ms.author="raynew"/>

# Azure Site Recovery 支援矩陣

本文摘要說明 Azure Site Recovery 部署支援的作業系統和元件。每份/部署文件均提供每種部署案例支援的元件和必要條件清單，本主題僅提供摘要說明。

## 虛擬機器伺服器支援的作業系統


**複寫** | **複寫項目** | **複寫目標** | **主機作業系統**
---|---|---|--- 
**HYPER-V 主機** | 任何工作負載 | Azure | 具有最新更新的 Windows Server 2012 R2
**VMM 雲端中的 HYPER-V 主機** | 任何工作負載 | Azure | 具有最新更新的 Windows Server 2012 R2
**VMM 雲端中的 HYPER-V 主機** | 任何工作負載 | 次要 VMM 站台 | 至少為具有最新更新的 Windows Server 2012
**VMware 主機/vCenter** | 任何工作負載 | Azure | vCenter 5.5 或 6.0 (僅支援 5.5 功能)<br/><br/> vSphere 6.0、 5.5 或 5.1 (含最新更新)
**VMware 主機/vCenter** | 任何工作負載 | 次要 VMware 站台 | vCenter 5.5 或 6.0 (僅支援 5.5 功能)<br/><br/> vSphere 6.0、 5.5 或 5.1 (含最新更新)

## 複寫機器支援的需求

**複寫** | **複寫項目** | **複寫目標** | **主機作業系統**
---|---|---|--- 
**Hyper-V VM** | 任何工作負載 | Azure | [Azure 支援的](https://technet.microsoft.com/library/cc794868.aspx)任何客體 OS<br/><br/> VM 必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMM 雲端中的 Hyper-V VM** | 任何工作負載 | Azure | [Azure 支援的](https://technet.microsoft.com/library/cc794868.aspx)任何客體 OS<br/><br/> VM 必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMM 雲端中的 Hyper-V VM** | 任何工作負載 | 次要 VMM 站台 | [Hyper-V 支援的](https://technet.microsoft.com/library/mt126277.aspx)任何客體 OS
**VMware VM** | Windows VM 上執行的任何工作負載 | Azure | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)<br/><br/> VM 必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware VM** | Linux VM 上執行的任何工作負載 | Azure | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) 的 Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。不支援使用 HP CCISS 控制站儲存體的實體伺服器。只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。<br/><br/> 虛擬機器必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware VM** | Windows VM 上執行的任何工作負載 | 次要 VMware 站台 | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)
**VMware VM** | Linux VM 上執行的任何工作負載 | 次要 VMware 站台 | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) 的 Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。不支援使用 HP CCISS 控制站儲存體的實體伺服器。只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。
**實體伺服器** | Windows 上執行的任何工作負載 | Azure | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 (至少含 SP1)
**實體伺服器** | Linux 上執行的任何工作負載 | Azure | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) 的 Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。不支援使用 HP CCISS 控制站儲存體的實體伺服器。只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。
**實體伺服器** | Windows 上執行的任何工作負載 | 次要網站 | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 (至少含 SP1)
**實體伺服器** | Linux 上執行的任何工作負載 | 次要網站 | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) 的 Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。不支援使用 HP CCISS 控制站儲存體的實體伺服器。只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。


## 提供者版本

**名稱** | **說明** | **最新版本** | **支援** | **詳細資料**
---|---|---|---| ---
**Azure Site Recovery 提供者** | 協調內部部署伺服器與 Azure/次要站台間通訊 <br/><br/> 安裝於內部部署 VMM 伺服器上，或 HYPER-V 伺服器上 (若沒有 VMM 伺服器) | 5\.1.1700 (可從入口網站取得) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 整合安裝 (VMware 到 Azure)** | 協調內部部署 VMware 伺服器與 Azure 之間的通訊 <br/><br/> 安裝在內部部署 VMware 伺服器上 | 9\.3.4246.1 (可從入口網站取得) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**行動服務** | 協調內部部署 VMware 伺服器/實體伺服器和 Azure/次要站台間複寫 | NA (可從入口網站取得) | 安裝在每個 VMware VM 上或您想要複寫的實體伺服器上。**Microsoft Azure 復原服務 (MARS) 代理程式** | 協調 HYPER-V VM 與 Azure 之間的複寫<br/><br/> 安裝在內部部署 HYPER-V 伺服器上 (無論是否有 VMM 伺服器) | 2\.0.8689.0 (可從入口網站取得) | 此代理程式是供 Azure Site Recovery 和 Azure 備份使用。[深入了解](https://support.microsoft.com/zh-TW/kb/2997692)

## 後續步驟

[準備部署](site-recovery-best-practices.md)

<!----HONumber=AcomDC_0907_2016-->