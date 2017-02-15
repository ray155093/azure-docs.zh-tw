---
title: "使用 Azure 監視器活動記錄檔追蹤服務健全狀況 | Microsoft Docs"
description: "在 Azure 發生效能降低或服務中斷時獲知消息。 "
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 30db9258-98e6-4855-abcd-f06c06277aa8
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d31ee12c783b845e0a8ff1791d21b2ef9013eee3


---
# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>使用 Azure 監視器活動記錄檔追蹤服務健全狀況
每次發生服務中斷或效能降低，Azure 就會發出公告。 您可以在 Azure 入口網站中瀏覽這些事件，您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以程式控制的方式存取完整的事件集。

## <a name="browse-the-service-health-logs-for-your-subscription"></a>瀏覽訂用帳戶的服務健全狀況記錄檔
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [首頁] 中，您會看到 [服務健全狀況] 圖格。 按一下它。
   
    ![Home](./media/insights-service-health/Insights_Home.png)
3. 會顯示 Azure 中所有區域的清單。 按一下任何區域，會帶出 [活動記錄檔] 查詢，其中顯示過去 24 小時內影響訂用帳戶的所有服務事件。
   
    ![活動記錄檔訂用帳戶服務健全狀況](./media/insights-service-health/AzureActivityLogServiceHealth3.png)
4. 您可以按一下表格中的個別事件，查看該事件的詳細資料。
5. 變更 [時間範圍] 以查看更長的時間範圍內。

## <a name="next-steps"></a>後續步驟
* [監視任何網頁的可用性和回應性](../application-insights/app-insights-monitor-web-app-availability.md) ，讓您可以找出您的頁面是否關閉。




<!--HONumber=Nov16_HO3-->


