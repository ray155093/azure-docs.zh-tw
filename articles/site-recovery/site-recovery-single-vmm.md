
<properties
	pageTitle="Azure Site Recovery：在單一 VMM 伺服器上複寫 Hyper-V 虛擬機器 | Microsoft Azure"
	description="本文章說明當您只有單一 VMM 伺服器時該如何複寫 Hyper-V 虛擬機器。"
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
	ms.workload="backup-recovery"
	ms.date="07/06/2016"
	ms.author="raynew"/>

#  複寫單一 VMM 伺服器上的 Hyper-V 虛擬機器

閱讀本文來了解當您的部署中只有單一 VMM 伺服器時，如何覆寫位在 VMM 雲端 Hyper-V 主機伺服器上的 Hyper-V 虛擬機器。

如果您在閱讀本文後有任何問題，請將問題張貼在本頁底部或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## 概觀

有數種方式可以覆寫位在 VMM 雲端 Hyper-V 主機上的 Hyper-V VM：

- 複寫至 Azure。
- 複寫至次要 VMM 站台

但如果您想要複寫至次要 VMM 位置，可是您的部署中只有單一 VMM 伺服器時該怎麼辦？

在這個案例中，您有下列幾個選項：

- 將 VMM 雲端中的 Hyper-V VM 複寫至 Azure。[深入了解](site-recovery-vmm-to-azure.md)此案例。
- 在單一 VMM 伺服器上的雲端之間進行複寫。

我們建議第一個選項，因為第二個選項的容錯移轉和復原不會直接順暢進行，且需要一些手動步驟。如果您想在站台之間複寫，而不是覆寫至 Azure，則您有幾個選項。


## 在單一的獨立 VMM 伺服器與站台間複寫

若在這個案例中要於站台之間覆寫，您會將單一 VMM 伺服器部署為主要站台中的虛擬機器，並使用 Site Recovery 與 Hyper-V 複本將此 VM 複寫到次要站台。作法：

1. 在 Hyper-V VM 上設定 VMM。建議您在這樣做的時候也考慮將 VMM 使用的 SQL Server 執行個體共置在相同的 VM 上。因為只有一個 VM 必須具現化，這樣可以節省時間。如果您想要使用遠端 SQL Server 執行個體卻發生中斷，則必須先復原該執行個體後再復原 VMM。
2. VMM 伺服器必須設定至少兩個雲端。其中一個雲端包含您想要複寫的 VM，另一個雲端做為次要位置。包含您要保護之 VM 的雲端必須有：

	- 一或多個 VMM 主機群組，且其中各包含一或多個 Hyper-V 主機伺服器。
	- 每個 Hyper-V 主機伺服器上至少有一個 Hyper-V 虛擬機器。
3. 建立 Site Recovery 保存庫，產生並下載保存庫註冊金鑰，然後在保存庫中註冊 VMM 伺服器。在註冊期間，您會在 VMM 伺服器上安裝 Azure Site Recovery 提供者。
4. 在 VMM 的虛擬機器中設定一或多個雲端，然後將含有受保護 VM 的 Hyper-V 主機加入這些雲端。
3. 在 Azure Site Recovery 中，設定這些雲端的保護設定。在來源和目標位置中，指定單一 VMM 伺服器的名稱。如果您設定網路對應，則會針對下列兩處的 VM 網路進行對應：含有受保護 VM 的雲端與您要複寫的雲端。
4. 因為兩個雲端位於相同的伺服器上，您可使用 [透過網路] 做為複寫方法，以啟用受保護 VM 的複寫功能。
4. 使用 Hyper-V 管理員主控台，在包含 VMM VM 的 Hyper-V 主機上啟用 Hyper-V 複本，並啟用 VM 的複寫功能。請不要將 VMM 虛擬機器加入受 Site Recovery 保護的雲端，以確保 Site Recovery 不會覆寫 Hyper-V 複本設定。
5. 如果您想要建立復原方案，請針對來源和目標指定相同的 VMM 伺服器。

按照[這篇文章](site-recovery-vmm-to-vmm.md)中的指示來建立保存庫、取得金鑰、註冊伺服器，以及設定保護。

### 中斷之後

發生中斷時，您可以復原 Hyper-V VM 上的工作負載，如下所示：

1. 搭配使用 Hyper-V 管理員與規劃的容錯移轉，手動將 VMM VM 容錯移轉至次要站台。
2. 復原 VMM VM 之後，您可以從次要站台登入 Hyper-V 復原管理員，並執行從次要站台至主要站台的非計劃性 VM 容錯移轉。請注意，必須手動將 VMM VM 容錯移轉至次要站台，然後工作負載 VM 才能進行容錯移轉。
3. 在非計劃性的容錯移轉完成之後，將可透過主要站台再次存取所有資源。


![獨立虛擬 VMM 伺服器](./media/site-recovery-single-vmm/single-vmm-standalone.png)

## 在延伸叢集中的單一 VMM 伺服器與站台間複寫

您不需要將獨立的 VMM 伺服器部署為複寫至次要站台的虛擬機器，而可將其部署為 Windows 容錯移轉叢集中的 VM，讓 VMM 發揮高可用性。這可以提供工作負載彈性及硬體故障的防護。若要使用 Site Recovery 來部署，應將 VMM VM 部署至跨地理不同位置之站台的延伸叢集中。作法：

1. 在 Windows 容錯移轉叢集中的虛擬機器上安裝 VMM，然後選取在安裝期間以高度可用性執行伺服器的選項。
2. VMM 所使用的 SQL Server 執行個體應以 SQL Server AlwaysOn 可用性群組來複寫，這樣一來次要站台就會有資料庫的複本。

請注意，當您設定 Site Recovery 時，您必須在 Site Recovery 保存庫中註冊叢集內的每個 VMM 伺服器。您需要將提供者安裝在作用中的節點上，並完成安裝以在保存庫中註冊該 VMM 伺服器。然後在其他節點上安裝提供者。
 
### 中斷之後 

如果發生中斷，VMM 伺服器及其對應的 SQL Server 資料庫會容錯移轉並可從次要站台存取。

![叢集虛擬 VMM 伺服器](./media/site-recovery-single-vmm/single-vmm-cluster.png)

## 後續步驟

[深入了解](site-recovery-vmm-to-vmm.md) VMM 至 VMM 複寫的 Site Recovery 部署的詳細資訊。




 

<!---HONumber=AcomDC_0706_2016-->