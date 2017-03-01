---
title: "Azure 建議程式成本建議 | Microsoft Docs"
description: "使用 Azure 建議程式將 Azure 部署的成本最佳化。"
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
ms.openlocfilehash: 7999398f69ed0ce5129dd38f8e9e1994db25f9a5
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-cost-recommendations"></a>建議程式成本建議

建議程式可找出閒置和未充分利用的資源，協助您最佳化並減少 Azure 整體費用。 您可以從 [建議程式] 儀表板中的 [成本] 索引標籤取得成本建議。

![建議程式成本索引標籤](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="low-utilization-virtual-machines"></a>降低使用率虛擬機器 

雖然特定應用程式案例的設計可能導致低使用率，但您通常可藉由管理虛擬機器的大小和數目來節省金錢。 建議程式可監視 14 天的虛擬機器使用量，並找出低使用率的虛擬機器。 CPU 使用率等於或低於 5% 且網路使用量等於或低於 7 MB 長達 4 天 (含) 以上的虛擬機器，會被視為低使用率虛擬機器。

建議程式會顯示繼續執行虛擬機器的預估成本。 您可以選擇關閉虛擬機器或調整其大小。  

![調整虛擬機器大小的建議程式成本建議](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="sql-elastic-database-pool-recommendations"></a>SQL 彈性資料庫集區建議

建議程式會找出可受益於建立彈性資料庫集區的 SQL Server 執行個體。 彈性資料庫集區提供符合成本效益的簡單解決方案，以管理多個具有不同使用模式之資料庫的效能目標。 如需 Azure 彈性集區的詳細資訊，請參閱[什麼是 Azure 彈性集區？](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)

![彈性資料庫集區的建議程式成本建議](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何在 Azure 建議程式中存取成本建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左導覽窗格中的 [更多服務]，在服務功能表窗格中，向下捲動至 [監視和管理]，然後按一下 [Azure 建議程式]。 這會啟動 [建議程式] 儀表板。 
3. 在 [建議程式] 儀表板上，按一下 [成本] 索引標籤，選取您想要接收建議的訂用帳戶，然後按一下 [取得建議]

> [!NOTE]
> 若要存取 Advisor 的建議，您必須先向 Advisor「註冊」您的訂用帳戶。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，訂用帳戶、資源群組或特定的資源的 [擁有者]、[參與者] 或 [讀取者]，都可以存取 Advisor 建議。

## <a name="next-steps"></a>後續步驟

若要深入了解建議程式建議，請參閱下列資源：
-  [建議程式簡介](advisor-overview.md)
-  [開始使用](advisor-get-started.md)
-  [建議程式高可用性建議](advisor-cost-recommendations.md)
-  [建議程式安全性建議](advisor-cost-recommendations.md)
-  [建議程式效能建議](advisor-cost-recommendations.md)

