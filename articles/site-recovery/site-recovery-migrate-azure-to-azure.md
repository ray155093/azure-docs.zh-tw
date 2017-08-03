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
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ef2972c077a2b1dd2b2fd6ce53cc6560520ea870
ms.contentlocale: zh-tw
ms.lasthandoff: 07/14/2017

---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在 Azure 區域之間移轉 Azure IaaS 虛擬機器
## <a name="overview"></a>Overview
歡迎使用 Azure Site Recovery！ 如果您想要在 Azure 區域之間移轉 Azure VM，請使用本文。 開始之前，請注意：

* Azure 建立和處理資源的部署模型有二種：Azure Resource Manager 和傳統。 Azure 也有兩個入口網站 – 支援傳統部署模型的 Azure 傳統入口網站，以及支援兩種部署模型的 Azure 入口網站。 無論您是在 Resource Manager 還是傳統中設定 Site Recovery，基本的移轉步驟都是相同的。 不過本文中的 UI 指示和螢幕擷取畫面都是和 Azure 入口網站相關。
* **目前您只能從一個區域移轉到另一個區域。您可以將 VM 從一個 Azure 區域容錯移轉至另一個，但是無法將它們容錯移轉回來。**

在這篇文章下方或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## <a name="prerequisites"></a>必要條件
以下是您針對此部署所需要的項目︰

* **IaaS 虛擬機器**：您想要移轉的 VM。 您可以將那些 VM 視為實體機器來移轉它們。

## <a name="deployment-steps"></a>部署步驟
本節描述新 Azure 入口網站中的部署步驟。

1. [建立保存庫](site-recovery-vmware-to-azure.md)。
2. [啟用複寫](site-recovery-vmware-to-azure.md)。 針對您想要移轉的虛擬機器啟用複寫，並選擇 Azure 做為來源。 
3. [ 執行非計劃性容錯移轉](site-recovery-failover.md)。 初始複寫完成之後，您可以執行從一個 Azure 區域到另一個區域的非計劃性容錯移轉。 (選擇性) 您可以建立復原計劃並執行非計劃性容錯移轉，在區域與區域之間移轉多部虛擬機器。 [深入了解](site-recovery-create-recovery-plans.md) 復原計劃。

## <a name="next-steps"></a>後續步驟
在 [什麼是 Azure Site Recovery？](site-recovery-overview.md)

