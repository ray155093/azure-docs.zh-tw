---
title: "開始使用 Azure 建議程式 | Microsoft Docs"
description: "開始使用 Azure 建議程式。"
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: 8ed18f6f62b6d2895e2470df1c3bdd448ea98210
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-with-azure-advisor"></a>開始使用 Azure 建議程式

了解如何使用 Azure 入口網站存取建議程式、取得建議、實作建議、搜尋建議，以及重新整理建議。

## <a name="get-advisor-recommendations"></a>取得建議程式的建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左導覽窗格中的 [更多服務]，接著在服務功能表窗格中，向下捲動至 [監視和管理]，然後按一下 [Azure 建議程式]。 這會啟動 [建議程式] 儀表板。

  ![使用 Azure 入口網站存取 Azure 建議程式](./media/advisor-overview/advisor-azure-portal-menu.png) 

3. 在 [建議程式] 儀表板上，選取您想要接收建議的訂用帳戶。 [建議程式] 儀表板會顯示所選訂用帳戶的個人化建議。 
4. 若要取得特定類別的建議，請按下列其中一個索引標籤 - [高可用性]、[安全性]、[效能] 或 [成本]。
 
> [!NOTE]
> 若要存取 Advisor 的建議，您必須先向 Advisor「註冊」您的訂用帳戶。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，訂用帳戶、資源群組或特定的資源的 [擁有者]、[參與者] 或 [讀取者]，都可以存取 Advisor 建議。

  ![Azure 建議程式儀表板](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-recommendation"></a>取得建議程式的建議詳細資料，並實建議

建議程式中的 [建議] 刀鋒視窗會提供其他有關建議程式建議的資訊。 

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後啟動 [Azure 建議程式](https://aka.ms/azureadvisordashboard)。
2. 在 [建議程式建議] 儀表板上，按一下 [取得建議]。
3. 從建議清單中，按一下您想要詳細檢閱的建議。 這會啟動 [建議] 刀鋒視窗。
4. 在 [建議] 刀鋒視窗上，檢閱您可執行以解決潛在問題或利用成本節約機會的動作相關資訊。 
  
  ![建議程式建議動作範例](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>搜尋建議程式的建議

您可以搜尋特定訂用帳戶或資源群組的建議。 您也可以依照狀態搜尋建議。

1. 登入 Azure 入口網站，然後啟動 Azure 建議程式。
2. 透過篩選訂用帳戶、資源群組和建議狀態 ([使用中] 或 [已延遲]) 來搜尋建議。
3. 按一下 [取得建議] 可取得以您的搜尋篩選條件為基礎的建議程式建議清單。

  ![](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-advisor-recommendations"></a>延遲建議程式的建議

1. 登入 Azure 入口網站，然後啟動 Azure 建議程式。
2. 按一下 [取得建議]，然後按一下建議清單中的建議。
3. 在 [建議] 刀鋒視窗上，按一下 [延遲]。  您可以指定延遲時間週期，或選取 [永不] 來解除建議。

  ![建議程式建議動作範例](./media/advisor-get-started/advisor-snooze.png)



## <a name="next-steps"></a>後續步驟

若要深入了解建議程式，請參閱下列資源：
-  [Azure 建議程式簡介](advisor-overview.md)
-  [建議程式高可用性建議](advisor-high-availability-recommendations.md)
-  [建議程式安全性建議](advisor-security-recommendations.md)
-  [建議程式效能建議](advisor-performance-recommendations.md)
-  [建議程式成本建議](advisor-performance-recommendations.md)

