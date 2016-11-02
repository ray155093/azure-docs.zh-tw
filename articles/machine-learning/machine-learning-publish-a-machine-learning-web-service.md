<properties
    pageTitle="部署 Machine Learning Web 服務 | Microsoft Azure"
    description="如何將訓練實驗轉換為預測實驗，將其準備妥當進行部署，然後當做 Azure Machine Learning Web 服務發佈。"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="deploy-an-azure-machine-learning-web-service"></a>部署 Azure Machine Learning Web 服務

Azure Machine Learning 可讓您建置、測試以及部署預測性分析解決方案。

從高階觀點而言，由下列三個步驟完成這個動作：

- **[建立訓練實驗]** - Azure Machine Learning Studio 是共同作業的視覺化開發環境，您使用所提供的訓練資料來訓練和測試預測分析模型。
- **[將其轉換為預測實驗]** - 一旦您的模型已使用現有資料訓練好，並準備好使用該模型為新資料評分之後，您就是在準備並簡化您的實驗進行預測。
- **將其部署為 Web 服務** - 您可以將預測實驗部署為[新式]或[傳統] Azure Web 服務。 使用者可以將資料傳送到您的模型以及接收您的模型的預測。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>建立訓練實驗

若要訓練預測分析模型，您使用 Azure Machine Learning Studio 以建立訓練實驗，在其中包含各種模組以載入訓練資料、視需要準備資料、套用機器學習演算法，以及評估結果。 您可以逐一查看實驗，並且嘗試不同的機器學習演算法以比較及評估結果。

關於建立和管理訓練實驗的處理，其他地方有更詳盡的說明。 如需詳細資訊，請參閱這些文章：

