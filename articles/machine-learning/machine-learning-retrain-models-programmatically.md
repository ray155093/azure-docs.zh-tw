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
	ms.date="08/23/2016"
	ms.author="raymondl;garye;v-donglo"/>


#以程式設計方式重新定型機器學習服務模型  

在 Azure Machine Learning 中進行機器學習服務模型的實作程序時，需要定型並儲存您的模型。接著，使用它來建立預測性 Web 服務。接著才能在網站、儀表板及行動應用程式取用 Web 服務。

您使用 Machine Learning 建立的模型通常不是靜態。因為當有新資料或 API 取用者有自己的資料時，模型就必須重新定型。或者，您可能需要套用篩選條件來取得資料子集，並重新定型定型模型。

重新定型可能經常會發生。有了程式設計重新定型 API 功能後，您能夠使用重新定型 API 以程式設計方式重新定型模型，並使用新定型的模型來更新 Web 服務。

本文說明重新定型程序，並示範如何使用重新定型 API。

## 為何要重新定型：定義問題  

進行 ML 定型程序時，會使用一組資料來將模型定型。您使用 Machine Learning 建立的模型通常不是靜態。因為當有新資料或 API 取用者有自己的資料時，模型就必須重新定型。其他案例可能會需要套用篩選條件來取得資料子集，並重新定型定型模型。

在這些情況下，程式設計 API 可方便您或 API 取用者建立能夠單次或定期使用自己的資料重新定型模型的用戶端。接著可以評估重新定型的結果，然後更新 Web 服務 API 以使用新定型的模型。

##如何重新定型：端對端程序  

首先，此程序包含下列部分：訓練實驗以及以 Web 服務形式發佈的預測性實驗。若要啟用已定型模型的重新定型功能，必須利用定型模型的輸出將訓練實驗發佈為 Web 服務。這樣做可讓 API 存取模型進行重新定型。

設定重新定型的程序包含下列步驟：

![重新定型程序概觀][1]

圖 1：重新定型程序概觀

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
3.	實驗執行完成之後，按一下 [部署 Web 服務[傳統]]。這會將預測性實驗部署為傳統 Web 服務。

## 將訓練實驗部署為定型 Web 服務

若要重新定型已定型的模型，您必須將您建立的訓練實驗部署為重新定型 Web 服務。這個 Web 服務需要將 Web 服務輸出模組連接到[定型模型][train-model]模組，才能產生新的定型模組。

1. 若要回到訓練實驗，按一下左窗格中的 [實驗] 圖示，然後按一下名為 [普查模型] 的實驗。
2. 在 [搜尋實驗項目] 方塊中，輸入「Web 服務」。
3. 將 [Web 服務輸入] 模組拖曳到實驗畫布，然後將其連接到 [清除遺漏資料] 模組。
4. 將兩個 [Web 服務輸出] 模組拖曳到實驗畫布。將 [定型模組] 模組的輸出連接到其中一個，將 [評估模型] 模組的輸出連接到另一個。定型模型的 Web 服務輸出將會提供新的已定型模型。連接到評估模型的輸出將傳回該模組的評估模型輸出。
5. 按一下 **[執行]**。
6. 在實驗畫布底端，按一下 [設定 Web 服務]，然後選取 [重新定型 Web 服務]。這樣就會將訓練實驗部署為可產生定型模型與模型評估結果的 Web 服務。顯示的 Web 服務 [儀表板] 中也包含批次執行的 API 金鑰與 API 說明頁面。只有批次執行方法能夠用來建立定型模型。
  
在實驗完成執行後，產生的工作流程應該看起來像這樣：

![執行後產生的工作流程。][4]

圖 3︰執行後產生的工作流程。

## 新增端點
 
您部署的預測性 Web 服務是預設評分端點。預設端點會與原始定型和計分實驗同步，因此無法取代預設端點的定型模型。

在能以定型模型更新的預測性 Web 服務上，建立新的評分端點︰

>[AZURE.NOTE] 請確定您將端點新增至預測性 Web 服務，而不是定型 Web 服務。如果您正確部署定型和預測性 Web 服務，您應該會看到列出兩個不同的 Web 服務。預測性 Web 服務應是以 "[predictive exp.]" 結尾。

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下左側功能表中的 [Machine Learning]。
3. 在 [名稱] 下，按一下您的工作區，然後按一下 [Web 服務]。
4. 在 [名稱] 下，按一下 [普查模型 [predictive exp.]]。
5. 按一下頁面底部的 [新增端點]。如需有關新增端點的詳細資訊，請參閱[建立端點](machine-learning-create-endpoint.md)。

