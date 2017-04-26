---
title: "將 Hyper-V VM 複寫至 Azure | Microsoft Docs"
description: "說明如何將內部部署 Hyper-V VM 針對 Azure 進行協調複寫、容錯移轉及復原"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 2254b06d37b9090e1ca5e4e7db83e35e732e01a3
ms.lasthandoff: 04/06/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>使用 Azure Site Recovery 搭配 Azure 入口網站將 Hyper-V 虛擬機器 (不使用 VMM) 複寫至 Azure

> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-hyper-v-site-to-azure.md)
> * [Azure 傳統型](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - 資源管理員](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

本文說明如何在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md)，將內部部署 Hyper-V 虛擬機器複寫至 Azure。 在此部署中，Hyper-V VM 並非由 System Center Virtual Machine Manager (VMM) 所管理。

閱讀本文之後，在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。

如果您想要將電腦移轉至 Azure (不含容錯回復)，請在[這篇文章](site-recovery-migrate-to-azure.md)中深入了解。



## <a name="deployment-steps"></a>部署步驟

請依照下列文件完成這些部署步驟︰

1. [深入了解](site-recovery-components.md#hyper-v-to-azure)此部署的架構。 此外，[深入了解](site-recovery-hyper-v-azure-architecture.md) Site Recovery 中 Hyper-V 複寫的運作方式。
2. 確認必要條件和限制。
3. 設定 Azure 網路和儲存體帳戶。
4. 準備 Hyper-V 主機。
5. 建立復原服務保存庫。 保存庫包含組態設定，並協調複寫。
6. 指定來源設定。 建立包含 Hyper-V 主機的 Hyper-V 網站，並在保存庫註冊網站。 安裝 Azure Site Recovery Provider，然後在 Hyper-V 主機上安裝 Microsoft 復原服務代理程式。
7. 設定目標和複寫設定。
8. 啟用 VM 複寫。
9. 執行測試容錯移轉，確定一切都沒問題。



## <a name="prerequisites"></a>必要條件


**需求** | **詳細資料** |
--- | --- |
**Azure** | 了解 [Azure 需求](site-recovery-prereq.md#azure-requirements)。
**內部部署伺服器** | [深入了解](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager)內部部署 Hyper-V 主機的需求。
**內部部署 Hyper-V VM** | 您想要複寫的 VM 應該執行[支援的作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，也要符合 [Azure 必要條件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
**Azure URL** | Hyper-V 主機需要存取這些 URL：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> 如果您有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。<br/></br> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br/></br> 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。



## <a name="prepare-for-deployment"></a>準備部署

若要準備進行部署，您必須︰

1. [設定 Azure 網路](#set-up-an-azure-network) ，這是 Azure VM 於容錯移轉後建立時將所在的網路。
2. [設定 Azure 儲存體帳戶](#set-up-an-azure-storage-account) 。
3. [準備 Hyper-V 主機](#prepare-the-hyper-v-hosts) 以確保它們可以存取所需的 URL。

### <a name="set-up-an-azure-network"></a>設定 Azure 網路

設定 Azure 網路。 您將需要 Azure 網路，以便在容錯移轉後建立的 Azure VM 連接網路。

* 此網路應位於與復原服務保存庫相同的區域。
* 視您想要針對已容錯移轉的 Azure VM 使用的資源模型而定，您會以 [Resource Manager 模式](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)，或[傳統模式](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)設定 Azure 網路。
* 建議您在開始之前先設定網路。 若非如此，則必須在 Site Recovery 部署期間這麼做。
- Site Recovery 所使用的儲存體帳戶不能在相同訂用帳戶內或跨越不同的訂用帳戶[移動](../azure-resource-manager/resource-group-move-resources.md)。


### <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

- 您需要標準/進階 Azure 儲存體帳戶才可將複寫到 Azure 的資料進行保存。[進階儲存體](../storage/storage-premium-storage.md)通常是用於需要持續高 IO 效能和低延遲性以裝載 IO 密集型工作負載的虛擬機器。
- 如果您想要使用進階帳戶來儲存複寫的資料，就也需要標準儲存體帳戶來儲存複寫記錄檔，這些記錄檔會擷取內部部署資料的進行中變更。
- 視您想要針對已容錯移轉的 Azure VM 使用的資源模型而定，您會以 [Resource Manager 模式](../storage/storage-create-storage-account.md)，或[傳統模式](../storage/storage-create-storage-account-classic-portal.md)設定帳戶。
- 建議您在開始之前先設定儲存體帳戶。 若非如此，則必須在 Site Recovery 部署期間這麼做。 帳戶必須位於與復原服務保存庫相同的區域中。
- 您無法跨相同訂用帳戶內的資源群組，或跨越不同訂用帳戶移動 Site Recovery 所使用的儲存體帳戶。


### <a name="prepare-the-hyper-v-hosts"></a>準備 Hyper-V 主機

* 確定 Hyper-V 主機符合[必要條件](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager)。
- 請確定主機可以存取必要的 URL。


## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [新增] > [監視 + 管理] > [備份和 Site Recovery (OMS)]。  

    ![新增保存庫](./media/site-recovery-hyper-v-site-to-azure/new-vault.png)

3. 在 [名稱] 中，指定保存庫的易記識別名稱。 如果您有多個訂用帳戶，請選取其中一個。

4. [建立新的資源群組](../azure-resource-manager/resource-group-template-deploy-portal.md) 或選取現有的資源群組，並指定 Azure 區域。 機器將會複寫到此區域。 若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的＜各地區上市情況＞。

5. 如果您想要從「儀表板」快速存取保存庫，請按一下 [釘選到儀表板]，然後按一下 [建立]。

    ![新增保存庫](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

新的保存庫會出現在 [儀表板] > [所有資源] 清單中，以及主要 [復原服務保存庫] 刀鋒視窗上。



## <a name="select-the-protection-goal"></a>選取保護目標

選取您要複寫的項目以及您要複寫到的位置。

1. 在 [復原服務保存庫] 中選取保存庫。
2. 在 [快速入門] 中，按一下 [Site Recovery] > [準備基礎結構] > [保護目標]。

    ![選擇目標](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)
3. 在 [保護目標] 中，選取 [至 Azure]，然後選取 [是，利用 Hyper-V]。 選取 [否]  以確認您未使用 VMM。 然後按一下 [確定] 。

    ![選擇目標](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>設定來源環境

設定 Hyper-V 網站、在 Hyper-V 主機上安裝 Azure Site Recovery Provider 和 Azure 復原服務代理程式，並在保存庫註冊網站。

1. 在 [準備基礎結構] 中，按一下 [來源]。 若要加入新的 Hyper-V 網站作為 Hyper-V 主機或叢集的容器，請按一下 [+Hyper-V 網站] 。

    ![設定來源](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)
2. 在 [建立 Hyper-V 網站] 中，指定網站的名稱。 然後按一下 [確定] 。 現在，選取您建立的網站，然後按一下 [+Hyper-V Server]，將伺服器新增至網站。

    ![設定來源](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. 在 [新增伺服器] > [伺服器類型] 中，檢查是否已顯示 [Hyper-V 伺服器]。

    - 確定您想要新增的 Hyper-V 伺服器符合[必要條件](#on-premises-prerequisites)，而且能夠存取指定的 URL。
    - 下載 Azure Site Recovery Provider 安裝檔案。 您將執行這個檔案，在每個 Hyper-V 主機上安裝 Provider 和復原服務代理程式。

    ![設定來源](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)


## <a name="install-the-provider-and-agent"></a>安裝 Provider 和代理程式

1. 在您已加入 Hyper-V 網站中的每個主機上執行 Provider 安裝程式檔案。 如果您安裝在 Hyper-V 叢集上，請在每個叢集節點上執行安裝程式。 安裝和註冊每個 Hyper-V 叢集節點，可確保 VM 即使跨節點移轉，都還是會受保護。
2. 在 [Microsoft Update] 中，您可以選擇進行更新，以便根據您的 Microsoft Update 原則安裝 Provider 更新。
3. 在 [安裝] 中接受或修改預設 Provider 安裝位置，然後按一下 [安裝]。
4. 在 [保存庫設定] 中，按一下 [瀏覽] 來選取您已下載的保存庫金鑰檔案。 指定 Azure Site Recovery 訂用帳戶、保存庫名稱，以及 Hyper-V 伺服器所屬的 Hyper-V 網站。

    ![伺服器註冊](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. 在 [Proxy 設定] 中，指定在 Hyper-V 主機上執行的 Provider 要如何透過網際網路連線到 Azure Site Recovery。

    * 如果您想要讓 Provider 直接連線，請選取 [不使用 Proxy 直接連接至 Azure Site Recovery] 。
    * 如果您現有的 Proxy 需要驗證，或您想要使用自訂 Proxy 進行 Provider 連線，請選取 [使用 Proxy 伺服器連線至 Azure Site Recovery]。
    * 如果您使用 Proxy：
        - 請指定位址、連接埠以及認證
        - 請確定已允許[必要條件](#prerequisites)中所述的 URL 通過 Proxy。

    ![網際網路](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. 安裝完成之後，按一下 [註冊] 以在保存庫中註冊該伺服器。

    ![安裝位置](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. 註冊完成之後，Azure Site Recovery 便會抓取來自 Hyper-V 伺服器的中繼資料，而該伺服器會顯示在 [站台復原基礎結構] > [Hyper-V 主機] 中。


## <a name="set-up-the-target-environment"></a>設定目標環境

指定要用於複寫的 Azure 儲存體帳戶，以及 Azure VM 在容錯移轉後會連線的 Azure 網路。

1. 按一下 [準備基礎結構] > [目標]。
2. 選取您想要在容錯移轉後，在其中建立 Azure VM 的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或資源管理) 中，針對 VM 使用的部署模型。

3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

    - 如果您沒有儲存體帳戶，請按一下 [+儲存體]，建立以 Resource Manager 為基礎的內嵌帳戶。 深入了解[儲存體需求](site-recovery-prereq.md#azure-requirements)。
    - 如果您沒有 Azure 網路，請按一下 [+網路]，建立以 Resource Manager 為基礎的內嵌網路。

    ![儲存體](./media/site-recovery-vmware-to-azure/enable-rep3.png)

    


## <a name="configure-replication-settings"></a>設定複寫設定

1. 若要建立新的複寫原則，請按一下 [準備基礎結構] > [複寫設定] > [+建立及關聯]。

    ![網路](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)
2. 在 [建立及關聯原則] 中指定原則名稱。
3. 在 [複製頻率] 中，指定您要在初始複寫後複寫差異資料的頻率 (每隔 30 秒、5 或 15 分鐘)。

    > [!NOTE]
    > 複寫到進階儲存體時，不支援 30 秒的頻率。 限制取決於進階儲存體所支援之每 blob (100) 的快照集數目。 [深入了解](../storage/storage-premium-storage.md#snapshots-and-copy-blob)。

4. 在 [復原點保留] 中，指定每個復原點的保留週期長度 (以小時為單位)。 VM 可以復原到週期內的任意點。
5. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (1-12 小時)。

    - Hyper-V 使用兩種類型的快照，一個是標準快照，提供整個虛擬機器的增量快照，另一個是應用程式一致快照，會建立虛擬機器內應用程式資料的時間點快照。
    - 應用程式一致快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。
    - 如果您啟用應用程式一致快照，它會影響在來源虛擬機器上執行的應用程式效能。 確認您設定的值低於您設定的其他復原點數目。

6. 在 [初始複寫開始時間]  中，指定開始初始複寫的時間。 複寫會透過您的網際網路頻寬發生，所以您可能想將它排程在忙碌時間之外。 然後按一下 [確定] 。

    ![複寫原則](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

當您建立新的原則時，該原則會自動與 Hyper-V 網站產生關聯。 您可以在 [複寫] > 原則名稱 > [關聯 Hyper-V 網站] 中，將 Hyper-V 網站 (及其中的 VM) 與多個複寫原則建立關聯。

## <a name="capacity-planning"></a>容量規劃

您現已設定您的基本基礎結構，您可以思考容量規劃並找出您是否需要額外的資源。

Site Recovery 會提供 Capacity Planner 以協助您為計算、網路及儲存體配置適當的資源。 您可以在快速模式中執行規劃工具，以便根據 VM、磁碟和儲存體的平均數量進行估計，或在詳細模式中執行規劃工具，以包含工作負載層級的自訂數字。 開始之前，您必須︰

* 收集有關複寫環境的資訊，包括 VM、每個 VM 的磁碟和每個磁碟的儲存體。
* 估計複寫資料的每日變更 (流失) 率。 您可以使用 [Capacity Planner for Hyper-V Replica (適用於 Hyper-V 複本的 Capacity Planner)](https://www.microsoft.com/download/details.aspx?id=39057) 來協助您執行這項操作。

1. 按一下 [下載] 來下載此工具並加以執行。 [文章](site-recovery-capacity-planner.md) 。
2. 當您完成時，請在 [是否已執行 Capacity Planner?]中選取 [是]。

   ![容量規劃](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

深入了解[控制網路頻寬](#network-bandwidth-considerations)



## <a name="enable-replication"></a>啟用複寫

啟用 VM 複寫，如下所示︰          

1. 按一下 [複寫應用程式] > [來源]。 第一次設定複寫之後，您可以按一下 [+複寫]，以對其他電腦啟用複寫。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)
2. 在 [來源] 中，選取 Hyper-V 網站。 然後按一下 [確定] 。
3. 在 [目標] 中，選取保存庫訂用帳戶，以及您想要在容錯移轉後於 Azure 中使用的容錯移轉模型 (傳統或資源管理)。
4. 選取您要使用的儲存體帳戶。 如果您沒有想要使用的帳戶，您可以[建立一個](#set-up-an-azure-storage-account)。 然後按一下 [確定] 。
5. 選取 Azure VM 在容錯移轉後啟動時所要連線的 Azure 網路和子網路。

    - 若要將網路設定套用至您啟用要進行複寫的所有電腦，請選取 [立即設定選取的機器]。
    - 選取 [稍後設定] 以選取每部機器的 Azure 網路。
    - 如果您沒有想要使用的網路，您可以[建立一個](#set-up-an-azure-network)。 選取適用的子網路。 然後按一下 [確定] 。

   ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. 在 [虛擬機器] > [選取虛擬機器] 中，按一下並選取您要複寫的每部機器。 您只能選取可以啟用複寫的機器。 然後按一下 [確定] 。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication5-for-exclude-disk.png)

7. 在 [名稱]  > 中，為選取的 VM 選取作業系統，以及 OS 磁碟。
8. 確認 Azure VM 名稱 (目標名稱) 符合 [Azure 虛擬機器需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
9. 依預設會選取 VM 的所有磁碟以進行複寫，但您可以將磁碟清除以排除它們。
    - 您可能想要排除磁碟來減少複寫頻寬。 例如，您可能不想要複寫具有暫存資料的磁碟，或是每次電腦或應用程式重新啟動時便重新整理的資料 (例如 pagefile.sys 或 Microsoft SQL Server tempdb)。 您可以取消選取磁碟以將磁碟排除複寫。
    - 只有基本磁碟可以排除。 您無法排除作業系統磁碟。
    - 我們建議不要排除動態磁碟。 Site Recovery 無法識別客體 VM 內的虛擬硬碟為基本還是動態磁碟。 如果未排除所有的相依動態磁碟區磁碟，受保護的動態磁碟會在 VM 容錯移轉時顯示為失敗的磁碟，且該磁碟上的資料無法存取。
        - 啟用複寫後，您無法加入或移除複寫的磁碟。 如果您想要新增或排除磁碟，必須停用 VM 的保護，然後重新啟用它。
        - 您以手動方式在 Azure 中建立的磁碟將不會容錯回復。 例如，如果您容錯移轉三個磁碟，並直接在 Azure VM 中建立兩個磁碟，則只有那三個容錯移轉的磁碟會從 Azure 容錯回復至 Hyper-V。 您無法在容錯回復，或是從 Hyper-V 至 Azure 的反向複寫中包含手動建立的磁碟。
        - 如果您排除應用程式運作所需的磁碟，在容錯移轉至 Azure 之後，您必須在 Azure 中手動建立它，複寫的應用程式才能執行。 或者，您可以將 Azure 自動化整合至復原計畫，在電腦容錯移轉期間建立磁碟。

10. 按一下 [確定] 儲存變更。 您可以稍後再設定其他屬性。

     ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication6-with-exclude-disk.png)

11. 在 [複寫設定]  >  [進行複寫設定] 中，選取您要套用於受保護 VM 的複寫原則。 然後按一下 [確定] 。 您可以在 [複寫原則] > 原則名稱 > [編輯設定] 中，修改複寫原則。 您套用的變更將用於已在複寫的機器和新的機器。


   ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

您可以在 [作業] >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 執行 [完成保護]  作業之後，機器即準備好進行容錯移轉。

### <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

建議您確認來源機器的屬性。

1. 在 [受保護的項目]中，按一下 [複寫的項目]，然後選取機器。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)
2. 在 [屬性] 中，您可以檢視 VM 的複寫和容錯移轉資訊。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)
3. 在 [計算和網路] > [計算屬性] 中，您可以指定 Azure VM 名稱和目標大小。 視需要修改名稱以符合 Azure 需求。 您也可以檢視和修改目標網路、子網路的相關資訊，以及將指派給 Azure VM 的 IP 位址。 請注意：

   * 您可以設定目標 IP 位址。 如果您未提供地址，則容錯移轉的機器會使用 DHCP。 如果您設定無法用於容錯移轉的位址，則容錯移轉會失敗。 如果位址可用於測試容錯移轉網路，則相同的目標 IP 位址可用於測試容錯移轉。
   * 網路介面卡的數目會視您指定給目標虛擬機器的大小而有所不同，如下所示：

     * 如果來源電腦上的網路介面卡數目小於或等於針對目標機器大小所允許的介面卡數目，則目標將具備與來源相同的介面卡數目。
     * 如果來源虛擬機器的介面卡數目超過針對目標大小所允許的數目，則將使用目標大小的最大值。
     * 例如，如果來源機器具有兩張網路介面卡，而目標機器大小支援四張，則目標機器將會有兩張介面卡。 如果來源機器具有兩張介面卡，但支援的目標大小僅支援一張，則目標機器將只會有一張介面卡。     
     * 如果 VM 有多張網路介面卡，則全部會連接至相同的網路。
     * 如果虛擬機器具有多個網路介面卡，則清單中顯示的第一個會變成 Azure 虛擬機器中的*預設*網路介面卡。

     ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

4. 在 [磁碟] 中，您可以看見 VM 上將要複寫的作業系統和資料磁碟。




## <a name="test-the-deployment"></a>測試部署

若要測試部署，您可以針對單一虛擬機器執行測試容錯移轉，或執行包含一或多部虛擬機器的復原方案。

### <a name="before-you-start"></a>開始之前

 - 如果您想要在容錯移轉後使用 RDP 連線到 Azure VM，請了解[準備連線](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。
 - 若要完整測試，您需要在測試環境中將 Active Directory 和 DNS 進行複製。 [深入了解](site-recovery-active-directory.md#test-failover-considerations)。

### <a name="run-a-test-failover"></a>執行測試容錯移轉

1. 若要容錯移轉單一機器，請在 [複寫的項目] 中，按一下 VM > [+測試容錯移轉] 圖示。
2. 若要容錯移轉復原方案，請在 [復原方案] 中，以滑鼠右鍵按一下方案 > [測試容錯移轉]。 若要建立復原方案，請[遵循這些指示](site-recovery-create-recovery-plans.md)。
3. 在 [測試容錯移轉] 中，選取 Azure VM 在容錯移轉之後要連接的 Azure 網路。
4. 按一下 [確定]  即可開始容錯移轉。 您可以按一下 VM 以開啟其屬性，或在保存庫名稱 > [作業]  > [Site Recovery 作業] 中的 [測試容錯移轉] 作業上按一下，來追蹤進度。
5. 容錯移轉完成之後，您應該也會看到複本 Azure 機器出現在 Azure 入口網站 > [虛擬機器]中。 您應該確定 VM 為適當的大小、已連接到適當的網路，而且正在執行中。
6. 如果您已準備好在容錯移轉後進行連線，應該能夠連線到 Azure VM。
7. 完成後，在復原方案上按一下 [清除測試容錯移轉]。 在 [記事]  中，記錄並儲存關於測試容錯移轉的任何觀察。 這將刪除在測試容錯移轉期間所建立的虛擬機器。

如需詳細資訊，請參閱[測試容錯移轉至 Azure](site-recovery-test-failover-to-azure.md) 文章。



## <a name="monitor-the-deployment"></a>監視部署

監視 Site Recovery 部署的組態設定、狀態和健康情況︰

1. 按一下保存庫名稱來存取 [基本資訊]  儀表板。 在此儀表板中，您可以追蹤 Site Recovery 作業、複寫狀態、復原方案、伺服器健康情況和事件。  

    ![基本資訊](./media/site-recovery-hyper-v-site-to-azure/essentials.png)
2. 在 [健康情況]  圖格中，您可以監視在過去 24 小時內發生問題的站台伺服器，以及 Site Recovery 引發的事件。 您可以自訂 [基本資訊] 以顯示最適合您的圖格和配置，包括其他 Site Recovery 和備份保存庫的狀態。
3. 您可以在 [複寫的項目]、[復原方案] 和 [Site Recovery 作業] 圖格中管理和監視複寫。 您可以在 [作業]  >  [Site Recovery 作業] 中鑽研作業以了解詳細資訊。

## <a name="command-line-provider-and-agent-installation"></a>命令列 Provider 和代理程式安裝

您也可以使用下列命令列來安裝 Azure Site Recovery Provider 和代理程式。 這個方法可以用來在適用於 Windows Server 2012 R2 的伺服器核心上安裝提供者。

1. 將提供者安裝檔案和註冊金鑰下載至資料夾。 例如 C:\ASR。
2. 在提高權限的命令提示字元中，執行下列命令來擷取 Provider 安裝程式：

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 執行這個命令來安裝元件︰

            C:\ASR> setupdr.exe /i
4. 然後執行下列命令以在保存庫中註冊伺服器：

            CD C:\Program Files\Microsoft Azure Site Recovery Provider\
            C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>

<br/>
其中：

* **/Credentials**：必要參數，用來指定註冊金鑰檔案所在的位置  
* **/FriendlyName**：對於 Hyper-V 主機伺服器名稱的必要參數，該伺服器會出現在 Azure Site Recovery 入口網站中。
* **/proxyAddress**：指定 Proxy 伺服器位址的選用參數。
* **/proxyport** ：指定 Proxy 伺服器連接埠的選用參數。
* **/proxyUsername**：指定 Proxy 使用者名稱 (如果 Proxy 需要驗證) 的選用參數。
* **/proxyPassword**：指定用於驗證 Proxy 伺服器的密碼 (如果 Proxy 需要驗證) 的選用參數。


## <a name="network-bandwidth-considerations"></a>網路頻寬考量
您可以使用 [Hyper-V 容量規劃工具](site-recovery-capacity-planner.md)來計算複寫 (初始複寫，而後是差異複寫) 所需的頻寬。 若要控制複寫所用的頻寬數量，您有幾個選項可用︰

* **節流頻寬**︰複寫至 Azure 的 Hyper-V 流量會經過特定的 Hyper-V 主機。 您可以在主機伺服器上進行頻寬節流。
* **調整頻寬**︰您可以使用幾個登錄機碼來影響用於複寫的頻寬。

### <a name="throttle-bandwidth"></a>節流頻寬
1. 在 Hyper-V 主機伺服器上開啟 Microsoft Azure 備份 MMC 嵌入式管理單元。 根據預設，Microsoft Azure 備份的捷徑位於桌面上或在 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中。
2. 在嵌入式管理單元中，按一下 [變更屬性] 。
3. 在 [節流] 索引標籤上，選取 [啟用備份操作的網際網路頻寬使用節流設定]，然後設定工作和非工作時數的限制。 有效範圍是每秒 512 Kbps 到 102 Mbps。

    ![節流頻寬](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

您也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) Cmdlet 來設定節流。 以下是一個範例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要節流。

### <a name="influence-network-bandwidth"></a>影響網路頻寬
1. 在登錄中瀏覽至 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**。
   * 若要影響複製磁碟上的頻寬流量，請修改 **UploadThreadsPerVM**的值，如果不存在則請建立機碼。
   * 若要影響從 Azure 容錯回復流量的頻寬，請修改 **DownloadThreadsPerVM**的值。
2. 預設值為 4。 在 “overprovisioned” 網路中，這些登錄機碼必須變更自其預設值。 最大值為 32。 監視流量，將此值最佳化。

## <a name="next-steps"></a>後續步驟

在初始複寫完成，且您已測試部署之後，便可以視需要叫用容錯移轉。 [深入了解](site-recovery-failover.md)不同類型的容錯移轉及執行方法。