- [在 Azure Machine Learning Studio 中建立簡單實驗](machine-learning-create-experiment.md)
- [使用 Azure Machine Learning 開發預測解決方案](machine-learning-walkthrough-develop-predictive-solution.md)
- [將訓練資料匯入 Azure Machine Learning Studio](machine-learning-data-science-import-data.md)
- [在 Azure Machine Learning Studio 中管理實驗逐一查看](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>將訓練實驗轉換為預測實驗

完成模型訓練後，您就可以將訓練實驗轉換成預測實驗，給新資料評分。

轉換為預測實驗之後，您就可以將訓練過的模型部署為評分 Web 服務部署。 Web 服務的使用者可以將輸入資料傳送到您的模型，然後您的模型就會傳送回預測結果。 當您轉換為預測實驗時，必須記住您預期其他人會如何使用您的模型。

若要將您的訓練實驗轉換為預測實驗，請按一下實驗畫布底端的 [執行]，按一下 [設定 Web 服務]，然後選取 [預測 Web 服務]。

![轉換為評分實驗](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

如需如何執行此轉換的詳細資訊，請參閱[將機器學習服務訓練實驗轉換成預測實驗](machine-learning-convert-training-experiment-to-scoring-experiment.md)。

下列步驟說明將預測實驗部署為新式 Web 服務。 您也可以將實驗部署為傳統 Web 服務。

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>將預測實驗部署為新式 Web 服務

既然已經備妥預測實驗，您可以將它部署為 Azure Web 服務。 使用者可以使用 Web 服務將料傳送到您的模型，模型就會傳回其預測。

若要部署您的預測性實驗，請按一下實驗畫布底端的 [執行]  。 實驗完成執行之後，請按一下 [部署 Web 服務]，然後選取 [部署 Web 服務[新式]]。  Machine Learning Web 服務入口網站的 [部署] 頁面將會開啟。 

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Machine Learning Web 服務入口網站 [部署實驗] 頁面

在 [部署實驗] 頁面上，輸入 Web 服務的名稱。
選取定價方案。 如果您有現有的定價方案，可以進行選取，否則您必須為服務建立新的定價方案。 

1.  在 [價格方案] 下拉式清單中，選取現有的方案或選取 [選取新的方案] 選項。
2.  在 [方案名稱] 中，輸入將識別您帳單上方案的名稱。
3.  選取其中一個 [每月方案層] 。 方案層預設為您預設區域的方案，而您的 Web 服務會部署到該區域。

按一下 [部署]，Web 服務的 [快速入門] 頁面就會開啟。

Web 服務的 [快速入門] 頁面可讓您存取建立 Web 服務之後最常執行的工作，並提供指引。 從這裡您可以輕鬆地存取 [測試] 頁面和 [取用] 頁面。

<!-- ![Deploy the Web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>測試您的 Web 服務

若要測試新式 Web 服務，請在常見工作下按一下 [測試 Web 服務]。 在 [測試] 頁面上，您可以將 Web 服務當成要求-回應服務 (RRS) 或批次執行服務 (BES) 來測試。 

RRS 測試頁面會顯示輸入、輸出以及任何您已為實驗定義的全域參數。 若要測試 Web 服務，您可以手動輸入適當的值作為輸入，或提供包含測試值的逗號分隔值 (CSV) 格式檔案。 

若要使用 RRS 測試，請從清單檢視模式，針對輸入輸入適當的值，並按一下 [測試要求-回應] 。 您的預測結果會顯示在左邊的輸出資料行。

![部署 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

若要測試您的 BES，請按一下 [批次] 。 在 [批次] 測試頁面上，在您的輸入下按一下 [瀏覽] 並選取包含適當範例值的 CSV 檔案。 如果您沒有 CSV 檔案，而且是使用 Machine Learning Studio 建立預測實驗，您可以下載預測實驗的資料集來使用。

若要下載資料集，請開啟 Machine Learning Studio。 開啟您的預測實驗，並以滑鼠右鍵按一下實驗的輸入。 從操作功能表中，選取 [資料集]，然後選取 [下載]。 

![部署 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

按一下 [ **測試**]。 批次執行作業的狀態會顯示在右邊的 [測試批次作業] 之下。

![部署 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the Web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

在 [組態] 頁面上，可以變更 Web 服務的描述和標題、更新儲存體帳戶金鑰，以及啟用範例資料。

![設定 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

部署 Web 服務之後，您可以：

- 透過 Web 服務 API **存取**它。
- 透過 Azure Machine Learning Web 服務入口網站或 Azure 傳統入口網站**管理**它。 
- **更新** 它。

### <a name="access-the-web-service"></a>存取 Web 服務

從 Machine Learning Studio 部署您的 Web 服務之後，您可以用程式設計方式傳送資料給服務並接收回應。

[取用] 頁面提供您存取 Web 服務所需的所有資訊。 例如，API 金鑰可用來允許經過授權的存取服務。 

如需存取 Machine Learning Web 服務的詳細資訊，請參閱[如何使用已發佈的 Azure Machine Learning Web 服務](machine-learning-consume-Web-services.md)。

### <a name="manage-your-new-web-service"></a>管理新式 Web 服務

您可以在 Machine Learning Web 服務入口網站中管理傳統 Web 服務。 從[入口網站主頁面](https://services.azureml-test.net/)按一下 [Web 服務]。 您從 Web 服務頁面可以刪除或複製服務。 若要監視特定的服務，請按一下服務，然後按一下 [儀表板] 。 若要監視與 Web 服務相關聯的批次作業，請按一下 [批次要求記錄檔]。

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>將預測實驗部署為傳統 Web 服務

既然已經充分備妥預測實驗，您可以將它部署為 Azure Web 服務。 使用者可以使用 Web 服務將料傳送到您的模型，模型就會傳回其預測。

若要部署您的預測實驗，請按一下實驗畫布底端的 [執行]，然後按一下 [部署 Web 服務]。 系統會設定 Web 服務，且會將您帶往 Web 服務儀表板。

![部署 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

若要測試 Web 服務，請按一下 Web 服務儀表板中的 [測試] 連結。 對話方塊隨即顯示，要求您提供服務的輸入資料。 這些是評分實驗預期的資料行。 輸入一組資料，然後按一下 [確定] 。 Web 服務產生的結果會顯示在儀表板底部。

您可以按一下 [測試] 預覽連結，在 Azure Machine Learning Web 服務入口網站中測試您的服務，如先前的「新式 Web 服務」一節所示。

![測試 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

在 [組態] 索引標籤上，您可以變更服務的顯示名稱，並且給予描述。 名稱和描述會顯示在您管理 Web 服務的 [Azure 傳統入口網站](http://manage.windowsazure.com/)中。

您可以為輸入資料、輸出資料及 Web 服務參數提供描述，方法是在 [輸入結構描述]、[輸出結構描述] 及 [WEB 服務參數] 底下的每個資料行輸入字串。 這些描述會用於為 Web 服務提供的範例程式碼文件。

您可以啟用記錄以診斷當您存取 Web 服務時看到的任何錯誤。 如需詳細資訊，請參閱[為 Machine Learning Web 服務啟用記錄](machine-learning-web-services-logging.md)。

![設定 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

您也可以在 Machine Learning Web 服務入口網站中設定 Web 服務的端點，作法類似先前在「新式 Web 服務」一節所示的程序。 選項有所不同，您可以新增或變更服務描述、啟用記錄，以及啟用範例資料進行測試。

### <a name="access-the-web-service"></a>存取 Web 服務

從 Machine Learning Studio 部署您的 Web 服務之後，您可以用程式設計方式傳送資料給服務並接收回應。

儀表板提供您存取 Web 服務所需的所有資訊。 例如，提供 API 金鑰以允許服務的授權存取權，以及提供 API 說明頁面以協助您開始撰寫程式碼。

如需存取 Machine Learning Web 服務的詳細資訊，請參閱[如何使用已發佈的 Azure Machine Learning Web 服務](machine-learning-consume-Web-services.md)。

### <a name="manage-the-web-service"></a>管理 Web 服務

您可以執行各種動作來監視 Web 服務。 您可以更新它和刪除它。 除了部署傳統 Web 服務時所建立預設端點，您還可以新增其他端點。

如需相關資訊，請參閱[管理 Azure Machine Learning 工作區](machine-learning-manage-workspace.md)和[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](machine-learning-manage-new-webservice.md)。
  
<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning Web service endpoints using the REST API, see **Azure machine learning Web service endpoints**.
-->

## <a name="update-the-web-service"></a>更新 Web 服務

您可以對您的 Web 服務進行變更，例如使用其他訓練資料更新模型，也可以再次部署來覆寫原始 Web 服務。

若要更新 Web 服務，請開啟您用來部署 Web 服務的原始預測實驗，然後按一下 [另存新檔] 以製作可編輯的複本。 進行變更，然後按一下 [部署 Web 服務] 。 

由於您之前部署了這項實驗，所以會詢問您要覆寫 (傳統 Web 服務) 或更新 (新式 Web 服務) 現有的服務。 按一下 [是] 或 [更新] 會停止現有的 Web 服務，然後在其位置部署新的預測實驗。

> [AZURE.NOTE] 如果您在原始 Web 服務中進行組態變更，例如輸入新的顯示名稱或描述，則您必須再次輸入這些值。

更新 Web 服務的一個選擇是以程式設計方式重新訓練模型。 如需詳細資訊，請參閱 [以程式設計方式重塑機器學習模型](machine-learning-retrain-models-programmatically.md)。


<!-- internal links -->
[建立訓練實驗]: #create-a-training-experiment
[將其轉換為預測實驗]: #convert-the-training-experiment-to-a-predictive-experiment
[新的]: #deploy-the-predictive-experiment-as-a-new-Web-service
[傳統]: #deploy-the-predictive-experiment-as-a-new-Web-service
[存取]: #access-the-Web-service
[管理]: #manage-the-Web-service-in-the-azure-management-portal
[更新]: #update-the-Web-service



<!--HONumber=Oct16_HO2-->


