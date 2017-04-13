---
title: "Log Analytics 常見問題集 | Microsoft Docs"
description: "Azure Log Analytics 服務的相關常見問題的解答。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 9ee8f4aafcc35e43c4fcba5a3a72b043dd9fc32c
ms.openlocfilehash: 4695669dc20b4b4b90ccdaf4db06df2cfcba2167
ms.lasthandoff: 02/21/2017


---
# <a name="log-analytics-faq"></a>Log Analytics 常見問題集
此 Microsoft 常見問題集是 Microsoft Operations Management Suite (OMS) 中 Log Analytics 常見問題的清單。 若您有任何關於 Log Analytics 的其他問題，請前往 [論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) 並張貼您的問題。 我們社群的服務人員將協助您找到答案。 若遇到常見問題，我們會將其加入此文章，以便您可以快速且輕鬆地找到。

## <a name="general"></a>一般
**問：AD 和 SQL 評估解決方案會執行哪些檢查？**

A. 下列查詢會顯示目前執行的所有檢查的描述：

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

然後可將結果匯出至 Excel 供進一步檢閱。

**問：為什麼我在 SCOM 管理中看到與 *OMS* 不同的項目？**

問：根據您所採用的 SCOM 更新彙總套件而定，您可能會看到 *System Center Advisor*、*Operational Insights* 或 *Log Analytics* 節點。

對 *OMS* 文字字串的更新包含在管理組件西，必須手動匯入它。 遵循最新的 SCOM 更新彙總套件知識庫文章中的指示，並重新整理 OMS 主控台以在 *OMS* 節點中看見最新的更新。

**問：OMS 是否有*內部部署*版本？**

答：沒有。 Log Analytics 會處理並儲存非常大量的資料。 作為一項雲端服務，Log Analytics 在必要時可以向上延展，並避免對您的環境效能的任何影響。

此外，作為一項雲端服務表示您不需要保持 Log Analytics 基礎結構啟動並執行，並且可以接收常見的功能更新和修正程式。

## <a name="configuration"></a>組態
**問：可以變更用來從 Azure 診斷 (WAD) 讀取的資料表/Blob 容器的名稱嗎？**  

答：    不行，這目前不可行，但對未來版本有規劃。

**問：OMS 服務使用哪些 IP 位址？如何確保我的防火牆只允許對 OMS 服務的流量？**  

A. Log Analytics 服務以 Azure 為建置基礎，而端點會接收 [Microsoft Azure 資料中心 IP 範圍](http://www.microsoft.com/download/details.aspx?id=41653)中的 IP。

進行服務部署時，OMS 服務的實際 IP 位址會變更。 [在 Log Analytics 中設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)中記載要允許通過防火牆的 DNS 名稱。

**問：我可以使用 ExpressRoute 連接到 Azure。我的 Log Analytics 流量是否會使用我的 ExpressRoute 連線？**  

A. [ExpressRoute 文件](../expressroute/expressroute-faqs.md#supported-services)中描述不同類型的 ExpressRoute 流量。

通往 Log Analytics 的流量都會使用公用互連 ExpressRoute 電路。

**問：有簡單且輕鬆的方法，可將現有的 Log Analytics 工作區移至另一個 Log Analytics 工作區/Azure 訂用帳戶嗎？**  我們在我們的 Azure 訂用帳戶中有測試和追蹤中的數個客戶的 OMS 工作區，而它們要移入到實際執行環境，所以我們想要將它們移至其自己的 Azure/OMS 訂用帳戶。  

A. `Move-AzureRmResource` 指令程式可讓您將 Log Analytics 工作區及自動化帳戶從一個 Azure 訂用帳戶移動到另一個 Azure 訂用帳戶。 如需詳細資訊，請參閱 [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx)。

這項變更也可在 Azure 入口網站進行。

您無法將資料在不同一個 Log Analytics 工作區間移動，或是變更 Log Analytics 資料儲存所在的區域。

**問：如何將 OMS 加入 SCOM？**

答：更新為最新的更新彙總套件和匯入管理組件可讓您將 SCOM 連接到 Log Analytics。

請注意，對 Log Analytics 的 SCOM 連接只適用 SCOM 2012 SP1 和更新版本。

**問：如何確認代理程式可與 Log Analytics 通訊？**

答：若要確保代理程式可以與 OMS 通訊，請移至：[控制台] > [安全性和設定] > [Microsoft Monitoring Agent]。

在 [Azure Log Analytics (OMS)]  索引標籤中，找出綠色的核取記號。 綠色核取記號圖示可確認代理程式能夠與 OMS 服務進行通訊。

黃色警告圖示表示代理程式有與 OMS 通訊的問題。 一個常見原因是 Microsoft Monitoring Agent 服務已停止，需要重新啟動。

**問：如何阻止代理程式與 Log Analytics 通訊？**

答：在 SCOM 中，從 OMS 受管理的清單中移除該電腦。 如此一來，將會阻止透過該代理程式的 SCOM 的所有通訊。 針對直接連線到 OMS 的代理程式，您可以阻止它們與 OMS 通訊，透過：[控制台] > [安全性和設定] > [Microsoft Monitoring Agent]。
在 **Azure Log Analytics (OMS)** 下，移除所有列出的工作區。

**問︰當我試著將工作區從某個 Azure 訂用帳戶移到另一個時，為什麼會發生錯誤？**

答︰當您新增方案時，Azure 會在工作區所在的 Azure 訂用帳戶中建立資源。

新增訂用帳戶的人員通常是「Azure 訂用帳戶」的系統管理員或參與者。 如果使用者在 Azure 入口網站中並未同時擁有與 Azure 訂用帳戶相同的權限，則 OMS 入口網站中的系統管理員或參與者權限是不夠的。


## <a name="agent-data"></a>代理程式資料
**問：我可以透過代理程式傳送多少資料到 Log Analytics？是否有每位客戶最大的資料量？**  
A. 免費方案每個工作區的每日容量設定為 500 MB。 標準和進階計畫對於所上傳的資料量沒有限制。 做為一項雲端服務，OMS 中的 Log Analytics 的設計可自動相應增加，以處理來自客戶的資料量 - 即使是每日數 TB。

Log Analytics 代理程式的設計是為了確保它的使用量很小，而且可執行一些基本的資料壓縮。 我們的其中一個客戶撰寫了一篇部落格，內容是有關他們為我們的代理程式執行的測試以及如何讓其印象深刻。 資料量會視您的客戶啟用的解決方案而不同。 您可以在 OMS 概觀頁面的 [使用量]  圖格下中找到有關資料量的詳細資訊，並依解決方案查看劃分。

如需詳細資訊，您可以閱讀 [客戶部落格](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) 以了解 OMS 代理程式的低使用量。

**問：傳送資料到 Log Analytics 時，Microsoft 管理代理程式 (MMA) 使用多少網路頻寬？**

A. 頻寬是關於傳送的資料量的功能。 透過網路傳送資料時，會壓縮資料。

**問：每個代理程式會傳送多少資料？**

A. 這主要是取決於：

* 您已啟用的解決方案
* 記錄檔和要收集的效能計數器的數目
* 記錄檔中的資料量

免費定價層是將數個伺服器上架和量測典型資料量的好方法。 [使用量]  頁面會顯示整體使用方式。
對於可執行 WireData 代理程式的電腦，您可以使用下列查詢查看正在傳送的資料量：

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>後續步驟
* [開始使用 Log Analytics](log-analytics-get-started.md) 以深入了解 Log Analytics，並幾分鐘內就啟動並執行。

