---
title: "檢視事件和活動 (稽核) 記錄檔"
description: "了解如何查看發生在 Azure 訂用帳戶中的所有事件。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 60c38217-c3b5-4ef1-98ac-d045fdd5549b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2015
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c767480f072feaa0fbb0b5bdaec992a6f2381040


---
# <a name="view-events-and-activity-logs"></a>檢視事件和活動記錄檔
Azure 資源管理員會完全稽核在 Azure 資源上執行的所有作業，從建立和刪除到授與或撤銷存取權。 您可以在 Azure 入口網站中瀏覽這些記錄檔，而且您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以程式設計方式存取完整的事件集合。

## <a name="browse-the-events-impacting-your-azure-subscription"></a>瀏覽影響您 Azure 訂用帳戶的事件
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下 [瀏覽]，然後選取 [稽核記錄檔]。  
    ![瀏覽中樞](./media/insights-debugging-with-events/Insights_Browse.png)
3. 這將會開啟一個刀鋒視窗，顯示過去 7 天影響任何訂用帳戶的所有事件。 上方是一張按層級顯示資料的圖表，圖表下方是記錄檔的完整清單： ![所有事件](./media/insights-debugging-with-events/Insights_AllEvents.png)

> [!NOTE]
> 您只能在 Azure 入口網站中檢視指定訂用帳戶的 500 筆最新事件。
> 
> 

1. 您可以按一下任何記錄項目，以查看其構成事件。 例如，當您在資源群組中部署項目時，可能會建立或修改許多不同的資源。 在每個項目中，您可以看到：
   
   * 事件的**層級** - 例如，它可以只是可追蹤的資訊 (**資訊**)，或是您必須知道的發生錯誤 (**錯誤**)。
   * **狀態** - 最終狀態通常是**成功**或**失敗**，但若是長時間執行的作業，它也可以是**已接受**。
   * *時機* 。
   * *人員* (如果有人執行作業)。 並非所有作業都是由使用者執行，有些是由後端服務執行，因此他們不會有 **呼叫端**。
   * 事件的 **相互關聯識別碼** - 這是這組作業的唯一識別碼。
2. 您可以從這裡移至 [詳細資料] 刀鋒視窗，以查看事件的細節。
   
    ![資源群組](./media/insights-debugging-with-events/Insights_EventDetails.png)
   
    對於**失敗**事件，此頁面通常會顯示 [子狀態] 和 [屬性] 區段，其中包含可用於偵錯的詳細資料。

## <a name="filter-to-specific-logs"></a>篩選特定記錄檔
若要查看套用至特定實體或特定類型的事件，您可以按一下 **Filter** 命令來篩選 [稽核記錄檔] 刀鋒視窗。 您也可以使用 [篩選器] 刀鋒視窗來變更 [稽核記錄] 刀鋒視窗的 [ **時間範圍** ]。

在您按一下此命令後，便會隨即開啟新的刀鋒視窗：

![Filter](./media/insights-debugging-with-events/Insights_EventFilter.png)

篩選器的類型共有四種：

1. 依訂用帳戶
2. 依 **資源群組**
3. 依 **資源類型**
4. 依特定 **資源** - 此時，您必須貼上您感興趣資源的完整 *資源識別碼* 。

此外，您也可以依據執行事件的人員，依據事件的層級來篩選事件。

完成選擇想要看到的內容之後，請按一下刀鋒視窗底部的 [ **更新** ] 按鈕。

## <a name="monitor-events-impacting-specific-resources"></a>監視影響特定資源的事件
1. 按一下 [ **瀏覽** ] 以找到您感興趣的資源。 您也可以查看整個 **資源群組**的所有記錄檔。
2. 在資源的刀鋒視窗上，向下捲動直到您找到 [ **事件** ] 磚為止。  
    ![事件圖格](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. 按一下該磚，以查看經篩選後僅顯示所選資源的事件。 您可以使用 **Filter** 命令來變更時間範圍，或套用更具體的篩選器。

## <a name="next-steps"></a>後續步驟
* [接收警示通知](insights-receive-alert-notifications.md) 。
* [監視服務計量](insights-how-to-customize-monitoring.md) 以確保您的服務可用且可回應。
* [追蹤服務健康狀況](insights-service-health.md) 可以找出 Azure 何時遭遇效能降低或服務中斷。  




<!--HONumber=Nov16_HO3-->


