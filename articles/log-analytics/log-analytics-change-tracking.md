<properties
    pageTitle="Log Analytics 中的變更追蹤方案 | Microsoft Azure"
    description="您可以在 Log Analytics 中使用組態變更追蹤方案，幫助您輕鬆地識別軟體，以及您環境中發生的 Windows 服務變更 — 識別這些組態變更可以協助您找出操作問題。"
    services="operations-management-suite"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operations-management-suite"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="change-tracking-solution-in-log-analytics"></a>Log Analytics 中的變更追蹤方案


您可以在 Log Analytics 中使用組態變更追蹤方案，幫助您輕鬆地識別軟體，以及您環境中發生的 Windows 服務變更和 Linux 精靈變更 — 識別這些組態變更可以協助您找出操作問題。

您需要安裝此方案以更新您已安裝的代理程式類型。 系統會讀取受監視伺服器上安裝的軟體和 Windows 服務，然後將資料傳送至雲端中的 Log Analytics 服務進行處理。 會將邏輯套用至接收的資料，且雲端服務會記錄資料。 發現變更時，會在變更追蹤儀表板中顯示伺服器及其變更。 使用 [變更追蹤] 儀表板上的資訊，您可以輕鬆地看到您的伺服器基礎結構中所做的變更。

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案
請使用下列資訊來安裝和設定方案。

- 變更追蹤方案需要 Operations Manager。
- 您要監視變更的每部電腦上都必須有 Windows 或 Operations Manager 代理程式。
- 使用 [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)中的程序，將變更追蹤方案加入您的 OMS 工作區。  不需要進一步的組態。


## <a name="change-tracking-data-collection-details"></a>「變更追蹤」資料收集詳細資訊

變更追蹤會使用您已啟用的代理程式，來收集軟體清查和 Windows 服務中繼資料。

下表顯示變更追蹤的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|
|Windows|![是](./media/log-analytics-change-tracking/oms-bullet-green.png)|![是](./media/log-analytics-change-tracking/oms-bullet-green.png)|![否](./media/log-analytics-change-tracking/oms-bullet-red.png)|            ![否](./media/log-analytics-change-tracking/oms-bullet-red.png)|![是](./media/log-analytics-change-tracking/oms-bullet-green.png)| 每小時|

## <a name="use-change-tracking"></a>使用變更追蹤

安裝此方案之後，您可以在 OMS 的 [概觀] 頁面中使用 [變更追蹤] 圖格，檢視受監視伺服器的變更摘要。

![變更追蹤磚的影像](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

您可以檢視基礎結構的變更，然後向下鑽研下列類別的詳細資訊：

- 軟體和 Windows 服務的組態類型所做的變更
- 應用程式的軟體變更和個別伺服器的更新
- 每個應用程式的軟體變更總數
- 個別伺服器的 Windows 服務變更

![變更追蹤儀表板的影像](./media/log-analytics-change-tracking/oms-changetracking01.png)

![變更追蹤儀表板的影像](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>檢視任何變更類型的變更

1. 在 [概觀] 頁面上，按一下 [變更追蹤] 圖格。
2. 在 [變更追蹤] 儀表板上，檢閱其中一個變更類型刀鋒視窗中的摘要資訊，然後按一下其中一個，在 [記錄搜尋] 頁面中檢視詳細資訊。
3. 您可以在任何 [記錄搜尋] 頁面上，按時間、詳細結果和您的記錄搜尋記錄來檢視結果。 您也可以按 Facet 篩選以縮減結果。

## <a name="next-steps"></a>後續步驟

- 使用 [Log Analytics 中的記錄檔搜尋](log-analytics-log-searches.md) ，檢視詳細的變更追蹤資料。



<!--HONumber=Oct16_HO2-->


