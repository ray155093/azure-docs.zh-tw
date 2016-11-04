---
title: 使用 Machine Learning Management PowerShell Cmdlet 重新定型新的 Web 服務 | Microsoft Docs
description: 使用 Machine Learning Management PowerShell Cmdlet 了解如何在 Azure Machine Learning 中以程式設計方式重新定型模型，以及使用新定型的模型來更新 Web 服務。
services: machine-learning
documentationcenter: ''
author: vDonGlover
manager: raymondlaghaeian
editor: ''

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: v-donglo

---
# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>使用 Machine Learning Management PowerShell Cmdlet 重新定型新的 Web 服務
當您重新定型新的 Web 服務時，可以更新預測性 Web 服務定義以參考新的定型模型。  

## <a name="prerequisites"></a>必要條件
您必須已設定訓練實驗與預測性實驗，如以程式設計方式重新定型機器學習服務模型中所示。 如需建立訓練與預測性實驗的相關資訊，請參閱[以程式設計方式重新定型 Machine Learning 模型](machine-learning-retrain-models-programmatically.md)。

此程序要求您已安裝 Azure Machine Learning Cmdlet。 如需安裝 Machine Learning cmdlet 的資訊，請參閱 MSDN 上的 [Azure Machine Learning Cmdlet](https://msdn.microsoft.com/library/azure/mt767952.aspx) 參考。

已從重新定型輸出中複製下列資訊︰

* BaseLocation
* RelativeLocation

您採取的步驟如下︰

1. 登入您的 Azure Resource Manager 帳戶。
2. 取得 Web 服務定義
3. 將 Web 服務定義匯出為 JSON
4. 在 JSON 中將參考更新為 ilearner blob。
5. 將 JSON 匯入至 Web 服務定義
6. 使用新的 Web 服務定義更新 Web 服務

## <a name="sign-in-to-your-azure-resource-manager-account"></a>登入您的 Azure Resource Manager 帳戶
您必須先在 PowerShell 環境中，使用 [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) Cmdlet 登入您的 Azure 帳戶。

## <a name="get-the-web-service-definition"></a>取得 Web 服務定義
接下來，呼叫 [Get AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) Cmdlet 取得 Web 服務。 Web 服務定義是 Web 服務定型模型的內部表示法，且不可直接修改。 請確定您要擷取的是預測性實驗而非訓練實驗的 Web 服務定義。

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

若要判斷現有 Web 服務的資源群組名稱，執行不含任何參數的 Get-AzureRmMlWebService cmdlet 來顯示您訂用帳戶中的 Web 服務。 找出 Web 服務，再查看其 Web 服務識別碼。 資源群組的名稱是識別碼中的第四個元素，緊接在 resourceGroups  元素之後。 在下列範例中，資源群組名稱是 Default-MachineLearning-SouthCentralUS。

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

或者，若要判斷現有 Web 服務的資源群組名稱，登入 Microsoft Azure Machine Learning Web 服務入口網站。 選取 Web 服務。 資源群組名稱是 Web 服務 URL 的第五個元素，緊接在 resourceGroups  元素之後。 在下列範例中，資源群組名稱是 Default-MachineLearning-SouthCentralUS。

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>將 Web 服務定義匯出為 JSON
若要將定義修改為定型模型以使用新定型的模型，您必須先使用 [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) cmdlet 將其匯出為 JSON 格式檔案。

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json."></a>在 JSON 中將參考更新為 ilearner blob。
在資產中，找出 [定型模型]，使用 ilearner blob 的 URI 更新 locationInfo 節點中的 uri 值。 URI 的產生方式為結合來自 BES 重新定型呼叫輸出的 BaseLocation 和 RelativeLocation。

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition"></a>將 JSON 匯入至 Web 服務定義
您必須使用 [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) Cmdlet 將修改過的 JSON 檔案轉換回可用來更新預測性實驗的 Web 服務定義。

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>使用新的 Web 服務定義更新 Web 服務
最後，使用 [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) Cmdlet 來更新預測性實驗。

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>摘要
您可以使用 Machine Learning PowerShell Management Cmdlet 來更新預測性 Web 服務的定型模型，運用於如下案例︰

* 定期以新的資料重新定型模型。
* 散發模型給客戶，目的是要讓他們使用自己的資料重新定型模型。

<!--HONumber=Oct16_HO2-->


