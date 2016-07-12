<properties 
	pageTitle="Azure 資料處理站的疑難排解" 
	description="了解如何使用 Azure Data Factory 進行問題的疑難排解。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="spelluru"/>

# 資料處理站的疑難排解
這篇文章提供使用 Azure Data Factory 時的問題疑難排解提示。並非是使用服務時的所有可能問題，但是涵蓋部分問題和一般疑難排解提示。

## 疑難排解秘訣

### 錯誤︰訂用帳戶未註冊為使用命名空間 'Microsoft.DataFactory'
如果您收到此錯誤，Azure Data Factory 資源提供者尚未在您的電腦上註冊。請執行以下動作：

1. 啟動 Azure PowerShell。
2. 使用下列命令來登入您的 Azure 帳戶。Login-AzureRmAccount
3. 執行下列命令以註冊 Azure Data Factory 提供者。Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

### 問題：執行 Data Factory Cmdlet 時發生未授權錯誤
您在 Azure PowerShell 中可能未使用正確的 Azure 帳戶或訂用帳戶。請使用下列 Cmdlet 來選取要用於 Azure PowerShell 的正確 Azure 帳戶和訂用帳戶帳戶。

1. Login-AzureRmAccount - 使用正確的使用者識別碼和密碼
2. Get-AzureRmSubscription - 檢視帳戶的所有訂用帳戶。
3. Select-AzureRmSubscription <訂用帳戶名稱> - 選取正確的訂用帳戶。請使用您在 Azure 入口網站上用來建立 Data Factory 的相同帳戶。

### 問題：無法從 Azure 入口網站啟動資料管理閘道器快速安裝
資料管理閘道器的快速安裝需要有 Internet Explorer 或 Microsoft ClickOnce 相容的 Web 瀏覽器。如果無法啟動快速安裝，請執行下列其中一項：

- 請使用 Internet Explorer 或 Microsoft ClickOnce 相容的 Web 瀏覽器。

	如果您使用 Chrome，請移至 [Chrome 線上應用程式商店](https://chrome.google.com/webstore/)，使用關鍵字 "ClickOnce" 進行搜尋，選擇其中一個 ClickOnce 擴充功能並安裝。
	
	您需要針對 Firefox 進行相同動作 (安裝附加元件)。按一下工具列上的 [開啟功能表] 按鈕 (右上角的三條水平線)，按一下 [附加元件]，以「ClickOnce」關鍵字進行搜尋，選擇其中一個 ClickOnce 延伸模組並安裝。

- 使用入口網站中相同刀鋒視窗上顯示的 [手動安裝] 連結，下載安裝檔案並手動執行。成功安裝之後，您會看到 [資料管理閘道器組態] 對話方塊。從入口網站畫面複製**金鑰**，並且在組態管理員中使用它來手動向服務註冊閘道器。

### 問題：無法連線到內部部署 SQL Server 
在閘道器機器上啟動 [資料管理閘道器組態管理員]，使用 [疑難排解] 索引標籤以測試從閘道器機器到 SQL Server 的連接。如需詳細資訊，請參閱[閘道器疑難排解](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting)。
 

### 問題：輸入配量永遠處於 Waiting 狀態

配量處於 **Waiting** 狀態的原因有許多種，其中一種常見的原因是 **external** 屬性沒有設為 **True**。在 Azure Data Factory 範圍外產生的任何資料集，都應該標示 **external** 屬性。這表示資料在外部，且未由 Data Factory 內的任何管線所支持。一旦資料在個別的存放區可用，資料配量就會標示為 [就緒]。

關於 **external** 屬性的用法，請參閱下列範例。當您將 external 設為 true 時，可以選擇性地指定 **externalData***。

如需此屬性的詳細資訊，請參閱[資料集](data-factory-create-datasets.md)文章。
	
	{
	  "name": "CustomerTable",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      }
	    }
	  }
	}

若要解決這個錯誤，請將 **external** 屬性和選用 **externalData** 區段加入輸入資料表的 JSON 定義，並重新建立資料表。

### 問題：混合式複製作業失敗
請參閱[閘道器疑難排解](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting)以取得對於使用資料管理閘道器複製到/自內部部署資料存放區的問題的疑難排解步驟。

### 問題：隨選 HDInsight 佈建失敗
使用 HDInsightOnDemand 類型的連結服務時，您必須指定指向 Azure Blob 儲存體的 linkedServiceName。Data Factory 服務會使用此儲存體來儲存記錄檔和您的隨選 HDInsight 叢集的支援檔案。有時候隨選 HDInsight 叢集的佈建會失敗，並且有下列錯誤︰

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

此錯誤通常表示 linkedServiceName 中指定的儲存體帳戶的位置，與進行 HDInsight 佈建的資料中心位置不同。例如，如果您的 Azure Data Factory 位置是美國西部，且隨選 HDInsight 佈建在美國西部進行，但是 Azure Blob 儲存體帳戶位置設定為美國東部，則隨選佈建會失敗。

此外，還有第二個的 JSON 屬性 additionalLinkedServiceNames，可供隨選 HDInsight 中指定其他儲存體帳戶。這些其他的連結儲存體帳戶應該與 HDInsight 叢集位在相同位置，否則會失敗，並產生同樣的錯誤。

### 問題：自訂 .NET 活動失敗
如需詳細步驟，請參閱[偵錯具有自訂活動的管線](data-factory-use-custom-activities.md#debug-the-pipeline)。

## 使用 Azure 入口網站進行疑難排解 

### 使用入口網站刀鋒視窗
請參閱[監視管線](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline)以取得步驟。

### 使用監視及管理應用程式
如需詳細資訊，請參閱[使用監視及管理應用程式，來監視及管理 Data Factory 管線](data-factory-monitor-manage-app.md)。

## 使用 Azure PowerShell 進行疑難排解

### 使用 Azure PowerShell 對錯誤進行疑難排解  
如需詳細資料，請參閱[使用 Azure PowerShell 監視 Data Factory 管線](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline)。


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 

<!---HONumber=AcomDC_0629_2016-->