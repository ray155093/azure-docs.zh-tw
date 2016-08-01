<properties
	pageTitle="連線到 Azure Machine Learning Web 服務 | Microsoft Azure"
	description="透過 C# 或 Python，使用授權金鑰連線到 Azure Machine Learning Web 服務。"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016" 
	ms.author="garye" />


# 連線到 Azure Machine Learning Web 服務
Azure Machine Learning 開發人員體驗是一個 Web 服務 API，可即時或以批次模式從輸入資料進行預測。您可以使用 Azure Machine Learning Studio 來建立預測及部署 Azure 機器學習 Web 服務。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

若要了解如何使用 Machine Learning Studio 建立和部署機器學習 Web 服務，請參閱：

- 如需如何在 Machine Learning Studio 中建立實驗的教學課程，請參閱[建立您的第一個實驗](machine-learning-create-experiment.md)。
- 如需如何部署 Web 服務的詳細資訊，請參閱[部署 Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)。
- 如需 Machine Learning 的一般詳細資訊，請參閱 [Machine Learning 文件中心](https://azure.microsoft.com/documentation/services/machine-learning/)。

## Azure Machine Learning Web 服務 ##

使用 Azure Machine Learning Web 服務，外部應用程式會即時與機器學習服務工作流程計分模型通訊。機器學習 Web 服務呼叫會將預測結果傳回外部應用程式。若要進行機器學習 Web 服務呼叫，您可以傳遞部署預測時所建立的 API 金鑰。機器學習服務 Web 服務以 REST 為基礎，這是一種常見的 Web 程式設計專案架構。

Azure Machine Learning 有兩種類型的服務：

- 要求-回應服務 (RRS) – 這是一種低延遲、調整性高的服務，針對從 Machine Learning Studio 建立和部署的無狀態模型提供介面。
- 批次執行服務 (BES) – 這是一種非同步的服務，為一批資料記錄進行計分。

如需 Machine Learning Web 服務的詳細資訊，請參閱[部署 Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

## 取得 Azure Machine Learning 授權金鑰 ##

當您部署實驗時，會為 Web 服務產生 API 金鑰。您取得金鑰的位置取決於實驗是部署為新的 Web 服務或傳統的 Web 服務而定。

## 傳統 Web 服務 ##

 您可以從 Machine Learning Studio 或 Azure 入口網站取得金鑰。

### Machine Learning Studio ###

1. 在 Machine Learning Studio 中，按一下左側的 [Web 服務]。
2. 按一下某個 Web 服務。[API 金鑰] 位於 [儀表板] 索引標籤上。

### Azure 入口網站 ###

1. 按一下左側的 [機器學習]。
2. 按一下工作區。
3. 按一下 [Web 服務]。
4. 按一下某個 Web 服務。
5. 按一下某個端點。[API 金鑰] 位於右下角。


## 新的 Web 服務 

若要取得 API 金鑰，如果是新的機器學習 Web 服務，您必須登入 [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) 入口網站。

1. 在 Azure Machine Learning Web Services 入口網站中，按一下頂端功能表上的 [Web 服務]。
2. 按一下您要取得金鑰的 Web 服務。
3. 在頂端功能表上，按一下 [取用]。
4. 複製並儲存 [主要金鑰]。

## <a id="connect"></a>連線到機器學習 Web 服務

您可以使用任何支援 HTTP 要求和回應的程式設計語言，連線到機器學習 Web 服務。您可以從機器學習 Web 服務說明頁面檢視 C#、Python 和 R 的範例。

**機器學習服務 API 說明** 當您部署 Web 服務時，會建立機器學習服務 API 說明。請參閱 [Azure 機器學習逐步解說 - 部署 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)。機器學習服務 API 說明包含有關預測 Web 服務的詳細資訊。

**檢視傳統 Web 服務的機器學習服務 API 說明** 在 Machine Learning Studio 中：

1. 按一下 [Web 服務]。
2. 按一下某個 Web 服務。
3. 按一下 [API 說明頁面] - [要求/回應] 或 [批次執行]

**檢視新 Web 服務的機器學習服務 API 說明** 在 Azure Machine Learning Web Services 入口網站中︰

1. 按一下頂端功能表上的 [Web 服務]。
2. 按一下您要取得金鑰的 Web 服務。

按一下 [取用] 取得要求-回應服務和批次執行服務的 URI，以及以 C#、R 和 Python 撰寫的範例程式碼。

按一下 [Swagger API] 從提供的 URI，取得所呼叫 API 的 Swagger 相關文件。

### C# 範例 ###

若要連線到機器學習 Web 服務，請使用 **HttpClient** 傳遞 ScoreData。ScoreData 包含 FeatureVector，這是代表 ScoreData 的數值特徵 N 維向量。您要使用 API 金鑰向機器學習服務驗證。

若要連線到機器學習 Web 服務，必須安裝 **Microsoft.AspNet.WebApi.Client** Nuget 封裝。

**在 Visual Studio 中安裝 Microsoft.AspNet.WebApi.Client Nuget**

1. 發佈 Download dataset from UCI: Adult 2 class dataset 的 Web 服務。
2. 按一下 [**工具**] > [**NuGet 封裝管理員**] > [**封裝管理員主控台**]。
2. 選擇 [**Install-package Microsoft.AspNet.WebApi.Client**]。

**執行程式碼範例**

1. 發佈機器學習服務範例集合中的 "Sample 1: Download dataset from UCI: Adult 2 class dataset" 實驗。
2. 使用來自 Web 服務的金鑰指派 apikey。請參閱前述的**取得 Azure Machine Learning 授權金鑰**。
3. 使用要求 URI 指派 serviceUri。


### Python 範例 ###

若要連線到機器學習 Web 服務，請使用 **urllib2** 程式庫傳遞 ScoreData。ScoreData 包含 FeatureVector，這是代表 ScoreData 的數值特徵 N 維向量。您要使用 API 金鑰向機器學習服務驗證。


**執行程式碼範例**

1. 部署機器學習服務範例集合中的 "Sample 1: Download dataset from UCI: Adult 2 class dataset" 實驗。
2. 使用來自 Web 服務的金鑰指派 apikey。請參閱前述的**取得 Azure Machine Learning 授權金鑰**。
3. 使用要求 URI 指派 serviceUri。

<!---HONumber=AcomDC_0720_2016-->