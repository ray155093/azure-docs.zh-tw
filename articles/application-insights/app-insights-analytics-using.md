<properties 
	pageTitle="使用分析 - 強大的 Application Insights 搜尋工具 | Microsoft Azure" 
	description="使用分析，這是強大的 Application Insights 診斷搜尋工具。" 
	services="application-insights" 
    documentationCenter=""
	authors="danhadari" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/06/2016" 
	ms.author="danha"/>


# 使用 Application Insights 中的分析


[分析](app-insights-analytics.md)是 [Application Insights](app-insights-overview.md) 的強大搜尋功能。這些頁面說明 Analytics 查詢語言。

* **[觀看簡介影片](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[在我們的模擬資料上測試分析](https://analytics.applicationinsights.io/demo)**，如果您的應用程式還未傳送資料至 Application Insights。

## 開啟分析

在 Application Insights 中，從您的應用程式的首頁資源，按一下 [分析]。

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-insights-analytics-using/001.png)

內嵌教學課程會提供您一些可執行作業的概念。

[這裡有更廣泛的教學課程](app-insights-analytics-tour.md)。

## 查詢您的遙測

### 撰寫查詢

![結構描述顯示](./media/app-insights-analytics-using/150.png)

以任何列在左側的資料表名稱 (或 [range](app-insights-analytics-reference.md#range-operator) 或 [union](app-insights-analytics-reference.md#union-operator) 運算子) 開頭。使用 `|` 建立[運算子](app-insights-analytics-reference.md#queries-and-operators)的直立線符號。IntelliSense 會對您提示運算子和某些您可以使用的運算式元素。

請參閱[分析語言概觀](app-insights-analytics-tour.md)和[語言參考](app-insights-analytics-reference.md)。

### 執行查詢

![執行查詢](./media/app-insights-analytics-using/130.png)

1. 您可以在查詢中使用單一分行符號。
2. 將游標放在您要執行的查詢內部或結尾。
3. 按一下 [執行] 來執行查詢。
4. 請勿在查詢中放置空白行。您可以用空白行來分隔一個查詢索引標籤中的數個查詢，讓它們保持分離狀態。只會執行游標所在的查詢。

### 儲存查詢

![儲存查詢](./media/app-insights-analytics-using/140.png)

1. 儲存目前的查詢檔案。
2. 開啟已儲存的查詢檔案。
3. 建立新的查詢檔案。


## 請參閱詳細資料

展開結果中的任何資料列，以查看其完整屬性清單。您可以進一步展開任屬於何結構化值的屬性 - 例如，自訂維度或例外狀況中的堆疊清單。

![展開資料列](./media/app-insights-analytics-using/070.png)

 

## 排列結果

您可以排序、篩選、分頁和分組查詢所傳回的結果。

> [AZURE.NOTE] 在瀏覽器中排序、分組和篩選不會重新執行查詢。這些作業只會重新排列最後一個查詢所傳回的結果。
> 
> 若要在傳回結果之前，在伺服器中執行這些工作，請使用 [sort](app-insights-analytics-reference.md#sort-operator)、[summarize](app-insights-analytics-reference.md#summarize-operator) 和 [where](app-insights-analytics-reference.md#where-operator) 運算子撰寫查詢。

挑選您想要查看的資料行，拖曳資料行標頭進行重新排列，然後拖曳其框線以調整資料行的大小。

![排列資料行](./media/app-insights-analytics-using/030.png)

### 排序和篩選項目

按一下資料行的標頭來排序結果。再次按一下以其他方式排序，而第三次按一下即可還原成查詢所傳回的原始順序。

使用篩選圖示縮小搜尋範圍。

![排序和篩選資料行](./media/app-insights-analytics-using/040.png)



### 將項目分組

若要依照多個資料行排序，請使用群組。請先啟用它，接著將資料行標頭拖曳到資料表上方的空間。

![群組](./media/app-insights-analytics-using/060.png)



### 遺漏某些結果？

從入口網站所傳回的結果有大約 1 萬個資料列的限制。如果超過此限制，就會顯示警告。如果發生這種情況，排序資料表中的結果不一定會顯示所有實際的第一個或最後一個結果。

最好避免達到限制。使用如下所示的運算子：

* [where timestamp > ago(3d)](app-insights-analytics-reference.md#where-operator)
* [top 100 by timestamp](app-insights-analytics-reference.md#top-operator)
* [take 100](app-insights-analytics-reference.md#take-operator)
* [summarize](app-insights-analytics-reference.md#summarize-operator)



## 圖表

選取您想要的圖表類型︰

![選取圖表類型](./media/app-insights-analytics-using/230.png)

如果您有數個正確類型的資料行，您可以選擇 x 和 y 軸，以及一個資料行的維度來據以分割結果。

根據預設，結果一開始會顯示為資料表，而您會手動選取圖表。但您可以在查詢結尾使用 [Render 指示詞](app-insights-analytics-reference.md#render-directive)以選取圖表。

## 釘選到儀表板

您可以將圖表或資料表釘選至您的其中一個[共用儀表板](app-insights-dashboards.md) - 只要按一下 [釘選]。(您可能需要[升級應用程式的資費套餐](app-insights-pricing.md)才能開啟此功能。)

![按一下 [釘選]](./media/app-insights-analytics-using/pin-01.png)

這表示，當您組建出儀表板來協助您監控 Web 服務的效能或使用量時，您可以在其中加入相當複雜的分析以及其他度量。

如果資料表有四個或更少的資料行，即可將該資料表釘選到儀表板。只會顯示前七個資料列。


#### 儀表板重新整理

釘選到儀表板的圖表大約每半小時就會藉由自動重新執行查詢來加以重新整理。

#### 自動簡化

在某些情況下，當您將圖表釘選到儀表板時，圖表會套用某些簡化效果。

當您釘選顯示了許多不連續長條的圖表 (通常是長條圖) 時，所佔比例較少的長條會自動分組到單一的「其他」長條。例如，下列查詢︰

    requests | summarize count_search = count() by client_CountryOrRegion

在分析中看起來像這樣︰


![具有長尾的圖表](./media/app-insights-analytics-using/pin-07.png)

但是，當您將它釘選到儀表板時，它看起來像這樣︰


![具有有限長條的圖表](./media/app-insights-analytics-using/pin-08.png)




## 匯出至 Excel

執行查詢之後，您可以下載 .csv 檔案。按一下 [匯出至 Excel]。

## 匯出至 Power BI

1. 將游標放在查詢中，然後選擇 [匯出至 Power BI]。

    ![](./media/app-insights-analytics-using/240.png)

    這會下載 M 指令碼檔案。

3. 將 M 語言指令碼複製到 Power BI Desktop 進階查詢編輯器中。
 * 開啟匯出的檔案。
 * 在 Power BI Desktop 中選取︰[取得資料]、[空白查詢]、[進階編輯器]，並貼上 M 語言指令碼。

    ![](./media/app-insights-analytics-using/250.png)

4. 視需要編輯認證，您現在可以建置您的報告。

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!----HONumber=AcomDC_0907_2016-->