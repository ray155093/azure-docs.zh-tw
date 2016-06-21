<properties
	pageTitle="快速入門指南：Machine Learning 建議 API | Microsoft Azure"
	description="Azure Machine Learning Recommendations - 快速入門手冊"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="luisca"/>

#  組建類型與模型品質 #

<a name="TypeofBuilds"></a>
## 應該使用何種建議組建類型？ ##

我們目前支援兩種組建類型︰[建議] 和 [FBT] 組建。每個使用不同的演算法所建立，並具有不同的優點。本文件說明這其中每一個組建，以及用來比較所產生之模型品質的技術。


> 如果您尚未這樣做，我們建議您先完成[快速入門指南](cognitive-services-recommendations-quick-start.md)。

<a name="RecommendationBuild"></a>
### 建議組建類型 ###

「建議」組建類型使用矩陣分解來提供建議。簡短版本是它會使用使用者的交易來產生[潛伏功能](https://en.wikipedia.org/wiki/Latent_variable)向量以描述每個項目，然後使用這些潛伏的向量來比較類似的項目。

假設您根據在您的電子產品商店中所做的採購來定型模型，而在評分時您會提供 Lumia 650 手機做為模型的輸入，則會傳回可能購買 Lumia 650 手機的人通常會購買的一組項目。請注意，項目可能不互補。比方說，在此範例中，可能會傳回其他手機，因為喜歡 Lumia 650 的人可能會喜歡其他手機。

建議組建有兩項功能可讓它更具吸引力︰

-	其支援冷項目放置。

 「冷項目」這個術語用於沒有高使用量的項目。比方說，假設您剛收到超優質的新款 Lumia 手機出貨。因為您在過去絕對不曾銷售該手機，所以系統無法單獨推斷此產品的交易建議。這表示系統應該了解產品本身的相關資訊。

 如果您想要使用冷項目放置，必須提供目錄中每個項目的功能資訊，您的目錄前幾行可能如下所示 (請注意，功能的索引鍵=值格式)︰

> 6CX-00001,Surface Pro2, Surface, Type=Hardware, Storage=128GB, Memory=4G, Manufacturer=Microsoft

> 73H-00013,Wake Xbox 360,Gaming, Type=Software, Language=English, Rating=Mature

> WAH-0F05,Minecraft Xbox 360,Gaming, * Type=Software, Language=Spanish, Rating=Youth

> ...

 此外，在組建參數中，您需要設定下列組建參數︰

| 組建參數 | 注意事項
|------------------     |-----------
|useFeaturesInModel | 設定為 True。指出是否可以使用功能以加強建議模型。 
|allowColdItemPlacement | 設定為 True。指出建議是否也應該透過功能相似度推入冷項目。
| modelingFeatureList | 使用於建議組建中的功能名稱逗號分隔清單，可加強建議。例如，上述範例中的 “Language,Storage”。

-	其支援使用者建議。

 建議組建支援[使用者建議](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)。這表示可以使用使用者的交易歷程記錄，為該使用者提供個人化建議。對於使用者建議，您可以提供該使用者的使用者識別碼和/或最近的交易歷程記錄。

 建議您套用使用者建議的典型範例之一，就是當使用者在歡迎使用頁面上第一次登入您的存放區/網站時。您可以在那裡推銷適用於特定使用者的內容。
 
 您可能也想要在使用者即將簽出時套用建議組建類型。這時候，您會有客戶即將購買的項目清單，而這就是您根據目前的購物籃提供建議的機會。

<a name="FBTBuild"></a>
### FBT 組建類型 ###

FBT 代表經常一起購買。FBT 組建會進行分析，以計算兩項或三項不同產品一起共同出現的次數，然後根據相似度函數 (Co-occurrences、Jaccard、Lift) 將集合排序。

將 Jaccard 和 Lift 視為標準化 Co-occurrences 的方法。這表示只有在項目確實與種子項目一起購買時才會傳回這些項目。

在 Lumia 650 手機範例中，只有在與 Lumia 650 手機相同的工作階段中購買手機 X 時，才會傳回手機 X。因為這不太可能發生，所以我們會預期傳回 Lumia 650 的補充項目；例如，螢幕保護裝置或 Lumia 650 的電源配接器。

目前，如果有兩個具有相同使用者識別碼和時間戳記的項目出現在一筆交易中，則假設會在相同的工作階段中購買這兩個項目。

FBT 組建目前不支援冷項目，因為其依照定義預期會在同一筆交易中實際購買兩個項目。FBT 組建可傳回項目集 (三個一組)，但因為它們接受以單一種子項目做為輸入，所以不支援個人化建議。

<a name="SelectBuild"></a>
## 如何選取要使用的確切組建？ ##

上述指引可能就足以讓您判斷是否應該使用建議組建或 FBT 組建，但是在您可以使用其中任一組建的情況下，這並不是明確的答案。此外，即使您知道要使用 FBT 組建類型，但仍可能想要決定使用 Jaccard 或 Lift 做為相似度函式。

在這兩種組建之間進行選取的最佳方式就是在真實世界中實際進行測試 (線上評估版)，並追蹤不同組建的轉換率。根據建議點選次數、來自顯示建議的實際購買數目，或甚至是顯示不同建議時的實際銷售金額，可以測量轉換率。您可以根據您的商務目標來選取轉換率計量。

在某些情況下，建議您在模型投入生產之前，先離線評估模型。雖然離線評估無法取代線上評估，但可以做為計量以供我們參考。

<a name="OfflineEvaluation"></a>
## 離線評估  ##

離線評估的目的是要預測精確度 (實際會購買其中一個建議項目的使用者數目) 和建議的多樣性 (建議的實際項目數)。在精確度和多樣性計量評估中，系統發現使用者樣本，而這些使用者的交易會分成兩個群組︰訓練資料集和測試資料集。

> 注意：
>
> 若要使用離線計量，您必須有使用量資料的時間戳記。(因為需要時間資料，才能正確分割訓練和測試資料集之間的使用量)。

> 此外，離線評估可能不會產生小型使用量檔案的結果，只是因為為了徹底評估，我們預期測試資料集中至少有 1000 個使用點。

<a name="Precision"></a>
### Precision-at-K ###
下表代表 precision-at-k 離線評估的輸出。

| K | 1 | 2 | 3 | 	4 | 	5
|---|---|---|---|---|---|
|百分比 |	13\.75 |	18\.04 | 21 |	24\.31 |	26\.61
|測試的使用者 |	10,000 |	10,000 |	10,000 |	10,000 |	10,000
|考量的使用者 |	10,000 |	10,000 |	10,000 |	10,000 |	10,000
|不考量的使用者 |	0 |	0 |	0 |	0 |	0

#### K
在上表中，K 代表要向客戶顯示的建議數目。所以資料表顯示的意思如下：「在測試期間，只會對客戶顯示一個建議，只有 13.75 的使用者會實際購買該項建議」。(假設模型是利用採購資料進行訓練)。另一種說法是「K=1 時的精確度」是 13.75。

您發現，對客戶顯示的項目愈多，客戶購買建議項目的可能性愈大。在上述實驗中，建議 5 個項目時的機率幾乎倍增至 26.61%。

#### 百分比
與至少一個顯示的 K 建議互動的使用者百分比。此百分比的計算方式：將與至少一個建議互動的使用者人目除以考量的使用者總數。(請參閱考量的使用者定義)。

#### 測試的使用者
測試資料集中的使用者總數。

#### 考量的使用者
只有在系統根據使用訓練資料集所產生的模型而建議至少 K 個項目時，使用者才會納入考量。

#### 不考量的使用者
任何不考量的使用者；未收到至少 K 個建議項目的使用者。

不考量的使用者 = 測試的使用者 – 考量的使用者

<a name="Diversity"></a>
### 多樣性 ###
多樣性計量會測量建議的項目類型。下表代表多樣性離線評估的輸出。

|百分位數值區 |	0-90| 90-99| 99-100
|------------------|--------|-------|---------
|百分比 | 34\.258 | 55\.127| 10\.615


建議的項目總數：100,000

建議的唯一項目數：954

#### 百分位數值區
每個百分位數值區都是以一個範圍 (介於 0 與 100 之間的最小/最大值) 表示。接近 100 的項目是最受歡迎的項目，而接近 0 的項目則最不受歡迎。比方說，如果 99-100 百分位數值區的百分比值為 10.6，這表示 10.6% 的建議只傳回前 1% 最受歡迎的項目。百分位數值區最小值會包含在內，而最大值則是排除在外 (但 100 除外）。
#### 建議的唯一項目數
傳回進行評估的不同項目數目。
#### 建議的項目總數：
建議的項目總數。(有些可能是重複項目)


<a name="ImplementingEvaluation"></a>
### 如何取得離線評估？ ###
精確度和多樣性離線計量可能有助於您選取要使用的組建。若要取得離線計量，您必須執行下列作業：

在建置階段，於個別的 FBT 或建議組建參數中︰
1.	將 enableModelingInsights 組建參數設為 true。

2.	您可以選擇性地選取 splitterStrategy (RandomSplitter 或 LastEventSplitter)。RandomSplitter 會根據指定的 randomSplitterParameters 測試百分比和隨機種子值，分割訓練和測試集中的使用量資料。LastEventSplitter 會根據每個使用者的最後一筆交易，分割訓練和測試集中的使用量資料。

這會觸發一個組建，該組建僅使用一部分的資料進行訓練，而其餘的資料則用來計算評估計量。在組建完成之後，若要取得評估的輸出，您只需呼叫[取得組建計量 API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/573e43bb3e9d4627a8c4bd3e/console)，並傳遞個別的 modelId 和 buildId。

 以下是我們執行的範例評估的 JSON 輸出︰


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }

<!---HONumber=AcomDC_0608_2016-->