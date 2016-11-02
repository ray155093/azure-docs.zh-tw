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
    ms.date="10/05/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-support-matrix"></a>Azure Site Recovery 支援矩陣

本文摘要說明 Azure Site Recovery 部署支援的作業系統和元件。 針對每個部署案例的支援元件和必要條件清單將在相對應的個別部署文章中提供，本文件僅提供摘要說明。

## <a name="supported-operating-systems-for-virtualization-servers"></a>虛擬機器伺服器支援的作業系統


**來源** | **目標** | **主機作業系統**
---|---|---|--- 
**Hyper-V 主機 (不使用 VMM)** | Azure | 具有最新更新的 Windows Server 2012 R2
**Hyper-V 主機 (使用 VMM)** | Azure | 具有最新更新的 Windows Server 2012 R2
**Hyper-V 主機 (使用 VMM)** | 次要 VMM 站台 | 至少為具有最新更新的 Windows Server 2012
**VMware 主機/vCenter** | Azure | vCenter 5.5 或 6.0 (僅支援 5.5 功能) <br/><br/>  vSphere 6.0、 5.5 或 5.1 (含最新更新)
**VMware 主機/vCenter** | 次要 VMware 站台 | vCenter 5.5 或 6.0 (僅支援 5.5 功能) <br/><br/>  vSphere 6.0、 5.5 或 5.1 (含最新更新)

## <a name="supported-requirements-for-replicated-machines"></a>複寫機器支援的需求

**來源** | **複寫項目** | **目標** | **主機作業系統**
---|---|---|--- 
**Hyper-V VM** | 任何工作負載 | Azure | 任何 [Hyper-V 支援的](https://technet.microsoft.com/library/cc794868.aspx)的客體 OS<br/><br/> VM 必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Hyper-V VM (使用 VMM)** | 任何工作負載 | Azure | 任何 [Hyper-V 支援的](https://technet.microsoft.com/library/cc794868.aspx)的客體 OS<br/><br/> VM 必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Hyper-V VM (使用 VMM)** | 任何工作負載 | 次要 VMM 站台 |  [Hyper-V 支援的](https://technet.microsoft.com/library/mt126277.aspx)
**VMware VM** | Windows VM 上執行的任何工作負載 | Azure | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)<br/><br/> VM 必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware VM** | Linux VM 上執行的任何工作負載 | Azure | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/>  所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。 不支援使用 HP CCISS 控制站儲存體的實體伺服器。 只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。<br/><br/> VM 必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware VM** | Windows VM 上執行的任何工作負載 | 次要 VMware 站台 | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)
**VMware VM** | Linux VM 上執行的任何工作負載 | 次要 VMware 站台 | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/>  所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。 不支援使用 HP CCISS 控制站儲存體的實體伺服器。 只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。
**實體伺服器** | Windows 上執行的任何工作負載 | Azure | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 (至少含 SP1)
**實體伺服器** | Linux 上執行的任何工作負載 | Azure | Red Hat Enterprise Linux 6.7、7.1、7.2  <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/>  所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。 不支援使用 HP CCISS 控制站儲存體的實體伺服器。 只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。
**實體伺服器** | Windows 上執行的任何工作負載 | 次要網站 | 64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 (至少含 SP1)
**實體伺服器** | Linux 上執行的任何工作負載 | 次要網站 | Red Hat Enterprise Linux 6.7、7.1、7.2  <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/>  所需的儲存體︰檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。 不支援使用 HP CCISS 控制站儲存體的實體伺服器。 只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。


## <a name="provider-versions"></a>提供者版本

**名稱** | **說明** | **最新版本** | **支援** | **詳細資料**
---|---|---|---| ---
**Azure Site Recovery 提供者** | 協調內部部署伺服器與 Azure/次要站台間通訊  <br/><br/>  安裝於內部部署 VMM 伺服器上，或 HYPER-V 伺服器上 (若沒有 VMM 伺服器) | 5.1.1700 (可從入口網站取得) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 整合安裝 (VMware 到 Azure)** | 協調內部部署 VMware 伺服器與 Azure 之間的通訊  <br/><br/>  安裝在內部部署 VMware 伺服器上 | 9.3.4246.1 (可從入口網站取得) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**行動服務** | 協調內部部署 VMware 伺服器/實體伺服器和 Azure/次要站台間複寫 | NA (可從入口網站取得) | 安裝在每個 VMware VM 上或您想要複寫的實體伺服器上。 **Microsoft Azure 復原服務 (MARS) 代理程式** | 協調 HYPER-V VM 與 Azure 之間的複寫<br/><br/>  安裝在內部部署 HYPER-V 伺服器上 (無論是否有 VMM 伺服器) | 2.0.8689.0 (可從入口網站取得) | 此代理程式是供 Azure Site Recovery 和 Azure 備份使用。 [深入了解] (https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>後續步驟

[準備部署](site-recovery-best-practices.md)




<!--HONumber=Oct16_HO2-->