您也可以使用此 [GitHub 儲存機制](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs)提供的範例程式碼來新增評分端點。

## 使用 BES 以新資料重新定型模型

呼叫重新定型 API：

1. 在 Visual Studio 中建立 C# 主控台應用程式 ([新增]->[專案]->[Windows 桌面]->[主控台應用程式])。
2. 從定型 Web 服務的 API 說明頁面中複製可用於批次執行的範例 C# 程式碼，然後貼入 Program.cs 檔案；請確定命名空間保持不變。
3. 範例程式碼中有註解，指出您必須更新的程式碼部分。
4. 在要求承載中指定輸出位置時，必須將 RelativeLocation 中指定檔案的副檔名從 csv 變更為 .ilearner。請參閱下列範例。

		Outputs = new Dictionary<string, AzureBlobDataReference>()
		{
			{
				"output1",
				new AzureBlobDataReference()
				{
					ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
					RelativeLocation = "mycontainer/output1results.ilearner"
				}
			},
		},

>[AZURE.NOTE] 輸出位置的名稱可能不同於此逐步解說中的名稱，取決於您新增 Web 服務輸出模組的順序。因為您設定這個訓練實驗有兩個輸出時，結果會包含這兩者的儲存位置資訊。

### 更新 Azure 儲存體資訊

BES 範例程式碼會將檔案從本機磁碟機 (例如 C:\\temp\\CensusIpnput.csv) 上傳至 Azure 儲存體、加以處理後，再將結果寫回 Azure 儲存體。

若要完成此工作，您必須從 Azure 傳統入口網站擷取您儲存體帳戶的儲存體帳戶名稱、金鑰以及容器資訊，然後更新程式碼裡的對應值。

您也必須確保程式碼中指定的位置有輸入檔案。

![重新定型輸出][6]

圖 4：重新定型輸出。

## 評估重新定型結果
 
當您執行應用程式時，輸出會包含存取評估結果所需的 URL 和 SAS 權杖。

您可以組合 output2 輸出結果中的 BaseLocation、RelativeLocation、SasBlobToken (如之前重新定型輸出的圖中所示)，再將完整 URL 貼入瀏覽器網址列，便能看到重新定型模型的效能結果。

查看結果以判斷新的定型模型的執行效能是否良好並足以取代現有模型。

## 更新新增端點的定型模型

若要完成重新定型程序，您必須更新新增端點的定型模型。

* 如果您是使用 Azure 入口網站新增端點，可在 Azure 入口網站中按一下新端點的名稱，然後按一下 [更新資源] 連結，以取得您更新端點模型時所需的 URL。
* 如果您是使用範例程式碼新增端點，這會包含說明 URL 的位置 (靠輸出中的 HelpLocationURL 值識別)。

擷取路徑 URL：

1. 將 URL 複製並貼到瀏覽器。
2. 按一下 [更新資源] 連結。
3. 複製 PATCH 要求的 POST URL。例如：

		PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

您現在可以使用定型模型來更新您先前建立的評分端點。

下列範例程式碼示範如何使用 BaseLocation、RelativeLocation、SasBlobToken 和 PATCH URL 來更新端點。

	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}

在端點儀表板上可以看到呼叫的 apiKey 與 endpointUrl。

Resources 中 Name 參數的值，應該符合預測性實驗中儲存之定型模型的資源名稱。取得資源名稱：

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下左側功能表中的 [Machine Learning]。
3. 在 [名稱] 下，按一下您的工作區，然後按一下 [Web 服務]。
4. 在 [名稱] 下，按一下 [普查模型 [predictive exp.]]。
5. 按一下您新增的端點。
6. 在端點儀表板中，按一下 [更新資源]。
7. 在 Web 服務的更新資源 API 文件頁面，您可以在 [可更新的資源] 下找到 [資源名稱]。

如果在您完成端點更新之前 SAS 權杖已到期，您必須執行 GET 以作業識別碼取得新的權杖。

程式碼執行成功後，新的端點應該會在大約 30 秒後開始使用重新定型模型。

##摘要  
使用重新定型 API 可以更新預測性 Web 服務的定型模型，運用於如下案例︰

* 定期以新的資料重新定型模型。
* 散發模型給客戶，目的是要讓他們使用自己的資料重新定型模型。

## 後續步驟
[ Azure Machine Learning 傳統 Web 服務的重新訓練進行疑難排解](machine-learning-troubleshooting-retraining-models.md)

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0824_2016-->