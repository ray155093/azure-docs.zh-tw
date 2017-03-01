---
title: "Azure 建議程式高可用性建議 | Microsoft Docs"
description: "使用 Azure 建議程式來改善 Azure 部署的高可用性。"
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: a6ee1d018bf33aa76d089d20f6e71318a3586aa8
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-high-availability-recommendations"></a>建議程式高可用性建議

建議程式可協助您確保和改善業務關鍵應用程式的持續性。 您可以從 [建議程式] 儀表板的 [高可用性] 索引標籤，利用建議程式取得高可用性建議。

![](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="virtual-machines-without-an-availability-set"></a>沒有可用性設定組的虛擬機器

建議程式會識別不屬於可用性設定組的虛擬機器，並建議將它們移至某個可用性設定組中。 若要為應用程式提供備援，建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 Azure 虛擬機器 SLA。 您可以選擇建立虛擬機器的可用性設定組，或將虛擬機器新增至現有的可用性設定組。

> [!NOTE]
> 如果您選擇建立可用性設定組，您必須在建立可用性設定組之後，至少再將一個虛擬機器新增至該可用性設定組。 我們建議讓兩部或多部虛擬機器組成一個可用性設定組，以確保其中一部機器可用於中斷期間。

![](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="availability-sets-with-a-single-virtual-machine"></a>包含單一虛擬機器的可用性設定組 

建議程式會識別包含單一虛擬機器的可用性設定組，並建議將一部或多部虛擬機器新增至該可用性設定組。 若要為應用程式提供備援，建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 Azure 虛擬機器 SLA。 您可以選擇建立虛擬機器或使用現有的虛擬機器，並將它新增至可用性設定組。  


![](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)

## <a name="virtual-machines-with-standard-disks"></a>使用標準磁碟的虛擬機器

建議程式會識別使用標準磁碟的虛擬機器，並建議升級為進階磁碟。  
針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用「進階儲存體」的虛擬機器磁碟會將資料儲存在固態硬碟 (SSD) 上。 建議您將任何需要高 IOPS 的虛擬機器磁碟移轉到「Azure 進階儲存體」，以發揮應用程式最佳效能。 如果您的磁碟不需要高 IOPS，您可以在「標準儲存體」中維護它以限制成本。 「標準儲存體」會將虛擬機器磁碟資料儲存在硬碟機 (HDD) 而非 SSD 上。 您可以選擇將虛擬機器磁碟移轉到進階磁碟。 大部分的虛擬機器 SKU 都支援進階磁碟。 不過在某些情況下，如果您想要使用進階磁碟，您可能還需要升級虛擬機器 SKU。   

![](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png) 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>如何存取建議程式中的高可用性建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左導覽窗格中的 [更多服務]，在服務功能表窗格中，向下捲動至 [監視和管理]，然後按一下 [Azure 建議程式]。 這會啟動 [建議程式] 儀表板。 
3. 在 [建議程式] 儀表板上，按一下 [高可用性] 索引標籤，然後選取您想要接收建議的訂用帳戶。

> [!NOTE]
> 若要存取 Advisor 的建議，您必須先向 Advisor「註冊」您的訂用帳戶。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，訂用帳戶、資源群組或特定的資源的 [擁有者]、[參與者] 或 [讀取者]，都可以存取 Advisor 建議。

## <a name="next-steps"></a>後續步驟

若要深入了解建議程式建議，請參閱下列資源：
-  [Azure 建議程式簡介](advisor-overview.md)
-  [開始使用建議程式](advisor-get-started.md)
-  [建議程式安全性建議](advisor-security-recommendations.md)
-  [建議程式效能建議](advisor-performance-recommendations.md)
-  [建議程式成本建議](advisor-performance-recommendations.md)

