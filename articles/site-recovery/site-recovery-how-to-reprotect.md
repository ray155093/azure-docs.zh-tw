---
title: "如何從 Azure 重新保護到內部部署 | Microsoft Docs"
description: "將 VM 容錯移轉到 Azure 之後，您可以起始將 VM 容錯回復到內部部署的作業。 了解在容錯回復之前進行重新保護的步驟。"
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
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: f2da9c4b02f0bb8a93347c05be358516f39e2df0
ms.lasthandoff: 03/09/2017


---
# <a name="reprotect-from-azure-to-on-premises"></a>從 Azure 移至內部部署來重新保護

## <a name="overview"></a>概觀
此文章說明如何將 Azure 虛擬機器從 Azure 重新保護到內部部署網站。 當您準備好使用本 [教學課程](site-recovery-failover.md)，將已從內部部署網站容錯移轉至 Azure 的 VMware 虛擬機器或 Windows/Linux 實體伺服器容錯回復時，請遵循本文中的指示。

完成重新保護且受保護的虛擬機器開始複寫之後，您可以在 VM 上起始容錯回復，以將它們回復到內部部署環境。

在這篇文章下方或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

如需觀看快速的視訊概觀，您也可以播放這裡的視訊。
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

在這裡了解容錯回復的完整程序。

## <a name="pre-requisites"></a>必要條件
以下是準備重新保護之前必須採取的一些先決條件步驟或考量事項。

* 如果您想要容錯回復到的目標 VM 是由 vCenter 伺服器進行管理，您必須確定已擁有在 vCenter 伺服器上探索 VM 的必要權限。 [閱讀更多資訊](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)。
* 如果內部部署 VM 上有快照，重新保護程序將會失敗。 您可以先刪除快照，再進行重新保護。
* 在容錯回復之前，您將需要先建立兩個其他元件：
  * **建立處理伺服器**。 處理伺服器是用於接收 Azure 中的受保護 VM 資料並將該資料傳送到內部部署。 這要求它必須位於處理伺服器與受保護 VM 之間的低延遲網路上。 因此，處理伺服器可以是內部部署 (若您使用快速路由連線) 或位於 Azure (若您使用 VPN)。
  * **建立主要目標伺服器**：主要目標伺服器會接收容錯回復資料。 您在內部部署環境中建立的管理伺服器預設會安裝主要目標伺服器。 不過，您可能必須根據容錯回復流量的大小，另外建立一個用來容錯回復的主要目標伺服器。
        * [Linux VM 需要 Linux 主要目標伺服器](site-recovery-how-to-install-linux-master-target.md)。
        * Windows VM 需要 Windows 主要目標。 您可以重複使用內部部署 PS+MT 機器。
* 執行容錯回復時，組態伺服器必須為內部部署。 容錯回復期間，虛擬機器必須存在於組態伺服器資料庫中，而失敗的容錯回復將不會成功。 因此，請確定您定期排定備份伺服器。 發生災害時，您需要使用相同的 IP 位址進行還原，讓容錯回復運作。
* 確定您在 VMware 中將主要目標 VM 的組態參數設定設為 disk.enableUUID=true。 如果該資料列不存在，請新增一個。 這樣才能提供一致的 UUID 給 VMDK，使其可正確掛接。
* **主要目標伺服器的儲存空間不可被 vMotion**。 這會造成容錯回復失敗。 VM 將不會啟動，因為磁碟將不供其使用。
* 您需要新的磁碟機新增至主要目標伺服器。 此磁碟機稱為保留磁碟機。 新增磁碟並格式化磁碟機。
* [重新保護之前在主要目標上檢查的常見項目](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-master-target)中，列出主要目標的其他必要條件。


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-to-replicate-data-back-to-on-premises"></a>為什麼需要 S2S VPN 或 ExpressRoute，才能將資料複寫回到內部部署？
從內部部署複寫到 Azure 的動作可以透過網際網路或具有公用對等互連的 ExpressRoute 來執行，重新保護和容錯回復要求您必須設定 S2S VPN 以複寫資料。 **必須提供網路，讓 Azure 中已容錯移轉的 VM 可以觸達 (ping) 內部部署組態伺服器**。 您也必須在已容錯移轉之 VM 所屬的 Azure 網路中部署處理伺服器，而且此處理伺服器必須能夠與內部部署設定伺服器通訊。

### <a name="when-should-i-install-a-process-server-in-azure"></a>何時應該在 Azure 中安裝處理序伺服器？


您要重新保護的 Azure VM 會將複寫資料傳送至處理序伺服器。 您應該設定網路，以便能夠從 Azure VM 連線到處理伺服器。

您可以在 Azure 中部署處理伺服器，或使用在容錯移轉期間使用的現有處理伺服器。 要考量的重點是將資料從 Azure VM 傳送到處理伺服器的延遲。

