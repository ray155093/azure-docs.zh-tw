---
title: "如何從 Azure 容錯回復至 Hyper-v | Microsoft Docs"
description: "將 VM 容錯移轉到 Azure 之後，您可以起始將 VM 容錯回復到內部部署的作業。 了解執行容錯回復的步驟。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: bedb59414ed328fe2fcca2b09f3360a98c98ff60
ms.openlocfilehash: b4bdd27db343bfb4a0e47c432f726ff454f7f55a
ms.lasthandoff: 02/23/2017


---
# <a name="failback-from-azure-to-on-premises"></a>從 Azure 容錯回復至內部部署
此文章說明如何將 Azure 虛擬機器從 Azure 容錯回復到內部部署網站。 當您準備好使用此[教學課程](site-recovery-vmware-to-azure-classic.md)，將已從內部部署網站容錯移轉至 Azure 的 VMware 虛擬機器或 Windows/Linux 實體伺服器容錯回復時，請依照此文章中的指示執行。

## <a name="overview-of-failback"></a>容錯回復的概觀
以下是容錯回復的運作方式 - 在容錯移轉至 Azure 之後，您可以透過幾個階段來容錯回復至內部部署網站：

1. [重新保護](site-recovery-how-to-reprotect.md) Azure VM，使其開始複寫回在內部部署網站中執行的 VMware VM。 對於此動作，您也需要 
    1. 設定內部部署主要目標 - Windows VM 的 Windows MT 和 Linux VM 的 [Linux MT](site-recovery-how-to-install-linux-master-target.md)
    2. 設定[處理序伺服器](site-recovery-vmware-setup-azure-ps-resource-manager.md)
    3. 接著，起始[重新保護](site-recovery-how-to-reprotect.md)
5. 在 Azure VM 複寫至內部部署網站後，您可以起始從 Azure 容錯移轉至內部部署。
  
容錯回復資料之後，您必須重新保護已容錯回復到的內部部署 VM，使其開始複寫至 Azure。

如需觀看快速的視訊概觀，您也可以播放這裡的視訊。
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="failback-to-the-original-or-alternate-location"></a>容錯回復到原始位置或替代位置
    
如果您已容錯移轉 VMware VM，在它仍存在於內部部署環境的前提下，您可以將它容錯回復到相同的來源 VM。 在此案例中，只會複寫差異變更。 此案例稱為原始位置復原。 如果內部部署 VM 不存在，則是替代位置復原。

#### <a name="original-location-recovery"></a>原始位置復原

如果您要容錯回復到原始 VM，則必須符合下列條件：
* 如果 VM 是由 vCenter 伺服器管理，主要目標的 ESX 主機應該可以存取 VM 資料存放區。
* 如果 VM 位於 ESX 主機上，但不受 vCenter 管理，則 VM 的硬碟必須位於可由主要目標主機存取的資料存放區內。
* 如果 VM 位於 ESX 主機上，但未使用 vCenter，則應該先完成主要目標 ESX 主機的探索後，才能重新保護。 如果您要容錯回復實體伺服器，則也適用這一條件。
* 如果磁碟已存在且連線至內部部署 VM，您可以容錯回復至 vSAN 或 RDM 磁碟。

#### <a name="alternate-location-recovery"></a>替代位置復原
在重新保護 VM 之前，如果內部部署 VM 不存在，則稱為替代位置復原。 在這裡，重新保護工作流程會重新建立內部部署 VM。 這也會導致下載完整資料。

* 當您容錯回復至替代位置時，VM 將會復原到部署主要目標伺服器所使用的相同 ESX 主機上。 用於建立磁碟的資料存放區將會是重新保護 VM 時選取的相同資料存放區。
* 您只能容錯回復到 VMFS 資料存放區。 若您有 vSAN 或 RDM，重新保護與容錯回復將無法運作。
* 重新保護牽涉到一個大型的初始資料傳輸，後面接著差異變更。 這是因為 VM 不存在於內部部署環境中，而系統必須將完整資料複寫回來。 此重新保護作業也需要比原始位置復原更長的時間。
* 您無法容錯回復至 vSAN 或 RDM 磁碟。 在 VMFS 資料存放區上只能建立新的 VMDK。

將實體機器容錯回復到 Azure 時，只能容錯回復為 VMware 虛擬機器 (亦稱為 P2A2V)。 此流程是屬於替代位置復原的範圍。

