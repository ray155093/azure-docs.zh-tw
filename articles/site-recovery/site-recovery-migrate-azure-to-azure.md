---
title: "在 Azure 區域之間移轉 Azure IaaS VM | Microsoft Docs"
description: "使用 Azure Site Recovery，將 Azure IaaS 虛擬機器從一個 Azure 區域移轉到另一個區域。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b0fb6e2b86aa0a47b7250face90be8ab2d06b78e
ms.contentlocale: zh-tw
ms.lasthandoff: 03/15/2017


---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在 Azure 區域之間移轉 Azure IaaS 虛擬機器
## <a name="overview"></a>Overview
歡迎使用 Azure Site Recovery！ 如果您想要在 Azure 區域之間移轉 Azure VM，請使用本文。 開始之前，請注意：

* Azure 建立和處理資源的部署模型有二種：Azure Resource Manager 和傳統。 Azure 也有兩個入口網站 – 支援傳統部署模型的 Azure 傳統入口網站，以及支援兩種部署模型的 Azure 入口網站。 無論您是在 Resource Manager 還是傳統中設定 Site Recovery，基本的移轉步驟都是相同的。 不過本文中的 UI 指示和螢幕擷取畫面都是和 Azure 入口網站相關。
* **目前您只能從一個區域移轉到另一個區域。您可以將 VM 從一個 Azure 區域容錯移轉至另一個，但是無法將它們容錯移轉回來。**
* 本文中的移轉指示是以將實體機器複寫到 Azure 的指示為基礎。 它包含了 [將 VMware VM 或實體伺服器複寫至 Azure](site-recovery-vmware-to-azure.md)中步驟的連結，其內容描述如何在 Azure 入口網站中複寫實體伺服器。
* 如果您是在傳統入口網站中設定 Site Recovery，請遵循 [這篇文章](site-recovery-vmware-to-azure-classic.md)中的詳細指示。

在這篇文章下方或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## <a name="prerequisites"></a>必要條件
以下是您針對此部署所需要的項目︰

* **組態伺服器**︰執行 Windows Server 2012 R2 並做為組態伺服器的內部部署 VM。 您也可在此 VM 上安裝其他 Site Recovery 元件 (包括處理序伺服器和主要目標伺服器)。 請參閱[案例架構](site-recovery-components.md#vmware-to-azure)和[組態伺服器必要條件](site-recovery-vmware-to-azure.md#prerequisites)以深入了解。
* **IaaS 虛擬機器**：您想要移轉的 VM。 您可以將那些 VM 視為實體機器來移轉它們。

## <a name="deployment-steps"></a>部署步驟
本節描述新 Azure 入口網站中的部署步驟。

1. [建立保存庫](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault)。
2. [部署組態伺服器](site-recovery-vmware-to-azure.md#prepare-the-configuration-server)。
3. 部署組態伺服器之後，請檢查該伺服器是否能夠與您要移轉的 VM 通訊。
4. [設定複寫設定](site-recovery-vmware-to-azure.md#set-up-replication-settings)。 建立複寫原則並將它指派到組態伺服器。
5. [安裝行動服務](site-recovery-vmware-to-azure.md#prepare-vms-for-replication)。 所有要保護的 VM 都需要安裝行動服務。 這項服務會將資料傳送至處理序伺服器。 行動服務可以手動方式安裝，或在為 VM 啟用保護時由處理序伺服器自動推入安裝。 您所要移轉 VM 上的防火牆規則應該設定為允許推入安裝這項服務。
6. [啟用複寫](site-recovery-vmware-to-azure.md#enable-replication)。 針對您想要移轉的 VM 啟用複寫。 您可以使用虛擬機器的私人 IP 位址，探索要移轉至 Azure 的 IaaS 虛擬機器。 這個位址可在 Azure 的虛擬機器儀表板上找到。 當您啟用複寫時，您會將 VM 的機器類型設定為實體機器。
7. [ 執行非計劃性容錯移轉](site-recovery-failover.md)。 初始複寫完成之後，您可以執行從一個 Azure 區域到另一個區域的非計劃性容錯移轉。 (選擇性) 您可以建立復原計劃並執行非計劃性容錯移轉，在區域與區域之間移轉多部虛擬機器。 [深入了解](site-recovery-create-recovery-plans.md) 復原計劃。

## <a name="next-steps"></a>後續步驟
在 [什麼是 Azure Site Recovery？](site-recovery-overview.md)