* 若您已設定快速路由，可以使用內部部署處理伺服器來傳送資料。 這是因為 VM 與處理伺服器之間的延遲應該很低。

    ![ExpressRoute 的架構圖](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* 然而，若您只有 S2S VPN，則我們建議您在 Azure 中部署處理伺服器。

    ![VPN 的架構圖](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


請記住，複寫將只會在 S2S VPN 或您快速路由網路的私人對等互連上發生。 請確定該網路通道上有足夠的可用頻寬。

深入了解如何安裝 [Azure 處理序伺服器](site-recovery-vmware-setup-azure-ps-resource-manager.md)。

### <a name="what-are-the-different-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>在不同的元件上要開啟哪些不同的連接埠，重新保護才能運作？

![容錯移轉-容錯回復所有的連接埠](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-to-use-for-reprotect"></a>哪一個主要目標伺服器要用於重新保護？
內部部署環境中必須有主要目標伺服器可接收來自處理伺服器的資料，並接著將資料寫入到內部部署 VM 的 VMDK。 若您要保護 Windows VM，您需要 Windows 主要目標伺服器，您可以在這裡重複使用內部部署處理伺服器+主要目標。<!-- !todo component --> 對於 Linux VM，您將必須設定另一部內部部署 Linux 主要目標。


按下列連結，以了解主要目標伺服器的安裝步驟。

* [如何安裝 Windows 主要目標伺服器](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [如何安裝 Linux 主要目標伺服器](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>完成主要目標安裝後必須檢查的常見項目

* 若 VM 存在於內部部署 vCenter 伺服器上，主要目標伺服器必須能夠存取內部部署 VM 的 VMDK。 這是用來將複寫的資料寫入到 VM 的磁碟。 對於此動作，您必須確定**內部部署 VM 的資料存放區已掛接在主要目標的主機上並已設定讀寫權限**。

* 若 VM 不存在於內部部署 vCenter 伺服器上，您必須在重新保護期間建立新 VM。 您必須在建立主要目標的 ESX 主機上建立此 VM。 因此，請謹慎選擇 ESX 主機，以便在您要的主機上建立容錯回復 VM。

* **主要目標伺服器的儲存空間不可被 vMotion**。 這會造成容錯回復失敗。 VM 將不會啟動，因為磁碟將不供其使用。

* 您必須將新的磁碟機新增至現有的 Windows 主要目標伺服器。 此磁碟機稱為保留磁碟機。 新增磁碟並格式化磁碟機。 「保留磁碟機」是在將 VM 複寫回內部部署時，用來停止時間點。 保留磁碟機的某些準則如下，如果沒有這些準則，磁碟機將不會針對主要目標伺服器列出。

   * 磁碟區不能使用於其他用途 (做為複寫目標等)

   * 磁碟區不能處於鎖定模式。

   * 磁碟區不能是快取磁碟區。 (該磁碟區上不能存在 MT 安裝。 PS+MT 自訂安裝磁碟區不適合做為保留磁碟區。 這裡已安裝的 PS+MT 磁碟區是 MT 的快取磁碟區。)

   * 磁碟區檔案系統類型不能是 FAT 和 FAT32。

   * 磁碟區容量不能為零。

   * Windows 的預設保留磁碟區為 R 磁碟區。

   * Linux 的預設保留磁碟區為 /mnt/retention。

* Linux 已容錯移轉 VM 需要 Linux 主要目標伺服器。 Windows 已容錯移轉 VM 需要 Windows 主要目標伺服器。

* 在主要目標伺服器上安裝 VMware 工具。 若沒有 VMware 工具，將無法偵測主要目標之 ESXi 主機上的資料存放區。

* 透過 vCenter 屬性在主要目標 VM 上啟用 disk.EnableUUID = True 參數。 <!-- !todo Needs link. -->

* 主要目標應該至少連結一個 VMFS 資料存放區。 如果沒有的話，重新保護頁面上的資料存放區輸入會空白，您將無法繼續。

* 主要目標伺服器在磁碟上不能有任何快照集。 如果有快照集，重新保護/容錯回復會失敗。

* MT 不能有 Paravirtual SCSI 控制器。 只能是 LSI Logic 控制器。 如果沒有 LSI Logic 控制器，重新保護會失敗。

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>重新保護的步驟

重新保護之前，請確定您已在 Azure 和內部部署 Windows 或 [Linux 主要目標](site-recovery-how-to-install-linux-master-target.md)中安裝[處理序伺服器](site-recovery-vmware-setup-azure-ps-resource-manager.md)。

> [!NOTE]
> 在 Azure 中啟動 VM 之後，需要經過一些時間，代理程式才會註冊回到組態伺服器中 (最多 15 分鐘)。 在這段時間，您會發現重新保護失敗，錯誤訊息會指出未安裝代理程式。 請等候幾分鐘，然後再次嘗試重新保護。



1. 在 [保存庫] > [複寫項目] > 選取容錯移轉的 VM，並以滑鼠右鍵按一下 [重新保護]。 您也可以按一下機器，並從命令按鈕中選取重新保護。
2. 在刀鋒視窗中，您可以看到已選取保護方向 [Azure 至內部部署]。
3. 在 [主要目標伺服器] 和 [處理伺服器] 中，選取內部部署主要目標伺服器和處理伺服器。
4. 選取您想要將內部部署磁碟復原至該位置的 [資料存放區]  。 當刪除內部部署 VM 且需要建立新的磁碟時會使用此選項。 如果磁碟已存在，則會忽略此選項，但您仍然需要指定一個值。
5. 選擇保留磁碟機。
6. 將會自動選取容錯回復原則。
7. 在您按一下 [確定]  以開始重新保護之後，就會開始執行將 VM 從 Azure 複寫到內部部署網站的作業。 您可以在 [作業]  索引標籤上追蹤進度。

如果您想要復原到替代位置 (當內部部署 VM 已被刪除時)，請選取為主要目標伺服器所設定的保留磁碟機和資料存放區。 當您容錯回復到內部部署網站時，容錯回復保護計畫中的 VMware VM 會使用與主要目標伺服器相同的資料存放區，而且會在 vCenter 中建立新 VM。
若要將 Azure VM 復原到現有的內部部署 VM，則必須在主要目標伺服器的 ESXi 主機上掛接內部部署 VM 的資料存放區並設定讀寫權限。
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

您也可以在復原計劃層級重新保護。 如果您有一個複寫群組，則只能使用復原計畫來重新保護它。 透過復原計劃重新保護時，您必須針對每一部受保護的機器提供上述的值。

> [!NOTE]
> 複寫群組應該使用相同的主要目標重新保護。 如果使用不同的主要目標伺服器重新保護，則無法針對它採用一般時間點。


重新保護成功之後，VM 將進入受保護狀態。

## <a name="next-steps"></a>後續步驟

一旦 VM 進入受保護狀態，您就可以起始容錯回復。 容錯回復會將 Azure 中的 VM 關機並將內部部署 VM 開機。 因此，應用程式有一小段停機時間。 因此，請選擇應用程式允許停機的容錯回復時間。

[起始 VM 容錯回復的步驟](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-failback)

## <a name="common-issues"></a>常見問題

* 如果虛擬機器是使用範本建立，則在保護之前，您應該確定每個 VM 的磁碟都有唯一 UUID。 如果內部部署 VM 和主要目標的 UUID 衝突 (因為兩者都是從相同的範本建立)，則重新保護會失敗。 您必須部署另一個不是從相同範本建立的主要目標。
* 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，這樣會成功且容錯回復可作用。 在重新保護期間，容錯回復會失敗，因為無法探索資料存放區。 徵兆是重新保護期間不會列出資料存放區。 若要解決此問題，您可以使用有適當權限的帳戶更新 vCenter 認證並重試作業。 如需詳細資訊，請參閱[使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
* 當您將 Linux VM 容錯回復並在內部部署執行它時，您會看到 Network Manager 套件已從該機器解除安裝。 發生此解除安裝的原因是，當在 Azure 中復原 VM 時，Network Manager 套件已被移除。
* 當 Linux VM 已設定靜態 IP 位址且容錯移轉至 Azure 時，就會透過 DHCP 取得 IP 位址。 當您容錯移轉回內部部署時，該 VM 會繼續使用 DHCP 取得 IP 位址。 視需要手動登入機器，並將 IP 位址設定回靜態位址。 Windows VM 可以重新取得其靜態 IP。
* 如果您是使用 ESXi 5.5 免費版或 vSphere 6 Hypervisor 免費版，則容錯移轉會成功，但容錯回復不會成功。 若要啟用容錯回復，請升級到上述程式的評估授權。
* 若無法從處理伺服器連線到組態伺服器，請嘗試使用 Telnet 連線到組態伺服器的連接埠 443，以檢查與組態伺服器之間的連線。 您也可以嘗試從處理伺服器機器 Ping 組態伺服器。 當處理伺服器已連線到組態伺服器時，應該會有活動訊號。
* 如果您要嘗試容錯回復至替代的 vCenter，請確定您的新 vCenter 已被探索到，而且主要目標伺服器也已被探索到。 一般的徵兆是，您會發現在 [重新保護] 對話方塊中無法存取/看到資料存放區。
* 做為實體內部部署電腦保護的 WS2008R2SP1 電腦無法從 Azure 容錯回復到內部部署。

