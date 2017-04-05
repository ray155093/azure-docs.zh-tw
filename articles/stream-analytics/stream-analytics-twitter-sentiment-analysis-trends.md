---
title: "使用 Azure 串流分析執行即時 Twitter 情感分析 | Microsoft Docs"
description: "了解如何使用串流分析來執行即時 Twitter 情感分析。 從事件產生到即時儀表板資料的逐步指導。"
keywords: "即時的 twitter 趨勢分析, 情感分析, 社交媒體分析, 趨勢分析範例"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 0ba5baf71176e8d5967775dae50b6577f8b5c54c
ms.lasthandoff: 03/29/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure 串流分析中的即時 Twitter 情感分析

本文將透過把即時的 Twitter 事件帶入 Azure 事件中樞，來讓您了解如何建立社交媒體分析的情感分析解決方案。 在此案例中，您會編寫 Azure 串流分析查詢來分析資料。 然後您會儲存結果以供後續使用，或使用儀表板和 [Power BI](https://powerbi.com/) 來提供即時的見解。

社交媒體分析工具可協助組織了解熱門話題。 熱門話題就是在社交媒體中有大量文章的話題及意見。 情感分析 (亦稱為*意見挖掘*) 會使用社交媒體分析工具來判斷使用者對產品、概念等等項目的態度。

即時 Twitter 趨勢分析便是分析工具的絕佳範例，因為雜湊標記訂閱模型可讓您接聽摘要上的特定關鍵字，然後逐漸產生情感分析。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>案例：即時的社交媒體情感分析

擁有新聞媒體網站的公司提供與讀者有直接關聯的網站內容，試圖打敗競爭對手。 公司透過對 Twitter 資料執行即時情感分析，來針對與讀者相關的話題使用社交媒體分析。

具體來說，為了在 Twitter 上即時找出熱門話題，公司需要即時分析熱門話題的推文數量和感受度。 換言之，他們需要以該社交媒體摘要為基礎的情感分析分析引擎。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶
* Twitter 帳戶和 [OAuth 存取權杖](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* 來自 GitHub 的 [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 檔案
* 選擇性：[GitHub](https://aka.ms/azure-stream-analytics-twitterclient) 中的 Twitter 用戶端原始程式碼

## <a name="create-an-event-hub-input"></a>建立事件中樞輸入

此範例應用程式會產生事件，並它們推送至「事件中樞」執行個體 (簡稱為事件中樞)。 Azure 服務匯流排事件中樞是串流分析慣用的事件擷取方法。 如需詳細資訊，請檢閱 [Azure 服務匯流排文件](/azure/service-bus/)中的事件中樞文件。

### <a name="create-an-event-hub"></a>建立事件中心

請採用下列步驟來建立事件中樞：

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [新增]，輸入「事件中樞」，然後從產生的搜尋結果中選取 [事件中樞]。 然後選取 [建立]。

2. 提供事件中樞的名稱，然後建立資源群組。 我們已分別指定 `socialtwitter-eh` 和 `socialtwitter-rg`。 請選取選項來將帳戶釘選到儀表板，然後選取 [建立] 按鈕。

3. 完成部署後，選取事件中樞。 然後，在 [實體] 之下，選取 [事件中樞]。

4. 若要建立事件中樞，請選取 [+事件中樞] 按鈕。 再次提供您的名稱 (我們的是 `socialtwitter-eh`)，然後選取 [建立]。

5. 為了將存取權授與該事件中樞，我們必須建立一個共用存取原則。 選取事件中樞，然後選取 [設定] 底下的 [共用存取原則]。

6. 在 [共用存取原則] 底下，選取 [+ 新增] 以建立具有 [管理] 權限的原則。 為該原則命名，勾選 [管理]，然後選取 [建立]。

7. 選取建立後的新原則，然後選取 [連接字串 - 主要金鑰] 實體的 [複製] 按鈕。 我們稍後在練習中需要此資訊。 接著，返回儀表板。

![共用存取原則端點](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>設定並啟動 Twitter 用戶端應用程式

我們建立了一個用戶端應用程式，讓您能透過 [Twitter 的串流 API](https://dev.twitter.com/streaming/overview) 來與 Twitter 資料連線，以便收集與某個參數化話題集相關的推文事件。 [Sentiment140](http://help.sentiment140.com/) 開放原始碼工具會將感受值指派給每一則推文，如下所示：

* 0 = 負值
* 2 = 中性
* 4 = 正值

接著，推文事件會推送至事件中樞。  

### <a name="set-up-the-application"></a>設定範例應用程式
 請遵循下列步驟來設定應用程式：

1. [下載 TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip)，然後將它解壓縮。

2. 執行 `TwitterWPFClient.exe` 應用程式。 然後輸入您的「Twitter API 金鑰與密碼」、「Twitter 存取權杖與密碼」資料，以及事件中樞資訊。 最後，定義您想要判斷情緒的關鍵字。

### <a name="generate-an-oauth-access-token"></a>產生 OAuth 存取權杖
如需詳細資訊，請參閱[產生 OAuth 存取權杖的步驟](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)。  

 若要產生權杖，您必須建立一個空白應用程式。  

1. 以您事件中樞的連接字串和名稱取代 TwitterWpfClient.exe.config 中的 EventHubConnectionString 和 EventHubName 值。 您稍早複製的連接字串會提供事件中樞的連接字串和名稱。 請務必加以區隔，並將每個項目放在正確的欄位中。 例如，請考慮下列連接字串：

 ```
    Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub
 ```  

   TwitterWpfClient.exe.config 檔案應該包含您的設定，如以下範例所示：

 ```
      add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
      add key="EventHubName" value="yourhub"
   ```
   > [!NOTE]
   > EventHubName 值中**不會**出現 "EntityPath=" 文字。

    您也可以直接在用戶端中輸入您 Twitter 和 Azure 連線資訊的值。 相同的邏輯也適用於未使用 "EntityPath=" 的地方。

   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

2. **選用：** 調整搜尋關鍵字。 此應用程式預設會尋找一些遊戲關鍵字。  您可以視需要調整 TwitterWpfClient.exe.config 中 **twitter_keywords** 的值。

3. 執行 TwitterWpfClient.exe。 然後選取綠色啟動按鈕來收集社交情緒資料。 您會看到推文事件連同 **CreatedAt**、**Topic** 和 **SentimentScore** 值，一起傳送到事件中樞。

## <a name="create-a-stream-analytics-job"></a>建立串流分析工作

現在，來自 Twitter 的 Twitter 事件正在進行即時串流，我們可以設定串流分析工作並即時分析這些事件。

### <a name="provision-a-stream-analytics-job"></a>佈建資料流分析工作

若要佈建資料流分析作業，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [新增]，輸入**串流分析**，然後選取 [串流分析] 圖格結果。

2. 指定下列值，然後選取 [建立]。

   * **工作名稱**：輸入工作名稱。

   * **資源群組**：從 [使用現有的] 選項中選取稍早在本練習中建立的資源群組。

   * **儲存體帳戶**：選擇您為在此區域內執行的所有串流分析作業儲存監視資料時所要使用的 Azure 儲存體帳戶。 您可以選擇現有的儲存體帳戶，或建立新帳戶。   

![新增作業](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

建立作業之後，它會在 Azure 入口網站中開啟。

## <a name="specify-the-job-input"></a>指定工作輸入
若要指定作業輸入，請採取下列步驟：

1. 在串流分析作業中，於作業窗格中間的 [作業拓撲] 中選取 [輸入]。 然後選取 [新增]。

    接著，入口網站會提示您輸入下列某些資訊。 大部分的預設值都有作用，其定義如下︰

   * **輸入別名**：輸入此工作輸入的易記名稱，例如 `TwitterStream`。 您會在稍後的查詢中使用此名稱。  

   * **事件中樞名稱**：選取事件中樞的名稱。

   * **事件中樞原則名稱**：選取先前在本教學課程中建立的事件中樞原則。

2. 選取 [建立] 按鈕。

## <a name="specify-the-job-query"></a>指定作業查詢

串流分析支援說明轉換的簡單、宣告式查詢模型。 若要深入了解語言，請參閱 [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)。  本教學課程可協助您撰寫以及測試數個 Twitter 資料查詢。

為了比較提及不同話題的次數，我們會使用[輪轉視窗 (英文)](https://msdn.microsoft.com/library/azure/dn835055.aspx)，依話題每隔五秒取得一次提及次數。

在程式碼編輯器中將查詢變更成以下的程式碼，然後選取 [儲存]：

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

此查詢會使用 **TIMESTAMP BY** 關鍵字，在裝載中指定一個暫時運算時會用到的時間戳記欄位。 如果未指定這個欄位，則會使用每個事件到達事件中樞的時間，執行時間範圍設定作業。 若要深入了解，請參閱[串流分析查詢參考 (英文)](https://msdn.microsoft.com/library/azure/dn834998.aspx) 中的＜到達時間與應用程式時間之比較 (英文)＞一節。

此查詢也可以使用 **System.Timestamp** 屬性存取每個視窗結束時的時間戳記。

![查詢方塊](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-an-output-sink"></a>建立輸出接收器

現在我們已經定義好一個事件資料流、一個負責擷取事件的事件中樞，以及一個進行資料流轉換處理的查詢，最後的步驟就是定義工作的輸出接收。  

我們會編寫從作業查詢送往 Azure Blob 儲存體的彙總推文事件。  您也可以根據特定應用程式需求，將結果推送至 Azure SQL Database、Azure 表格儲存體或事件中樞。

## <a name="specify-the-job-output"></a>指定作業輸出
若要指定作業輸出，請採取下列步驟：

1. 在串流分析作業中，按一下 [作業拓撲] 中的 [輸出]，然後選取 [新增]。

2. 在窗格中輸入或選取下列值：

   * **輸出別名**：為這個工作輸出設定一個容易記的名稱。 本示範會使用 `Output`。

   * **接收器**：選取 [Blob 儲存體]。

   * **儲存體帳戶**：選取 [建立新的儲存體帳戶]。

    * **儲存體帳戶**：提供新儲存體帳戶的名稱 (此範例中為 `socialtwitter`)。

    * **容器**：提供新容器的名稱 (此範例中為 `socialtwitter`)。

3. 讓其餘項目保留預設值。 最後，選取 [建立]。

## <a name="start-the-job"></a>啟動工作

由於工作輸入、查詢及輸出都已經指定好了，現在可以開始串流分析工作。

若要啟動作業，請採取下列步驟：

1. 在 [作業概觀] 窗格中，選取頁面頂端的 [開始]。

2. 在開啟的對話方塊中，選取 [作業開始時間] ，然後選取對話方塊底部的**核取記號**按鈕。 作業狀態會先變更為 [啟動中]，然後再變更為 [執行中]。

![工作正在執行](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>檢視情感分析輸出

在您的作業已開始執行並處理即時 Twitter 串流之後，請選擇您要檢視情感分析輸出的方式。

使用 [Azure 儲存體總管](https://http://storageexplorer.com/) 或 [Azure 總管](http://www.cerebrata.com/products/azure-explorer/introduction) 此類的工具，即時檢視您的工作輸出。 從這裡，您可以使用 [Power BI](https://powerbi.com/) 來擴充應用程式，以包含如下列螢幕擷取畫面中所示的自訂儀表板。

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="create-another-query-to-identify-trending-topics"></a>建立另一個查詢來識別熱門話題

我們為此案例建立的另一個範例查詢是以[滑動時間範圍](https://msdn.microsoft.com/library/azure/dn835051.aspx)為基礎。 若要識別熱門話題，我們會找出各種在指定時間內，被提及的次數超過臨界值的話題。

基於本教學課程的目的，我們會找出過去 5 秒內提及次數超過 20 次的話題。

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="use-field-headers"></a>使用欄位標頭

您可以在這個練習中使用的欄位標籤都列在此表中。 您可在查詢編輯器中自由進行試驗。

JSON 屬性 | 定義
--- | ---
CreatedAt | 建立推文的時間
話題 | 符合所指定關鍵字的話題
SentimentScore | 來自 Sentiment140 的情感分數
作者 | 傳送推文的 Twitter 控制代碼
文字 | 推文的全文


## <a name="get-support"></a>取得支援
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

