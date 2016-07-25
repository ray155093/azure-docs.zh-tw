<properties
	pageTitle="使用 Azure 入口網站將 Hyper-V 虛擬機器 (位於 VMM 雲端中) 複寫至次要 VMM 站台 | Microsoft Azure"
	description="描述如何使用 Azure 入口網站部署 Azure Site Recovery，以協調將 VMM 雲端中的 Hyper-V VM 複寫、容錯移轉和復原至次要 VMM 站台。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2016"
	ms.author="raynew"/>

# 使用 Azure 入口網站將位於 VMM 雲端的 Hyper-V 虛擬機器複寫至次要 VMM 站台

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmm-to-vmm.md)
- [傳統入口網站](site-recovery-vmm-to-vmm-classic.md)

歡迎使用 Azure Site Recovery！ 如果您想要將 System Center Virtual Machine Manager (VMM) 雲端中管理的內部部署 Hyper-V 虛擬機器複寫至次要站台，請使用本文。本文說明如何在 Azure 入口網站中使用 Azure Site Recovery 設定複寫。

> [AZURE.NOTE] Azure 建立和處理資源的[部署模型](../resource-manager-deployment-model.md)有二種：Azure Resource Manager (ARM) 部署模型和傳統部署模型。Azure 也有兩個入口網站 – 支援傳統部署模型的 Azure 傳統入口網站，以及支援兩種部署模型的 Azure 入口網站。


Azure 入口網站中的 Azure Site Recovery 提供許多新功能︰

- 在 Azure 入口網站中，Azure 備份和 Azure Site Recovery 服務會結合成單一復原服務保存庫，以便從單一位置設定和管理業務持續性和災害復原 (BCDR)。統一儀表板可讓您監視及管理您的內部部署網站與 Azure 公用雲端的作業。
- 具有與雲端解決方案提供者 (CSP) 程式一起佈建之 Azure 訂用帳戶的使用者，現在可以在 Azure 入口網站中管理 Site Recovery 作業。


閱讀本文之後，請將任何意見張貼在底部的 Disqus 註解中。請在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。


## 概觀

組織需要 BCDR 策略，以決定應用程式、工作負載和資料如何在規劃與未規劃停機期間維持運作，並儘速復原到正常運作的情況。BCDR 策略應保護商務資料安全且可復原，並確保發生災害時工作負載仍持續可用。

Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。當您的主要位置發生故障時，您容錯移轉至次要位置，讓應用程式和工作負載保持可用。當它恢復正常作業時，容錯回復至您的主要位置。深入了解[什麼是 Azure Site Recovery？](site-recovery-overview.md)

本文提供將 VMM 雲端中的內部部署 Hyper-V VM 複寫至次要 VMM 站台所需的全部資訊。其中包括架構概觀、規劃資訊，以及設定內部部署伺服器、複寫設定和容量規劃的部署步驟。設定好基礎結構之後，您可以在您要保護的電腦上啟用複寫，並檢查容錯移轉是否可以運作。

## 商務優點

- Site Recovery 能將執行於 Hyper-V VM 的商務工作負載及應用程式複寫至次要 Hyper-V 伺服器而加以保護。
- 復原服務入口網站提供單一位置來設定、管理及監視複寫、容錯移轉和復原。
- 您可以輕鬆地從主要的內部部署基礎結構執行容錯移轉到次要站台，並從次要站台容錯回復 (還原) 到主要站台。
- 您可以設定包含多部電腦的復原方案，以便階層式應用程式工作負載一起容錯移轉。

## 案例架構

案例元件如下：

