---
title: "準備 Site Recovery 部署 | Microsoft Docs"
description: "本文說明如何準備利用 Azure Site Recovery 部署複寫。"
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
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5ed46a6bd8931d3b8829a053ace12fd219f108c4
ms.openlocfilehash: 6430ae9a97888ef993cd3dd35eea765fe95bb43f

---

# <a name="prepare-for-azure-site-recovery-deployment"></a>準備 Azure Site Recovery 部署

組織需要 BCDR 策略，以決定應用程式、工作負載和資料如何在規劃與未規劃停機期間維持運作，並儘速復原到正常運作的情況。 Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。 當您的主要位置發生故障時，您容錯移轉至次要位置，讓應用程式和工作負載保持可用。 當它恢復正常作業時，容錯回復至您的主要位置。 深入了解 [什麼是 Site Recovery？](site-recovery-overview.md)

本文摘要說明每個 Site Recovery 複寫案例的高階需求，並提供詳細部署逐步解說的連結。  

在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。



## <a name="azure-deployment-models"></a>Azure 部署模型

Azure 有兩種不同的[部署模型](../azure-resource-manager/resource-manager-deployment-model.md) 來建立和使用資源：Azure Resource Manager 模型和傳統模型。 Azure 也有兩個入口網站 – 支援傳統部署模型的 [Azure 傳統入口網站](https://manage.windowsazure.com/)，以及同時支援兩種部署模型的 [Azure 入口網站](https://ms.portal.azure.com/)。

新的站台復原案例應該部署在 [Azure 入口網站](https://portal.azure.com)中。 此入口網站提供新的功能和增強的部署經驗。 保存庫可保存在傳統入口網站，但無法建立新的保存庫。


## <a name="deployment-scenarios"></a>部署案例

以下是您可以使用 Site Recovery 複寫的項目。

| **部署** | **詳細資料** | **Azure 入口網站** | **傳統入口網站** | **PowerShell 部署** |
| --- | --- | --- | --- | --- |
| [VMware 至 Azure](site-recovery-vmware-to-azure.md) | 將內部部署 VMware VM 複寫至 Azure 儲存體 | 使用 Resource Manager 或傳統儲存體和網路。 容錯移轉至 Resource Manager 為基礎或傳統 VM。 | 僅限使用維護模式。 無法建立新的保存庫。 | 目前不支援。 |
| [實體伺服器至 Azure](site-recovery-vmware-to-vmware.md) | 將實體 Windows/Linux 伺服器複寫至 Azure 儲存體 | 使用 Resource Manager 或傳統儲存體和網路。 容錯移轉至 Resource Manager 為基礎或傳統 VM。 | 僅限使用維護模式。 無法建立新的保存庫。  |
| [VMM 雲端中的 Hyper-V VM 至 Azure](site-recovery-vmm-to-azure.md) | 將 VMM 雲端中的內部部署 Hyper-V VM 複寫至 Azure 儲存體。<br/><br/> | 使用 Resource Manager 或傳統儲存體和網路。 容錯移轉至 Resource Manager 為基礎或傳統 VM。   | 僅限使用維護模式。 無法建立新的保存庫。 | 支援 |
| [Hyper-V VM 至 Azure (沒有 VMM)](site-recovery-hyper-v-site-to-azure.md) | 將內部部署 Hyper-V VM 複寫至 Azure 儲存體。 | 使用 Resource Manager 或傳統儲存體和網路。 容錯移轉至 Resource Manager 為基礎或傳統 VM。 | 僅限使用維護模式。 無法建立新的保存庫。 | 支援 |
| VMware VM/實體伺服器至次要網站 (site-recovery-vmware-to-vmware.md) | 將 VMware VM 或實體 Windows/Linux 伺服器複寫至次要站台。<br/><br/> [下載說明指南](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) InMage Scout 使用者指南。 | Azure 入口網站中未提供 | 從 Site Recovery 保存庫下載 InMage Scout。 | 不支援 |
| [Hyper-V VM 至次要站台](site-recovery-vmm-to-vmm.md) | 將 VMM 雲端中的 Hyper-V VM 複寫至次要雲端  | 複寫會使用標準的 HYPER-V 複本。 SAN 不支援此做法。 | 使用 Hyper-V 複本或 [SAN 複寫](site-recovery-vmm-san.md)進行複寫 | 支援 |

## <a name="requirements-for-replication-to-azure"></a>複寫至 Azure 的需求

| **需求** | **詳細資料** |
| --- | --- |
| **Azure 帳戶** | [Microsoft Azure](http://azure.microsoft.com/) 帳戶。<br/><br/> 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。 |
| **Azure 儲存體** | 複寫的資料會儲存在 Azure 儲存體，而在發生容錯移轉時會建立 Azure VM。 您需要 [Azure 儲存體帳戶](../storage/storage-introduction.md)。<br/><br/>在 Azure 入口網站中使用 [GRS](../storage/storage-redundancy.md#geo-redundant-storage) 或 LRS 儲存體。 傳統的入口網站只支援 GRS。<br/><br/> 如果您在 Azure 入口網站中複寫 VMware VM 或實體伺服器，則支援進階儲存體。<br/><br/>  |
| **Azure 網路** | 您需要 Azure VM 進行連接的 Azure 網路 <br/><br/> 在 Azure 入口網站中，可以使用傳統網路或 Resource Manager 網路。<br/><br/> 此網路必須位於與保存庫相同的區域中。<br/><br/> 如果您從 VMM 複寫至 Azure，您將設定 VMM VM 網路與 Azure 網路之間的[網路對應](site-recovery-network-mapping.md)，以確保 Azure VM 在容錯移轉之後連接到適當的網路。 |
| **內部部署** |**VMware VM**︰執行 Site Recovery 元件的內部部署電腦。 此外，您需要 VMware vSphere 主機/vCenter 伺服器和您要複寫的 VM。 [深入了解](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites)。<br/><br/> **實體伺服器**︰執行 Site Recovery 元件的內部部署電腦，以及您想要複寫的實體伺服器。 [深入了解](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites)。 如果您想要在從 Azure [容錯回復](site-recovery-failback-azure-to-vmware.md)實體伺服器，您將需要有 VMware 基礎結構，才能這麼做。<br/><br/> **Hyper-V VM**：VMM 伺服器和包含您想要複寫的 VM 之 Hyper-V 主機。 [深入了解](site-recovery-vmm-to-azure.md#on-premises-prerequisites)。<br/><br/> 若要複寫沒有 VMM 的 Hyper-V VM，您僅需要 Hyper-V 主機。 [深入了解](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites)。 |
| **受保護的機器** | 將複寫至 Azure 的受保護機器必須符合下述的 [Azure 必要條件](#azure-virtual-machine-requirements) 。 |

## <a name="requirements-for-replication-to-a-secondary-site"></a>複寫至次要站台的需求

| **需求** | **詳細資料** |
| --- | --- |
| **Azure** | [Microsoft Azure](http://azure.microsoft.com/) 帳戶。<br/><br/> 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。 |
| **內部部署** |**VMware VM**：在主要站台中，您將需要一部處理序伺服器，以便將複寫資料快取、壓縮及加密。 在次要站台中，您需要一部組態伺服器，以便管理及監視部署和主要目標伺服器。 為了更方便管理，建議您也安裝一部 vContinuum 伺服器。 此外，您還需要一或多個 VMware vSphere 主機，並可選擇是否要有一部 vCenter 伺服器。 [深入了解](site-recovery-vmware-to-vmware.md)<br/><br/> **VMM 雲端中的 Hyper-V VM**︰VMM 伺服器，以及包含您想要複寫之 VM 的 Hyper-V 主機。 [深入了解](site-recovery-vmm-to-vmm.md#on-premises-prerequisites)。 |
| **網路 (VMM 至 VMM)** | 如果您從 VMM 複寫至 VMM，[網路對應](site-recovery-network-mapping.md)可確保在容錯移轉之後，複本 VM 會連線到適當的網路，並且會以最佳方式置於 Hyper-V 主機上。 |

## <a name="url-access"></a>URL 存取

這些 URL 應可透過 VMware、VMM 和 Hyper-V 主機伺服器提供。

**URL** | **VMM 至 VMM** | **VMM 至 Azure** | **Hyper-V 至 Azure** | **VMware 至 Azure** |
|--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | 允許 | 允許 | 允許 | 允許
``*.backup.windowsazure.com`` | 不需要 | 允許 | 允許 | 允許
``*.hypervrecoverymanager.windowsazure.com`` | 允許 | 允許 | 允許 | 允許
``*.store.core.windows.net`` | 允許 | 允許 | 允許 | 允許
``*.blob.core.windows.net`` | 不需要 | 允許 | 允許 | 允許
``https://www.msftncsi.com/ncsi.txt`` | 允許 | 允許 | 允許 | 允許
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 不需要 | 不需要 | 不需要 | 允許 SQL 下載
``time.windows.com`` | 允許 | 允許 | 允許 | 允許
``time.nist.gov`` | 允許 | 允許 | 允許 | 允許



## <a name="azure-virtual-machine-requirements"></a>Azure 虛擬機器需求

您可以部署 Site Recovery 以複寫執行受 Azure 支援的任何作業系統的虛擬機器和實體伺服器。 這包括大部分的 Windows 和 Linux 版本。 您想要複寫的內部部署虛擬機器必須符合 Azure 要求。

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
**VM type** | 第 1 代<br/><br/> 第 2 代 - Windows | OS 磁碟基本類型的第 2 代 VM，其中包含一或兩個格式化為支援 VHDX 的資料磁碟區且支援低於 300 GB。<br/><br/>]。 不支援 Linux 第 2 代 VM。 [深入了解](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

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
* [利用單一 VMM 伺服器複寫 Hyper-V VM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


