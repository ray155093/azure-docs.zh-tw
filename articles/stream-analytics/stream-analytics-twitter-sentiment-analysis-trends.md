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
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3c97604b17636f011ddb2acda40fbc77afeab590
ms.openlocfilehash: 9f7e9008f29b2b1a3a0422133e15871c4ce7cca8


---
# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>社交媒體分析：Azure 串流分析中的即時 Twitter 情感分析
本文將透過把即時的 Twitter 事件帶入 Azure 事件中樞，來讓您了解如何建立社交媒體分析的情感分析解決方案。 您將編寫 Azure 串流分析查詢來分析資料。 然後儲存結果以供後續讀取使用，或使用儀表板和 [Power BI](https://powerbi.com/) 提供即時的見解。

社交媒體分析工具可協助讓組織來了解熱門話題，也就是社群媒體中擁有大量文章的主題以及態度。 情感分析 (亦稱為*意見挖掘*) 會使用社交媒體分析工具來判斷使用者對產品、概念等等項目的態度。 即時的 Twitter 趨勢分析是絕佳的範例，因為雜湊標記訂用帳戶模型可讓您針對摘要來聆聽特定的關鍵字，並逐漸產生情感分析。

## <a name="scenario-sentiment-analysis-in-real-time"></a>案例：即時的情感分析
擁有新聞媒體網站的公司提供與讀者有直接關聯的網站內容，試圖打敗競爭對手。 公司透過對 Twitter 資料執行即時情感分析，來針對與讀者相關的主題使用社交媒體分析。 具體來說，為了在 Twitter 上即時找出熱門話題，公司需要即時分析熱門話題的推文數量和感受度。 因此基本上，他們需要以該社交媒體摘要為基礎的情感分析分析引擎。

## <a name="prerequisites"></a>必要條件
* Twitter 帳戶和 [OAuth 存取權杖](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) 。
* 選擇性：[GitHub](https://aka.ms/azure-stream-analytics-twitterclient) 中的 Twitter 用戶端原始程式碼

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>建立事件中樞輸入和取用者群組
這個範例應用程式會產生事件並推送至事件中樞執行個體 (簡稱為事件中樞)。 服務匯流排事件中樞是串流分析慣用的事件擷取方法。 請參閱 [Azure 服務匯流排文件](/azure/service-bus/)中的事件中樞文件。

請使用下列步驟建立新的事件中樞。

1. 在 Azure 入口網站中，依序按一下 [新增] > [應用程式服務] > [服務匯流排] > [事件中樞] > [快速建立]，並提供名稱、區域，以及新的或現有的命名空間。  
2. 每一個串流分析工作應該從單一事件中樞取用者群組讀取資料，這是最好的做法。 稍後我們將逐步引導您進行建立取用者群組。 您可以在[Azure 事件中樞概觀](https://msdn.microsoft.com/library/azure/dn836025.aspx)中深入了解取用者群組 。 若要建立取用者群組，請移至新建立的事件中樞，然後依序按一下 [取用者群組] 索引標籤、頁面最下方的 [建立]，然後提供取用者群組的名稱。
3. 為了授權存取事件中樞，我們需要建立一個共用存取原則。 按一下事件中樞的 [設定] 索引標籤。
4. 在 [共用存取原則] 下方，建立一個擁有**管理**權限的新原則。

   ![共用存取原則，您可以利用管理權限來建立原則。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)
5. 按一下頁面底部的 [儲存]  。
6. 移至 [儀表板]，按一下頁面最下方的 [連線資訊]，然後複製並儲存連線資訊。 (使用搜尋圖示下方的複製圖示)。

## <a name="configure-and-start-the-twitter-client-application"></a>設定並啟動 Twitter 用戶端應用程式
我們提供一個用戶端應用程式，讓您能透過 [Twitter 的串流 API](https://dev.twitter.com/streaming/overview) 來連接 Twitter 資料，以便收集與某個參數化主題集相關的推文事件。 [Sentiment140](http://help.sentiment140.com/) 開放原始碼工具用來將感受值指派給每一則推文。

* 0 = 負值
* 2 = 中性
* 4 = 正值

接著，推文事件會推送至事件中樞。  

請遵循下列步驟來設定應用程式：

1. [下載 TwitterClient 解決方案](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip)。
2. 開啟 TwitterClient.exe.config，然後將 oauth_consumer_key、oauth_consumer_secret、oauth_token，以及 oauth_token_secret 換成具有您的值的 Twitter 權杖。  

   [產生 OAuth 存取權杖的步驟](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   請注意，您必須建立一個空白應用程式，才能產生權杖。  
3. 將 TwitterClient.exe.config 中的 EventHubConnectionString 和 EventHubName 值換成事件中樞的連接字串和名稱。 您稍早複製的連接字串會提供事件中樞的連接字串和名稱，因此請務必加以區隔，並將每個項目放在正確的欄位中。 例如，請考慮下列連接字串：

     Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

   TwitterClient.exe.config 檔案應包含您的設定，如下面範例所示：

     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"   add key="EventHubName" value="yourhub"

   請注意，EventHubName 值中**不會**出現 "EntityPath=" 文字。
4. *選用：* 調整搜尋關鍵字。  根據預設，這個應用程式會尋找 Azure、Skype、XBox、Microsoft、Seattle。  如有需要，您可以調整 TwitterClient.exe.config 中的 **twitter_keywords** 值。
5. 執行 TwitterClient.exe 以啟動應用程式。 您會看到推文事件連同 **CreatedAt**、**Topic** 和 **SentimentScore** 值，一起傳送到事件中樞。

   ![情感分析：傳送至事件中樞的 SentimentScore 值。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>建立串流分析工作
現在，來自 Twitter 的 Twitter 事件正在進行即時串流，我們可以設定串流分析工作並即時分析這些事件。

### <a name="provision-a-stream-analytics-job"></a>佈建資料流分析工作
1. 在 [Azure 入口網站](https://manage.windowsazure.com/)中，按一下 [新增] > [資料服務] > [串流分析] > [快速建立]。
2. 指定下列值，然後按一下 [建立 STREAM ANALYTICS 工作] ：

   * **工作名稱**：輸入工作名稱。
   * **區域**：選取要執行此工作的區域。 請考慮將工作和事件中樞放在相同的區域以確保更好的效能，以及在區域之間傳輸資料時無須付費。
   * **儲存體帳戶**：選擇您為在此區域內執行的所有串流分析工作儲存監視資料時所要使用的 Azure 儲存體帳戶。 您可以選擇現有的儲存體帳戶，或建立新帳戶。
3. 按一下左窗格中的 [串流分析]，以列出串流分析工作。  
   ![串流分析服務圖示](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

   新工作會以 [已建立] 的狀態列出。 請注意，頁面底部的 [啟動]  按鈕會停用。 您必須先設定工作輸入、輸出、查詢等項目，才能啟動工作。

### <a name="specify-job-input"></a>指定工作輸入
1. 在串流分析工作中，按一下頁面上方的 [輸入]，然後按一下 [新增輸入]。 開啟的對話方塊會逐步引導您完成設定輸入。
2. 按一下 [資料流]，然後按一下右鍵。
3. 按一下 [事件中樞]，然後按一下右鍵。
4. 在第三頁上輸入或選取下列值：

   * **輸入別名**：輸入此工作輸入的易記名稱，例如 *TwitterStream*。 請注意，您將會在後續查詢中使用此名稱。
     **事件中樞**：如果您建立的事件中樞與串流分析工作位於相同的訂用帳戶中，請選取事件中樞所在的命名空間。

     如果您的事件中樞位於不同的訂用帳戶中，請按一下 [使用其他訂用帳戶中的事件中樞]，然後手動輸入 [服務匯流排命名空間]、[事件中樞名稱]、[事件中樞原則名稱]、[事件中樞原則索引鍵] 及 [事件中樞資料分割計數] 資訊。
   * **事件中樞名稱**：選取事件中樞的名稱。
   * **事件中樞原則名稱**：選取先前在本教學課程中建立的事件中樞原則。
   * **事件中樞取用者群組**：輸入在本教學課程稍早建立的取用者群組名稱。
5. 按一下向右按鈕。
6. 指定下列值：

   * **事件序列化程式格式**：JSON
   * **編碼**：UTF8
7. 按一下 [核取] 按鈕以新增此來源，並確認串流分析可成功連接到事件中樞。

### <a name="specify-job-query"></a>指定工作查詢
串流分析支援說明轉換的簡單、宣告式查詢模型。 若要深入了解語言，請參閱 [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)。  本教學課程將協助您撰寫以及測試數個 Twitter 資料查詢。

#### <a name="sample-data-input"></a>資料輸入範例
若要驗證對實際工作資料的查詢結果，您可以使用 [範例資料] 功能，從資料流擷取事件並為這些事件建立 .json 檔案進行測試。

1. 選取事件中樞輸入，然後按一下頁面最下方的 [範例資料]。
2. 在開啟的對話方塊中，指定開始收集資料的 [開始時間] 以及表示要取用多少其他的資料 [持續時間]。
3. 按一下 [詳細資料] 按鈕，然後按一下 [按一下這裡] 連結，以下載並儲存產生的 .json 檔案。

#### <a name="pass-through-query"></a>傳遞查詢
一開始，我們會執行一項投射事件中所有欄位的簡單傳遞查詢。

1. 按一下串流分析頁面頂端的 [查詢] 。
2. 在程式碼編輯器中，將初始查詢範本取代為以下各項：

     SELECT * FROM TwitterStream

   請確定輸入來源的名稱符合您先前指定的輸入名稱。
3. 在查詢編輯器中按一下 [測試]  。
4. 移至範例 .json 檔案。
5. 按一下 [核取] 按鈕，然後參閱查詢定義下方的結果。

   ![查詢定義下方顯示的結果](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>話題的推文次數：含彙總的輪轉視窗
若要比較不同話題被提到的次數，我們會使用 [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) 每隔五秒取得話題被提到的次數。

1. 在程式碼編輯器中，將查詢變更成：

     SELECT System.Timestamp as Time, Topic, COUNT(*)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic

   此查詢會使用 **TIMESTAMP BY** 關鍵字，在裝載中指定一個暫時運算時會用到的時間戳記欄位。 如果未指定這個欄位，會根據每個事件到達事件中樞的時間，計算時間窗口。  如需詳細資訊，請參閱[串流分析查詢參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)中的＜到達時間與應用程式時間＞一節。

   此查詢也可以使用 **System.Timestamp** 屬性存取每個視窗結束時的時間戳記。
2. 按一下查詢編輯器中的 [重新執行]  ，即可看到查詢結果。

#### <a name="identify-trending-topics-sliding-window"></a>識別熱門話題：滑動視窗
若要識別熱門話題，我們會找出各種在指定時間內，被提及的次數超過臨界值的話題。 基於本教學課程的目的，我們會使用 [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx) 來尋找在前五秒內提及次數超過 20 次的話題。

1. 在程式碼編輯器中，將查詢變更為：SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY SLIDINGWINDOW(s, 5), topic   HAVING COUNT(*) > 20
2. 按一下查詢編輯器中的 [重新執行]  ，即可看到查詢結果。

   ![滑動視窗查詢輸出](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>計算被提到的次數和感受度：輪轉視窗與彙總
我們最後測試的查詢會使用 **TumblingWindow**，每隔五秒鐘的時間，取得每一個話題的被提到次數和情感數值平均值、最小值、最大值以及標準離差。

1. 在程式碼編輯器中，將查詢變更成：

     SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),   Max(SentimentScore), STDEV(SentimentScore)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic
2. 按一下查詢編輯器中的 [重新執行]  ，即可看到查詢結果。
3. 我們在儀表板中用的就是這個查詢。  按一下頁面底部的 [儲存]  。

## <a name="create-output-sink"></a>建立輸出接收
現在我們已經定義好一個事件資料流、一個負責擷取事件的事件中樞，以及一個進行資料流轉換處理的查詢，最後的步驟就是定義工作的輸出接收。  我們會編寫從工作查詢送往 Azure Blob 儲存體的推文事件彙總。  您也可以根據特定應用程式需求，將結果推送至 Azure SQL Database、Azure 表格儲存體或事件中樞。

請使用下列步驟來建立 Blob 儲存體的容器 (如果沒有的話)：

1. 使用現有的儲存體帳戶，或依序按一下 [新增] > [資料服務] > [儲存體]**STORAGE** > [快速建立] 建立新的儲存體帳戶，然後依照畫面上的指示操作。
2. 選取儲存體帳戶，按一下頁面上方的 [容器]，然後按一下 [新增]。
3. 指定容器的 [名稱]，並將 [存取] 設定為 [公用 Blob]。

## <a name="specify-job-output"></a>指定工作輸出
1. 在串流分析工作中，按一下頁面上方的 [輸出]，然後按一下 [新增輸出]。 開啟的對話方塊會逐步引導您完成設定輸出。
2. 按一下 [BLOB 儲存體]，然後按一下右鍵。
3. 在第三頁上輸入或選取下列值：

   * **輸出別名**：為這個工作輸出設定一個容易記的名稱。
   * **訂用帳戶**：如果建立的 Blob 儲存體與串流分析工作屬於相同的訂用帳戶，請按一下 [從目前的訂用帳戶使用儲存體帳戶]。 如果儲存體屬於不同的訂用帳戶，請按一下 [從另一個訂用帳戶使用儲存體帳戶]，然後手動輸入 [儲存體帳戶]、[儲存體帳戶金鑰]、[容器] 等資訊。
   * **儲存體帳戶名稱**：選取儲存體帳戶的名稱。
   * **容器**：選取容器的名稱。
   * **檔案名稱前置詞**：輸入當您填寫 Blob 輸出時，所使用的檔案前置詞。
4. 按一下向右按鈕。
5. 指定下列值：
   * **事件序列化程式格式**：JSON
   * **編碼**：UTF8
6. 按一下 [核取] 按鈕即可新增這個來源，並確認串流分析可以成功連接到儲存體帳戶。

## <a name="start-job"></a>開始工作
由於工作輸入、查詢及輸出都已經指定好了，現在可以開始串流分析工作。

1. 從 [儀表板] 工作，按一下頁面最下方的 [開始]。
2. 在開啟的對話方塊中，按一下 [工作開始時間]，然後按一下對話方塊最下方的 [核取] 按鈕。 工作狀態會變更為 [啟動中] 而且短時間內就會變成 [執行中]。

## <a name="view-output-for-sentiment-analysis"></a>檢視情感分析輸出
當您的工作在執行及處理即時 Twitter 串流之後，請選擇情感分析輸出的檢視方式。 使用 [Azure 儲存體總管][](https://azurestorageexplorer.codeplex.com/) 或 [Azure 總管][](http://www.cerebrata.com/products/azure-explorer/introduction) 此類的工具，即時檢視您的工作輸出。 從這裡，您可以使用 [Power BI][](https://powerbi.com/) 來擴充應用程式以包含在下列螢幕擷取畫面中類似的自訂儀表板。

![社交媒體分析：Power BI 儀表板中的串流分析情感分析 (意見挖掘) 輸出。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>取得支援
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


