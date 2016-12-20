---
title: "Log Analytics 中的 Azure 網路分析解決方案 | Microsoft Docs"
description: "您可以使用 Log Analytics 中的 Azure 網路分析解決方案，以檢閱 Azure 網路安全性群組記錄檔和 Azure 應用程式閘道記錄檔。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eed3bd763edb94d7bea28b4039c03afa7359fee1


---
# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Log Analytics 中的 Azure 網路分析 (預覽) 解決方案
> [!NOTE]
> 這是[預覽解決方案](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features)。
> 
> 

您可以使用 Log Analytics 中的 Azure 網路分析解決方案，以檢閱 Azure 應用程式閘道記錄檔和 Azure 網路安全性群組記錄檔。

您可以對 Azure 應用程式閘道記錄檔和 Azure 網路安全性群組啟用記錄。 這些記錄會寫入至 Blob 儲存體，經過 Log Analytics 編製索引後，即可供搜尋和分析。

應用程式閘道支援下列記錄檔︰

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog

網路安全性群組支援下列記錄檔︰

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>安裝和設定解決方案
使用下列指示來安裝和設定 Azure 網路分析解決方案︰

1. 對您想要監視的資源啟用診斷記錄︰
   * [應用程式閘道](../application-gateway/application-gateway-diagnostics.md)
   * [網路安全性群組](../virtual-network/virtual-network-nsg-manage-log.md)
2. 使用 [Blob 儲存體中的 JSON 檔案](log-analytics-azure-storage-json.md)中所述的程序，設定 Log Analytics 從 Blob 儲存體讀取記錄。
3. 使用[從方案庫新增 Log Analytics 方案](log-analytics-add-solutions.md)中所述的程序，啟用 Azure 網路分析解決方案。  

如果您沒有為特定資源類型啟用診斷記錄，該資源的儀表板刀鋒視窗會空白。

## <a name="review-azure-networking-analytics-data-collection-details"></a>檢閱 Azure 網路分析資料收集的詳細資料
Azure 網路分析解決方案會從 Azure Blob 儲存體收集 Azure 應用程式閘道和網路安全性群組的診斷記錄檔。
資料收集不需要任何代理程式。

下表顯示 Azure 網路分析的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | Systems Center Operations Manager (SCOM) 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |![是](./media/log-analytics-azure-networking/oms-bullet-green.png) |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |10 分鐘 |

## <a name="use-azure-networking-analytics"></a>使用 Azure 網路分析
安裝此解決方案之後，您可以在 Log Analytics 的 [概觀] 頁面使用 [Azure 網路分析] 圖格，以針對您監視的應用程式閘道，檢視用戶端和伺服器錯誤的摘要。

![Azure 網路分析圖格的影像](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

按一下 [概觀] 圖格之後，您可以檢視記錄檔的摘要，然後深入下列類別的詳細資訊︰

* 應用程式閘道存取記錄檔
  * 應用程式閘道存取記錄檔的用戶端和伺服器錯誤
  * 每個應用程式閘道的每小時要求數
  * 每個應用程式閘道的每小時失敗要求數
  * 應用程式閘道依使用者代理程式分類的錯誤
* 應用程式閘道效能
  * 應用程式閘道的主機健康狀態
  * 應用程式閘道失敗要求的最大和第 95 個百分位數
* 網路安全性群組封鎖流量
  * 網路安全性群組規則與封鎖流量
  * MAC 位址與封鎖流量
* 網路安全性群組允許流量
  * 網路安全性群組規則與允許流量
  * MAC 位址與允許流量

![Azure 網路分析儀表板的影像](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![Azure 網路分析儀表板的影像](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![Azure 網路分析儀表板的影像](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![Azure 網路分析儀表板的影像](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>檢視任何記錄檔摘要的詳細資料
1. 在 [概觀] 頁面上，按一下 [Azure 網路分析] 圖格。
2. 在 [Azure 網路分析] 儀表板上，檢閱其中一個刀鋒視窗中的摘要資訊，然後按一下其中一個，在記錄搜尋頁面中檢視詳細資訊。
   
    您可以在任何 [記錄搜尋] 頁面上，按時間、詳細結果和您的記錄搜尋記錄來檢視結果。 您也可以按 Facet 篩選以縮減結果。

## <a name="next-steps"></a>後續步驟
* 使用 [Log Analytics 中的記錄檔搜尋](log-analytics-log-searches.md)來檢視詳細的 Azure 網路分析資料。




<!--HONumber=Nov16_HO3-->


