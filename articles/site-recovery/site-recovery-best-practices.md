---
title: "Azure Site Recovery 最佳做法 | Microsoft Docs"
description: "本文說明 Azure Site Recovery 部署的最佳做法"
services: site-recovery
documentationCenter: 
author: rayne-wiselman"
manager: cfreeman
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-support-matrix-to-azure
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a53e2a09fb2aabe96dd5347ea7509815d92bfdb8
ms.contentlocale: zh-tw
ms.lasthandoff: 03/15/2017

---

# <a name="get-ready-to-deploy-azure-site-recovery"></a>準備好部署 Azure Site Recovery

本文說明如何準備 Azure Site Recovery 部署。

## <a name="protecting-hyper-v-virtual-machines"></a>保護 Hyper-V 虛擬機器

您有一些保護 Hyper-V 虛擬機器的部署選項。 您可以將內部部署 Hyper-V VM 複寫至 Azure，或次要資料中心。 每個部署有不同的需求。

**需求** | **複寫至 Azure (具有 VMM)** | **將 Hyper-V VM 複寫至 Azure (沒有 VMM)** | **將 Hyper-V VM 複寫至次要網站 (具有 VMM)** | **詳細資料**
---|---|---|---|---
**VMM** | 在 System Center 2012 R2 上執行的 VMM <br/><br/>至少一個 VMM 雲端，其中包含一或多個 VMM 主機群組。 | NA | 至少在具有最新更新的 System Center 2012 SP1 上執行的主要和次要網站中的 VMM 伺服器。 <br/><br/> 每個 VMM 伺服器上至少一個雲端。 雲端應該設定 Hyper-V 容量設定檔集。<br/><br/> 來源雲端應該有至少一個 VMM 主機群組。 | 選用。 您不需要部署 System Center VMM，即可將 Hyper-V 虛擬機器複寫至 Azure，但是如果必須部署，請務必確定已正確設定 VMM 伺服器。 這包括確認您正在執行正確的 VMM 版本，且已設定雲端。
**Hyper-V** | 內部部署網站中至少有一部 Hyper-V 主機伺服器執行 Windows Server 2012 R2 或更新版本 | 來源和目標網站中，至少有一部 Hyper-V 伺服器執行已安裝最新更新的 Windows Server 2012，並且連線至網際網路。<br/><br/> Hyper-V 伺服器必須位於 VMM 雲端中的主機群組。 | 來源和目標網站中，至少有一部 Hyper-V 伺服器執行已安裝最新更新的 Windows Server 2012，並且連線至網際網路。<br/><br/> Hyper-V 伺服器必須位於 VMM 雲端中的主機群組。 |
**虛擬機器** | 來源 Hyper-V 主機伺服器上至少有一個 VM | 來源 VMM 雲端中的 Hyper-V 主機伺服器上至少有一個 VM | 來源 VMM 雲端中的 Hyper-V 主機伺服器上至少有一個 VM。 |  複寫至 Azure 的 VM 必須符合 Azure 虛擬機器必要條件
**Azure 帳戶** | 您會需要 Azure 帳戶和 Site Recovery 服務的訂用帳戶。 | 您會需要 Azure 帳戶和 Site Recovery 服務的訂用帳戶。 | NA | 如果您沒有帳戶，請先免費試用。
**Azure 儲存體** | 您會需要已啟用異地複寫之 Azure 儲存體帳戶的訂用帳戶。 | 您會需要已啟用異地複寫之 Azure 儲存體帳戶的訂用帳戶。 | NA | 此帳戶應該與 Azure Site Recovery 保存庫位於相同的區域，並與同一個訂用帳戶建立關聯。
**網路功能** | 設定網路對應，以確定無論使用哪個復原計劃，在相同 Azure 網路上容錯移轉的所有機器都能彼此連接。 此外，如果目標 Azure 網路上已設定網路閘道，則虛擬機器可以連接到其他內部部署虛擬機器。 如果您未設定網路對應，則只能連接在相同復原方案中容錯移轉的機器。 | NA |  <br/><br/>設定網路對應，以確定在容錯移轉之後虛擬機器會連線至適當的網路，且複本虛擬機器位於最佳的 Hyper-V 主機伺服器。 如果您沒有設定網路對應，則複寫的機器將不會在容錯移轉之後連接到任何 VM 網路。 |  若要使用 VMM 設定網路對應，您必須確定該 VMM 邏輯和 VM 網路是否正確設定。
**提供者和代理程式** | 在部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery 提供者。 您會在 VMM 雲端的 Hyper-V 伺服器上安裝 Azure 復原服務代理程式。 | 在部署期間，您會在 Hyper-V 主機伺服器或叢集上，安裝 Azure Site Recovery 提供者和 Azure 復原服務代理程式。| 在部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery 提供者。 您會在 VMM 雲端的 Hyper-V 伺服器上安裝 Azure 復原服務代理程式。 | 提供者和代理程式會使用加密的 HTTPS 連線，透過網際網路連接至 Site Recovery。 您不需要新增防火牆例外或為提供者連線建立特定 Proxy。
**網際網路連線** | 只有 VMM 伺服器才需要網際網路連線 | 只有 Hyper-V 主機伺服器才需要網際網路連線 | 只有 VMM 伺服器才需要網際網路連線 | 虛擬機器不需要安裝任何項目，且不會直接連線到網際網路。



