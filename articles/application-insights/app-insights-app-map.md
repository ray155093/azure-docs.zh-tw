<properties 
	pageTitle="Application Insights 的應用程式對應 | Microsoft Azure" 
	description="應用程式元件之間的相依性視覺化呈現方式，其中會標示 KPI 和警示。" 
	services="application-insights" 
    documentationCenter=""
	authors="SoubhagyaDash" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2016" 
	ms.author="awills"/>
 
# Application Insights 的應用程式對應

在 [Visual Studio Application Insights](app-insights-overview.md) 中，應用程式對應是應用程式元件相依性關係的視覺化版面配置。每個元件都會顯示負載、效能、失敗和警示等 KPI，以協助您找出任何造成效能問題或失敗的元件。您可以從 Application Insights 和 Azure 診斷 (例如 SQL Database 建議程式的建議，前提是您的應用程式使用 Azure 服務) 逐一點選任何元件以及更詳細的診斷。

和其他圖表一樣，您可以將應用程式對應釘選至 Azure 儀表板，而其功能仍可完整運作。

## 開啟應用程式對應

從應用程式的概觀刀鋒視窗中開啟對應︰

![開啟應用程式對應](./media/app-insights-app-map/01.png)

![應用程式對應](./media/app-insights-app-map/02.png)

此對應會顯示︰

* 可用性測試
* 用戶端端元件 (使用 JavaScript SDK 來監視)
* 伺服器端元件
* 用戶端和伺服器元件的相依性

您可以展開與摺疊相依性連結群組︰

![摺疊](./media/app-insights-app-map/03.png)
 
如果您有某種類型 (SQL、HTTP 等) 的大量相依性，它們可能會以群組方式出現。


![群組的相依性](./media/app-insights-app-map/03-2.png)
 
 
## 找出問題

每個節點都有相關的效能指標，例如該元件的負載、效能和失敗率。

警告圖示會點出可能的問題。橘色的警告表示要求、頁面檢視或相依性呼叫發生失敗。紅色表示失敗率大於 5 %。


![失敗圖示](./media/app-insights-app-map/04.png)

 
也會出現作用中警示︰


![作用中警示](./media/app-insights-app-map/05.png)
 
如果您使用 SQL Azure，當系統有如何改善效能的建議時，便會出現圖示。


![Azure 建議](./media/app-insights-app-map/06.png)

按一下任何圖示即可取得詳細資料：


![Azure 建議](./media/app-insights-app-map/07.png)
 
 
## 診斷點選連結

對應上的每個節點都會提供以診斷做為目標的點選連結。這些選項會隨節點類型而有所不同。

![伺服器選項](./media/app-insights-app-map/09.png)

 
對於裝載在 Azure 中的元件，這些選項包括元件的直接連結。


## 篩選和時間範圍

根據預設，對應中會摘要列出選定時間範圍內的所有可用資料。但是，您可以透過篩選，使其只包含特定作業名稱或相依性。

* 作業名稱︰這包括頁面檢視和伺服器端要求類型。使用此選項時，對應中只會顯示伺服器端/用戶端節點上所選作業的 KPI。它會顯示在這些特定作業內容中呼叫的相依性。
* 相依性基底名稱︰這包括 AJAX 瀏覽器端相依性和伺服器端相依性。如果您使用 TrackDependency API 報告自訂相依性遙測，則也會在此顯示這些相依性。您可以選取要顯示在對應中的相依性。請注意，在此階段中，這並不會篩選伺服器端要求或用戶端頁面檢視。


![設定篩選](./media/app-insights-app-map/11.png)

 
 
## 儲存篩選

若要儲存您已套用的篩選，請將篩選後的檢視釘選到[儀表板](app-insights-dashboards.md)。


![釘選到儀表板](./media/app-insights-app-map/12.png)
 


## 意見反應

請[透過入口網站的意見反應選項提供意見反應](app-insights-get-dev-support.md)。


![MapLink-1 影像](./media/app-insights-app-map/13.png)

<!---HONumber=AcomDC_0622_2016-->