* Windows Server 2008 R2 SP1 電腦如果受保護且容錯移轉至 Azure，則無法容錯回復。
* 請確認您連同需要容錯回復的必要 ESX/ESXi 主機，有探索至少一部主要目標伺服器。

## <a name="have-you-completed-reprotection"></a>您已完成重新保護嗎？
進一步執行之前，請先完成重新保護步驟，使虛擬機器處於已複寫狀態，這樣才能起始容錯移轉回到內部部署。
[如何從 Azure 移至內部部署來重新保護](site-recovery-how-to-reprotect.md)。

## <a name="pre-requisites"></a>必要條件
 
* 執行容錯回復時，組態伺服器必須為內部部署。 容錯回復期間，虛擬機器必須存在於組態伺服器資料庫中，而失敗的容錯回復將不會成功。 因此，請確定您定期排定備份伺服器。 發生災害時，您需要使用相同的 IP 位址進行還原，讓容錯回復運作。
* 觸發容錯回復之前，主要目標伺服器不能有任何快照集。

## <a name="steps-to-failback"></a>容錯回復步驟

起始容錯回復之前，**確定您已完成虛擬機器的重新保護**。 虛擬機器應該處於受保護狀態，且其健康狀態必須是「良好」。 若要重新保護虛擬機器，請參閱[如何重新保護](site-recovery-how-to-reprotect.md)。

1. 在 [複製的項目] 頁面中，請選取虛擬機器，並以滑鼠右鍵按一下以選取 [非計劃性容錯移轉] 。
2. 在 [確認容錯移轉]  中，確認容錯移轉方向 (以 Azure 為來源)，並選取您想要用來進行容錯移轉的復原點 (最近的復原點或最近的應用程式一致復原點)。 應用程式一致復原點會在最近的復原點之後，並造成部分資料遺失。
3. 在容錯移轉期間，Site Recovery 會關閉 Azure VM。 在確認容錯回復已如預期完成後，您可以確認 Azure VM 是否已關閉。

### <a name="to-what-recovery-point-can-i-failback-the-vms-to"></a>我可以將 VM 容錯回復至哪個復原點？

在容錯回復期間，有兩個選項讓您容錯回復 VM/復原方案。

如果您選取最近處理的時間點，所有虛擬機器會容錯移轉至其最新可用的時間點。 如果復原方案內有複寫群組，則複寫群組的每個 VM 會容錯移轉至其獨自的最新時間點。

VM 至少要有一個復原點才能容錯回復。 復原方案的所有 VM 至少要有一個復原點，您才能容錯回復此復原方案。

> [!NOTE]
> 最新復原點是損毀一致復原點。

如果您選取應用程式一致復原點，單一 VM 容錯回復會復原至其最新可用的應用程式一致復原點。 如果復原方案有複寫群組，每個複寫群組會復原至其一般可用的復原點。
請注意，應用程式一致復原點的時間會落後，可能會遺失資料。

## <a name="next-steps"></a>後續步驟

容錯回復完成之後，您必須認可虛擬機器，以確保會刪除在 Azure 中復原的 VM。

### <a name="commit"></a>認可
需要認可，以便從 Azure 移除容錯移轉的虛擬機器。
在受保護的項目上按一下滑鼠右鍵，然後按一下 [認可]。 這將會觸發一個移除在 Azure 中已容錯移轉之虛擬機器的作業。

### <a name="reprotect-from-on-premises-to-azure"></a>從內部部署移至 Azure 來重新保護

在認可完成後，內部部署網站上的 VM 會恢復，但不會受到保護。 若要再次啟動複寫至 Azure，請執行下列動作：

1. 在 [保存庫] > [設定] > [複寫項目] 中，選取具有容錯回復的 VM，按一下 [重新保護]。
2. 提供必須用來將資料傳送回 Azure 之處理伺服器的值。
3. 按一下 [確定] 開始重新保護工作。

> [!NOTE]
> 在內部部署啟動 VM 之後，需要經過一些時間，代理程式才會註冊回到組態伺服器中 (最多 15 分鐘)。 在這段時間，您會發現重新保護失敗，錯誤訊息會指出未安裝代理程式。 請等候幾分鐘，然後再次嘗試重新保護。

重新保護作業完成之後，VM 會複寫回 Azure，而且您可以執行容錯移轉。

## <a name="common-issues"></a>常見問題
* 執行容錯回復之前，請確定 vCenter 處於已連線狀態，否則，中斷連線磁碟再連結回到 VM 會失敗。


