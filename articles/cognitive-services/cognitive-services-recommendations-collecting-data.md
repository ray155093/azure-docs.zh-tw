<properties
	pageTitle="收集資料以訓練您的模型：Machine Learning 建議 API | Microsoft Azure"
	description="Azure Machine Learning 建議 - 收集資料以訓練您的模型"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="luisca"/>

#  收集資料以訓練您的模型 #

建議 API 會從您過去的交易進行學習，以找出應該為特定使用者建立哪些項目。

建立模型之後，您必須先提供兩部分的資訊，才能進行任何訓練︰目錄檔案和使用狀況資料。

>   如果您尚未這樣做，我們建議您先完成[快速入門指南](cognitive-services-recommendations-quick-start.md)。


## 目錄資料 ##

### 目錄檔案格式 ###

目錄檔案包含您要提供給客戶之項目的相關資訊。目錄資料應該遵循下列格式：

- 沒有功能 - `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- 具有功能 - `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### 目錄檔案中的範例資料列

沒有功能：

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

具有功能：

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### 格式詳細資料

| 名稱 | 強制 | 類型 | 說明 |
|:---|:---|:---|:---|
| 項目識別碼 |是 | [A-z]、[a-z]、[0-9]、[\_] &#40;底線&#41;、[-] &#40;虛線&#41;<br> 最大長度：50 | 項目的唯一識別碼。 |
| 項目名稱 | 是 | 任何英數字元<br> 最大長度：255 | 項目名稱。 |
| 項目類別 | 是 | 任何英數字元 <br> 最大長度：255 | 此項目所屬類別 (例如烹飪書籍、劇本...) 可以是空的。 |
| 說明 | 否，除非顯示功能 (但可能是空的) | 任何英數字元 <br> 最大長度：4000 | 此項目的說明。 |
| 功能清單 | 否 | 任何英數字元 <br> 最大長度：4000；功能數目上限：20 | 以逗號分隔的「功能名稱=功能值」清單，可用來增強模型建議。|

#### 新增目錄檔案

請參閱可用來上傳目錄檔案的 [API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1)。

請注意，目錄檔案的內容應該以要求主體來傳遞。

如果您將數個目錄檔案上傳至具有多個呼叫的相同模型，我們只會插入新的目錄項目。現有的項目會保留原始值。您無法使用這個方法更新目錄資料。

>   注意：檔案大小上限為 200 MB。在支援的目錄中，項目數目上限是 100,000 個項目。


## 為什麼要將功能加入至目錄？

建議引擎會建立統計模型，告知您客戶可能喜歡或可能購買的項目。當您有未曾與其互動過的新產品時，就無法根據共同出現的次數單獨建立模型。假設您在商店中開始提供新的「孩童用的小提琴」，由於您先前未曾銷售過該小提琴，因此無法告知是否有任何其他項目可與該小提琴一起建議。

也就是說，如果引擎知道關於該小提琴的資訊 (例如，它是一種樂器、它適用於年齡 7-10 歲的孩童、這把小提琴的價格不高等)，則引擎可以從其他具有類似功能的產品中進行學習。例如，您過去曾銷售過小提琴，而購買小提琴的人通常都會傾向購買古典樂 CD 和樂譜架。儘管新小提琴的使用狀況很低，但系統可以在功能之間找到這些關聯，並根據功能提供建議。

匯入功能做為目錄資料的一部分，然後在排名組建完成時建立與其排名 (或模型中功能的重要性) 的關聯。功能排名可根據使用狀況資料和項目類型而變更。但是對於一致的使用量/項目，排名只能有小幅的起伏。功能的排名為非負數的數字。編號 0 表示未排名功能 (如果您在第一個排名組建完成之前叫用這個 API，就會發生這個情形)。配置排名的日期稱為分數有效時間。


###功能具有類別性質

這表示您應該建立類似於類別的功能。例如，price=9.34 不是類別功能。另一方面，priceRange=Under5Dollars 之類的功能則是類別功能。另一個常見錯誤是使用項目名稱做為功能。這會導致項目名稱具有唯一性，因此不會描述類別。請確定功能代表項目的類別。


###應該使用多少功能以及哪些功能？


Recommendations 組建最終會支援建置具有最多 20 個功能的模型。您可以對目錄中的項目指派超過 20 個功能，但您應該進行排名組建，並只挑選高排名的功能 (排名 2.0 以上的功能是真的可使用的好功能)。


###何時會實際使用功能？

當沒有足夠的交易資料可單獨提供有關交易資訊的建議時，模型就會使用功能。因此功能對「冷項目」具有最大影響 (冷項目是只具有少數交易的項目)。如果所有項目都有足夠的交易資訊，您可能就不需要使用功能來擴充模型。


###使用產品功能

若要使用功能做為組建的一部分，您需要：

1. 當您上傳目錄時，確定您的目錄具有功能。

2. 觸發排名組建。這將會針對功能的重要性/排名進行分析。

3. 觸發建議組建，設定下列組建參數︰將 useFeaturesInModel 設為 true、將 allowColdItemPlacement 設為 true，並且應將 modelingFeatureList 設為以逗號分隔的功能清單，而您想要使用此清單來增強您的模組。如需詳細資訊，請參閱[建議組建類型參數](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0)。





## 使用狀況資料 ##
使用狀況檔案包含這些項目的使用方式，或者您商務交易的相關資訊。

#### 使用狀況格式詳細資料
使用狀況檔案是一個 CSV (逗號分隔值) 檔案，使用狀況檔案中的每一列都代表使用者和項目之間的互動。每一列的格式如下：<br>`<User Id>,<Item Id>,<Time>,[<Event>]`



| 名稱 | 強制 | 類型 | 說明
|-------|------------|------|---------------
|使用者識別碼| 是|[A-z]、[a-z]、[0-9]、[\_] &#40;底線&#41;、[-] &#40;虛線&#41;<br> 最大長度：255 |使用者的唯一識別碼。
|項目識別碼|是|[A-z]、[a-z]、[0-9]、[&#95;] &#40;底線&#41;、[-] &#40;虛線&#41;<br> 最大長度：50|項目的唯一識別碼。
|時間|是|日期格式：YYYY/MM/DDTHH:MM:SS (例如 2013/06/20T10:00:00)|資料的時間。
|Event|否 | 下列其中之一︰<br>• 按一下<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• 購買| 交易的類型。 |

#### 使用狀況檔案中的範例資料列

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### 上傳使用狀況檔案

請參閱可用來上傳使用狀況檔案的 [API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2)。請注意，您必須將使用狀況檔案的內容做為 HTTP 呼叫的主體來傳遞。

>  注意：

>  檔案大小上限：200 MB。您可能會上傳數個使用狀況檔案。

>  您需要先上傳目錄檔案，然後才能開始將使用狀況資料加入至模型。在訓練階段期間，只會使用目錄檔案中的項目。所有其他項目都會被忽略。

## 您需要多少資料？

模型品質非常依賴資料的品質和數量。系統會在使用者購買不同項目時進行學習 (我們稱之為共同出現次數)。針對 FBT 組建，也請務必了解在相同交易中購買了哪些項目。

好的經驗法則是讓大部分的項目位於 20 個以上的交易中，因此，如果您的類別目錄中有 10,000 個項目，我們建議您擁有的至少是該交易數目的 20 倍或大約 200,000 個交易。再次強調，此為經驗法則。您必須使用您的資料來試驗。

一旦建立模型之後，就可以執行[離線評估](cognitive-services-recommendations-buildtypes.md)來檢查您的模型可能執行的程度。

<!---HONumber=AcomDC_0914_2016-->