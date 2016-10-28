<properties
	pageTitle="適用於 Machine Learning 的 PowerShell 模組 | Microsoft Azure"
	description="適用於 Azure Machine Learning 的 PowerShell 模組提供公開預覽模式。您可以使用 PowerShell 來建立及管理工作區中、實驗、Web 服務等。"
	keywords="實驗、線性迴歸、機器學習服務演算法、機器學習服務教學課程、預測性模型技術、資料科學實驗"
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/05/2016"
	ms.author="garye;haining"/>

# 適用於 Microsoft Azure Machine Learning 的 PowerShell 模組

適用於 Azure Machine Learning 的 PowerShell 模組是一款功能強大的工具，它能讓您使用 Windows PowerShell 來管理工作區、實驗、資料集、Web 服務等。

若要檢視文件及下載模組和完整的原始程式碼，請前往 [https://aka.ms/amlps](https://aka.ms/amlps)。

## 什麼是 Machine Learning PowerShell 模組？

Machine Learning PowerShell 模組是以 .NET 為基礎的 DLL 模組，它能讓您從 Windows PowerShell 全權管理 Azure Machine Learning 工作區、實驗、資料集、Web 服務及 Web 服務端點。您可以隨著模組一同下載完整的原始程式碼，其中包括完全分隔的 [C# API 層](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)。這表示您可以從自己的 .NET 專案參考此 DLL，並透過 .NET 程式碼來管理 Azure Machine Learning。此外，對於 DLL 仰賴的基礎 REST API，您可以直接取自喜愛的用戶端。

## PowerShell 模組有什麼功能？

以下是一些您可以使用此 PowerShell 模組來執行的工作。如需以下功能和其他更多功能的相關資訊，請參閱[完整文件](https://aka.ms/amlps)。

- 使用管理憑證佈建新工作區 ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- 匯出及匯入代表實驗圖形的 JSON 檔案 ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) 和 [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- 執行實驗 ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- 利用預測性實驗建立 Web 服務 ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- 在發佈的 Web 服務上建立新端點 ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- 叫用 RRS 和/或 BES Web 服務端點 ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) 和 [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

以下是使用 PowerShell 執行現有實驗的簡單範例︰

		#Find the first Experiment named “xyz”
		$exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
		#Run the Experiment
		Start-AmlExperiment -ExperimentId $exp.ExperimentId 

如需更深入的使用案例，請參閱以下有關使用 PowerShell 模組將非常普遍要求之工作自動化的文章：[使用 PowerShell，從一個實驗中建立許多機器學習服務模型和 Web 服務端點](machine-learning-create-models-and-endpoints-with-powershell.md)。

## 如何開始使用？

若要開始使用 Machine Learning PowerShell，請從 GitHub 下載[發行套件](https://github.com/hning86/azuremlps/releases)並遵循[安裝指示](https://github.com/hning86/azuremlps/blob/master/README.md)。您將需要解除封鎖已下載/解壓縮的 DLL，然後再匯入 PowerShell 環境。大部分的 Cmdlet 都會要求您提供工作區識別碼、工作區授權權杖，以及工作區所在的 Azure 區域。提供這些資訊最簡單的方法是透過預設的 config.json 檔案，安裝指示有該方法的詳細資料。當然，您也可以使用 Visual Studio 複製 git 樹狀結構，以及在本機修改/編譯程式碼。

## 後續步驟

在此預覽期間內，我們將會持續改善及擴充 PowerShell 模組。如需更多新聞和資訊，敬請關注 [Cortana Intelligence 和 Machine Learning 部落格](https://blogs.technet.microsoft.com/machinelearning/)。

<!---HONumber=AcomDC_0914_2016-->