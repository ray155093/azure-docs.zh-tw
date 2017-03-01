---
title: "Azure 建議程式安全性建議 | Microsoft Docs"
description: "使用 Azure 建議程式協助改善 Azure 部署的安全性。"
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
ms.openlocfilehash: dbdbae0f87d8d115407026b263c65c3b387fa178
ms.lasthandoff: 02/21/2017

---
# <a name="advisor-security-recommendations"></a>建議程式安全性建議

建議程式可針對所有的 Azure 資源提供一致的合併建議檢視。 它與 Azure 資訊安全中心整合，可為您提供安全性建議。 您可以從 [建議程式] 儀表板中的 [安全性] 索引標籤取得安全性建議。

![建議程式安全性索引標籤](./media/advisor-security-recommendations/advisor-security-tab.png)

資訊安全中心利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。 它會定期分析 Azure 資源的安全性狀態。 當資訊安全中心識別潛在的安全性弱點時，它會建立建議。 這些建議會引導您完成設定所需控制項的程序。 

![建議程式安全性索引標籤](./media/advisor-security-recommendations/advisor-security-recommendations.png)

如需安全性建議的詳細資訊，請參閱[在 Azure 資訊安全中心管理安全性建議](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>如何在 Azure 建議程式中存取安全性建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左導覽窗格中的 [更多服務]，在服務功能表窗格中，向下捲動至 [監視和管理]，然後按一下 [Azure 建議程式]。 這會啟動 [建議程式] 儀表板。 
3. 在 [建議程式] 儀表板中，按一下 [安全性] 索引標籤，然後選取您想要接收建議的訂用帳戶。

> [!NOTE]
> 若要存取 Advisor 的建議，您必須先向 Advisor「註冊」您的訂用帳戶。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，訂用帳戶、資源群組或特定的資源的 [擁有者]、[參與者] 或 [讀取者]，都可以存取 Advisor 建議。

## <a name="next-steps"></a>後續步驟

若要深入了解建議程式建議，請參閱下列資源：
-  [建議程式高可用性建議](advisor-high-availability-recommendations.md)
-  [建議程式效能建議](advisor-performance-recommendations.md)
-  [建議程式成本建議](advisor-performance-recommendations.md)
 

