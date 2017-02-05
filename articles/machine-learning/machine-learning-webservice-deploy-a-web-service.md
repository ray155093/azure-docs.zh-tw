---
title: "部署新的 Web 服務"
description: "部署以 ARM 為基礎 Web 服務的的工作流程"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: a358b04f-0d08-4d50-820e-eeac971854cf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 201b07536bcee58e2b7102379dff1c1c93c4b675
ms.openlocfilehash: 70db87762757c42790047362ded24263157e6ae7


---
# <a name="deploy-a-new-web-service"></a>部署新的 Web 服務
Microsoft Azure Machine Learning 現在提供的 Web 服務是以 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 為基礎，允許新的計費方案選項以及將您的 Web 服務部署到多個區域。

使用 Microsoft Azure Machine Learning Web Services 部署 Web 服務的一般工作流程為︰

* 建立預測性實驗
* 進行部署
* 設定其名稱
* 計費方案
* 進行測試
* 加以取用。

下圖說明此工作流程。

![Web 服務部署工作流程][1]

## <a name="deploy-web-service-from-studio"></a>從 Studio 部署 Web 服務
將實驗部署為新的 Web 服務。 登入 Machine Learning Studio，並建立新的預測性 Web 服務。 

**注意**︰如果您已將實驗部署為傳統 Web 服務，您就無法將它部署為新的 Web 服務。

按一下實驗畫布底端的 [執行]，然後按一下 [部署 Web 服務] 和 [部署 Web 服務 [新式]]。 機器學習 Web 服務管理員的部署頁面將會開啟。

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>機器學習 Web 服務管理員部署實驗頁面
在 [部署實驗] 頁面上，輸入 Web 服務的名稱。
選取定價方案。 如果您有現有的定價方案，可以進行選取，否則您必須為服務建立新的定價方案。 

1. 在 [價格方案] 下拉式清單中，選取現有的方案或選取 [選取新的方案] 選項。
2. 在 [方案名稱] 中，輸入將識別您帳單上方案的名稱。
3. 選取其中一個 [每月方案層] 。 請注意，方案層預設為您預設區域的方案，您的 Web 服務會部署到該區域。

按一下 [部署]  ，Web 服務的 [快速入門] 頁面就會開啟。

## <a name="quickstart-page"></a>快速入門頁面
Web 服務的 [快速入門] 頁面提供您存取建立新的 Web 服務之後最常執行的工作及其指引。 從這裡您可以輕鬆地存取 [測試] 頁面和 [取用] 頁面。

## <a name="testing-your-web-service"></a>測試您的 Web 服務
從 [快速入門] 頁面中，按一下常見工作下的 [測試 Web 服務]。   

以要求-回應服務 (RRS) 測試 Web 服務：

* 按一下功能表列上的 [測試]  。
* 按一下 [要求-回應] 。
* 為實驗的輸入資料行輸入適當的值。
* 按一下 [測試要求-回應] 。

您的結果將會顯示在頁面的右手邊。

若要測試批次執行服務 (BES) Web 服務，您要使用 CSV 檔案︰

* 按一下功能表列上的 [測試]  。
* 按一下 [批次] 。
* 在您的輸入下，按一下 [瀏覽] 並導覽至您的範例資料檔案。
* 按一下 [ **測試**]。

測試的狀態會顯示在 [測試批次作業] 下。

## <a name="consuming-your-web-service"></a>取用您的 Web 服務
部署為 Web 服務時，Azure Machine Learning 實驗所提供的 REST API，可供各種裝置和平台使用。 這是因為簡單的 REST API 可接受並回應 JSON 格式化的訊息。 Azure Machine Learning 入口網站提供的程式碼可用來呼叫 R、C# 和 Python 的 Web 服務。

您可以在 [取用中] 頁面上找到︰

* 應用程式中取用中 Web 服務的 API 金鑰和 URI。
* Excel 和 Web 應用程式範本，以開始啟動您的耗用程序。
* 以 C#、Python 和 R 撰寫，讓您開始的範例程式碼。

如需如何取用 Web 服務的詳細資訊，請參閱 [如何取用已從機器學習實驗部署的 Azure Machine Learning Web 服務](machine-learning-consume-web-services.md)。

## <a name="next-steps"></a>後續步驟
如需有關如何取用 Web 服務的詳細資訊，請參閱︰

[如何使用已從機器學習實驗部署的 Azure Machine Learning Web 服務](machine-learning-consume-web-services.md)

[Azure Machine Learning Web 服務：部署和取用](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->



<!--HONumber=Jan17_HO2-->


