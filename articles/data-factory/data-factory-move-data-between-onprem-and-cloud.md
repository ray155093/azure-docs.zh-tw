<properties 
	pageTitle="移動資料 - 資料管理閘道器 | Microsoft Azure"
	description="設定資料閘道器以在內部部署與雲端之間移動資料。使用 Azure Data Factory 中的資料管理閘道器移動資料。" 
    keywords="資料閘道器, 資料整合, 移動資料, 閘道認證"
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
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# 利用資料管理閘道在內部部署來源和雲端之間移動資料
本文提供使用 Data Factory 整合內部部署資料存放區與雲端資料存放區資料的概觀。本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文和其他 Data Factory 核心概念文章：[資料集](data-factory-create-datasets.md)和[管線](data-factory-create-pipelines.md)。

## 資料管理閘道
若要將資料移入/移出內部部署資料存放區，您必須在內部部署機器上安裝資料管理閘道。您可以將閘道安裝在與資料存放區相同或相異的電腦上，只要閘道可以連接資料存放區即可。如需資料管理閘道的所有詳細資料，請參閱[資料管理閘道](data-factory-data-management-gateway.md)一文。

以下逐步解說將示範如何使用將資料從內部部署 SQL Server 資料庫移至 Azure Blob 的管線來建立 Data Factory。在逐步解說中，您會在電腦上安裝及設定資料管理閘道。

## 逐步解說︰將內部部署資料複製到雲端
  
## 建立 Data Factory
在此步驟中，您將使用 Azure 入口網站來建立名為 **ADFTutorialOnPremDF** 的 Azure Data Factory 執行個體。您也可以使用 Azure Data Factory Cmdlet 來建立 Data Factory。