- **主要站台**︰在主要站台中，有一或多部 Hyper-V 主機伺服器執行您想要複寫的來源 VM。這些主要的主機伺服器都位於 VMM 私人雲端。
- **次要站台**︰在次要站台中，有一或多部 Hyper-V 主機伺服器執行著數個 VM，即將作為複寫的目的地。這些主機伺服器都位於 VMM 私人雲端。雲端可位於主要伺服器 (如果您只有單一 VMM 伺服器) 或次要 VMM 伺服器上。
- **提供者**：在 Site Recovery 部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery Provider，並在復原服務保存庫中註冊這些服務。在 VMM 上執行的 Provider 會透過 HTTPS 443 與 Site Recovery 通訊，以複寫協調流程。資料複寫會在主要和次要 Hyper-V 主機伺服器之間發生。複寫的資料會保留於內部部署網站和網路中，不會傳送到 Azure。深入了解[隱私權](#privacy-information-for-site-recovery)。

![E2E 拓樸](./media/site-recovery-vmm-to-vmm/architecture.png)

### 資料隱私權概觀

下表摘要說明此案例中儲存資料的方式︰
****
動作 | **詳細資料** | **收集的資料** | **使用** | **必要** 
--- | --- | --- | --- | ---
**註冊** | 您在復原服務保存庫中註冊 VMM 伺服器。如果您稍後想要取消註冊伺服器，可以從 Azure 入口網站刪除該伺服器資訊來達成此目的。 | 註冊 VMM 伺服器之後，Site Recovery 即會收集、處理和傳輸有關 VMM 伺服器的中繼資料以及 Site Recovery 偵測到的 VMM 雲端名稱。 | 資料可用來識別適當的 VMM 伺服器並進行通訊，以及設定適用於 VMM 雲端的設定。 | 此為必要功能。如果您不想將此資訊傳送到 Site Recovery，就不應該使用 Site Recovery 服務。 
**啟用複寫** | 安裝在 VMM 伺服器上的 Azure Site Recovery Provider 是用來和 Site Recovery 服務通訊的管道。提供者是裝載在 VMM 程序中的動態連結程式庫 (DLL)。安裝 Provider 之後，「Datacenter Recovery」功能就會在 VMM 系統管理員主控台中啟用。新的和現有的 VM 可以啟用此功能來啟用對 VM 的保護。 | 設定這個屬性，Provider 就能將 VM 的名稱和識別碼傳送到 Site Recovery。複寫是透過 Windows Server 2012 或 Windows Server 2012 R2 Hyper-V 複本來啟用。虛擬機器資料會從一個 Hyper-V 主機複寫至另一個 (通常位於不同的「復原」資料中心)。 | Site Recovery 會使用中繼資料來填入 Azure 入口網站中的 VM 資訊。 | 此功能是服務不可或缺的一部分，而且無法關閉。如果您不想傳送此資訊，請勿針對 VM 啟用 Site Recovery 保護。請注意，由 Provider 傳送到 Site Recovery 的所有資料都是透過 HTTPS 傳送。
**復原計畫** | 復原方案可協助您建立復原資料中心的協調流程計劃。您可以定義 VM 或虛擬機器群組應在復原站台上啟動的順序。您也可以指定在每個 VM 復原時任何要執行的自動化指令碼，或任何要採取的手動動作。容錯移轉通常會在協調復原的復原方案層級觸發。 | Site Recovery 會收集、處理和傳輸復原方案的中繼資料，包含虛擬機器中繼資料，以及任何自動化指令碼和手動動作備註的中繼資料。 | 中繼資料可用來建置 Azure 入口網站中的復原方案。 | 此功能是服務不可或缺的一部分，而且無法關閉。如果您不想將此資訊傳送到 Site Recovery，請勿建立復原方案。
**網路對應** | 將網路資訊從主要資料中心對應至復原資料中心。在復原站台上復原 VM 時，網路對應可協助建立網路連線。 | Site Recovery 會為各站台 (主要與資料中心) 收集、處理並傳輸其邏輯網路的中繼資料。 | 中繼資料可用來填入網路設定，讓您能夠對應網路資訊。 | 此功能是服務不可或缺的一部分，而且無法關閉。如果您不想將此資訊傳送到 Site Recovery，請勿使用網路對應。
**容錯移轉 (計劃性/非計劃性/測試)** | 容錯移轉會將 VM 從某一個 VMM 管理的資料中心容錯移轉到另一個。容錯移轉動作在 Azure 入口網站中是手動觸發的。 | VMM 伺服器上的 Provider 會收到來自 Site Recovery 有關容錯移轉事件的通知，並在 Hyper-V 主機上透過 VMM 介面執行容錯移轉動作。VM 的實際容錯移轉是從某一部 Hyper-V 主機到另一部，並透過 Windows Server 2012 或 Windows Server 2012 R2 Hyper-V 複本來處理。完成容錯移轉之後，復原資料中心之 VMM 伺服器上的 Provider 會傳送成功資訊給 Site Recovery。 | Site Recovery 會使用傳送的資訊，在 Azure 入口網站上填入容錯移轉動作資訊的狀態。 | 此功能是服務不可或缺的一部分，而且無法關閉。如果您不想將此資訊傳送到 Site Recovery，請勿使用容錯移轉。


## Azure 必要條件

以下是您在 Azure 中部署此案例所需的項目：

**必要條件** | **詳細資料** 
--- | ---
**Azure**| 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。您可以從[免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。[深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定價。 


## 內部部署必要條件

以下是您需要在主要和次要內部部署站台中部署此案例的情況：

**必要條件** | **詳細資料** 
--- | ---
**VMM** | 我們建議您在主要站台與次要站台中各部署一部 VMM 伺服器。<br/><br/> 您也可以[在單一 VMM 伺服器上的雲端之間進行複寫](site-recovery-single-vmm.md)。若要這樣做，您至少需要在 VMM 伺服器上設定兩個雲端。<br/><br/> VMM 伺服器至少應執行含有最新更新的 System Center 2012 SP1。<br/><br/> 每部 VMM 伺服器都必須設定一或多個雲端，而所有雲端都必須設定 Hyper-V 容量設定檔。<br/><br/>雲端必須包含一或多個 VMM 主機群組。<br/><br/>在[設定 VMM 雲端網狀架構](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)和[逐步解說：使用 System Center 2012 SP1 VMM 建立私人雲端](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)中深入了解設定 VMM 雲端的方式。<br/><br/> VMM 伺服器需要網際網路存取。 
**Hyper-V** | Hyper-V 伺服器至少必須以 Hyper-V 角色執行 Windows Server 2012，並已安裝最新更新。<br/><br/> Hyper-V 伺服器應該包含一或多部 VM。<br/><br/> Hyper-V 主機伺服器應該位於主要和次要 VMM 雲端的主機群組中。<br/><br/> 如果您正在 Windows Server 2012 R2 上的叢集中執行 Hyper-V，則應安裝[更新 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> 如果您正在 Windows Server 2012 上的叢集中執行 Hyper-V，請注意，如果您具有以靜態 IP 位址為基礎的叢集，該叢集代理人不會自動建立。您必須手動設定叢集代理。[閱讀更多資訊](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。
**提供者** | 在 Site Recovery 部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。Provider 會透過 HTTPS 443 與 Site Recovery 通訊來協調複寫。資料複寫是透過 LAN 或 VPN 連線在主要和次要 Hyper-V 伺服器之間發生。<br/><br/> 在 VMM 伺服器上執行的提供者需要存取下列 URL：*.hypervrecoverymanager.windowsazure.com、*.accesscontrol.windows.net、*.backup.windowsazure.com、*.blob.core.windows.net、*.store.core.windows.net。<br/><br/> 此外，還要允許從 VMM 伺服器到 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)的防火牆通訊，並允許 HTTPS (443) 通訊協定。

## 準備部署

若要準備進行部署，您必須︰

1. [準備 VMM 伺服器](#prepare-the-vmm-server)以便進行 Site Recovery 部署。
2. [準備網路對應](#prepare-for-network-mapping)。設定網路，以便設定網路對應。

### 準備 VMM 伺服器

確定 VMM 伺服器符合[必要條件](#on-premises-prerequisites)，而且能夠存取列出的 URL。


### 準備網路對應

網路對應會在主要和次要 VMM伺服器上的 VMM VM 網路之間進行對應：

- 容錯移轉之後，選擇性地在次要 Hyper-V 主機上放置複本 VM。
- 將複本 VM 連接到適當的 VM 網路。
- 如果您沒有設定網路對應，複本 VM 將不會在容錯移轉之後連接到任何網路。
- 如果您想要在 Site Recovery 部署期間設定網路對應，就需要執行下列動作：

	- 確認來源 Hyper-V 主機伺服器上的 VM 已連接到 VMM VM 網路。該網路應該連結到與雲端相關聯的邏輯網路。
	- 確認您將用於復原的次要雲端已設定相對應的 VM 網路。該 VM 網路應該連結到與次要雲端相關聯的邏輯網路。

- [深入了解](site-recovery-network-mapping.md)網路對應的運作方式。

## 準備使用單一 VMM 伺服器進行部署

如果您只有單一 VMM 伺服器，就能在 VMM 雲端中，將 Hyper-V 主機上的 VM 複寫到 [Azure](site-recovery-vmm-to-azure.md) 或次要 VMM 雲端。我們建議使用第一個選項，因為雲端之間的複寫並不是非常順暢，但是，如果您需要執行此動作，就需要執行下列動作：

1. **在 Hyper-V VM 上設定 VMM**。當我們執行此動作時，建議您將 VMM 使用的 SQL Server 執行個體共置在相同的 VM 上。因為只需建立一個 VM，這樣就能節省時間。如果您想要使用遠端 SQL Server 執行個體卻發生中斷，則必須先復原該執行個體後再復原 VMM。
2. **確定 VMM 伺服器至少已設定兩個雲端**。其中一個雲端包含您想要複寫的 VM，另一個雲端做為次要位置。包含您要保護之 VM 的雲端必須遵守[必要條件](#on-premises-prerequisites)。
3. 以本文所述的方式設定 Site Recovery。在保存庫中建立並註冊 VMM 伺服器、設定複寫原則，然後啟用複寫。您應該指定初始複寫要透過網路執行。
4. 當您設定網路對應時，要將主要雲端的 VM 網路對應到次要雲端的 VM 網路。
5. 使用 Hyper-V 管理員主控台，在包含 VMM VM 的 Hyper-V 主機上啟用 Hyper-V 複本，並啟用 VM 的複寫功能。請不要將 VMM 虛擬機器加入受 Site Recovery 保護的雲端，以確保 Site Recovery 不會覆寫 Hyper-V 複本設定。
6. 如果您建立適用於容錯移轉的復原方案，將會使用同一部 VMM 伺服器做為來源與目標。
7. 您將以如下方式進行容錯移轉和復原︰

	- 搭配使用 Hyper-V 管理員與規劃的容錯移轉，手動將 VMM VM 容錯移轉至次要站台。
	- 容錯移轉 VM。
	- 復原主要 VMM VM 之後，請登入 Azure 入口網站 -> 復原服務保存庫，然後執行從次要站台至主要站台的非計劃性 VM 容錯移轉。
	- 在非計劃性的容錯移轉完成之後，將可透過主要站台再次存取所有資源。


### 建立復原服務保存庫

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [新增] > [管理] > [復原服務]。或者，您可以按一下 [瀏覽] > [復原服務保存庫] > [加入]。

	![新增保存庫](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. 在 [名稱] 中，指定保存庫的易記識別名稱。如果您有多個訂用帳戶，請選取其中一個。
4. [建立新的資源群組](../resource-group-portal.md#create-resource-group)，或選取現有的資源群組，並指定 Azure 區域。機器將會複寫到此區域。若要查看支援的地區，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/) (英文) 中的＜各地區上市情況＞。
4. 如果您想要從儀表板快速存取保存庫，請按一下 [釘選到儀表板] > [建立保存庫]。

	![新增保存庫](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

新的保存庫會出現於 [儀表板] > [所有資源]，以及主要 [復原服務保存庫] 刀鋒視窗上。




## 快速入門

Site Recovery 提供的「快速入門」經驗可協助您盡快部署。「快速入門」會檢查必要條件並引導您以正確順序完成 Site Recovery 部署步驟。

在「快速入門」中，您會選取您要複寫的機器類型，以及您要複寫到的位置。您會設定內部部署伺服器、建立複寫原則，並執行容量規劃。已設定好您的基礎結構之後，您可啟用 VM 的複寫。您可以對特定電腦執行容錯移轉，或建立復原方案來容錯移轉多部電腦。

選擇您要部署 Site Recovery 的方式，即可開始「快速入門」。視您的複寫需求而定，「快速入門」流程會稍有不同。

## 步驟 1︰選擇您的保護目標

選取您要複寫的項目以及您要複寫到的位置。

1. 在 [復原服務保存庫] 刀鋒視窗中選取您的保存庫，並按一下 [設定]。
2. 在 [設定] > [快速入門] 中，按一下 [Site Recovery] > [步驟 1︰準備基礎結構] > [保護目標]。
3. 在 [保護目標] 中，選取 [至復原站台]，然後選取 [是，利用 Hyper-V]。
4. 選取 [是] 以表示您使用 VMM 來管理 Hyper-V 主機，然後如果您有次要 VMM 伺服器，請選取 [是]。如果您要在單一 VMM 伺服器上的雲端之間部署複寫，就按一下 [否]。然後按一下 [確定]。

	![選擇目標](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## 步驟 2︰設定來源環境

在 VMM 伺服器上安裝 Azure Site Recovery Provider，並在保存庫中註冊伺服器。

1. 按一下 [步驟 2：準備基礎結構] > [來源]。

	![設定來源](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. 在 [準備來源] 中，按一下 [+ VMM] 以新增 VMM 伺服器。

	![設定來源](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. 在 [新增伺服器] 刀鋒視窗中，檢查 [System Center VMM 伺服器] 是否出現在 [伺服器類型] 中，以及 VMM 伺服器是否符合[必要條件和 URL 需求](#on-premises-prerequisites)。
4. 下載 Azure Site Recovery Provider 安裝檔案。
5. 下載註冊金鑰。您會在執行安裝程式時用到此金鑰。該金鑰在產生後會維持 5 天有效。

	![設定來源](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. 您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。

> [AZURE.NOTE] 您不需要明確地在 Hyper-V 主機伺服器上安裝任何項目。


### 設定 Azure Site Recovery Provider

1. 在每部 VMM 伺服器上執行 Provider 安裝檔案。如果 VMM 部署在叢集中，且您是第一次安裝提供者，請將其安裝在作用中節點上，並完成安裝以在保存庫中註冊 VMM 伺服器。然後在其他節點上安裝提供者。叢集節點全都應該執行相同版本的 Provider。
2. 安裝程式會執行數個必要條件檢查，並要求停止 VMM 服務的權限。當安裝完成之後，VMM 服務將會自動重新啟動。如果您安裝在 VMM 叢集上，您會收到停止叢集角色的提示。

2.  在 [Microsoft Update] 中，您可以選擇進行更新，以便根據您的 Microsoft Update 原則安裝 Provider 更新。
3. 在 [安裝] 中接受或修改預設 Provider 安裝位置，然後按一下 [安裝]。

	![安裝位置](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. 安裝完成之後，按一下 [註冊] 以在保存庫中註冊伺服器。

	![安裝位置](./media/site-recovery-vmm-to-vmm/provider-register.png)

4. 在 [Proxy 設定] 中，指定在 VMM 伺服器上執行的 Provider 透過網際網路連接到 Site Recovery 的方式。

	- 如果您要讓提供者直接連線，請選取 [不使用 Proxy 直接連線]。
	- - 如果您要使用目前在伺服器上設定的 Proxy 來連線，請選取 [以現有的 Proxy 設定連線]。
	- 如果現有的 Proxy 需要驗證，或是您要讓提供者使用自訂 Proxy 來連線，請選取 [以自訂 Proxy 設定連線]。
	- 如果您使用自訂 proxy，您必須指定位址、連接埠及認證。
	- 如果您使用 Proxy，您應該已經允許[必要條件](#provider-and-agent-prerequisites)中所述的 URL。
	- 如果您使用的是自訂 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。設定 proxy 伺服器，讓此帳戶可以成功進行驗證。在 VMM 主控台中，可以修改 VMM RunAs 帳戶設定。在 [設定] 中，展開 [安全性] > [執行身分帳戶]，然後修改 DRAProxyAccount 的密碼。您必須重新啟動 VMM 服務，這項設定才會生效。

	![網際網路](./media/site-recovery-vmm-to-vmm/provider3.png)

4. 在 [保存庫設定] 中，按一下 [瀏覽] 來選取下載的金鑰檔案。指定 Azure 訂用帳戶和保存庫名稱。

	![伺服器註冊](./media/site-recovery-vmm-to-vmm/provider-key2.png)

5. 在 [註冊] > [儲存憑證的位置] 中，按 [下一步]。適用於加密的憑證與此案例無關。它只會在您複寫到 Azure 儲存體時使用。

8. 在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。在叢集設定中，指定 VMM 叢集角色名稱。
9. 如果您想要將 VMM 伺服器上所有雲端的中繼資料與保存庫進行同步，請啟用 [同步處理雲端中繼資料]。這個動作只需要在每個伺服器上進行一次。如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。按 [下一步]，完成此程序。

	![伺服器註冊](./media/site-recovery-vmm-to-vmm/provider-sync.png)

10. 註冊作業隨即開始。註冊完成後，VMM 伺服器會顯示於保存庫的 [設定] > [伺服器] 刀鋒視窗上。
11. 當伺服器可以在 Site Recovery 主控台中使用之後，於 [來源] > [準備來源] 中選取 VMM 伺服器，然後選取 Hyper-V 主機所在的雲端。然後按一下 [確定]。

#### 命令列安裝

您可以從命令列安裝 Azure Site Recovery Provider。這個方法可以用來在適用於 Windows Server 2012 R2 的伺服器核心上安裝 Provider。

1. 將提供者安裝檔案和註冊金鑰下載至資料夾。例如 C:\\ASR。
2. 停止 System Center Virtual Machine Manager 服務。
3. 在提高權限的命令提示字元中，執行下列命令來擷取 Provider 安裝程式：

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. 執行下列命令來安裝 Provider︰

    	C:\ASR> setupdr.exe /i

5. 然後執行下列命令以在保存庫中註冊伺服器：

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

其中參數為：

 - **/Credentials**：必要參數，用來指定註冊金鑰檔案所在的位置
 - **/FriendlyName**：對於 Hyper-V 主機伺服器名稱的必要參數，該伺服器會出現在 Azure Site Recovery 入口網站中。
 - **/EncryptionEnabled**︰您只會在從 VMM 複寫到 Azure 時使用的選用參數。
 - **/proxyAddress**：指定 Proxy 伺服器位址的選用參數。
 - **/proxyport**：指定 Proxy 伺服器連接埠的選用參數。
 - **/proxyUsername**：指定 Proxy 使用者名稱 (如果 Proxy 需要驗證) 的選用參數。
 - **/proxyPassword**：指定用以驗證 Proxy 伺服器之密碼 (如果 Proxy 需要驗證) 的選用參數。

## 步驟 3︰設定目標環境

選取目標 VMM 伺服器和雲端。

1. 按一下 [準備基礎結構] > [目標]，然後選取您要使用的目標 VMM 伺服器。
2.	隨即顯示伺服器上與 Site Recovery 同步處理的雲端。選取目標雲端。

	![目標](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## 步驟 4：設定複寫設定

1. 若要建立新的複寫原則，請按一下 [準備基礎結構] > [複寫設定] > [+建立及關聯]。

	![網路](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. 在 [建立及關聯原則] 中指定原則名稱。來源和目標類型應該是 **Hyper-V**。
3. 在 [Hyper-V 主機版本] 中，選取在主機上執行的作業系統。

	> [AZURE.NOTE] VMM 雲端可以包含執行不同 (支援) 版本之 Windows Server 的 Hyper-V 主機，但一個複寫原則適用於執行相同作業系統的主機。如果您的主機會執行多個作業系統版本，請建立不同的複寫原則。

4. 在 [驗證類型] 和 [驗證連接埠] 中，指定如何驗證主要與復原 Hyper-V 主機伺服器之間的流量。除非您有正常運作的 Kerberos 環境，否則請選取 [憑證]。Azure Site Recovery 會自動設定用於 HTTPS 驗證的憑證。您不需要手動執行任何動作。根據預設，連接埠 8083 和 8084 (用於憑證) 將在 Hyper-V 主機伺服器上的 Windows 防火牆中開啟。如果您確實已選取 [Kerberos]，Kerberos 票證將會用於主機伺服器的相互驗證。請注意，這項設定只有在 Hyper-V 主機伺服器是執行於 Windows Server 2012 R2 時才有重要性。
3. 在 [複製頻率] 中，指定您要在初始複寫後複寫差異資料的頻率 (每隔 30 秒、5 或 15 分鐘)。
4. 在 [復原點保留] 中，針對每個復原點指定保留週期的長度 (以小時為單位)。受保護的機器可以復原到週期內的任意點。
6. 在 [應用程式一致的快照頻率] 中，指定建立包含應用程式一致快照的復原點的頻率 (1-12 小時)。Hyper-V 使用兩種類型的快照，一個是標準快照，提供整個虛擬機器的增量快照，另一個是應用程式一致快照，會建立虛擬機器內應用程式資料的時間點快照。應用程式一致快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。請注意，如果您啟用應用程式一致快照，它會影響在來源虛擬機器上執行的應用程式效能。確認您設定的值低於您設定的其他復原點數目。
7. 在 [資料傳輸壓縮] 中，指定是否應該將傳輸的已複寫資料壓縮。
8. 選取 [刪除複本 VM]，以指定如果您停用對來源 VM 的保護，則應刪除複本虛擬機器。如果啟用此設定，當您停用對來源 VM 的保護時，便會從 Site Recovery 主控台中加以移除、在 VMM 主控台中移除 VMM 的 Site Recovery 設定，並刪除複本。
3. 在 [初始複寫方法] 中，如果您要透過網路進行複寫，請指定是否要啟動初始複寫，或將它排程。若要節省網路頻寬，您可能要將它排程在離峰時間執行。然後按一下 [確定]。

	![複寫原則](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. 當您建立新的原則時，該原則會自動與 VMM 雲端產生關聯。在 [複寫原則] 中按一下 [確定]。您可以在 [設定] > [複寫] > 原則名稱 > [關聯 VMM 雲端] 中，將其他 VMM 雲端 (及其中的 VM) 與此複寫原則產生關聯。

	![複寫原則](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### 準備進行離線初始複寫

您可以針對初始資料複本進行離線複寫。您可以如下所示方式進行準備︰

- 在來源伺服器上，您會指定要從中匯出資料的路徑位置。在匯出路徑上指派 NTFS 和共用權限的完整控制權給 VMM 服務。在目標伺服器上，您會指定要從中匯入資料的路徑位置。在這個匯入路徑上指派相同的權限。
- 如果共用匯入或匯出路徑，請在共用所在的遠端桌面上，指派系統管理員、進階使用者、列印操作員或伺服器操作員群組成員資格給 VMM 伺服器帳戶。
- 如果您使用任何執行身分帳戶加入主機，請在匯入和匯出路徑上指派讀取和寫入權限給 VMM 中的執行身分帳戶。
- 匯入和匯出共用不得位於任何做為 Hyper-V 主機伺服器的電腦，因為 Hyper-V 不支援回送設定。
- 在 Active Directory 的每部包含您想要保護之虛擬機器的 Hyper-V 主機伺服器上，啟用與設定限制委派，以信任匯入與匯出路徑所在的遠端電腦，如下所示：
	1. 在網域控制站上，開啟 [Active Directory 使用者和電腦]。
	2. 在主控台樹狀目錄中，按一下 [DomainName] > [電腦]。
	3. 以滑鼠右鍵按一下 Hyper-V 主機伺服器名稱 > [屬性]。
	4. 在 [委派] 索引標籤上，按一下 [信任這台電腦，但只委派指定的服務]。
	5. 按一下 [使用任何驗證通訊協定]。
	6. 按一下 [加入] > [使用者和電腦]。
	7. 輸入裝載匯出路徑的電腦名稱 > [確定]。從可用服務的清單中，按住 CTRL 鍵並按一下 [cifs] > [確定]。為裝載匯入路徑的電腦名稱重複動作。視需要為其他 Hyper-V 主機伺服器重複動作。


### 設定網路對應

設定來源與目標網路之間的網路對應。

- [閱讀](#prepare-for-network-mapping)網路對應的快速概觀。另請[閱讀](site-recovery-network-mapping.md)更深入的說明。
- 確認 VMM 伺服器上的虛擬機器已連線到 VM 網路。

設定對應，如下所示︰

1. [設定] > [Site Recovery 基礎結構] > [網路對應] > [網路對應]，按一下 [+網路對應]。

	![網路對應](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. 在 [新增網路對應] 索引標籤中，選取來源和目標 VMM 伺服器。隨即會擷取與 VMM 伺服器相關聯的 VM 網路。
3. 在 [來源網路] 中，從與主要 VMM 伺服器相關聯的 VM 網路清單中，選取您要使用的網路。
6. 在 [目標網路] 中，選取您要在次要 VMM 伺服器上使用的網路。然後按一下 [確定]。

	![網路對應](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

以下是網路對應開始時發生的事情︰

- 對應到來源 VM 網路的任何現有複本虛擬機器都將連線到目標 VM 網路。
- 連線到來源 VM 網路的新虛擬機器都會在複寫之後連線到目標對應的網路。
- 如果您修改與新網路的現有對應，複本虛擬機器將使用新設定進行連線。
- 如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在之子網路名稱相同，複本虛擬機器將會在容錯移轉之後連線到該目標子網路。如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。

## 步驟 5︰容量規劃

您現已設定您的基本基礎結構，您可以思考容量規劃並找出您是否需要額外的資源。

Site Recovery 會提供以 Excel 為基礎的 Capacity Planner，協助您為來源環境、Site Recovery 元件、網路和儲存體配置適當的資源。您可以在快速模式中執行規劃工具，以便根據 VM、磁碟和儲存體的平均數量進行估計，或在詳細模式中執行規劃工具，以輸入工作負載層級的數據。開始之前，您必須︰

- 收集有關複寫環境的資訊，包括 VM、每個 VM 的磁碟和每個磁碟的儲存體。
- 估計複寫差異資料的每日變更 (流失) 率。您可以使用[適用於 Hyper-V 複本的 Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057) 來協助您執行這項操作。

1.	按一下 [下載] 來下載此工具並加以執行。閱讀工具隨附的[文章](site-recovery-capacity-planner.md)。
2.	當您完成時，請在 [是否已執行 Capacity Planner?] 中選取 [是]

	![容量規劃](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### 控制頻寬

當您使用 Capacity Planner Hyper-V 複本收集即時的差異複寫資訊之後，以 Excel 為基礎的 Capacity Planner 工具就能協助您計算複寫 (初始和差異) 所需的頻寬。若要控制用於複寫的頻寬數量，您可以使用群組原則或 Windows PowerShell 來設定 NetQos 原則。您可以使用下列數種方式來執行此動作：

**PowerShell** | **詳細資料**
--- | ---
**New -NetQosPolicy "QoS to destination subnet"** | 將流量從執行 Windows Server 2012 R2 的 Hyper-V 主機節流處理至次要子網路。如果您的主要和次要子網路不同，請使用此方法。
**New -NetQosPolicy "QoS to destination port"** | 將流量從執行 Windows Server 2012 R2 的 Hyper-V 主機節流處理至目的地連接埠。
**New -NetQosPolicy "Throttle traffic from VMM"** | 節流處理來自 vmms.exe 的流量。這將會節流處理 Hyper-V 流量和即時移轉流量。您可以比對 IP 通訊協定和連接埠，以縮小搜尋範圍。

您可以使用頻寬權重設定，或利用每個次要的位元來限制流量。如果您使用叢集，就必須在所有叢集節點上執行這項操作。如需詳細資訊，請參閱：


- Thomas Maurer 的部落格 [Throttling Hyper-V Replica Traffic (節流處理 Hyper-V 複本流量)](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
- 其他關於 [New-NetQosPolicy Cmdlet](https://technet.microsoft.com/library/hh967468.aspx) 的資訊。


## 步驟 6：啟用複寫 

立即啟用複寫，如下所示︰

1. 按一下 [步驟 2︰複寫應用程式] > [來源]。第一次啟用複寫之後，請按一下保存庫中的 [+複寫]，以對其他機器啟用複寫。

	![啟用複寫](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. 在 [來源] 刀鋒視窗中，選取 VMM 伺服器和您要複寫之 Hyper-V 主機所在的雲端。然後按一下 [確定]。

	![啟用複寫](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. 在 [目標] 刀鋒視窗中，確認次要 VMM 伺服器和雲端。
4. 在 [虛擬機器] 中，從清單中選取您要保護的 VM。

	![啟用虛擬機器保護](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

您可以在 [設定] > [作業] > [Site Recovery 作業] 中，追蹤 [啟用保護] 動作的進度。執行「完成保護」作業之後，虛擬機器即準備好進行容錯移轉。


>[AZURE.NOTE] 您也可以在 VMM 主控台中啟用虛擬機器的保護。在虛擬機器屬性 > [Azure Site Recovery] 索引標籤中的工具列上按一下 [啟用保護]。

當您啟用複寫之後，就可以在 [設定] > [複寫項目] > VM 名稱中檢視 VM 的屬性。在 [基本資訊] 儀表板中，您可以看到關於 VM 複寫原則及其狀態的資訊。如需詳細資訊，按一下 [屬性]。

### 建立現有的虛擬機器

如果 VMM 中目前已經有使用 Hyper-V 複本複寫的虛擬機器，您可以使用下列方式加入它們以提供 Azure Site Recovery 保護：

1. 確認裝載現有 VM 的 Hyper-V 伺服器位於主要雲端，且裝載複本虛擬機器的 Hyper-V 伺服器位於次要雲端。
2. 確定已針對主要 VMM 雲端設定複寫原則。
2. 針對主要虛擬機器啟用複寫。Azure Site Recovery 和 VMM 將確保會偵測到相同的複本主機和虛擬機器，且 Azure Site Recovery 會使用指定的設定，重複使用及重新建立複寫。


## 步驟 7︰測試您的部署

若要測試部署，您可以針對單一虛擬機器執行測試容錯移轉，或建立包含一或多部虛擬機器的復原方案。

### 準備容錯移轉

- 若要完整測試您的部署，您需要有基礎結構，讓複寫的機器如預期般運作。如果您想要測試 Active Directory 和 DNS，您可以透過 DNS 建立虛擬機器做為網域控制站，並使用 Azure Site Recovery 將此虛擬機器複寫至 Azure。深入了解 [Active Directory 的測試容錯移轉考量](site-recovery-active-directory.md#considerations-for-test-failover)。
- 這篇文章中的指示說明如何不使用網路來執行測試容錯移轉。此選項將測試 VM 容錯移轉，但不會測試 VM 的網路設定。[深入了解](site-recovery-failover.md#run-a-test-failover)其他選項。
- 如果您想要執行非計劃性容錯移轉，而不是測試容錯移轉，請注意下列事項︰

	- 如果可能的話您應該在執行非計劃性容錯移轉之前關閉主要機器。這可確保您不需要同時執行來源和複本機器。
	- 當您執行非計劃性容錯移轉時，會停止從主要機器的資料複寫，讓任何資料差異不會在開始非計劃性容錯移轉之後傳送。此外，如果您在復原方案上執行非計劃性容錯移轉，它將會執行直到完成為止 (即使發生錯誤)。




### 執行測試容錯移轉

1. 若要容錯移轉單一 VM，請在 [設定] > [複寫的項目] 中，按一下 [VM] > [+測試容錯移轉]。

	![測試容錯移轉](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. 若要容錯移轉復原方案，請在 [設定] > [復原方案] 中，以滑鼠右鍵按一下方案 > [測試容錯移轉]。若要建立復原方案，請[遵循這些指示](site-recovery-create-recovery-plans.md)。
2. 在 [測試容錯移轉] 中，選取 [無]。使用此選項，您將測試 VM 會如預期般進行容錯移轉，但複寫的 VM 不會連線到任何網路。

	![測試容錯移轉](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. 按一下 [確定] 即可開始容錯移轉。您可以按一下 VM 以開啟其屬性，或在 [設定] > [作業] > [Site Recovery 作業] 中的 [測試容錯移轉] 作業上追蹤進度。
4. 當容錯移轉作業到達 [完成測試] 階段時，執行下列作業：

	-  在次要 VMM 雲端中檢視複本 VM。
	-  按一下 [完成測試] 來完成測試容錯移轉。
	-  按一下 [記事] 記錄並儲存關於測試容錯移轉的任何觀察。

5. 測試虛擬機器將建立在複本虛擬機器所在的相同主機上。它會新增至複本虛擬機器所在的相同雲端。
6. 確認 VM 啟動成功之後，按一下 [測試容錯移轉完成]。在此階段，會刪除 Site Recovery 在測試容錯移轉期間自動建立的所有元素。

	> [AZURE.NOTE] 如果測試容錯移轉持續兩週以上，系統即會強制完成該測試容錯移轉。



### 使用複本 VM IP 位址更新 DNS

容錯移轉之後，複本 VM 可能不會具備與主要虛擬機器相同的 IP 位址。

- 虛擬機器在啟動後將更新他們正在使用的 DNS 伺服器。
- 您也可以手動更新 DNS，如下所示︰

#### 擷取 IP 位址

執行此範例指令碼以抓取 IP 位址。

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

#### 更新 DNS

執行此範例指令碼來更新 DNS (使用上一個範例指令碼抓取到的 IP 位址)。

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## 後續步驟

在您的部署設定完成並開始執行之後，[深入了解](site-recovery-failover.md)不同類型的容錯移轉。

<!---HONumber=AcomDC_0713_2016-->