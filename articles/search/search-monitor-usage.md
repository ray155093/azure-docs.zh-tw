---
title: "監視 Azure 搜尋服務中的使用量和統計資料 | Microsoft Docs"
description: "追蹤 Azure 搜尋服務 (Microsoft Azure 上之託管的雲端搜尋服務) 的資源耗用量和索引大小。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e40acb01d6297aa66090859533fd48c108a9a88


---
# <a name="monitor-usage-and-query-metrics-in-an-azure-search-service"></a>在 Azure 搜尋服務中監視使用量和查詢度量
Azure 搜尋服務會收集服務層級的查詢執行統計資料，包含每秒查詢次數 (QPS)、延遲，以及磁碟區超出容量時卸除的查詢百分比。 透過 [監視] 刀鋒視窗，可在入口網站中中顯示度量。

   ![QPS 活動的螢幕擷取畫面][5]

本文說明所有服務的總度量。 若要深入了解索引層級的活動，請啟用搜尋流量分析並使用 Power BI 來檢視分析。 若要開始使用，請造訪 [Azure 搜尋服務的搜尋流量分析](search-traffic-analytics.md)

## <a name="view-query-throughput-statistics"></a>檢視查詢輸送量統計資料
按一下服務儀表板上的 [監視] 圖格，以開啟 [監視] 刀鋒視窗。

   ![監視圖格][2]

會記錄服務層級的查詢活動，以執行查詢、延遲和節流。 會持續收集度量，但是可能需要幾分鐘的時間，讓入口網站顯示最新的活動。 

按一下度量圖格以檢視詳細資料、新增警示、啟用診斷或編輯圖表。

  ![[度量] 刀鋒視窗上的命令][4]

### <a name="set-up-alerts"></a>設定警示
從 [度量詳細資料] 頁面中，您可以設定警示，以在查詢執行、延遲或節流活動超過為其定義的準則時觸發電子郵件通知。

### <a name="enable-diagnostics"></a>啟用診斷
當您開啟診斷時，可以設定診斷資料的儲存位置、是否要包含作業記錄檔和度量，以及要保留資料多長的時間。

### <a name="change-chart-type-and-data-collection-interval"></a>變更圖表類型和資料收集間隔
對於每個度量，您可以按一下 [編輯] 將視覺效果從折線圖變更為橫條圖，或修改 X 軸以涵蓋不同的時間範圍。

  ![時間範圍組態][3]

## <a name="view-counts-and-resource-usage-in-the-portal"></a>在入口網站中檢視計數和資源使用量
追蹤索引和文件大小的成長，可在達到您為服務所設定的上限之前，幫助您先主動調整容量。 

若要監視資源使用量，請在[入口網站](https://portal.azure.com)中檢視服務的計數和統計資料。 如果您正在建置自訂服務管理工具，也可以透過程式設計方式取得資訊。

1. 登入[入口網站](https://portal.azure.com)。 
2. 開啟 Azure 搜尋服務的服務儀表板。 可在首頁上找到並排的服務，或是您也可以使用 JumpBar 上的 [瀏覽] 來瀏覽服務。 

[使用量] 部分包含計量，可告知您目前正在使用可用資源的哪些部分。 如需每個服務的索引、文件和儲存體限制資訊，請參閱[服務限制](search-limits-quotas-capacity.md)。

  ![使用量圖格][1]

> [!NOTE]
> 上面的螢幕擷取畫面適用於「免費」服務 (其最多各有一個複本和資料分割)，而且只裝載 3 個索引、10,000 份文件或 50 MB 的資料 (看何者先達到)。 在「基本」或「標準」層所建立的服務會有較大的服務限制。 如需選擇層的詳細資訊，請參閱[選擇層或 SKU](search-sku-tier.md)。
> 
> 

### <a name="get-index-statistics-using-the-rest-api"></a>使用 REST API 取得索引統計資料
Azure 搜尋服務 REST API 和 .NET SDK 都提供以程式設計方式存取服務度量。  如果您正使用 [索引子](https://msdn.microsoft.com/library/azure/dn946891.aspx) 從 Azure SQL 資料庫或 DocumentDB 載入索引，則用來取得您所需數字的其他 API 也可供使用。 

* [取得索引統計資料](https://msdn.microsoft.com/library/azure/dn798942.aspx)
* [文件計數](https://msdn.microsoft.com/library/azure/dn798924.aspx)
* [取得索引子狀態](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>後續步驟
如需如何平衡現有服務之資料分割與複本配置的指引，請檢閱[調整複本和資料分割](search-limits-quotas-capacity.md)。 

如需服務管理的詳細資訊，請瀏覽[在 Microsoft Azure 上管理搜尋服務](search-manage.md)，或[效能和最佳化](search-performance-optimization.md)以取得微調指引。

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[2]: ./media/search-monitor-usage/AzSearch-Monitor-Tile.PNG
[3]: ./media/search-monitor-usage/AzSearch-Monitor-Intervals.PNG
[4]: ./media/search-monitor-usage/AzSearch-Monitor-AlertCmd.PNG
[5]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG








<!--HONumber=Nov16_HO3-->


