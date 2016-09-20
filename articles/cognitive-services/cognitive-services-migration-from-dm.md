
<properties
	pageTitle="從 DataMarket Recommendations API 移轉至 Azure Cognitive Services Recommendations API | Microsoft Azure"
	description="Azure 機器學習服務建議--移轉至 Recommendations Cognitive Services"
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
	ms.date="09/01/2016"
	ms.author="luisca"/>


# 從 DataMarket Recommendations API 移轉至 Azure Cognitive Services Recommendations API
本文說明如何從 [Microsoft DataMarket Recommendations API](https://datamarket.azure.com/dataset/amla/recommendations) 移轉至 [Microsoft Azure Cognitive Services Recommendations API](https://www.microsoft.com/cognitive-services/zh-TW/recommendations-api)。

DataMarket Recommendations API 會在 2016 年 12 月 31 日停止接受新客戶，並在 2017 年 2 月 28 日被取代。

## 如何開始使用 Azure Cognitive Services Recommendations API？

若要移轉至 Cognitive Services Recommendations API，請執行下列作業︰

1.	如果您還沒有 Azure 訂用帳戶，請[註冊](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)一個。

1.	從[快速入門指南](cognitive-services-recommendations-quick-start.md)取得逐步指示，以註冊 Cognitive Services Recommendations API，並以程式設計方式使用它。

1.	移至 [Cognitive Services Recommendations API 登陸頁面](https://www.microsoft.com/cognitive-services/zh-TW/recommendations-api)以了解服務和尋找文件。

## 我之前使用 Recommendations UI 來建置模型。Cognitive Services Recommendations API 是否有類似工具？

當然！ 新的 [Recommendations UI](http://recommendations-portal.azurewebsites.net/) 的 URL 是 http://recommendations-portal.azurewebsites.net。

>[AZURE.NOTE] DataMarket 認證在此處無效。請在 Azure 入口網站註冊訂用帳戶，並取得使用新的 [Recommendations UI](http://recommendations-portal.azurewebsites.net/) 所需的帳戶金鑰。如果您沒有帳戶金鑰，請參閱[快速入門指南](cognitive-services-recommendations-quick-start.md)的工作 1。

##新的 API 格式是否和 DataMarket Recommendations API 一樣？

此 API 可支援和 DataMarket 版本所支援之案例相同的案例和程序流程，但實際的 API 介面已更新為符合 [Microsoft REST API 指導方針](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)。API 變得更為一致，而且能與支援 Swagger 的工具配合得更好。

若要了解每一個 API，請參閱 [API 總管](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db)。使用 [試試看] 按鈕來測試 API 呼叫。Recommendations API 所取用的檔案格式 (目錄和使用方式檔案) 尚未變更，因此您可以繼續使用之前建置來產生這些檔案的相同檔案和 (或) 基礎結構。

##Cognitive Services Recommendations API 有哪些新功能？

過去兩個月內，我們已針對 Cognitive Services Recommendations API 發行了新功能：
-	用於定型和測試模型的 Recommendations UI，而不必撰寫一行程式碼
-	為您一次提供數千個建議的批次評分
-	建置度量支援以查詢建議模型的品質
-	支援商務規則
-	列舉和下載使用方式與目錄檔案的能力
-	排名建置支援以查詢建議模型中的項目特徵品質
-	新增了搜尋目錄中產品的能力

## Microsoft 何時會停止支援 DataMarket Recommendations API？

[DataMarket 的 Recommendations API](https://datamarket.azure.com/dataset/amla/recommendations) 會在 2016 年 12 月 31 日之後停止接受新的客戶，並於 2017 年 2 月 28 日之前完全被取代。

## 如果手上沒有在 Cognitive Services Recommendations API 中重新建立模型所需的資料會如何？

我們希望能為您盡量簡化此轉換作業。我們可以協助您將 DataMarket 帳戶中的舊模型移至新的 Azure Cognitive Services Recommendations API 訂用帳戶。請透過 [mlapi@microsoft.com](mailto://mlapi@microsoft.com) 與我們連絡。我們會在關聯模型與新帳戶之前，要求您提供 DataMarket 訂用帳戶識別碼和 Cognitive Services 訂用帳戶識別碼。

<!----HONumber=AcomDC_0907_2016-->