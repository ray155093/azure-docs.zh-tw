---
title: "Azure 建議程式簡介 | Microsoft Docs"
description: "使用 Azure 建議程式將 Azure 部署最佳化。"
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
ms.openlocfilehash: 3c4f6d13dd43ae557b177883e14044e637bd803b
ms.lasthandoff: 02/21/2017

---

# <a name="introduction-to-azure-advisor"></a>Azure 建議程式簡介

了解 Azure 建議程式、其主要功能以及常見問題集。

## <a name="what-is-azure-advisor"></a>何謂 Azure 建議程式？
Azure 建議程式是個人化的雲端顧問，可協助您依最佳做法來最佳化您的 Azure 部署。 它可分析您的資源組態和使用量遙測，並建議可協助您改善 Azure 資源的成本效益、效能、高可用性和安全性的解決方案。

使用 Azure 建議程式，您可以：
-    取得主動式、可採取動作且個人化的最佳作法建議 
-    改善資源的效能、安全性及高可用性，同時尋找降低整體 Azure 費用的機會
-    透過內嵌動作取得建議

您可以透過 [Azure 入口網站](https://aka.ms/azureadvisordashboard)存取建議程式。 登入[入口網站](https://portal.azure.com)，選取 [瀏覽]，然後捲動至 [Azure 建議程式]。 [建議程式] 儀表板會顯示所選訂用帳戶的個人化建議。 建議分為四個類別。 

-   **高可用性** - 確保和改善業務關鍵應用程式的持續性。 如需詳細資訊，請參閱[建議程式高可用性的建議](advisor-high-availability-recommendations.md)。

-   **安全性** – 偵測可能導致潛在安全性漏洞的威脅和弱點。 如需詳細資訊，請參閱[建議程式安全性建議](advisor-security-recommendations.md)。

-   **效能** – 加強應用程式的速度。 如需詳細資訊，請參閱[建議程式效能建議](advisor-performance-recommendations.md)。

-   **成本** – 最佳化並減少整體 Azure 費用。 如需詳細資訊，請參閱[建議程式成本建議](advisor-cost-recommendations.md)。

  ![建議程式建議類型](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> 若要存取 Advisor 的建議，您必須先向 Advisor「註冊」您的訂用帳戶。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，訂用帳戶、資源群組或特定的資源的 [擁有者]、[參與者] 或 [讀取者]，都可以存取 Advisor 建議。

您可以按一下建議，以了解其額外資訊。 您也可以了解您可以執行的動作，以便利用機會或解決問題。 建議程式可透過內嵌動作或文件連結提供建議。 按一下內嵌動作，可帶領您完成「引導式使用者旅程圖」以進行實作。 按一下文件連結，可將您指向說明如何以手動方式實作動作的文件。 

建議程式會每小時更新建議。 如果您不想立即對建議採取行動，您可以將它延遲一段時間或加以解除。 

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-do-i-access-advisor"></a>如何存取建議程式？
您可以透過 Azure 入口網站存取建議程式。 登入入口網站，選取 [瀏覽]，然後捲動至 [Azure 建議程式]。 您也可以透過虛擬機器資源刀鋒視窗檢視建議程式建議。 選擇虛擬機器，然後捲動至功能表中的建議程式建議。 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>我需要哪些權限才能存取建議程式？

若要存取 Advisor 的建議，您必須先向 Advisor「註冊」您的訂用帳戶。 當訂用帳戶擁有者啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，訂用帳戶、資源群組或特定的資源的 [擁有者]、[參與者] 或 [讀取者]，都可以存取 Advisor 建議。

### <a name="how-often-are-advisor-recommendations-updated"></a>建議程式建議的頻率為何？

建議程式建議會每小時進行更新。

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>建議程式可提供哪些資源的建議？

Advisor 可提供虛擬機器、可用性設定組、應用程式閘道、應用程式服務、SQL Server、SQL Database 和 Redis 快取的建議。

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>是否可以延遲或解除建議？

若要延遲或解除建議，請按一下 [延遲] 按鈕或連結。 您可以指定延遲時間週期，或選取 [永不] 來解除建議。

## <a name="next-steps"></a>後續步驟

若要深入了解建議程式建議，請參閱下列資源：

-  [開始使用建議程式](advisor-get-started.md)
-  [建議程式高可用性建議](advisor-high-availability-recommendations.md)
-  [建議程式安全性建議](advisor-security-recommendations.md)
-  [建議程式效能建議](advisor-performance-recommendations.md)
-  [建議程式成本建議](advisor-cost-recommendations.md)

