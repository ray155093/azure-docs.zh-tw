---
title: 從 Application Insights 匯出至 Power BI
description: 文章
services: application-insights
documentationcenter: ''
author: noamben
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/05/2016
ms.author: awills

---
# 從 Application Insights 提供 Power BI
[Power BI](http://www.powerbi.com/) 是一套商務分析工具，用來分析資料及分享見解。每個裝置上都提供豐富的儀表板。您可以結合許多來源的資料，包含來自 [Visual Studio Application Insights](app-insights-overview.md) 的資料。

若要開始，請參閱[在 Power BI 中顯示 Application Insights 資料](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)。

您會取得一個初始儀表板，您可以加以自訂，以及將 Application Insights 圖表與其他來源的圖表結合。還有一個視覺效果資源庫，您可以從中取得更多圖表，且每個圖表都有您可以設定的參數。

![](./media/app-insights-export-power-bi/010.png)

初始匯入之後，儀表板和報告會持續每日更新。您可以控制資料集上的重新整理排程。

**取樣** 如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則調適性取樣功能可能會運作，並只傳送一部分的遙測資料。如果您已經在 SDK 中或在擷取上手動設定取樣，也是如此。[深入了解取樣。](app-insights-sampling.md)

## 查看 Application Insights 資料的替代方式
* 如果您不需要顯示非 Azure 的資料，[包含 Application Insights 圖表的 Azure 儀表板](app-insights-dashboards.md)可能更為適用。例如，如果您想要設定監視不同系統元件之 Application Insights 圖表的儀表板 (也許和某些 Azure 服務監視一起)，Azure 儀表板就會是理想的選擇。根據預設，它會更頻繁地更新。 
* [連續匯出](app-insights-export-telemetry.md)會將您的傳入資料複製到 Azure 儲存體，您可以在其中以您想要的方式移動並處理資料。
* [分析](app-insights-analytics.md)可讓您在 Application Insights 保留的未經處理資料上執行複雜的查詢。

## 使用串流分析建立您自己的 Power BI 配接器
Application Insights 的 Power BI 內容套件會顯示一組有用的應用程式遙測子集，這對您的需求來說可能已足夠。但是，如果您想要取得範圍超過它所提供的更多遙測，或如果您想要計算來自原始遙測的部分資料，您可以使用 Azure 串流分析服務建立您自己的配接器。

在此配置中，我們將從 Application Insights 匯出資料至 Azure 儲存體。[串流分析](https://azure.microsoft.com/services/stream-analytics/)將從中提取資料、重新命名和處理某些欄位，並將它傳送到 Power BI。「串流分析」是一種服務，可以篩選、彙總和執行持續性資料流運算。

![透過 SA 匯出至 PBI 的區塊圖](./media/app-insights-export-power-bi/020.png)

> [!TIP]
> **您不需要遵循這篇文章其餘部分的程序** (使用「串流分析」) 即可查看 Power BI 中的 Application Insights 資料。還有更簡單的方法！ (改為[使用可用的配接器](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)。只有在該配接器未提供您想要的所有資料，或是您想要定義您自己的資料彙總或函式時，才遵循這篇文章的其餘部分。
> 
> 

### 在 Azure 中建立儲存體
連續匯出一律會將資料輸出至 Azure 儲存體帳戶，因此您必須先建立儲存體。

1. 您試用過 [Application Insights 的 Power BI PowerPack](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/) 嗎？ 如果它已經可以滿足您的需求，您就不需要這篇文章中其餘部分的任何項目。
2. 在 [Azure 入口網站](https://portal.azure.com)的訂用帳戶中建立「傳統」儲存體帳戶。
   
   ![在 Azure 入口網站中，依序選擇 [新增]、[資料]、[儲存體]](./media/app-insights-export-power-bi/030.png)
3. 建立容器
   
    ![在新的儲存體中，選取 [容器]，按一下容器磚，然後按一下 [新增]](./media/app-insights-export-power-bi/040.png)
4. 複製儲存體存取金鑰
   
    稍後您會需要使用它來設定串流分析服務的輸入。
   
    ![在儲存體中，依序開啟 [設定]、[金鑰]，然後複製主要存取金鑰](./media/app-insights-export-power-bi/045.png)

### 啟動對 Azure 儲存體的連續匯出
[連續匯出](app-insights-export-telemetry.md)會將資料從 Application Insights 移入 Azure 儲存體。

1. 在 Azure 入口網站中，瀏覽至您為應用程式建立的 Application Insights 資源。
   
    ![依序選擇 [瀏覽]、[Application Insights]、您的應用程式](./media/app-insights-export-power-bi/050.png)
2. 建立連續匯出。
   
    ![依序選擇 [設定]、[連續匯出]、[新增]](./media/app-insights-export-power-bi/060.png)

    選取您稍早建立的儲存體帳戶：

    ![設定匯出目的地](./media/app-insights-export-power-bi/070.png)

    設定您想要查看的事件類型：

    ![選擇事件類型](./media/app-insights-export-power-bi/080.png)

1. 可讓一些資料累積。請休息一下，讓其他人使用您的應用程式一段時間。遙測資料會送過來，而您會在[計量瀏覽器](app-insights-metrics-explorer.md)中看到統計圖表，並在[診斷搜尋](app-insights-diagnostic-search.md)中看到個別事件。
   
    此外，資料會匯出至您的儲存體。
2. 檢查匯出的資料。在 Visual Studio 中，依序選擇 [檢視]、[Cloud Explorer]，然後依序開啟 [Azure]、[儲存體]。(如果您沒有此功能表選項，您需要安裝 Azure SDK：開啟 [新增專案] 對話方塊，然後開啟 [Visual C#] / [Cloud] / [取得 Microsoft Azure SDK for .NET]。)
   
    ![](./media/app-insights-export-power-bi/04-data.png)
   
    記下衍生自應用程式名稱和檢測金鑰之路徑名稱的共同部分。

事件會以 JSON 格式寫入至 Blob 檔案。每個檔案可能會包含一或多個事件。因此我們想要讀取事件資料，並篩選出需要的欄位。該處有用這些資料所能做到的所有事情種類，但我們現在計劃要使用串流分析將資料傳送至 Power BI。

### 建立 Azure 串流分析執行個體
在[傳統 Azure 入口網站](https://manage.windowsazure.com/)中，選取 Azure 串流分析服務，然後建立新的串流分析工作：

![](./media/app-insights-export-power-bi/090.png)

![](./media/app-insights-export-power-bi/100.png)

建立新工作後，請展開其詳細資料：

![](./media/app-insights-export-power-bi/110.png)

#### 設定 Blob 位置
將此設定為從您的連續匯出 Blob 接收輸入：

![](./media/app-insights-export-power-bi/120.png)

現在您需要儲存體帳戶的主要存取金鑰 (您已在稍早記下此金鑰)。請將此金鑰設為儲存體帳戶金鑰。

![](./media/app-insights-export-power-bi/130.png)

#### 設定路徑前置詞模式
![](./media/app-insights-export-power-bi/140.png)

**請務必將 [日期格式] 設為 [YYYY-MM-DD]\(含連接號)。**

路徑前置詞模式會指定串流分析在存放區中尋找輸入檔案的位置。您需要將它設定為與連續匯出儲存資料的方式相對應。請設定如下：

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

在此範例中：

* `webapplication27` 是 Application Insights 資源名稱 (全部小寫 )。
* `1234...` 是 Application Insights 資源的檢測金鑰，**省略破折號**。 
* `PageViews` 是您想要分析的資料類型。可用的類型取決於您在「連續匯出」中設定的篩選。檢查匯出的資料以查看其他可用的類型，並查看[匯出資料模型](app-insights-export-data-model.md)。
* `/{date}/{time}` 是要依字面意思寫入資訊的格式。

> [!NOTE]
> 檢查儲存區以確定您取得正確的路徑。
> 
> 

#### 完成初始設定
確認序列化格式：

![確認並關閉精靈](./media/app-insights-export-power-bi/150.png)

關閉精靈，並等候設定完成。

> [!TIP]
> 您可以使用範例命令來下載一些資料。將其保留下來做為偵錯查詢的測試範例。
> 
> 

### 設定輸出
現在選取您的工作並設定輸出。

![選取新的通道，依序按一下 [輸出]、[新增]、[Power BI]](./media/app-insights-export-power-bi/160.png)

提供您的**工作或學校帳戶**來授權串流分析存取您的 Power BI 資源。接著自創一個輸出的名稱，以及目標 Power BI 資料集和資料表的名稱。

![創建三個名稱](./media/app-insights-export-power-bi/170.png)

### 設定查詢
查詢會控管從輸入到輸出的轉譯。

![選取工作並按一下 [查詢]。貼上下面的範例。](./media/app-insights-export-power-bi/180.png)

使用測試函式來確認您取得正確的輸出。填入您從輸入頁面所採用的範例資料。

#### 顯示事件計數的查詢
貼上此查詢：

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input 是我們提供給串流輸入的別名
* pbi-output 是我們所定義的輸出別名
* 我們會使用 [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx)，因為事件名稱是在巢狀 JSON 陣列中。然後 Select 會取用事件名稱，以及時間週期內具有該名稱之執行個體數目的計數。[Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) 子句會將項目分組到 1 分鐘的時間週期內。

#### 顯示度量值的查詢
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* 此查詢會切入度量遙測，來取得事件時間和度量值。度量值位於陣列中，因此我們使用 OUTER APPLY GetElements 模式來擷取資料列。在此情況下，"myMetric" 是度量的名稱。 

#### 包含維度屬性值的查詢
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* 此查詢包括維度屬性值，而不需根據陣列索引中固定索引的特定維度。

### 執行工作
您可以選取一個啟動工作的過去日期。

![選取工作並按一下 [查詢]。貼上下面的範例。](./media/app-insights-export-power-bi/190.png)

請等候直到作業執行。

### 在 Power BI 中查看結果
以您的工作或學校帳戶開啟 Power BI，並選取您定義為串流分析工作輸出的資料集與資料表。

![在 Power BI 中，選取您的資料集和欄位。](./media/app-insights-export-power-bi/200.png)

現在您可以使用報告中的此資料集和 [Power BI](https://powerbi.microsoft.com) 中的儀表板。

![在 Power BI 中，選取您的資料集和欄位。](./media/app-insights-export-power-bi/210.png)

### 沒有資料？
* 請檢查是否正確[設定日期格式](#set-path-prefix-pattern)：YYYY-MM-DD (含連接號)。

### 影片
Noam Ben Zeev 會示範如何匯出至 Power BI。

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## 相關項目
* [連續匯出](app-insights-export-telemetry.md)
* [屬性類型和值的詳細資料模型參考。](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [更多範例和逐步解說](app-insights-code-samples.md)

<!---HONumber=AcomDC_0420_2016-->