## <a name="protect-vmware-virtual-machines-or-physical-servers"></a>保護 VMware 虛擬機器或實體伺服器

您有一些保護 VMware 虛擬機器或 Windows/Linux 實體伺服器的部署選項。 您可以將它們複寫至 Azure，或次要資料中心。 每個部署有不同的需求。

**需求** | **將 VMware VM/實體伺服器複寫至 Azure** | *將 VMware VM/實體伺服器複寫至次要網站**  
---|---|---
**主要網站** | **處理序伺服器**：專用的 Windows 伺服器 (實體或虛擬) | **處理序伺服器**：專用的 Windows 伺服器 (實體或 VMware 虛擬機器<br/><br/>  
**次要內部部署網站** | NA | **組態伺服器**：專用的 Windows 伺服器 (實體或虛擬) <br/><br/> **主要目標伺服器**：專用伺服器 (實體或虛擬)。 以 Windows 設定來保護 Windows 機器，或以 Linux 設定來保護 Linux 機器。
**Azure** | **訂用帳戶**︰您需要 Site Recovery 服務的訂用帳戶。 <br/><br/> **儲存體帳戶**：您需要已啟用異地複寫的儲存體帳戶。 此帳戶應該與 Site Recovery 保存庫位於相同的區域，並與同一個訂用帳戶建立關聯。 <br/><br/> **組態伺服器**︰您必須將組態伺服器設定為 Azure VM <br/><br/> **主要目標伺服器**︰您必須將主要目標伺服器設定為 Azure VM <br/><br/> 以 Windows 設定來保護 Windows 機器，或以 Linux 設定來保護 Linux 機器。<br/><br/> **Azure 虛擬網路**：您需要 Azure 虛擬網路以部署設定伺服器與主要目標伺服器。 它應該與 Azure Site Recovery 保存庫位於相同的訂用帳戶和區域中 | NA  
**虛擬機器/實體伺服器** | 至少一部 VMware 虛擬機器或實體 Windows/Linux 伺服器。<br/><br/>部署期間會在每部機器上安裝行動服務| 至少一個 VMware VM 或實體 Windows/Linux 伺服器。<br/><br/> 在部署期間於每部機器上安裝整合代理程式。




## <a name="azure-virtual-machine-requirements"></a>Azure 虛擬機器需求

您可以部署 Site Recovery 以複寫執行受 Azure 支援的任何作業系統的虛擬機器和實體伺服器。 這包括大部分的 Windows 和 Linux 版本。 您必須確認想要保護的內部部署虛擬機器均符合 Azure 要求。


## <a name="optimizing-your-deployment"></a>最佳化您的部署

使用下列秘訣可協助您最佳化並調整您的部署。

- **作業系統磁碟區大小**：當您將虛擬機器複寫到 Azure 時，作業系統磁碟區必須小於 1TB。 如果磁碟區大小超過此值，您可以在開始部署之前，手動將磁碟區移動至不同的磁碟。
- 資料磁碟大小：如果複寫至 Azure，您可以在虛擬機器上擁有最多 32 個資料磁碟，每個資料磁碟的上限為 1 TB。 您可以有效地複寫及容錯移轉最多&32; TB 的虛擬機器。
- 復原方案限制：Site Recovery 可擴充至數千個虛擬機器。 復原方案是設計做為應用程式的模型，而由於應用程式應該一起進行容錯移轉，因此我們將一個復原方案中的機器數目限制為 50。
- **Azure 服務限制**：每個 Azure 訂用帳戶均隨附一組對核心、雲端服務等的預設限制。我們建議您執行容錯移轉測試，以驗證您訂用帳戶中的資源可用性。 您可以透過 Azure 支援修改這些限制。
- **容量規劃**︰規劃大小調整和效能。
- 複寫頻寬：如果您的複寫頻寬不足，請注意：
    - **ExpressRoute**：Site Recovery 可搭配 Azure ExpressRoute 和 WAN 最佳化工具，例如 Riverbed。
    - 複寫流量：Site Recovery 只會使用資料區塊 (而非整個 VHD) 來執行智慧型初始複寫。 只會在複寫進行期間複寫變更。
    - **網路流量**：您可以根據目的地 IP 位址和連接埠，以原則設定 Windows QoS，藉此控制用於複寫的網路流量。  此外，如果您使用 Azure 備份代理程式複寫至 Azure Site Recovery， 則您可以設定該代理程式的節流。
- **RTO**：如果您想要測量可以預期的 Site Recovery 復原時間目標 (RTO)，我們建議您執行容錯移轉測試並檢視 Site Recovery 工作，來分析需要多少時間才能完成作業。 如果您容錯移轉至 Azure，我們建議您藉由整合 Azure 自動化和復原方案，將所有的手動操作自動化，來取得最佳的 RTO。
- **RPO**：當您複寫至 Azure 時，Site Recovery 支援幾乎同步的復原點目標 (RPO)。 這是假設您的資料中心與 Azure 之間有足夠的頻寬。

