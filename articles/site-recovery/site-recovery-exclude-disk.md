---
title: "使用 Azure Site Recovery 排除磁碟不要保護 | Microsoft Docs"
description: "說明在 VMware 至 Azure 和 Hyper-V Azure 案例中，為何及如何排除 VM 磁碟不要複寫。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 1/24/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: af0d66d92ca542f415de779fb638db166ba5f26a
ms.openlocfilehash: 5e0527fb0a41d8892c9e22d6d6d2f252972e69d0
ms.lasthandoff: 02/17/2017


---
#<a name="exclude-disk-from-replication"></a>排除磁碟不要複寫
本文說明如何排除磁碟不要複寫，以最佳化耗用的複寫頻寬，或最佳化該磁碟所使用的目標端資源。 VMware 至 Azure 和 Hyper-V 至 Azure 案例支援此功能。

##<a name="prerequisites"></a>必要條件

依預設會複寫機器上的所有磁碟。 想要排除磁碟不要複寫時，如果複寫是從 **VMware 至 Azure**，您必須先在電腦上手動安裝行動服務，然後才啟用複寫


## <a name="why-exclude-disks-from-replication"></a>為什麼要排除磁碟不要複寫？
排除磁碟不要複寫往往是因為︰

1. 在排除的磁碟上變換的資料不重要或不需要複寫。

2. 儲存體和網路資源儲存時可以不要複寫此變換。

##<a name="what-are-the-typical-scenarios"></a>有哪些典型的案例？
有一些可輕鬆識別資料變換的具體例子，極適合排除 – 例如，任何分頁檔寫入、Microsoft SQL Server tempdb 寫入等。根據工作負載和儲存子系統，分頁檔可以註冊大量變換。 不過，將這項資料從主要網站複寫至 Azure 需要大量資源。 因此，如果 VM 的單一虛擬磁碟上同時有作業系統和分頁檔，則 VM 複寫最佳化的做法如下︰

1. 將單一虛擬磁碟分割成兩個虛擬磁碟 – 一個有作業系統，另一個有分頁檔
2. 排除分頁檔磁碟不要複寫

同樣地，如果 Microsoft SQL Server 的 tempdb 和系統資料庫檔案位於相同的磁碟上，最佳化做法如下︰

1. 將系統資料庫和 tempdb 放在兩個不同的磁碟上
2. 排除 tempdb 磁碟不要複寫。

##<a name="how-to-exclude-disk-from-replication"></a>如何排除磁碟不要複寫？

