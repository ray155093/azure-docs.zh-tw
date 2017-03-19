---
title: "使用串流分析執行即時 Twitter 情感分析 | Microsoft Docs"
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
ms.date: 03/03/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 576d8663f61d344628a38a98bf9902f4194949d0
ms.lasthandoff: 03/03/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure 串流分析中的即時 Twitter 情感分析

本文將透過把即時的 Twitter 事件帶入 Azure 事件中樞，來讓您了解如何建立社交媒體分析的情感分析解決方案。 您將編寫 Azure 串流分析查詢來分析資料。 然後儲存結果以供後續仔細研究，或是使用儀表板和 [Power BI](https://powerbi.com/) 來提供即時的見解。

社交媒體分析工具可協助讓組織來了解熱門話題，也就是社群媒體中擁有大量文章的主題以及態度。 情感分析 (亦稱為*意見挖掘*) 會使用社交媒體分析工具來判斷使用者對產品、概念等等項目的態度。 即時 Twitter 趨勢分析便是一個絕佳的範例，因為雜湊標記訂閱模型可讓您接聽摘要上的特定關鍵字，然後逐漸產生情感分析。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>案例：即時的社交媒體情感分析

擁有新聞媒體網站的公司提供與讀者有直接關聯的網站內容，試圖打敗競爭對手。 公司透過對 Twitter 資料執行即時情感分析，來針對與讀者相關的主題使用社交媒體分析。 具體來說，為了在 Twitter 上即時找出趨勢話題，公司需要即時分析關鍵話題的推文數量和感受反應。 因此基本上，他們需要以該社交媒體摘要為基礎的情感分析分析引擎。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶
* Twitter 帳戶和 [OAuth 存取權杖](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* 來自 GitHub 的 [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 檔案。
* 選擇性：[GitHub](https://aka.ms/azure-stream-analytics-twitterclient) 中的 Twitter 用戶端原始程式碼

## <a name="create-an-event-hub-input"></a>建立事件中樞輸入

此範例應用程式會產生事件，並它們推送至「事件中樞」執行個體 (簡稱為事件中樞)。 服務匯流排事件中樞是串流分析慣用的事件擷取方法。 如需詳細資訊，請檢閱 [Azure 服務匯流排文件](/azure/service-bus/)中的「事件中樞」文件。

### <a name="use-the-following-steps-to-create-an-event-hub"></a>請使用下列步驟建立新的事件中樞。

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [新增] > 輸入「事件中樞」，然後從產生的搜尋結果中選取 [事件中樞]。 接著，按一下 [建立]。
2. 為該「事件中樞」提供名稱，並建立「資源群組」。 我已分別指定 `socialtwitter-eh` 和 `socialtwitter-rg`。 請選取方塊來將帳戶釘選到儀表板，然後按一下 [建立] 按鈕。
3. 完成部署之後，按一下該「事件中樞」，然後按一下 [實體] 底下的 [事件中樞]。
4. 按一下 [+ 事件中樞] 按鈕來建立您的「事件中樞」。 再次提供您的名稱 (我的是 `socialtwitter-eh`)，然後按一下 [建立]。
5. 為了將存取權授與該事件中樞，我們必須建立一個共用存取原則。 請按一下該「事件中樞」，然後按一下 [設定] 底下的 [共用存取原則]。
6. 在 [共用存取原則] 底下，按一下 [+ 新增] 來建立一個擁有 [管理] 權限的新原則。 為該原則命名並選取 [管理]，然後按一下 [建立]。
7. 建立新原則之後，按一下該原則，然後按一下 [連接字串 - 主要金鑰] 實體的 [複製] 按鈕。 後續在此練習中，我們將會需要這項資訊。 接著，返回儀表板。

![共用存取原則端點](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>設定並啟動 Twitter 用戶端應用程式

我們提供一個用戶端應用程式，讓您能透過 [Twitter 的串流 API](https://dev.twitter.com/streaming/overview) 來連接 Twitter 資料，以便收集與某個參數化主題集相關的推文事件。 [Sentiment140](http://help.sentiment140.com/) 開放原始碼工具用來將感受值指派給每一則推文。

* 0 = 負值
* 2 = 中性
* 4 = 正值

接著，推文事件會推送至事件中樞。  

### <a name="follow-these-steps-to-set-up-the-application"></a>請遵循下列步驟來設定應用程式：

1. [下載 TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 並將它解壓縮。
2. 執行 `TwitterWPFClient.exe` 應用程式，然後輸入您的「Twitter API 金鑰與秘密」、「Twitter 存取權杖與秘密」，以及「Azure 事件中樞」資訊。 最後，定義您想要判斷情緒的關鍵字。 請注意，如果您指定多個字 (使用逗號來定義多個值) 並想要比對任何一項，就必須將控制項切換到 [Match ANY] (比對任何一項)。

   [產生 OAuth 存取權杖的步驟](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   您將必須建立一個空白應用程式來產生權杖。  

3. 以您事件中樞的連接字串和名稱取代 TwitterWpfClient.exe.config 中的 EventHubConnectionString 和 EventHubName 值。 您稍早複製的連接字串會提供事件中樞的連接字串和名稱，因此請務必加以區隔，並將每個項目放在正確的欄位中。 例如，請考慮下列連接字串：  
   
   `Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub`
   
   TwitterWpfClient.exe.config 檔案應該包含您的設定，如以下範例所示：
   
   ```
     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
     add key="EventHubName" value="yourhub"
   ```
   
   請注意，EventHubName 值中**不會**出現 "EntityPath=" 文字。
   
   您也可以直接在用戶端中輸入您 Twitter 和 Azure 連線資訊的值。 相同的邏輯也適用於未使用 "EntityPath=" 的地方。
   
   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

4. *選用：* 調整搜尋關鍵字。  此應用程式預設會尋找一些遊戲關鍵字。  您可以視需要調整 TwitterWpfClient.exe.config 中 **twitter_keywords** 的值。
5. 執行 TwitterWpfClient.exe，然後按一下綠色啟動按鈕來收集社交情緒資料。 您會看到推文事件連同 **CreatedAt**、**Topic** 和 **SentimentScore** 值，一起傳送到事件中樞。

## <a name="create-a-stream-analytics-job"></a>建立串流分析工作

既然推文事件是即時串流自 Twitter，我們現在便可設定「串流分析」工作來即時分析這些事件。

### <a name="provision-a-stream-analytics-job"></a>佈建資料流分析工作

在 [Azure 入口網站](https://portal.azure.com/) 中，按一下 [新增] > 輸入**串流分析**，然後按一下 [串流分析] 磚結果。 指定下列值，然後按一下 [建立]。

   * **工作名稱**：輸入工作名稱。
   * **資源群組**：從 [使用現有的] 選項中選取稍早在本練習中建立的資源群組。
   * **儲存體帳戶**：選擇您為在此區域內執行的所有串流分析工作儲存監視資料時所要使用的 Azure 儲存體帳戶。 您可以選擇現有的儲存體帳戶，或建立新帳戶。   

![New Job](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

建立工作之後，工作就會在 Azure 入口網站中開啟。

## <a name="specify-the-job-input"></a>指定工作輸入

在「串流分析工作」中，按一下工作窗格中間 [工作拓撲] 中的 [輸入]，然後按一下 [新增]。 接著，入口網站會提示您輸入以下所列的一些資訊。 大多數預設值都已夠用，但以下它們的定義可供您參考。
  
   * **輸入別名**：輸入此工作輸入的易記名稱，例如 `TwitterStream`。 您將會在稍後的查詢中使用此名稱。
   * **事件中樞名稱**：選取事件中樞的名稱。
   * **事件中樞原則名稱**：選取先前在本教學課程中建立的事件中樞原則。

按一下 [ **建立** ] 按鈕。

## <a name="specify-job-query"></a>指定工作查詢

串流分析支援說明轉換的簡單、宣告式查詢模型。 若要深入了解語言，請參閱 [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)。  本教學課程將協助您撰寫以及測試數個 Twitter 資料查詢。

為了比較提及不同話題的次數，我們會使用[輪轉視窗 (英文)](https://msdn.microsoft.com/library/azure/dn835055.aspx)，依話題每隔五秒取得一次提及次數。

請在程式碼編輯器中，將查詢變更成以下的程式碼，然後按一下 [儲存]：

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

此查詢會使用 **TIMESTAMP BY** 關鍵字，在裝載中指定一個暫時運算時會用到的時間戳記欄位。 如果未指定這個欄位，會根據每個事件到達事件中樞的時間，計算時間窗口。  若要深入了解，請參閱[串流分析查詢參考 (英文)](https://msdn.microsoft.com/library/azure/dn834998.aspx) 中的＜到達時間與應用程式時間之比較 (英文)＞一節。

此查詢也可以使用 **System.Timestamp** 屬性存取每個視窗結束時的時間戳記。

![查詢方塊](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-output-sink"></a>建立輸出接收

現在我們已經定義好一個事件資料流、一個負責擷取事件的事件中樞，以及一個進行資料流轉換處理的查詢，最後的步驟就是定義工作的輸出接收。  我們會編寫從工作查詢送往 Azure Blob 儲存體的推文事件彙總。  您也可以根據特定應用程式需求，將結果推送至 Azure SQL Database、Azure 表格儲存體或事件中樞。

## <a name="specify-job-output"></a>指定工作輸出

在「串流分析」工作中，按一下 [工作拓撲] 中的 [輸出]， 然後按一下 [新增]。 接著，在窗格上輸入或選取下列值：
   
   * **輸出別名**：為這個工作輸出設定一個容易記的名稱。 本文件將使用 `Output`。
   * **接收**：選取 [Blob 儲存體]。
   * **儲存體帳戶**：選取 [建立新的儲存體帳戶]。
    * **儲存體帳戶**：為新儲存體帳戶提供一個名稱 (此範例中為 `socialtwitter`)
    * **容器**：為新容器提供一個名稱 (此範例中為 `socialtwitter`)

讓其餘項目保留預設值。 最後，按一下 [建立]。

## <a name="start-the-job"></a>啟動工作

由於工作輸入、查詢及輸出都已經指定好了，現在可以開始串流分析工作。

在工作概觀窗格中，直接按一下頁面頂端的 [啟動]。

在開啟的對話方塊中，按一下 [工作開始時間]，然後按一下對話方塊最下方的 [核取] 按鈕。 工作狀態會變更為 [啟動中] 而且短時間內就會變成 [執行中]。

![工作正在執行](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>檢視情感分析輸出

當您的工作在執行及處理即時 Twitter 串流之後，請選擇情感分析輸出的檢視方式。 使用 [Azure 儲存體總管](https://http://storageexplorer.com/) 或 [Azure 總管](http://www.cerebrata.com/products/azure-explorer/introduction) 之類的工具來即時檢視您的工作輸出。 從這裡，您可以使用 [Power BI](https://powerbi.com/) 來擴充應用程式以包含在下列螢幕擷取畫面中類似的自訂儀表板。

![powerbi](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="another-query-of-interest--in-this-scenario"></a>此案例中另一個相關的查詢

我們為此案例建立的另一個範例查詢是根據[滑動視窗](https://msdn.microsoft.com/library/azure/dn835051.aspx)。 若要識別熱門話題，我們會找出各種在指定時間內，被提及的次數超過臨界值的話題。 基於本教學課程的目的，我們會找出過去五秒內提及次數超過 20 次的話題。

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="get-support"></a>取得支援
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


