
---
title: Azure Site Recovery：在單一 VMM 伺服器上複寫 Hyper-V 虛擬機器 | Microsoft Docs
description: 本文章說明當您只有單一 VMM 伺服器時該如何複寫 Hyper-V 虛擬機器。
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 08/24/2016
ms.author: raynew

---
# 複寫單一 VMM 伺服器上的 Hyper-V 虛擬機器
閱讀本文來了解當您的部署中只有單一 VMM 伺服器時，如何覆寫位在 VMM 雲端 Hyper-V 主機伺服器上的 Hyper-V 虛擬機器。

Azure 建立和處理資源的[部署模型](../resource-manager-deployment-model.md)有二種：Azure Resource Manager 和傳統。Azure 也有兩個入口網站 – 支援傳統部署模型的 Azure 傳統入口網站，以及支援兩種部署模型的 Azure 入口網站。本文包含在 Azure 入口網站中設定複寫的指示。

如果您在閱讀本文後有任何問題，請將問題張貼在本頁底部的 Disqus 註解中，或是 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上。

## Overview
如果您想要複寫位於 VMM 中 Hyper-V 主機上的 Hyper-V VM，且您只有單一 VMM 伺服器，您可以[複寫到 Azure](site-recovery-vmm-to-azure.md)，或是於單一 VMM 伺服器上在雲端之間進行複寫。

我們建議您複寫到 Azure，因為在雲端之間進行複寫時，並無法順暢地進行容錯移轉和復原，而且需要執行數個手動步驟。如果您只想要使用 VMM 伺服器進行複寫，您可以執行下列動作：

* 以單一的獨立 VMM 伺服器進行複寫
* 以部署在延伸 Windows 叢集中的單一 VMM 伺服器進行複寫

## 在單一的獨立 VMM 伺服器與站台間複寫
![獨立虛擬 VMM 伺服器](./media/site-recovery-single-vmm/single-vmm-standalone.png)

在這個案例中，您會將單一 VMM 伺服器部署為主要站台中的虛擬機器，並使用 Site Recovery 與 Hyper-V 複本將此 VM 複寫到次要站台。

1. 在 Hyper-V VM 上設定 VMM。為了節省時間，我們建議您將 VMM 使用的 SQL Server 執行個體安裝在相同的 VM 上。如果您想要使用遠端 SQL Server 執行個體卻發生中斷，則必須先復原該執行個體後再復原 VMM。
2. VMM 伺服器必須設定至少兩個雲端。其中一個雲端包含您想要複寫的 VM，另一個雲端做為次要位置。包含您要保護之 VM 的雲端必須有：
   
   * 一或多個 VMM 主機群組，且其中各包含一或多個 Hyper-V 主機伺服器。
   * 每個 Hyper-V 主機伺服器上至少有一個 Hyper-V 虛擬機器。
3. 建立復原服務保存庫，產生並下載保存庫註冊金鑰，然後在保存庫中註冊 VMM 伺服器。在註冊期間，您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。
4. 在 VMM VM 中設定一或多個雲端，然後將 Hyper-V 主機加入這些雲端。
5. 為雲端設定保護設定。您可以指定單一 VMM 伺服器的名稱，做為來源和目標位置。若要設定網路對應，您可以將雲端的 VM 網路及您想要保護的 VM，對應到複寫雲端的 VM 網路。
6. 因為兩個雲端都位於相同的伺服器上，您可以透過網路為您想要保護的 VM 啟用初始複寫。
7. 使用 Hyper-V 管理員主控台，在包含 VMM VM 的 Hyper-V 主機上啟用 Hyper-V 複本，並啟用 VM 的複寫功能。請確定您沒有將 VMM VM 新增到任何受 Site Recovery 保護的雲端。這能確保 Hyper-V 複寫設定不會受到 Site Recovery 覆寫。
8. 如果您想要建立復原方案，請針對來源和目標指定相同的 VMM 伺服器。

請遵循[這篇文章](site-recovery-vmm-to-vmm.md)中的指示來建立保存庫、註冊伺服器及設定保護。

### 發生中斷時的處理方式
如果發生完全中斷，而您必須從次要站台進行操作，請執行下列動作：

1. 在次要站台的 Hyper-V 管理員主控台中，執行非計劃性容錯移轉以將 VMM VM 從主要站台容錯移轉到次要站台。
2. 確認 VMM VM 已在次要站台中啟用並執行。
3. 在復原服務保存庫中，執行非計劃性容錯移轉以將工作負載 VM 從主要雲端容錯移轉到次要雲端。若要完成 VM 的非計劃性容錯移轉，請認可容錯移轉或視需要選取不同的復原點。
4. 完成非計劃性容錯移轉之後，使用者便可以存取次要站台中的工作負載資源。

當主要站台再次正常運作之後，請執行下列動作：

1. 在 Hyper-V 管理員主控台中，針對 VMM VM 啟用反轉複寫方向，以開始將它從次要站台複寫到主要站台。
2. 在 Hyper-V 管理員主控台中，執行計劃的容錯移轉以將 VMM VM 容錯移轉回主要站台。認可容錯移轉以完成它。然後啟用反轉複寫方向，以開始將 VMM 從主要站台複寫到次要站台。
3. 在復原服務保存庫中，針對工作負載 VM 啟用反轉複寫方向，以開始將它們從次要站台複寫到主要站台。
4. 在復原服務保存庫中，執行計劃的容錯移轉以將工作負載 VM 容錯移轉回主要站台。認可容錯移轉以完成它。然後啟用反轉複寫方向，以開始將工作負載 VM 從主要站台複寫到次要站台。

## 在延伸叢集中的單一 VMM 伺服器與站台間複寫
![叢集虛擬 VMM 伺服器](./media/site-recovery-single-vmm/single-vmm-cluster.png)

您不需要將獨立的 VMM 伺服器部署為複寫至次要站台的 VM，而可將它部署為 Windows 容錯移轉叢集中的 VM，讓 VMM 發揮高可用性。這可以提供工作負載彈性及硬體故障的防護。若要使用 Site Recovery 來部署，應將 VMM VM 部署至跨地理不同位置之站台的延伸叢集中。作法：

1. 在 Windows 容錯移轉叢集中的虛擬機器上安裝 VMM，然後選取在安裝期間以高度可用性執行伺服器的選項。
2. VMM 所使用的 SQL Server 執行個體應以 SQL Server AlwaysOn 可用性群組來複寫，這樣一來次要站台就會有資料庫的複本。
3. 請遵循[這篇文章](site-recovery-vmm-to-vmm.md)中的指示來建立保存庫、註冊伺服器及設定保護。您必須在復原服務保存庫的叢集中註冊每部 VMM 伺服器。若要這樣做，您必須在作用中的節點上安裝 Provider，並註冊 VMM 伺服器。然後在其他節點上安裝提供者。

### 發生中斷時的處理方式
如果發生中斷，VMM 伺服器及其對應的 SQL Server 資料庫會容錯移轉並可從次要站台存取。

## 後續步驟
[深入了解](site-recovery-vmm-to-vmm.md) VMM 至 VMM 複寫的 Site Recovery 部署詳細資訊。

<!---HONumber=AcomDC_0824_2016-->