###<a name="vmware-to-azure"></a>VMware 至 Azure
從 Azure Site Recovery 入口網站，依照[啟用複寫](site-recovery-vmware-to-azure.md#enable-replication)工作流程來保護 VM。 在「啟用複寫」的第 4 個步驟中，[複寫的磁碟] 資料行可用來排除磁碟不要複寫。 依預設會選取所有磁碟。 取消選取您想排除而不要複寫的磁碟，並完成步驟以啟用複寫。 

![啟用複寫](./media/site-recovery-exclude-disk/v2a-enable-replication-exclude-disk1.png)
    
    
>[!NOTE]
> 
> * 您只能排除已經安裝行動服務的磁碟。 您需要手動安裝行動服務，因為在啟用複寫後，只會透過推送機制來安裝行動服務。
> * 只有基本磁碟可以從複寫排除。 您無法排除 OS 或動態磁碟。
> * 啟用複寫後，您無法加入或移除複寫的磁碟。 如果您想要增加或排除磁碟，必須停用電腦的保護，然後重新啟用它。
> * 如果您排除應用程式運作所需的磁碟，在容錯移轉至 Azure 之後，您將必須在 Azure 中手動建立它，複寫的應用程式才能執行。 或者，您可以將 Azure 自動化整合至復原計畫，在機器容錯移轉期間建立磁碟。
> * Windows VM：您以手動方式在 Azure 中建立的磁碟將不會容錯回復。 例如，如果您容錯移轉三個磁碟，並直接在 Azure VM 中建立兩個磁碟，則只有三個已容錯移轉的磁碟會容錯回復。 您無法將手動建立的磁碟納入從內部部署至 Azure 的容錯回復或重新保護中。
> * Linux VM：您以手動方式在 Azure 中建立的磁碟會容錯回復。 例如，如果您容錯移轉三個磁碟，並直接在 Azure 中建立兩個磁碟，則五個磁碟全都將容錯回復。 您無法從容錯回復排除手動建立的磁碟。
> 

###<a name="hyper-v-to-azure"></a>Hyper-V 至 Azure
從 Azure Site Recovery 入口網站，依照[啟用複寫](site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication)工作流程來保護 VM。 在「啟用複寫」的第 4 個步驟中，[複寫的磁碟] 資料行可用來排除磁碟不要複寫。 依預設會選取所有磁碟進行複寫。 取消選取您想排除而不要複寫的磁碟，並完成步驟以啟用複寫。 

![啟用複寫](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)
    
>[!NOTE]
> 
> * 只有基本磁碟可以從複寫排除。 您無法排除 OS 磁碟，也不建議排除動態磁碟。 ASR 無法找出哪些 VHD 磁碟是來賓 VM 內的基本或動態磁碟。  如果未排除所有的相依動態磁碟區磁碟，受保護的動態磁碟會在容錯移轉 VM 上成為失敗的磁碟，且該磁碟上的資料無法存取。    
> * 啟用複寫後，您無法加入或移除複寫的磁碟。 如果您想要加入或排除磁碟，必須停用 VM 的保護，然後重新啟用它。
> * 如果您排除應用程式運作所需的磁碟，在容錯移轉至 Azure 之後，您將必須在 Azure 中手動建立它，複寫的應用程式才能執行。 或者，您可以將 Azure 自動化整合至復原計畫，在機器容錯移轉期間建立磁碟。
> * 您以手動方式在 Azure 中建立的磁碟不會容錯回復。 例如，如果您容錯移轉三個磁碟，並直接在 Azure VM 中建立兩個磁碟，則只有三個已容錯移轉的磁碟會從 Azure 容錯回復至 Hyper-V。 您無法包含在從 Hyper-V 至 Azure 的容錯回復中或反向複寫中手動建立的磁碟。
 


##<a name="end-to-end-scenarios-of-exclude-disks"></a>排除磁碟的端對端案例
我們來看一下兩個案例，以進一步了解排除磁碟功能。

1. SQL Server tempdb 磁碟
2. 分頁檔磁碟

###<a name="excluding-the-sql-server-tempdb-disk"></a>排除 SQL Server tempdb 磁碟
我們來看一下有 tempdb 可排除的 SQL Server 虛擬機器。

VM 的名稱︰來源 VM 上的 SalesDB 磁碟︰


**磁碟名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁碟上的資料類型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 作業系統磁碟
DB-Disk1| Disk1 | D:\ | SQL 系統資料庫和使用者資料庫&1;
DB-Disk2 (已排除磁碟不要保護) | Disk2 | E:\ | 暫存檔案
DB-Disk3 (已排除磁碟不要保護) | Disk3 | F:\ | SQL tempdb 資料庫 (資料夾路徑 (F:\MSSQL\Data\) --> 容錯移轉之前記下資料夾路徑
DB-Disk4 | Disk4 |G:\ |使用者資料庫&2;

因為 VM 的兩個磁碟上的資料變換是暫時性質，所以在保護 SalesDB VM 時，排除 'Disk2' 和 'Disk3' 不要複寫。 Azure Site Recovery 不會複寫這些磁碟，在容錯移轉時，這些磁碟將不會出現在 Azure 的容錯移轉 VM 上

容錯移轉之後在 Azure VM 上的磁碟：

**客體 OS 磁碟#** | **磁碟機代號** | **磁碟上的資料類型**
--- | --- | ---
DISK0 |    C:\ | 作業系統磁碟
Disk1 |    E:\ | 暫存儲存體 [Azure 會新增此磁碟，並指派第一個可用的磁碟機代號]
Disk2 | D:\ | SQL 系統資料庫和使用者資料庫&1;
Disk3 | G:\ | 使用者資料庫&2;

由於已從 SalesDB VM 中排除 Disk2 和 Disk3，E: 是可用清單中的第一個磁碟機代號。 Azure 會將 E: 指派給暫時存放磁碟區。 對於所有複寫的磁碟，磁碟機代號維持不變。

Disk3 是 SQL tempdb 磁碟 (tempdb 資料夾路徑 F:\MSSQL\Data\)，且已排除不要複寫，容錯移轉 VM 上不會有這個磁碟。 因此，SQL 服務會處於停止狀態，需要 F:\MSSQL\Data 路徑。

建立此路徑有兩種方式。

1. 新增磁碟並指派 tempdb 資料夾路徑，或
2. 使用現有的暫存磁碟來設定 tempdb 資料夾路徑

####<a name="add-a-new-disk"></a>新增磁碟：

1. 容錯移轉之前，記下 SQL tempdb.mdf 和 tempdb.ldf 路徑。
2. 從 Azure 入口網站中，將等於或大於來源 SQL tempdb 磁碟 (Disk3) 大小的新磁碟，新增至容錯移轉 VM。
3. 登入 Azure VM。 從磁碟管理 (diskmgmt.msc) 主控台，初始化並格式化剛新增的磁碟。
4. 指派 SQL tempdb 磁碟所使用的相同磁碟機代號 (F:)。
5. 在 F: 磁碟區上建立 tempdb 資料夾 (F:\MSSQL\Data)。
6. 從服務主控台啟動 SQL 服務。

####<a name="use-existing-temporary-storage-disk-for-sql-tempdb-folder-path"></a>使用現有的暫存磁碟來設定 SQL tempdb 資料夾路徑：

1. 開啟命令列主控台
2. 從命令列主控台，在復原模式下執行 SQL Server

        Net start MSSQLSERVER /f / T3608

3. 執行下列 sqlcmd，將 tempdb 路徑變更為新的路徑

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;        
        GO        
        ALTER DATABASE tempdb        
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO        
        ALTER DATABASE tempdb        
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');        
        GO


4. 停止 Microsoft SQL Server 執行個體。

        Net stop MSSQLSERVER
5. 啟動 Microsoft SQL Server 執行個體。

        Net start MSSQLSERVER

關於暫存磁碟，請參閱下列 Azure 指導方針

* 使用 Azure VM 中的 SSD 來儲存 SQL Server TempDB 和緩衝集區擴充
* Azure 虛擬機器中的 SQL Server 效能最佳做法

###<a name="failback-from-azure-to-on-premises"></a>容錯回復 (從 Azure 至內部部署)
現在讓我們了解從 Azure 容錯移轉至內部部署 VMware 或 Hyper-V 主機時，將會複製的所有磁碟。 您以手動方式在 Azure 中建立的磁碟不會複寫。 例如，如果您容錯移轉三個磁碟，並直接在 Azure VM 中建立兩個磁碟，則只有三個已容錯移轉的磁碟會容錯回復。 您無法將手動建立的磁碟納入從內部部署至 Azure 的容錯回復或重新保護中。 暫存磁碟也不會複寫至內部部署。

####<a name="failback-to-original-location-recovery-olr"></a>容錯回復至原始位置復原 (OLR)

在上述範例中，Azure VM 磁碟設定︰

**客體 OS 磁碟#** | **磁碟機代號** | **磁碟上的資料類型** 
--- | --- | --- 
DISK0 | C:\ | 作業系統磁碟
Disk1 |    E:\ | 暫存儲存體 [Azure 會新增此磁碟，並指派第一個可用的磁碟機代號]
Disk2 |    D:\ | SQL 系統資料庫和使用者資料庫&1;
Disk3 |    G:\ | 使用者資料庫&2;


####<a name="vmware-to-azure"></a>VMware 至 Azure
容錯回復至原始位置後，容錯回復 VM 磁碟設定不會有已排除的磁碟。 這表示從 WMware 至 Azure 中排除的磁碟，不會出現在容錯回復 VM 上。 

規劃從 Azure 容錯移轉至內部部署 VMware 之後，VMWare VM (原始位置) 上的磁碟︰

**客體 OS 磁碟#** | **磁碟機代號** | **磁碟上的資料類型** 
--- | --- | --- 
DISK0 | C:\ | 作業系統磁碟
Disk1 |    D:\ | SQL 系統資料庫和使用者資料庫&1;
Disk2 |    G:\ | 使用者資料庫&2;

####<a name="hyper-v-to-azure"></a>Hyper-V 至 Azure
容錯回復至原始位置後，容錯回復 VM 磁碟設定與 Hyper-V 的原始 VM 磁碟設定維持相同。 這表示從 Hyper-V 網站至 Azure 中排除的磁碟，將會出現在容錯回復 VM 上。

規劃從 Azure 容錯移轉至內部部署 Hyper-V 之後，Hyper-V VM (原始位置) 上的磁碟︰

**磁碟名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁碟上的資料類型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |    C:\ | 作業系統磁碟
DB-Disk1 | Disk1 | D:\ | SQL 系統資料庫和使用者資料庫&1;
DB-Disk2 (排除的磁碟) | Disk2 | E:\ | 暫存檔案
DB-Disk3 (排除的磁碟) | Disk3 | F:\ | SQL tempdb 資料庫 (資料夾路徑 (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | 使用者資料庫&2;


####<a name="exclude-paging-file-disk"></a>排除分頁檔磁碟

我們來看一下有分頁檔磁碟可排除的虛擬機器。
有兩種案例：

####<a name="case-1-pagefile-is-configured-on-the-d-drive"></a>案例 1：D: 磁碟機上設定分頁檔
磁碟設定︰


**磁碟名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁碟上的資料類型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 作業系統磁碟
DB-Disk1 (已排除磁碟不要保護) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

來源 VM 上的分頁檔設定︰

![啟用複寫](./media/site-recovery-exclude-disk/pagefile-on-d-drive-sourceVM.png)
    

VM 從 VMware 容錯移轉至 Azure，或從 Hyper-V 容錯移轉至 Azure 之後，Azure VM 上的磁碟︰
**磁碟名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁碟上的資料類型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 作業系統磁碟
DB-Disk1 | Disk1 | D:\ | 暫存儲存體 –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

由於已排除 Disk1 (D:)，D: 是可用清單中的第一個磁碟機代號，Azure 會將 D: 字母指派給暫時存放磁碟區。  由於 D: 在 Azure VM 上可用，VM 的分頁檔設定維持不變。

Azure VM 上的分頁檔設定︰

![啟用複寫](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

####<a name="case-2-pagefile-file-is-configured-on-any-other-driveother-than-d-drive"></a>案例 2︰在其他任何磁碟機 (非 D: 磁碟機) 上設定分頁檔

來源 VM 磁碟設定︰

**磁碟名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁碟上的資料類型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 作業系統磁碟
DB-Disk1 (已排除磁碟不要保護) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

內部部署 VM 上的分頁檔設定︰

![啟用複寫](./media/site-recovery-exclude-disk/pagefile-on-g-drive-sourceVM.png)

VM 從 VMware/Hyper-V 容錯移轉至 Azure 之後，Azure VM 上的磁碟︰

**磁碟名稱**| **客體 OS 磁碟#**| **磁碟機代號** | **磁碟上的資料類型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |作業系統磁碟
DB-Disk1 | Disk1 | D:\ | 暫存儲存體 –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

由於 D: 是清單中可用的第一個磁碟機代號，Azure 會將 D: 指派給暫時存放磁碟區。 對於所有複寫的磁碟，磁碟機代號維持不變。 因為沒有磁碟 G: 可用，系統會使用 C: 磁碟機給分頁檔使用。

Azure VM 上的分頁檔設定︰

![啟用複寫](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>後續步驟
在您的部署設定完成並開始執行之後，請 [深入了解](site-recovery-failover.md) 不同類型的容錯移轉。

