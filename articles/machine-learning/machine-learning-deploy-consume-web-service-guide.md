<properties
	pageTitle="Azure Machine Learning Web 服務：部署和取用 | Microsoft Azure"
	description="部署和使用 Web 服務的資源。"
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondl"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="v-donglo"/>

# Azure Machine Learning Web 服務：部署和取用

Azure Machine Learning (Azure ML) 可讓您部署機器學習服務的工作流程和模型做為 Web 服務。然後便可以透過網際網路利用這些 Web 服務從應用程式呼叫 ML 模型，進行即時預測或批次模式的預測。Web 服務為 RESTFul，可以從各種程式設計語言與平台 (如 .NET、Java) 和應用程式 (如 Excel) 呼叫它們。

在後續章節中，我們將討論步驟，並提供程式碼和文件的連結，協助您快速入門。

## 部署

### 使用 Azure ML Studio

Machine Learning Studio 和 Microsoft Azure Machine Learning Web 服務入口網站可讓您部署和管理 Web 服務，而不需要撰寫程式碼。

下列連結提供新 Web 服務的部署程序一般資訊︰

* 如需利用 Azure Resource Manager 部署新 Web 服務的概觀，請參閱[部署新的 Web 服務](machine-learning-webservice-deploy-a-web-service.md)。
* 如需部署 Web 服務的逐步解說，請參閱[部署 Azure Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

如需如何建立和部署 Web 服務的完整逐步解說，請參閱[逐步解說步驟 1︰建立 Machine Learning 工作區](machine-learning-walkthrough-1-create-ml-workspace.md)。

如需部署 Web 服務的特定範例，請參閱︰

* [逐步解說步驟 5：部署 Azure Machine Learning Web 服務](machine-learning-walkthrough-5-publish-web-service.md)
* [如何將 Web 服務部署到多個區域](machine-learning-how-to-deploy-to-multiple-regions.md)

### 使用 Web 服務資源提供者 API (Azure Resource Manager API)

用於 Web 服務的 Azure ML 資源提供者，可讓 REST API 呼叫用來部署和管理 Web 服務。如需更詳細的資訊，請參閱下列文章和範例程式碼。

* MSDN 上的 [Machine Learning Web 服務 (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) 參考資料。


### 使用 PowerShell Cmdlet

用於 Web 服務的 Azure ML 資源提供者，可讓 PowerShell Cmdlet 用來部署和管理 Web 服務。

若要使用 Cmdlet，您必須先在 PowerShell 環境中，使用 [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) Cmdlet 登入您的 Azure 帳戶。如果您不熟悉使用 PowerShell 命令呼叫 Resource Manager，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md#login-to-your-azure-account)。

若要匯出預測實驗，請使用這個[範例程式碼](https://github.com/ritwik20/AzureML-WebServices)。由程式碼建立 exe 之後，您可以輸入︰

	C:<folder>\GetWSD <experiment-url> <workspace-auth-token>

執行應用程式會建立 Web 服務的 JSON 範本。若要使用此範本部署 Web 服務，必須新增下列資訊︰

* 儲存體帳戶名稱和金鑰
	* 您可以從 Azure 入口網站或傳統入口網站取得儲存體帳戶的名稱和金鑰。
* 承諾用量方案識別碼
	* 您可以從 [Azure Machine Learning Web 服務](https://services.azureml.net)入口網站登入，按一下方案名稱取得方案識別碼。

將它們新增至 JSON 範本做為 Properties 節點的子系，與 MachineLearningWorkspace 節點位於相同層級。

請看以下範例：

	"StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
	},
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

如需更詳細的資訊，請參閱下列文章和範例程式碼。

* MSDN 上的 [Azure Machine Learning Cmdlet](https://msdn.microsoft.com/library/azure/mt767952.aspx) 參考資料。
* GitHub 上的範例[逐步解說](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt)。

## 取用 Web 服務

### 從 Azure ML Web 服務 UI (測試)

您可以從 Azure ML Web 服務入口網站測試您的 Web 服務。這包括測試 RRS 和 BES 介面。

* [部署新的 Web 服務](machine-learning-webservice-deploy-a-web-service.md)
* [部署 Azure Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)
* [逐步解說步驟 5：部署 Azure Machine Learning Web 服務](machine-learning-walkthrough-5-publish-web-service.md)

### 從 Excel

您可以下載 Excel 範本，可讓您輕鬆地取用 Web 服務︰

* [從 Excel 使用 Azure Machine Learning Web 服務](machine-learning-consuming-from-excel.md)
* [適用於 Azure Machine Learning Web 服務的 Excel 增益集](machine-learning-excel-add-in-for-web-services.md)


### 使用 REST 架構的用戶端

Azure ML Web 服務是 RESTful API。您可以從 .NET、Python、R、Java 等各種平台使用這些 API。在 [Microsoft Azure Machine Learning Web 服務入口網站](https://services.azureml.net)上，您的 Web 服務的取用頁面有提供範例程式碼，可協助您開始使用。

* [如何使用已從機器學習實驗部署的 Azure Machine Learning Web 服務](machine-learning-consume-web-services.md)

<!---HONumber=AcomDC_0824_2016-->