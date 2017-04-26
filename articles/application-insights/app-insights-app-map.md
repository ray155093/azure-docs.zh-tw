---
title: "Azure Application Insights 的應用程式對應 | Microsoft Docs"
description: "應用程式元件之間的相依性視覺化呈現方式，其中會標示 KPI 和警示。"
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 37b85ba4987f8f29e4e825a17f0a892ddabf9599
ms.lasthandoff: 04/12/2017


---
# <a name="application-map-in-application-insights"></a>Application Insights 的應用程式對應
在 [Azure Application Insights](app-insights-overview.md) 中，應用程式對應是應用程式元件相依性關係的視覺化版面配置。 每個元件都會顯示負載、效能、失敗和警示等 KPI，以協助您找出任何造成效能問題或失敗的元件。 您可以從任何元件逐一點選至更詳細的診斷，例如 Application Insights 事件。 如果您的應用程式使用 Azure 服務，您也可以逐一點選 Azure 診斷，例如 SQL 資料庫建議程式的建議。

和其他圖表一樣，您可以將應用程式對應釘選至 Azure 儀表板，而其功能仍可完整運作。 

## <a name="open-the-application-map"></a>開啟應用程式對應
從應用程式的概觀刀鋒視窗中開啟對應︰

![開啟應用程式對應](./media/app-insights-app-map/01.png)

![應用程式對應](./media/app-insights-app-map/02.png)

此對應會顯示︰

* 可用性集合
* 用戶端元件 (使用 JavaScript SDK 監視)
* 伺服器端元件
* 用戶端和伺服器元件的相依性

您可以展開與摺疊相依性連結群組︰

![摺疊](./media/app-insights-app-map/03.png)

如果您有某種類型 (SQL、HTTP 等) 的許多相依性，它們可能會以群組方式出現。 

![群組的相依性](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>找出問題
每個節點都有相關的效能指標，例如該元件的負載、效能和失敗率。 

警告圖示會點出可能的問題。 橘色的警告表示要求、頁面檢視或相依性呼叫發生失敗。 紅色表示失敗率大於 5 %。 如果您想要調整這些閾值，請開啟 [選項]。

![失敗圖示](./media/app-insights-app-map/04.png)

也會出現作用中警示︰ 

![作用中警示](./media/app-insights-app-map/05.png)

如果您使用 SQL Azure，當系統有如何改善效能的建議時，便會出現圖示。 

![Azure 建議](./media/app-insights-app-map/06.png)

按一下任何圖示即可取得詳細資料：

![Azure 建議](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>診斷點選連結
對應上的每個節點都會提供以診斷做為目標的點選連結。 這些選項會隨節點類型而有所不同。

![伺服器選項](./media/app-insights-app-map/09.png)

對於裝載在 Azure 中的元件，這些選項包括元件的直接連結。

## <a name="filters-and-time-range"></a>篩選和時間範圍
根據預設，對應中會摘要列出選定時間範圍內的所有可用資料。 但是，您可以透過篩選，使其只包含特定作業名稱或相依性。

* 作業名稱︰這包括頁面檢視和伺服器端要求類型。 使用此選項時，對應中只會顯示伺服器端/用戶端節點上所選作業的 KPI。 它會顯示在這些特定作業內容中呼叫的相依性。
* 相依性基底名稱︰這包括 AJAX 瀏覽器相依性和伺服器端相依性。 如果您使用 TrackDependency API 來報告自訂相依性遙測，這些資料也會在此出現。 您可以選取要顯示在對應中的相依性。 此選取項目目前不會篩選伺服器端要求或用戶端頁面檢視。

![設定篩選](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>儲存篩選
若要儲存您已套用的篩選，請將篩選後的檢視釘選到 [儀表板](app-insights-dashboards.md)。

![釘選到儀表板](./media/app-insights-app-map/12.png)

## <a name="end-to-end-system-app-maps"></a>端對端系統應用程式對應

如果您的應用程式有多個元件 (例如 Web 應用程式以外的後端服務)，則可以在一個整合式應用程式對應上顯示所有元件。

![設定篩選](./media/app-insights-app-map/multi-component-app-map.png)

應用程式對應會尋找目前資源群組內的所有 Application Insights 資源，藉此尋找伺服器節點。 它也會追蹤目前資源群組中的 Application Insights 資源所追蹤的任何相依性，藉此偵測伺服器節點。


### <a name="setting-up"></a>設定

> [!NOTE] 
> 端對端系統應用程式對應處於預覽狀態。 您必須使用特殊版本的 SDK 來檢測您的元件，而且您必須使用特殊的 URL 來查看應用程式對應。 [了解如何設定端對端系統應用程式對應](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-app-map-preview.md)。

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>意見反應
請透過入口網站的意見反應選項提供意見反應。

![MapLink-1 影像](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>後續步驟

* [Azure 入口網站](https://portal.azure.com)
