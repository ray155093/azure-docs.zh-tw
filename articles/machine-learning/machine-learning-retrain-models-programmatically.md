<properties
	pageTitle="以程式設計方式重新定型機器學習服務模型 | Microsoft Azure"
	description="了解如何在 Azure Machine Learning 中以程式設計方式重新定型模型，以及使用新定型的模型來更新 Web 服務。"
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2016"
	ms.author="raymondl;garye;v-donglo"/>


#以程式設計方式重新定型機器學習服務模型  

在 Azure Machine Learning 中進行機器學習服務模型的實作程序時，需要定型並儲存您的模型。接著，使用它來建立預測性 Web 服務。接著才能在網站、儀表板及行動應用程式取用 Web 服務。

您使用 Machine Learning 建立的模型通常不是靜態。因為當有新資料或 API 取用者有自己的資料時，模型就必須重新定型。或者，您可能需要套用篩選條件來取得資料子集，並重新定型定型模型。

重新定型可能經常會發生。有了程式設計重新定型 API 功能後，您能夠使用重新定型 API 以程式設計方式重新定型模型，並使用新定型的模型來更新 Web 服務。

本文說明重新定型程序，並示範如何使用重新定型 API。

## 為何要重新定型：定義問題  

進行 Machine Learning 定型程序時，會使用一組資料來將模型定型。您使用 Machine Learning 建立的模型通常不是靜態。因為當有新資料或 API 取用者有自己的資料時，模型就必須重新定型。其他案例可能會需要套用篩選條件來取得資料子集，並重新定型定型模型。

在這些情況下，程式設計 API 可方便您或 API 取用者建立能夠單次或定期使用自己的資料重新定型模型的用戶端。接著可以評估重新定型的結果，然後更新 Web 服務 API 以使用新定型的模型。

##如何重新定型：端對端程序  

首先，此程序包含下列部分：訓練實驗以及以 Web 服務形式發佈的預測性實驗。若要啟用已定型模型的重新定型功能，必須利用定型模型的輸出將訓練實驗發佈為 Web 服務。這樣做可讓 API 存取模型進行重新定型。

傳統 Web 服務設定重新定型的程序包含下列步驟：

![重新定型程序概觀][1]

圖 1︰傳統 Web 服務重新定型程序的概觀

新式 Web 服務設定重新定型的程序包含下列步驟：

![重新定型程序概觀][7]

圖 2︰新式 Web 服務重新定型程序的概觀

## 建立訓練實驗
 
針對這個範例，您將使用Microsoft Azure Machine Learning 範例當中的「範例 5：定型、測試、評估二進位分類：成人資料集」。
	
建立實驗：

