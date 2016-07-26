<properties
	pageTitle="Site Recovery 如何運作？| Microsoft Azure"
	description="本文提供 Site Recovery 架構的概觀"
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
	ms.topic="get-started-article"
	ms.date="07/12/2016"
	ms.author="raynew"/>

# Azure Site Recovery 如何運作？

閱讀本文，了解 Azure Site Recovery 服務的基礎架構以及讓它運作的元件。

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。


## 概觀

組織需要 BCDR 策略，以決定應用程式、工作負載和資料如何在規劃與未規劃停機期間維持運作，並儘速復原到正常運作的情況。BCDR 策略應保護商務資料安全且可復原，並確保發生災害時工作負載仍持續可用。

Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。當您的主要位置發生故障時，您容錯移轉至次要位置，讓應用程式和工作負載保持可用。當它恢復正常作業時，容錯回復至您的主要位置。深入了解[什麼是 Site Recovery？](site-recovery-overview.md)

## Azure 入口網站中的 Site Recovery

Azure 有兩種不同的[部署模型](../resource-manager-deployment-model.md)來建立和使用資源：Azure Resource Manager 模型和傳統服務管理模型。Azure 也有兩個入口網站 – 支援傳統部署模型的 [Azure 傳統入口網站](https://manage.windowsazure.com/)，以及同時支援兩種部署模型的 [Azure 入口網站](https://portal.azure.com)。

在傳統入口網站和 Azure 入口網站中都有提供 Site Recovery。在 Azure 傳統入口網站中，您可以支援搭配傳統服務管理模型的 Site Recovery。在 Azure 入口網站中，您可以支援傳統模型或資源模型部署。[深入了解](site-recovery-overview.md#site-recovery-in-the-azure-portal)如何使用 Azure 入口網站進行部署。

本文資訊適用於傳統和 Azure 入口網站的部署。兩者差異之處會適用指出。

## 部署案例

部署 Site Recovery 即可在一些案例中協調複寫：

- **複寫 VMware 虛擬機器**：您可以將內部部署 VMware 虛擬機器複寫至 Azure 或次要資料中心。
- - **複寫實體機器**：您可以將執行 Windows 或 Linux 的實體機器複寫至 Azure 或次要資料中心。複寫實體機器的程序和複寫 VMware VM 的程序幾乎完全相同
- **複寫 Hyper-V VM (不含 VMM)**：您可以將未受 VMM 管理的 Hyper-V VM 複寫至 Azure。
- **複寫在 System Center VMM 雲端管理的 Hyper-V VM**：您可以將 VMM 雲端的 Hyper-V 主機伺服器上執行的內部部署 Hyper-V 虛擬機器複寫至 Azure 或次要資料中心。您可以使用標準 Hyper-V 複本或使用 SAN 複寫來進行複寫。
- **移轉 VM**：您可以使用 Site Recovery 在區域之間[移轉 Azure IaaS VM](site-recovery-migrate-azure-to-azure.md)，或者[移轉 AWS Windows 執行個體](site-recovery-migrate-aws-to-azure.md)至 Azure IaaS VM。目前僅支援移轉表示您可以容錯移轉這些 VM，但您無法容錯回復它們。

Site Recovery 可複寫這些 VM 與實體伺服器上執行的大部分應用程式。您可以在 [Azure Site Recovery 可以保護哪些工作負載？](site-recovery-workload.md)中取得支援的應用程式的完整摘要。


## 複寫至 Azure：VMware 虛擬機器或實體 Windows/Linux 伺服器

有好幾種方法可使用 Site Recovery 複寫 VMware VM。

- **使用 Azure 入口網站** - 當您在 Azure 入口網站部署 Site Recovery，您可以將 VM 容錯移轉至傳統的服務管理員儲存體或 Resource Manager。在 Azure 入口網站複寫 VMware VM 可帶來許多好處，包括可以在 Azure 中複寫到傳統或 Resource Manager 儲存體。[深入了解](site-recovery-vmware-to-azure.md)。
- **使用傳統入口網站** - 您可以在傳統入口網站使用增強型體驗部署 Site Recovery。此方法應該用於傳統入口網站的所有新部署。在此部署中，您只能將 VM 容錯移轉至 Azure 的傳統儲存體，而非 Resource Manager 儲存體。[深入了解](site-recovery-vmware-to-azure-classic.md)。另外還有在傳統入口網站設定 VMware 複寫的[舊版體驗](site-recovery-vmware-to-azure-classic-legacy.md)。此方法則不應該用於新部署。如果您已使用舊版體驗進行部署，請[了解如何移轉](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment)至增強的部署。



在 Azure 入口網站或 Azure 傳統入口網站 (增強型) 中部署 Site Recovery 以複寫 VMware VM/實體伺服器的架構需求類似，兩者間只有幾項差異︰

- 如果您在 Azure 入口網站中部署，則可以複寫至 Resource Manager 型儲存體，並在容錯移轉之後使用 Resource Manager 網路連接 Azure VM。
- 在 Azure 入口網站中部署時，LRS 和 GRS 儲存體皆受到支援。在傳統入口網站中，則需要 GRS。
- Azure 入口網站的部署程序已簡化，更加方便使用者使用。


以下是您需要的內容：

- **Azure 帳戶**：您將需要一個 Microsoft Azure 帳戶。
- **Azure 儲存體**：您需要 Azure 儲存體帳戶來儲存複寫的資料。您可以使用傳統帳戶或 Resource Manager 儲存體帳戶。當您在 Azure 入口網站部署時，帳戶可以是 LRS 或 GRS。複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。
- **Azure 網路**：在容錯移轉時建立 Azure VM 時，您需要有 Azure VM 將連接的 Azure 虛擬網路。在 Azure 入口網站中，這可以是傳統服務管理員模型或 Resource Manager 模型中建立的網路。
- **內部部署組態伺服器**︰您需要有內部部署 Windows Server 2012 R2 機器，以執行組態伺服器和其他 Site Recovery 元件。如果您要複寫 VMware VM，這應該是具有高度可用性的 VMware VM。如果您想要複寫實體伺服器，則可以是實體機器。這些 Site Recovery 元件將安裝在機器上︰
	- **組態伺服器**：協調內部部署環境與 Azure 之間的通訊，以及管理資料複寫和復原。
	- **處理序伺服器**：做為複寫閘道器。從受保護的來源機器接收複寫資料、以快取最佳化、壓縮和加密，以及將資料傳送至 Azure 儲存體。它還會處理用來保護機器的行動服務的推入安裝，並執行 VMWare VM 的自動探索。隨著部署規模擴大，您可以新增更多個別的專用處理序伺服器，以處理日益增加的複寫流量。
	- **主要目標伺服器**：從 Azure 容錯回復期間處理複寫資料。
- **要複寫的 VMware VM 或實體伺服器**：您想要複寫至 Azure 的每部電腦都必須安裝行動服務元件。此服務會擷取在電腦上寫入的資料，並將它們轉送到處理序伺服器。您可以手動安裝此元件，或在對電腦啟用複寫時由處理序伺服器自動推送並安裝。
- **vSPhere 主機/vCenter 伺服器**：您需要一或多部執行 VMware VM 的 vSPhere 主機伺服器。建議您部署 vCenter 伺服器來管理這些主機。
- **容錯回復**：以下是您需要的項目：
	- **不支援實體對實體容錯回復**︰這表示如果您將實體伺服器容錯移轉至 Azure，然後想要容錯回復，則必須容錯回復至 VMware VM。您無法容錯回復到實體伺服器。您需要 Azure VM 以供容錯回復，如果您並未將組態伺服器部署為 VMware VM，則必須設定個別的主要目標伺服器來做為 VMware VM。之所以需要如此，是因為主要目標伺服器會與 VMware 儲存體互動並與其連接，以將磁碟還原至 VMware VM。
	- - **Azure 中的暫存處理序伺服器**︰如果您想要在容錯移轉後從 Azure 容錯回復，您必須將 Azure VM 設定為處理序伺服器，以處理來自 Azure 的複寫。容錯回復完成後，您可以刪除此 VM。
	- **VPN 連線**：如需容錯回復，您需要設定從 Azure 網路到內部部署網站的 VPN 連線 (或 Azure ExpressRoute)。
	- **個別內部部署主要目標伺服器**︰內部部署主要目標伺服器會處理容錯回復。主要目標伺服器預設會安裝在管理伺服器上，但如果要容錯回復大量資料，您應該就此目的設定個別的內部部署主要目標伺服器。

**一般架構**

![增強](./media/site-recovery-components/arch-enhanced.png)

**部署元件**

![增強](./media/site-recovery-components/arch-enhanced2.png)

**容錯回復**

![增強型容錯回復](./media/site-recovery-components/enhanced-failback.png)


- [深入了解](site-recovery-vmware-to-azure.md#azure-prerequisites) Azure 入口網站部署的需求。
- [深入了解](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)傳統入口網站的增強部署需求。
- [深入了解](site-recovery-failback-azure-to-vmware.md) Azure 入口網站的容錯回復。
- [深入了解](site-recovery-failback-azure-to-vmware-clas- [深入了解](site-recovery-failback-azure-to-vmware-classic.md) Auzre portal.sic.md 中的容錯回復) 傳統入口網站中的容錯回復。

## 複寫至 Azure︰未由 VMM 管理的 Hyper-V VM

您可以使用 Site Recovery 將未由 System Center VMM 管理的 Hyper-V VM 複寫至 Azure，如下所示：

- **使用 Azure 入口網站** - 當您在 Azure 入口網站部署 Site Recovery，您可以將 VM 容錯移轉至傳統儲存體或 Resource Manager。[深入了解](site-recovery-hyper-v-site-to-azure.md)。
- **使用傳統入口網站** - 您可以在傳統入口網站部署 Site Recovery。在此部署中，您只能將 VM 容錯移轉至 Azure 的傳統儲存體，而非 Resource Manager 儲存體。[深入了解](site-recovery-hyper-v-site-to-azure-classic.md)。

這兩個部署的架構很類似，不同之處在於︰

- 如果您在 Azure 入口網站中部署，則可以複寫至 Resource Manager 儲存體，並在容錯移轉之後使用 Resource Manager 網路連接 Azure VM。
- Azure 入口網站的部署程序已簡化，更加方便使用者使用。

以下是您需要的內容：

- **Azure 帳戶**：您將需要一個 Microsoft Azure 帳戶。
- **Azure 儲存體**：您需要 Azure 儲存體帳戶來儲存複寫的資料。在 Azure 入口網站中，您可以使用傳統帳戶或 Resource Manager 儲存體帳戶。在傳統入口網站中，您只能使用傳統帳戶。複寫的資料會儲存在 Azure 儲存體，而在容錯移轉時會建立 Azure VM。
- **Azure 網路**：在容錯移轉之後建立 Azure VM 時，您需要有 Azure VM 將連接的 Azure 網路。
- **Hyper-V 主機**︰您需要一或多個 Windows Server 2012 R2 Hyper-V 主機伺服器。在部署 Site Recovery 期間，您將會安裝
- **Hyper-V VM**︰您必須在 Hyper-V 主機伺服器上擁有一或多個 VM。在部署 Site Recovery 期間，Hyper-V 主機上的 Azure Site Recovery 提供者和 Azure 復原服務代理程式。此提供者會透過網際網路與 Site Recovery 服務協調進行複寫。此代理程式會透過 HTTPS 443 來處理資料複寫。來自提供者和代理程式的通訊都是安全且加密的。Azure 儲存體中的複寫的資料也會加密。

**一般架構**

![從 Hyper-V 站台到 Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- [深入了解](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) Azure 入口網站部署的需求。
- [深入了解](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites)傳統入口網站部署的需求。



## 複寫至 Azure︰由 VMM 管理的 Hyper-V VM

您可以使用 Site recovery 將 VMM 雲端中的 Hyper-V VM 複寫至 Azure，如下所示︰

- **使用 Azure 入口網站** - 當您在 Azure 入口網站部署 Site Recovery，您可以將 VM 容錯移轉至傳統儲存體或 Resource Manager。[深入了解](site-recovery-vmm-to-azure.md)。
- **使用傳統入口網站** - 您可以在傳統入口網站部署 Site Recovery。在此部署中，您只能將 VM 容錯移轉至 Azure 的傳統儲存體，而非 Resource Manager 儲存體。[深入了解](site-recovery-vmm-to-azure-classic.md)。

這兩個部署的架構很類似，不同之處在於︰

- 如果您在 Azure 入口網站中部署，則可以複寫至 Resource Manager 型儲存體，並在容錯移轉之後使用 Resource Manager 網路連接 Azure VM。
- Azure 入口網站的部署程序已簡化，更加方便使用者使用。


以下是您需要的內容：

- **Azure 帳戶**：您將需要一個 Microsoft Azure 帳戶。
- **Azure 儲存體**：您需要 Azure 儲存體帳戶來儲存複寫的資料。在 Azure 入口網站中，您可以使用傳統帳戶或 Resource Manager 儲存體帳戶。在傳統入口網站中，您只能使用傳統帳戶。複寫的資料會儲存在 Azure 儲存體，而在容錯移轉時會建立 Azure VM。
- **Azure 網路**︰您必須設定網路對應，以便在容錯移轉之後建立 VM 時，Azure VM 會連線到適當網路。
- **VMM 伺服器**︰您需要一或多個在 System Center 2012 R2 上執行的內部部署 VMM 伺服器，並為其設定一或多個私人雲端。如果您要在 Azure 入口網站部署，您需要邏輯和 VM 網路設定，以便能夠設定網路對應。在傳統入口網站中，這是選擇性項目。VM 網路應該連結到與雲端相關聯的邏輯網路。
- **Hyper-V 主機**︰您必須在 VMM 雲端擁有一或多個 Windows Server 2012 R2 Hyper-V 主機伺服器。
- **Hyper-V VM**︰您必須在 Hyper-V 主機伺服器上擁有一或多個 VM。

**一般架構**

	![VMM 至 Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- [深入了解](site-recovery-vmm-to-azure.md#azure-requirements) Azure 入口網站部署的需求。
- [深入了解](site-recovery-vmm-to-azure-classic.md#before-you-start)傳統入口網站部署的需求。




## 複寫至次要網站：VMware 虛擬機器或實體伺服器 

若要將 VMware VM 或實體伺服器複寫至次要網站做為 Azure Site Recovery 訂用帳戶中包含的下載 InMage Scout。您可以從 Azure 入口網站或 Azure 傳統入口網站加以下載。

您會在每個網站 (組態、處理序、主要目標) 中設定元件伺服器，並在您要複寫的機器上安裝整合代理程式。初始複寫之後，每部機器上的代理程式會將差異複寫變更傳送至處理序伺服器。處理序伺服器會最佳化此資料，並且將其傳輸至次要網站上的主要目標伺服器。設定伺服器會管理複寫程序。

以下是所需項目：

**Azure 帳戶**︰您會使用 InMage Scout 部署此案例。若要取得它，您必須要有 Azure 訂用帳戶。建立 Site Recovery 保存庫之後，您可下載 InMage Scout 並安裝最新的更新，以設定部署。**處理序伺服器 (主要網站)**：設定您的主要網站的處理序伺服器元件，以處理快取、壓縮和資料最佳化。它也會處理您想要保護的機器的整合代理程式推入安裝。**VMware ESX/ESXi 和 vCenter 伺服器 (主要網站)**：如果您要保護 VMware VM，您需要有 VMware EXS/ESXi Hypervisor 和 (選擇性) VMware vCenter 伺服器來管理 Hypervisor。
- **VM/實體伺服器 (主要網站)**：您想要保護的 VMware VM 或 Windows/Linux 實體伺服器需要安裝整合代理程式。整合代理程式也安裝在作為主要目標伺服器的電腦上。此代理程式會做為所有元件之間的通訊提供者。
- - **設定伺服器 (次要網站)**：設定伺服器是您安裝的第一個元件，它會安裝在次要網站以管理、設定和監視您的部署，使用管理網站或 vContinuum 主控台。在部署中只有單一設定伺服器，且必須安裝在執行 Windows Server 2012 R2 的電腦上。
- **vContinuum 伺服器 (次要網站)**：與設定伺服器安裝在相同的位置 (次要網站)。它會提供主控台來管理及監視您的受保護的環境。在預設安裝中，vContinuum 伺服器是第一個主要目標伺服器，並且已安裝整合代理程式。
- **主要目標伺服器 (次要網站)**：主要目標伺服器保留複製的資料。它會從處理序伺服器接收資料，在次要網站中建立複本機器，並且保存資料保留點。您需要的主要目標伺服器的數目取決於您要保護的機器數目。如果您想要容錯回復到主要網站，您也需要主要目標伺服器。

**一般架構**

![VMware 至 VMware](./media/site-recovery-components/vmware-to-vmware.png)


## 複寫至次要網站：由 VMM 管理的 Hyper-V VM


您可以使用 Site Recovery 將由 System Center VMM 管理的 Hyper-V VM 複寫至次要資料中心，如下所示：

- **使用 Azure 入口網站** - 當您在 Azure 入口網站部署 Site Recovery。[深入了解](site-recovery-hyper-v-site-to-azure.md)。
- **使用傳統入口網站** - 您可以在傳統入口網站部署 Site Recovery。[深入了解](site-recovery-hyper-v-site-to-azure-classic.md)。

這兩個部署的架構很類似，不同之處在於︰

- 如果您在 Azure 入口網站部署，您必須設定網路對應。這在傳統入口網站中是選擇性的。
- Azure 入口網站的部署程序已簡化，更加方便使用者使用。
- - 如果您在 Azure 傳統入口網站部署，則可以使用[儲存體對應](site-recovery-storage-mapping.md)。

以下是您需要的內容：

- **Azure 帳戶**：您將需要一個 Microsoft Azure 帳戶。
- **VMM 伺服器**：建議在主要網站和次要網站上都有一個 VMM 伺服器，且各自包含至少一個 VMM 私人雲端。伺服器至少應該執行 System Center 2012 SP1 與最新的更新，並且連線至網際網路。雲端應該設定 Hyper-V 容量設定檔。您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。此提供者會透過網際網路與 Site Recovery 服務協調進行複寫。Provider 和 Azure 之間的通訊都是安全且加密的。
- **Hyper-V 伺服器**：Hyper-V 主機伺服器應該位於主要和次要 VMM 雲端中。主機伺服器至少應該執行 Windows Server 2012 且安裝最新的更新，並且連接至網際網路。主要和次要 Hyper-V 主機伺服器之間，使用 Kerberos 或憑證驗證透過 LAN 或 VPN 來複寫資料。
- **受保護的電腦**：來源 Hyper-V 主機伺服器應該至少有一個您想要保護的 VM。

**一般架構**

	![內部部署至內部部署](./media/site-recovery-components/arch-onprem-onprem.png)


- [深入了解](site-recovery-vmm-to-vmm.md#azure-prerequisites) Azure 入口網站的部署需求。
- - [深入了解](site-recovery-vmm-to-vmm-classic.md#before-you-start) Azure 傳統入口網站的部署需求。




## 使用 SAN 複寫來複寫至次要網站：由 VMM 管理的 Hyper-V VM

您可以在 Azure 傳統入口網站使用 SAN 複寫，將 VMM 雲端中管理的 Hyper-V VM 複寫至次要網站。新的 Azure 入口網站目前不支援此案例。

在此案例中，您會在 Site Recovery 部署期間於 VMM 伺服器上安裝 Azure Site Recovery Provider。此提供者會透過網際網路與 Site Recovery 服務協調進行複寫。資料是在主要和次要儲存體陣列之間，使用同步 SAN 複寫進行複寫。

以下是您需要的內容：

**Azure 帳戶**︰您需要有 Azure 訂用帳戶
- **SAN 陣列**：主要 VMM 伺服器管理的[支援的 SAN 陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。SAN 與次要網站中的另一個 SAN 陣列共用網路基礎結構。
- **VMM 伺服器**：建議在主要網站和次要網站上都有一個 VMM 伺服器，且各自包含至少一個 VMM 私人雲端。伺服器至少應該執行 System Center 2012 SP1 與最新的更新，並且連線至網際網路。雲端應該設定 Hyper-V 容量設定檔。
- **Hyper-V 伺服器**：位於主要和次要 VMM 雲端中的 Hyper-V 主機伺服器。主機伺服器至少應該執行 Windows Server 2012 且安裝最新的更新，並且連接至網際網路。
- **受保護的電腦**：來源 Hyper-V 主機伺服器應該至少有一個您想要保護的 VM。

**SAN 複寫架構**

![SAN 複寫](./media/site-recovery-components/arch-onprem-onprem-san.png)

[深入了解](site-recovery-vmm-san.md#before-you-start)部署需求。
### 內部部署



## Hyper-V 保護生命週期

此工作流程會顯示保護、複寫和容錯移轉 Hyper-V 虛擬機器的程序。

1. **啟用保護**：設定 Site Recovery 保存庫、設定 VMM 雲端或 Hyper-V 網站的複寫設定，以及啟用 VM 的保護。會起始稱為「啟用保護」的作業，並且可以在 [作業] 索引標籤中監視。作業會檢查符合必要條件的機器，然後再叫用 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) 方法，它會使用您的設定來設定到 Azure 的複寫。「啟用保護」作業也會叫用 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 方法來初始化完整的 VM 複寫。
2. **初始複寫**：擷取虛擬機器快照，並且逐一複寫虛擬硬碟，直到它們全部複製到 Azure 或次要資料中心。完成此作業所需的時間取決於 VM 大小、網路頻寬，以及初始複寫方法。如果在初始複寫進行時發生磁碟變更，Hyper-V 複本複寫追蹤器會追蹤這些變更，並記錄在 Hyper-V 複寫記錄檔 (.hrl)，該檔案位於與磁碟相同的資料夾中。每個磁碟都有一個相關聯的.hrl 檔案，將會傳送至次要儲存體。請注意，當初始複寫正在進行時，快照和記錄檔會使用磁碟資源。當初始複寫完成時，會刪除 VM 快照，並且會同步處理和合併記錄檔中的差異磁碟變更。
3. **完成保護**：初始複寫完成之後，**完成保護**作業會設定網路和其他複寫後設定，虛擬機器就會受到保護。如果您要複寫至 Azure，您可能需要調整虛擬機器的設定，使其準備好進行容錯移轉。此時，您可以執行測試容錯移轉，以確認一切如預期般運作。
4. **複寫**：在初始複寫之後，會根據複寫設定，發生差異同步處理。
	- **複寫失敗**：如果差異複寫失敗且完整複寫因為頻寬或時間需要大量成本，就會發生重新同步處理。例如，如果 .hrl 檔案達到磁碟大小的 50% 時，系統就會標示 VM 以便重新同步處理。重新同步處理會計算來源和目標虛擬機器的總和檢查碼，並只傳送差異部分，藉此有效減少傳送的資料量。重新同步處理完成之後，會繼續進行差異複寫。根據預設，重新同步處理會排程在上班時間以外的時間自動執行，但是您可以手動重新同步處理虛擬機器。
	- **複寫錯誤**：如果發生複寫錯誤，會有內建的重試。如果它是無法復原的錯誤，例如驗證或授權錯誤，或複本機器處於無效狀態，則不會嘗試重試。如果它是可復原的錯誤，例如網路錯誤，或低磁碟空間/記憶體，則會發生重試，重試之間的間隔會遞增 (1、2、4、8、10，然後每隔 30 分鐘)。
4. **計劃性/非計劃性容錯移轉**：您可以視需要執行計劃性或非計劃性容錯移轉。如果您執行計劃性容錯移轉，則來源 VM 會關閉以確保不會遺失資料。建立複本 VM 之後，它們即會處於認可擱置中的狀態。您必須認可它們以完成容錯移轉，除非您使用 SAN 複寫，在此情況下認可是自動的。主要網站已啟動並執行之後，會發生容錯回復。如果您已複寫到 Azure，則反向複寫是自動的。否則您應該以手動方式開始反向複寫。
 

![工作流程](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## 後續步驟

[準備部署](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0720_2016-->