1.	登入 [Azure 入口網站](https://portal.azure.com)之後，請按一下左下角的 [新增]，並選取 [建立] 刀鋒視窗中的 [資料分析]，然後按一下 [資料分析] 刀鋒視窗中的 [Data Factory]。

	![新增->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. 在 [**新增 Data Factory**] 刀鋒視窗中：
	1. 輸入 **ADFTutorialOnPremDF** 做為名稱。
	2. 按一下 [**資源群組名稱**] 並選取 **ADFTutorialResourceGroup**。您可以選取現有的資源群組，或建立一個群組。若要建立資源群組︰
		1. 按一下 [建立新的資源群組]。
		2. 在 [**建立資源群組**] 刀鋒視窗中，輸入資源群組的 [**名稱**]，然後按一下 [**確定**]。

7. 請注意，[新增 Data Factory] 刀鋒視窗上已核取 [新增至開始面板]。

	![新增至儀表板](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. 在 [**新增 Data Factory**] 刀鋒視窗中，按一下 [**建立**]。

	Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：**Data Factory 名稱 “ADFTutorialOnPremDF” 無法使用**，請變更 Data Factory 名稱 (例如 yournameADFTutorialOnPremDF)，然後嘗試重新建立。執行此教學課程中的其餘步驟時，請使用此名稱來取代 ADFTutorialOnPremDF。

9. 按一下標題列上的 [通知] 按鈕，查看來自建立程序的通知，如下圖所示。再按一下按鈕可關閉通知視窗。

	![通知中樞](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. 建立完成之後，您會看到 [Data Factory] 刀鋒視窗，如下圖所示：

	![Data Factory 首頁](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## 建立閘道
5. 在 **DATA FACTORY** 刀鋒視窗中，按一下 [**製作和部署**] 磚來啟動 Data Factory 的 [**編輯器**]。

	![[製作和部署] 磚](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
6.	在 [Data Factory 編輯器] 中，按一下工具列的 [... (省略符號)]，選取 [新增資料閘道]。

	![工具列上的 [新增資料閘道]](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. 在 [**建立**] 刀鋒視窗上，輸入 **adftutorialgateway** 做為 [**名稱**]，然後按一下 [**確定**]。

	![[建立閘道器] 刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. 在 [**設定**] 分頁中，按一下 [**直接安裝在此電腦上**]。此動作會下載閘道的安裝套件、在電腦上安裝、設定和註冊閘道。

	> [AZURE.NOTE] 
	使用 Internet Explorer 或 Microsoft ClickOnce 相容的 Web 瀏覽器。
	> 
	> 如果您使用 Chrome，請移至 [Chrome 線上應用程式商店](https://chrome.google.com/webstore/)，使用關鍵字 "ClickOnce" 進行搜尋，選擇其中一個 ClickOnce 擴充功能並安裝。
	>  
	> 針對 Firefox 進行相同的操作 (安裝附加元件)。按一下工具列上的 [開啟功能表] 按鈕 (右上角的**三條水平線**)，按一下 [附加元件]，以「ClickOnce」關鍵字進行搜尋，選擇其中一個 ClickOnce 延伸模組並安裝。

	![閘道器 - [設定] 刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	這是最簡單的方式 (一鍵)，透過單一步驟即可下載、安裝、設定和註冊閘道。您可以看到「Microsoft 資料管理閘道組態管理員」應用程式已安裝在電腦上。您也可以在資料夾 **C:\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared** 中找到可執行檔 **ConfigManager.exe**。

	您也可以使用此刀鋒視窗中的連結手動下載與安裝閘道器，並使用 [新金鑰] 文字方塊中顯示的金鑰來加以註冊。
	
	如需閘道的所有詳細資料，請參閱[資料管理閘道](data-factory-data-management-gateway.md)一文。

	>[AZURE.NOTE] 您必須是本機電腦上的系統管理員，才能成功安裝和設定「資料管理閘道」。您可以將其他使用者加入至資料管理閘道使用者本機 Windows 群組。此群組的成員可以使用「資料管理閘道組態管理員」工具來設定閘道器。

5. 請等候幾分鐘，然後在電腦上啟動 [資料管理閘道器組態管理員] 應用程式。在 [搜尋] 視窗中，輸入**資料管理閘道器**以存取這個公用程式。您也可以在資料夾 **C:\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared** 中找到可執行檔 **ConfigManager.exe**

	![閘道器組態管理員](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. 等候直到︰
	2. [閘道器名稱] 設定為 **adftutorialgateway**。
	4. 底部的狀態列會顯示 [已連接到雲端服務] 和一個綠色的核取記號。

	您也可以在 [首頁] 索引標籤上執行以下操作：- 透過 [註冊] 按鈕使用來自 Azure 入口網站的金鑰**註冊**閘道。- **停止**在閘道電腦上執行的資料管理閘道主機服務。- 將更新安裝作業**排程**在一天當中的指定時間。- 檢視閘道的**上次更新**時間。

8. 切換到 [設定] 索引標籤。在 [憑證] 區段中指定的憑證，可用來加密/解密在入口網站指定的內部部署資料存放區認證 (選擇性)。按一下 [變更] 以改用您自己的憑證。根據預設，閘道器會使用由 Data Factory 服務自動產生的憑證。

	![閘道器憑證組態](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

	您也可以在 [設定] 索引標籤上執行以下操作：- 檢視或匯出閘道使用的憑證。- 變更閘道使用的 HTTPS 端點 -
9. (選擇性) 切換到 [診斷] 索引標籤，如果您想啟用詳細資訊記錄功能，以便對閘道的任何問題進行疑難排解，請勾選 [啟用詳細資訊記錄] 選項。在 [應用程式及服務記錄檔] -> [資料管理閘道器] 節點之下的 [事件檢視器] 中可找到記錄資訊。

	![[診斷] 索引標籤](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	您也可以在 [診斷] 索引標籤上執行以下操作：
	
	- 使用**測試連線**一節來對使用閘道器的內部部署資料來源。
	- 按一下 [檢視記錄檔] 以查看 [事件檢視器] 視窗中的資料管理閘道記錄檔。
	- 按一下 [傳送記錄檔] 將含有過去七天記錄檔的 zip 檔案上傳到 Microsoft，以幫助針對問題進行疑難排解。
10. 在 Azure 入口網站中，依序在 [設定] 刀鋒視窗和 [新增資料閘道] 刀鋒視窗中，按一下 [確定]。
6. 左側的樹狀檢視中，[資料閘道器] 下方應該會顯示 **adftutorialgateway**。如果按一下，應該會看到相關聯的 JSON。
	

## 建立連結服務 
在此步驟中，您會建立兩個連結服務：**AzureStorageLinkedService** 和 **SqlServerLinkedService**。**SqlServerLinkedService** 會連結內部部署 SQL Server 資料庫，而 **AzureStorageLinkedService** 連結服務則會將 Azure Blob 存放區連結至 Data Factory。稍後在本逐步解說中，您會建立可將內部部署 SQL Server 資料庫的資料複製到 Azure Blob 存放區的管線。

#### 在內部部署 SQL Server 資料庫中新增連結服務
1.	在 [Data Factory 編輯器] 中，按一下工具列上的 [新增資料存放區]，然後選取 [SQL Server]。

	![新增 SQL Server 連結服務](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
3.	在 [JSON 編輯器] 中，執行下列動作：
	1. 為 **gatewayName** 指定 **adftutorialgateway**。
	2. 如果您使用 Windows 驗證：
		1. 在 [connectionString] 中，將 [整合式安全性] 設為 [true]，指定資料庫**伺服器名稱**和**資料庫名稱**，並移除**使用者識別碼**和**密碼**。
		3. 為 **userName** 和 **password** 屬性指定使用者名稱和密碼。
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication. Example: <domain>\<user>",
            		"password": "<Specify password for the user account>"
        		}

	4. 如果您使用 SQL 驗證：
		1. 在 **connectionString** 中指定資料庫的**伺服器名稱**、**資料庫名稱**、**User ID** 和 **Password**。
		2. 從 JSON 中移除最後兩個 JSON 屬性：**userName** 和 **password**。
		3. 移除位於行尾的尾端 `,` (逗號)，其指定 **gatewayName** 屬性值。

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	
		認證會以 Data Factory 服務所擁有的憑證**加密**。如果您想要改用與「資料管理閘道」關聯的憑證，請參閱[安全地設定認證](#set-credentials-and-security)。
    
2.	按一下命令列上的 [部署]，部署 SQL Server 連結服務。

#### 新增 Azure 儲存體帳戶的連結服務
 
1. 在 [Data Factory 編輯器] 中，按一下命令列上的 [新增資料存放區]，然後按一下 [Azure 儲存體]。
2. 在 [帳戶名稱] 中輸入您的 Azure 儲存體帳戶名稱。
3. 在 [帳戶金鑰] 中輸入您的 Azure 儲存體帳戶金鑰。
4. 按一下 [部署] 以部署 **AzureStorageLinkedService**。
   
 
## 建立資料集
在此步驟中，您會建立代表複製作業的輸入和輸出資料的輸入和輸出資料集 (內部部署 SQL Server 資料庫 => Azure Blob 儲存體)。在建立資料集或資料表 (矩形資料集) 之前，您必須執行下列動作 (清單後列有詳細步驟)：

- 在您新增為 Data Factory 連結服務的 SQL Server 資料庫中，建立名為 **emp** 的資料表，並在資料表中插入幾個範例項目。
- 在您加入 Data Factory 作為連結服務的 Azure Blob 儲存體帳戶中，建立名為 **adftutorial** 的 Blob 容器。

### 準備用於教學課程的內部部署 SQL Server

1. 在您指定用於內部部署 SQL Server 連結服務 (**SqlServerLinkedService**) 的資料庫中，使用下列 SQL 指令碼在資料庫中建立 **emp** 資料表。


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. 在資料表中插入一些範例：


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### 建立輸入資料表

1. 在 [Data Factory 編輯器] 中，按一下命令列上的 [新增資料集]，然後按一下 [SQL Server 資料表]。
2.	使用下列文字取代右窗格中的 JSON：

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	請注意：
	
	- **type** 設定為 **SqlServerTable**。
	- **tableName** 設定為 **emp**。
	- **linkedServiceName** 設定為 **OnPremSqlLinkedService** (您已在步驟 2 中建立此連結服務)。
	- 針對並非由 Azure Data Factory 中另一個管線所產生的輸入資料表，您必須將 **external** 設定為 **true**。它代表輸入資料產生於 Azure Data Factory 服務外部。您可以使用 **Policy** 區段中的 **externalData** 元素，選擇性地指定任何外部資料原則。

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2. 按一下命令列的 [部署]，以部署資料集 (資料表是矩形的資料集)。確認您在標題列看到 [已成功部署資料表] 訊息。


### 建立輸出資料表

1.	在 [Data Factory 編輯器] 中，按一下命令列的 [新增資料集]，然後按一下 [Azure Blob 儲存體]。
2.	使用下列文字取代右窗格中的 JSON：

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	請注意：
	
	- **type** 設定為 **AzureBlob**。
	- **linkedServiceName** 是設定為 **AzureStorageLinkedService** (您已在步驟 2 中建立此連結服務)。
	- **folderPath** 設定為 **adftutorial/outfromonpremdf**，其中 outfromonpremdf 是 adftutorial 容器中的資料夾。建立 **adftutorial** 容器 (如果尚未存在)。
	- **availability** 設定為**每小時**，且 (**frequency** 設定為**小時**，**interval** 設定為 **1**)。Data Factory 服務會每隔一小時在 Azure SQL Database 的 **emp** 資料表中產生輸出資料配量。

	如果您沒有指定**輸入資料表**的 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案/Blob 都會視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。如需範例，請參閱 [教學課程][adf-tutorial] 中的範例檔案。
 
	如果您未指定**輸出資料表**的 **fileName**，**folderPath** 中產生的檔案會依照下列格式命名：Data.<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)。

	若要根據 **SliceStart** 時間動態地設定 **folderPath** 和 **fileName**，請使用 partitionedBy 屬性。在下列範例中，folderPath 使用 SliceStart (所處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2.	按一下命令列的 [部署]，以部署資料集 (資料表是矩形的資料集)。確認您在標題列看到 [已成功部署資料表] 訊息。
  

## 建立管線
在此步驟中，您會建立**管線**，其中包含一個使用 **EmpOnPremSQLTable** 做為輸入和 **OutputBlobTable** 做為輸出的**複製活動**。

1.	在 **DATA FACTORY** 刀鋒視窗中，按一下 [**製作和部署**] 磚來啟動 Data Factory 的 [**編輯器**]。

	![[製作和部署] 磚](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2.	按一下命令列的 [新增管線]。如果沒看到此按鈕，請按一下 [...] (省略符號) 展開命令列。
2.	使用下列文字取代右窗格中的 JSON：
	
			{
				"name": "ADFTutorialPipelineOnPrem",
				"properties": {
		    	"description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    	"activities": [
		      	{
		        	"name": "CopyFromSQLtoBlob",
			        "description": "Copy data from on-prem SQL server to blob",
		        	"type": "Copy",
		        	"inputs": [
		          	{
		            	"name": "EmpOnPremSQLTable"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "OutputBlobTable"
			          }
			        ],
			        "typeProperties": {
			          "source": {
			            "type": "SqlSource",
			            "sqlReaderQuery": "select * from emp"
			          },
			          "sink": {
			            "type": "BlobSink"
			          }
			        },
			        "Policy": {
			          "concurrency": 1,
			          "executionPriorityOrder": "NewestFirst",
			          "style": "StartOfInterval",
			          "retry": 0,
			          "timeout": "01:00:00"
			        }
			      }
			    ],
			    "start": "2016-07-05T00:00:00Z",
			    "end": "2016-07-06T00:00:00Z",
			    "isPaused": false
			  }
			}

	請注意：
 
	- 在 activities 區段中，只會有 **type** 設定為 **Copy** 的活動。
	- 活動的**輸入**設定為 **EmpOnPremSQLTable**，活動的**輸出**則設定為 **OutputBlobTable**。
	- 在 **transformation** 區段中，將 **SqlSource** 指定為**來源類型**，並將 **BlobSink** 指定為**接收類型**。
	- SQL 查詢 `select * from emp` 會指定用於 **SqlSource** 的 **sqlReaderQuery** 屬性。

	將 **start** 屬性的值替換為目前日期，並將 **end** 值替換為隔天的日期。開始和結束日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**end** 時間為選擇性項目，但在本教學課程中會用到。
	
	如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。若要無限期地執行管線，請指定 **9/9/9999** 做為 **end** 屬性的值。
	
	您定義會根據為每個 Azure Data Factory 資料表定義的 **Availability** 屬性來處理資料配量的持續時間。
	
	在此範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。

	
2. 按一下命令列的 [部署]，以部署資料集 (資料表是矩形的資料集)。確認您在標題列看到 [已成功部署管線] 訊息。
5. 現在，按一下 **X** 關閉 [**編輯器**] 刀鋒視窗。再按一次 **X**，關閉內含工具列和樹狀檢視的 [ADFTutorialDataFactory] 刀鋒視窗。如果您看到 [將捨棄未儲存的編輯] 訊息，請按一下 [確定]。
6. 您應該會回到 **ADFTutorialOnPremDF** 的 **DATA FACTORY** 刀鋒視窗。

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。

#### 在圖表檢視中檢視 Data Factory 
1. 在 **Azure 入口網站**中，按一下 **ADFTutorialOnPremDF** Data Factory 首頁上的 [圖表] 圖格：

	![圖表連結](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. 您應該會看到如下所示的圖表：

	![圖表檢視](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	您可以將管線和資料表放大、縮小、放大到 100%、縮放至適當比例和自動定位，以及顯示歷程資訊 (反白顯示所選取項目的上游和下游項目)。您可以按兩下物件 (輸入/輸出資料表或管線) 查看其屬性。

## 監視管線
在此步驟中，您會使用 Azure 入口網站來監視 Azure Data Factory 的運作情形。您也可以使用 PowerShell Cmdlet 來監視資料集和管線。如需監視的詳細資料，請參閱[監視和管理管線](data-factory-monitor-manage-pipelines.md)。

1. 巡覽至 **Azure 入口網站** (如果您已關閉它)
2. 如果 [**ADFTutorialOnPremDF**] 刀鋒視窗尚未開啟，請按一下**開始面板**上的 [**ADFTutorialOnPremDF**] 以開啟此刀鋒視窗。
3. 您應該會看到您在此刀鋒視窗上建立之資料表和管線的**計數**和**名稱**。

	![Data Factory 首頁](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. 現在，按一下 [資料集] 磚。
5. 在 [**資料集**] 刀鋒視窗中，按一下 [**EmpOnPremSQLTable**]。

	![EmpOnPremSQLTable 配量](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. 請注意，到目前為止已經產生資料配量，且它們的狀態為 [**就緒**]。這是因為您已將資料插入 SQL Server 資料庫中，而資料一直留存於其中。確認下方的 [問題配量] 區段中沒有顯示任何配量。
	
	[最近更新的配量] 和 [最近失敗的配量] 清單都依照 [上次更新時間] 排序。在下列情況中，配量的更新時間會變更。

	您以手動方式更新配量的狀態，例如，使用 **Set-AzureRmDataFactorySliceStatus** (或) 在配量的 [配量] 刀鋒視窗上按一下 [執行]。
	
	配量因為執行而變更狀態 (例如，開始執行、執行結束但失敗、執行結束且成功等)。

	按一下清單的標題或 **...** (省略符號)，查看更長的配量清單。按一下工具列上的 [篩選] 以篩選配量。

	若要改成依配量開始/結束時間來檢視資料配量，請按一下 [資料配量 (依配量時間)] 磚。
7. 現在，在 [**資料集**] 刀鋒視窗中，按一下 [**OutputBlobTable**]。

	![OputputBlobTable 配量](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
8. 確認已看到目前為止的配量已產生，且狀態為 [就緒]。等待到目前為止的配量的狀態設為 [就緒]。
9. 按一下清單中的任何資料配量，您應該會看到 [資料配量] 刀鋒視窗。

	![資料配量刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	如果配量不是 [就緒] 狀態，您可以在 [未就緒的上游配量] 清單中看到未就緒且阻礙目前配量執行的上游配量。

10. 按一下底部清單中的 [活動執行]，可查看 [活動執行詳細資料]。

	![[活動執行詳細資料] 刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

11. 按一下 **X** 關閉所有刀鋒視窗，直到您
12. 回到 **ADFTutorialOnPremDF** 的起始刀鋒視窗為止。
14. (選擇性) 依序按一下 [管線] 及 [ADFTutorialOnPremDF]，然後深入檢視輸入資料表 (**已使用**) 或輸出資料表 (**已產生**)。
15. 使用「Azure 儲存體總管」之類的工具來驗證輸出。

	![Azure 儲存體總管](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## 後續步驟

- 如需資料管理閘道的所有詳細資料，請參閱[資料管理閘道](data-factory-data-management-gateway.md)一文。
- 若要了解如何使用複製活動將資料從來源資料存放區移動到接收資料存放區，請參閱[從 Azure Blob 複製資料到 Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

<!---HONumber=AcomDC_0914_2016-->