1.	登入 Microsoft Azure Machine Learning Studio。
2.	按一下儀表板右下角的 [新增]。
3.	從 Microsoft 範例中，選取 [範例 5。
4.	若要重新命名此實驗，在頂端的實驗畫布上，選取實驗名稱 [範例 5：定型、測試、評估二進位分類：成人資料集]。
5.	輸入「普查模型」。
6.	在實驗畫布底端，按一下 [執行]。
7.	按一下 [設定 Web 服務]，然後選取 [重新定型 Web 服務]。

 	![初始實驗。][2]

圖 2︰初始實驗。

## 建立評分實驗並發佈為 Web 服務  

接下來，您要建立預測性實驗。

1.	在實驗畫布底端，按一下 [設定 Web 服務]，然後選取 [預測性 Web 服務]。這樣會將模型儲存為定型模型，並新增 Web 服務輸入與輸出模型。
2.	按一下 **[執行]**。
3.	實驗完成執行之後，請按一下 [部署 Web 服務 [傳統]] 或 [部署 Web 服務[新式]]。

## 將訓練實驗部署為定型 Web 服務

若要重新定型已定型的模型，您必須將您建立的訓練實驗部署為重新定型 Web 服務。這個 Web 服務需要將「Web 服務輸出」模組連接到「定型模型」*[][train-model]*模組，才能產生新的定型模組。

1. 若要回到訓練實驗，按一下左窗格中的 [實驗] 圖示，然後按一下名為 [普查模型] 的實驗。
2. 在 [搜尋實驗項目] 方塊中，輸入「Web 服務」。
3. 將 [Web 服務輸入] 模組拖曳到實驗畫布，然後將其連接到 [清除遺漏資料] 模組。
4. 將兩個 [Web 服務輸出] 模組拖曳到實驗畫布。將 [定型模組] 模組的輸出連接到其中一個，將 [評估模型] 模組的輸出連接到另一個。定型模型的 Web 服務輸出將會提供新的已定型模型。連接到評估模型的輸出將傳回該模組的評估模型輸出。
5. 按一下 **[執行]**。

接下來您必須將訓練實驗部署為可產生訓練模型與模型評估結果的 Web 服務。若要這麼做，您的下一組動作取決於您使用傳統 Web 服務或新式 Web 服務。
  
**傳統 Web 服務**

在實驗畫布底端，按一下 [設定 Web 服務]，然後選取 [部署 Web 服務[傳統]]。顯示的 Web 服務 [儀表板] 中也包含批次執行的 API 金鑰與 API 說明頁面。只有批次執行方法能夠用來建立定型模型。

**新式 Web 服務**

在實驗畫布底端，按一下 [設定 Web 服務]，然後選取 [部署 Web 服務[新式]]。Web 服務的 Azure Machine Learning Web Services 入口網站會開啟 [部署 Web 服務] 頁面。輸入您的 Web 服務名稱，選擇付款方案，然後按一下 [部署]。只有批次執行方法能夠用來建立定型模型

不論傳統或新式，在實驗完成執行後，產生的工作流程應該看起來像這樣：

![執行後產生的工作流程。][4]

圖 3︰執行後產生的工作流程。

## 使用 BES 以新資料重新定型模型

呼叫重新定型 API：

1. 在 Visual Studio 中建立 C# 主控台應用程式 ([新增]->[專案]->[Windows 桌面]->[主控台應用程式])。
2.	登入 Machine Learning Web 服務入口網站。
3.	如果您使用傳統 Web 服務，按一下 [傳統 Web 服務]。
	1.	按一下您要使用的 Web 服務。
	2.	按一下預設端點。
	3.	按一下 [取用]。
	4.	在 [取用] 頁面底部的 [範例程式碼] 區段，按一下 [批次]。
	5.	繼續執行此程序的步驟 5。
4.	如果您使用新式 Web 服務，按一下 [Web 服務]。
	1.	按一下您要使用的 Web 服務。
	2.	按一下 [取用]。
	3.	在 [取用] 頁面底部的 [範例程式碼] 區段，按一下 [批次]。
5.	複製可用於批次執行的範例 C# 程式碼，然後貼入 Program.cs 檔案；請確定命名空間保持不變。

新增 Nuget 套件 Microsoft.AspNet.WebApi.Client，如註解中所述。若要新增指向 Microsoft.WindowsAzure.Storage.dll 的參考，您可能需要先安裝 Microsoft Azure 儲存體服務的用戶端程式庫。如需詳細資訊，請參閱 [Windows 儲存體服務](https://www.nuget.org/packages/WindowsAzure.Storage)。

### 更新 apikey 宣告

找到 **apikey** 宣告。

	const string apiKey = "abc123"; // Replace this with the API key for the web service

在 [取用] 頁面的 [基本取用資訊] 區段中，找到主索引鍵，將其複製到 **apiKey** 宣告。

### 更新 Azure 儲存體資訊

BES 範例程式碼會將檔案從本機磁碟機 (例如 C:\\temp\\CensusIpnput.csv) 上傳至 Azure 儲存體、加以處理後，再將結果寫回 Azure 儲存體。

若要完成此工作，您必須從 Azure 傳統入口網站擷取儲存體帳戶的儲存體帳戶名稱、金鑰和容器資訊，然後更新程式碼裡的對應值。

1. 登入 Azure 傳統入口網站。
1. 在左側導覽中，按一下 [儲存體]。
1. 在儲存體帳戶的清單中，選取要儲存重新定型模型的帳戶。
1. 按一下此頁面底部的 [管理存取金鑰]。
1. 複製並儲存**主要存取金鑰**，然後關閉對話方塊。
1. 按一下頁面頂端的 [容器]。
1. 您可以使用現有容器，或建立新的容器並儲存名稱。

找到 *StorageAccountName*、*StorageAccountKey*、 *StorageContainerName* 宣告，更新為您從 Azure 入口網站儲存的值。

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
您也必須確保程式碼中指定的位置有輸入檔案。

### 指定輸出位置

在要求承載中指定輸出位置時，必須將 RelativeLocation 中指定檔案的副檔名指定為 .ilearner。請參閱下列範例。

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] 輸出位置的名稱可能不同於此逐步解說中的名稱，取決於您新增 Web 服務輸出模組的順序。因為您設定這個訓練實驗有兩個輸出時，結果會包含這兩者的儲存位置資訊。

![重新定型輸出][6]

圖 4：重新定型輸出。

## 評估重新定型結果
 
當您執行應用程式時，輸出會包含存取評估結果所需的 URL 和 SAS 權杖。

您可以組合 output2 輸出結果中的 BaseLocation、RelativeLocation、SasBlobToken (如之前重新定型輸出的圖中所示)，再將完整 URL 貼入瀏覽器網址列，便能看到重新定型模型的效能結果。

查看結果以判斷新的定型模型的執行效能是否良好並足以取代現有模型。

複製輸出結果中的 BaseLocation、RelativeLocation、SasBlobToken，您在重新定型程序中將用到它們。

## 後續步驟

[重新定型傳統 Web 服務](machine-learning-retrain-a-classic-web-service.md)

[使用 Machine Learning 的管理 Cmdlet 重新定型新式 Web 服務](machine-learning-retrain-new-web-service-using-powershell.md)

<!-- Retrain a New Web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0928_2016-->