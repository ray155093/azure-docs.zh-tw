---
title: "使用 Site Recovery 移轉至 Azure | Microsoft Docs"
description: "本文概述如何使用 Azure Site Recovery 將 VM 與實體伺服器移轉至 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f82634af931a1e9a9646c5631ebd0e5923a0adcc
ms.openlocfilehash: cbb6de4587871c40c9d4e97c9fb2a88eab4945a6


---
# <a name="migrate-to-azure-with-site-recovery"></a>使用 Site Recovery 移轉至 Azure？

請閱讀本文，以概略了解如何使用 Azure Site Recovery 服務來移轉虛擬機器和實體伺服器。

組織需要 BCDR 策略，以決定應用程式、工作負載和資料如何在規劃與未規劃停機期間維持運作，並儘速復原到正常運作的情況。 BCDR 策略應保護商務資料安全且可復原，並確保發生災害時工作負載仍持續可用。

Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。 當您的主要位置發生故障時，您容錯移轉至次要位置，讓應用程式和工作負載保持可用。 當它恢復正常作業時，容錯回復至您的主要位置。 深入了解 [什麼是 Site Recovery？](site-recovery-overview.md)

本文說明 [Azure 入口網站](https://portal.azure.com)中的部署作業。 [Azure 傳統入口網站](https://manage.windowsazure.com/)可用來維護現有的 Site Recovery 保存庫，但無法建立新的保存庫。

若有任何意見，請張貼於文末。 請在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)提出技術問題。


## <a name="what-do-we-mean-by-migration"></a>移轉的意思為何？

您可以部署 Site Recovery 以將內部部署 VM 和實體伺服器完整複寫至 Azure 或次要網站。您會複寫機器、在主要網站發生中斷時將機器從中容錯移轉出來，然後在機器復原時將它們容錯回復到主要網站。 除了完整複寫，您還可以使用 Site Recovery 將 VM 和實體伺服器移轉到 Azure，讓使用者可以從 Azure VM 存取機器的工作負載。 移轉作業需要進行複寫以及從主要網站容錯移轉至 Azure。 不過，和完整複寫不同的是，移轉不包含容錯回復機制。

## <a name="what-can-site-recovery-migrate"></a>Site Recovery 可以移轉什麼項目？

您可以：

- 將在內部部署 Hyper-V VM、VMware VM 和實體伺服器上執行的工作負載，移轉到 Azure VM 上來執行。 在此案例中，您也可以執行完整的複寫和容錯回復。
- 在 Azure 區域之間移轉 [Azure IaaS VM](site-recovery-migrate-azure-to-azure.md)。 此案例目前只支援移轉，亦即不支援容錯回復。
- 將 [AWS Windows 執行個體](site-recovery-migrate-aws-to-azure.md)移轉到 Azure IaaS VM。 此案例目前只支援移轉，亦即不支援容錯回復。

## <a name="migrate-on-premises-vms-and-physical-servers"></a>移轉內部部署 VM 和實體伺服器

若要移轉內部部署 Hyper-V VM、VMware VM 和實體伺服器，您所需遵循的步驟幾乎和一般複寫時所使用的步驟相同。 您要設定復原服務保存庫、設定所需的管理伺服器 (視您要移轉的項目而定)、將它們新增至保存庫，並指定複寫設定。 為您想要移轉的機器啟用複寫，並執行快速的測試容錯移轉，以確保一切運作正常。

確認複寫環境有用之後，您需要根據您的案例[所支援的項目](site-recovery-failover.md#failover-and-failback)使用計劃性或非計劃性容錯移轉。 若是進行移轉，您不需要認可容錯移轉或刪除任何項目。 相反地，您要為所要移轉的每一部機器選取**完成移轉**選項。 **完成移轉**動作會完成移轉程序、移除機器的複寫，並停止該機器的 Site Recovery 計費。

## <a name="migrate-between-azure-regions"></a>在不同的 Azure 地區之間移轉

您可以使用 Site Recovery 在區域之間移轉 Azure VM。 此案例只支援移轉。 換句話說，您可以複寫 Azure VM，並將它們容錯移轉至另一個區域，但您無法容錯回復。 在此案例中，您要設定復原服務保存庫、部署用來管理複寫的內部部署組態伺服器、將它新增至保存庫，以及指定複寫設定。 為您想要移轉的機器啟用複寫，並執行快速的測試容錯移轉。 然後使用**完成移轉**選項執行非計劃性容錯移轉。

## <a name="migrate-aws-to-azure"></a>將 AWS 移轉至 Azure

您可以將 AWS 執行個體移轉至 Azure VM。 此案例只支援移轉。 換句話說，您可以複寫 AWS 執行個體，並將它們容錯移轉至 Azure，但您無法容錯回復。 在進行移轉時，AWS 執行個體的處理方式和實體伺服器相同。 您要設定復原服務保存庫、部署用來管理複寫的內部部署組態伺服器、將它新增至保存庫，以及指定複寫設定。 為您想要移轉的機器啟用複寫，並執行快速的測試容錯移轉。 然後使用**完成移轉**選項執行非計劃性容錯移轉。




## <a name="next-steps"></a>後續步驟

- [將 VMware VM 移轉至 Azure](site-recovery-vmware-to-azure.md)
- [將實體伺服器移轉至 Azure](site-recovery-vmware-to-azure.md)
- [將 VMM 雲端中的 Hyper-V VM 移轉至 Azure](site-recovery-vmm-to-azure.md)
- [將沒有 VMM 的 Hyper-V VM 移轉至 Azure](site-recovery-hyper-v-site-to-azure.md)
- [在 Azure 區域之間移轉 Azure VM](site-recovery-migrate-azure-to-azure.md)
- [將 AWS 執行個體移轉至 Azure](site-recovery-migrate-aws-to-azure.md)



<!--HONumber=Jan17_HO4-->


