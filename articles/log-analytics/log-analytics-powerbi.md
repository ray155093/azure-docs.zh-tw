<properties
   pageTitle="將 Log Analytics 資料匯出至 Power BI | Microsoft Azure"
   description="Power BI 是 Microsoft 的雲端架構商務分析服務，能提供豐富的視覺效果和不同資料集的分析報告。Log Analytics 可以持續將資料從 OMS 儲存機制匯出到 Power BI，讓您可以利用其視覺效果和分析工具。本文說明如何在 Log Analytics 中設定查詢，自動定期匯出至 Power BI。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren" />

# 將 Log Analytics 資料匯出至 Power BI

[Power BI](https://powerbi.microsoft.com/zh-TW/documentation/powerbi-service-get-started/) 是 Microsoft 的雲端架構商務分析服務，能提供豐富的視覺效果和不同資料集的分析報告。Log Analytics 可以自動將資料從 OMS 儲存機制匯出到 Power BI，讓您可以利用其視覺效果和分析工具。

當您使用 Log Analytics 設定 Power BI 時，會建立記錄查詢，以將結果匯出至 Power BI 中的對應資料集。查詢和匯出會繼續自動依您定義的排程執行，讓資料集與 Log Analytics 收集的最新資料保持一致。

![Log Analytics 到 Power BI](media/log-analytics-power-bi/overview.png)

## Power BI 排程

「Power BI 排程」包含將一組資料從 OMS 儲存機制匯出至 Power BI 中對應資料集的記錄搜尋，還有定義這項搜尋執行頻率的排程，以維持資料集為最新狀態。

資料集中的欄位會符合記錄搜尋所傳回記錄的屬性。如果搜尋傳回不同類型的記錄，則資料集將包含來自每個包含之記錄類型的所有屬性。

最佳做法是使用會傳回原始資料的記錄搜尋查詢，而不要使用命令執行任何彙總，例如[量值](log-analytics-search-reference.md#measure)。您可以在 Power BI 中從原始資料執行任何彙總與計算。

## 將 OMS 工作區連接到 Power BI

您必須使用下列程序將 OMS 工作區連接到 Power BI 帳戶，才能從 Log Analytics 匯出至 Power BI。

1. 在 OMS 主控台中按一下 [設定] 圖格。
2. 選取 [帳戶]。
3. 在 [工作區資訊] 區段中，按一下 [Connect to Power BI Account] (連接到 Power BI 帳戶)。
4. 輸入 Power BI 帳戶的認證。

## 建立 Power BI 排程

使用下列程序為每個資料集建立 Power BI 排程。

1. 在 OMS 主控台中按一下 [記錄搜尋] 圖格。
2. 輸入新的查詢或選取已儲存的搜尋，傳回您想要匯出至 **Power BI** 的資料。  
3. 按一下頁面頂端的 [Power BI] 按鈕，開啟 [Power BI] 對話方塊。
4. 提供下列資料表中的資訊，並按一下 [儲存]。

| 屬性 | 說明 |
|:--|:--|
| 名稱 | 當您檢視 Power BI 排程清單時，用來識別排程的名稱。 |
| 已儲存的搜尋 | 要執行的記錄搜尋。您可以選取目前的查詢，或從下拉式清單方塊中選取現有的已儲存搜尋。 |
| 排程 | 執行已儲存的搜尋，並匯出至 Power BI 資料集的頻率。值必須介於 15 分鐘到 24 小時之間。 |
| 資料集名稱 | Power BI 中的資料集名稱。如果不存在便會建立，如果存在則會更新。 |

## 檢視和移除 Power BI 排程

使用下列程序檢視現有 Power BI 排程的清單。

1. 在 OMS 主控台中按一下 [設定] 圖格。
2. 選取 [Power BI]。

除了排程的詳細資訊之外，還會顯示過去一週執行排程的次數，以及上次同步處理的狀態。如果在同步處理時發生錯誤，您可以按一下連結以執行記錄的記錄搜尋，以及錯誤的詳細資訊。

您可以按一下 [移除資料行] 中的 **X** 來移除排程。您可以選取 [關閉] 來停用排程。若要修改排程，您必須移除它，然後再以新的設定重新建立。

![Power BI 排程](media/log-analytics-power-bi/schedules.png)

## 範例逐步解說
下一節逐步解說建立 Power BI 排程，並使用其資料集來建立簡單報表的範例。在此範例中，一組電腦的所有效能資料都匯出至 Power BI，然後會建立折線圖以顯示處理器使用率。

### 建立記錄搜尋
首先我們會建立記錄搜尋，尋找我們想要傳送至資料集的資料。在此範例中，我們將使用一個查詢，傳回名稱開頭為 *srv* 之電腦的所有效能資料。

![Power BI 排程](media/log-analytics-power-bi/walkthrough-query.png)

### 建立 Power BI 搜尋
我們按一下 [Power BI] 按鈕以開啟 [Power BI] 對話方塊，並提供必要的資訊。我們想要每小時執行一次此搜尋，並建立名為 *Contoso Perf* 的資料集。由於我們已經開啟會建立我們所要資料的搜尋，因此我們針對 [已儲存的搜尋] 保留預設值「使用目前的搜尋查詢」。

![Power BI 搜尋](media/log-analytics-power-bi/walkthrough-schedule.png)

### 驗證 Power BI 搜尋
為了驗證我們已正確建立排程，我們會在 OMS 儀表板的 [設定] 圖格中檢視 Power BI 搜尋的清單。我們稍候幾分鐘的時間，並重新整理此檢視，直到它報告已執行同步處理。

![Power BI 搜尋](media/log-analytics-power-bi/walkthrough-schedules.png)

### 在 Power BI 中驗證資料集
我們在 [powerbi.microsoft.com](http://powerbi.microsoft.com/) 登入我們的帳戶，並捲動至左窗格底部的 [資料集]。我們可以看到已列出 *Contoso Perf* 資料集，表示我們的匯出已順利執行。

![Power BI 資料集](media/log-analytics-power-bi/walkthrough-datasets.png)

### 根據資料集建立報表
我們選取 **Contoso Perf** 資料集，然後按一下右邉 [欄位] 窗格中的 [結果]，檢視屬於此資料集的欄位。為了建立折線圖，顯示每一部電腦的處理器使用率，我們執行下列動作。

1. 選取折線圖視覺效果。
2. 將 **ObjectName** 拖放到 [報表層級篩選] 並勾選 [處理器]。
3. 將 **CounterName** 拖放到 [報表層級篩選] 並勾選 [% 處理器時間]。
4. 將 **CounterValue** 拖放到 [值]。
5. 將 **Computer** 拖放到 [圖例]。
6. 將 **TimeGenerated** 拖放到 [軸]。

我們可以看到會顯示產生的折線圖，以及來自我們資料集的資料。

![Power BI 折線圖](media/log-analytics-power-bi/walkthrough-linegraph.png)

### 儲存報表
我們在畫面頂端按一下 [儲存] 按鈕來儲存報表，並驗證它現在會列在左窗格中的 [報表] 區段中。

![Power BI 報表](media/log-analytics-power-bi/walkthrough-report.png)

## 後續步驟

- 深入了解[記錄搜尋](log-analytics-log-searches.md)，建置可以匯出至 Power BI 的查詢。
- 深入了解 [Power BI](powerbi.microsoft.com)，建置以 Log Analytics 匯出為基礎的視覺效果。

<!---HONumber=AcomDC_0504_2016-->