---
title: 建置您的第一個 Data Factory (Resource Manager 範本) | Microsoft Docs
description: 在本教學課程中，您會使用 Azure Resource Manager 範本來建立範例 Azure Data Factory 管線。
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2016
ms.author: spelluru

---
# 教學課程：使用 Azure Resource Manager 範本建置您的第一個 Azure Data Factory
> [!div class="op_single_selector"]
> * [概觀和必要條件](data-factory-build-your-first-pipeline.md)
> * [Azure 入口網站](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager 範本](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
> 

在本文中，您會使用 Azure Resource Manager 範本來建立第一個 Azure Data Factory。

## 必要條件
* 詳讀[教學課程概觀](data-factory-build-your-first-pipeline.md)一文並完成**必要**步驟。
* 按照[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 一文中的指示，在您的電腦上安裝最新版的 Azure PowerShell。
* 若要了解 Azure Resource Manager 範本，請參閱[撰寫 Azure Resource Manager 範本](../resource-group-authoring-templates.md)。

## 建立 Resource Manager 範本
在這一節中，您會建立下列 Data Factory 實體：

1. 名為 **TutorialDataFactoryARM** 的 **ata Factory**。資料處理站可以有一或多個管線。其中的管線可以有一或多個活動。例如，「複製活動」會從來源複製資料到目的地資料存放區，HDInsight Hive 活動則是執行 Hive 指令碼來轉換輸入資料。
2. 兩個**連結的服務**：**StorageLinkedService** 和 **HDInsightOnDemandLinkedService**。這些連結的服務會將您的 Azure 儲存體帳戶和 Azure HDInsight 叢集連結到您的 Data Factory。Azure 儲存體帳戶會保留此範例中管線的輸入和輸出資料。HDInsight 連結服務會用來執行此範例中管線活動指定的 Hive 指令碼。識別案例中使用的資料存放區/計算服務，並建立連結的服務將這些服務連結到 Data Factory。
3. 兩個 (輸入/輸出) **資料集**：**AzureBlobInput** 和 **AzureBlobOutput**。這些資料集代表 Hive 處理的輸入和輸出資料。這些資料集是您稍早在本教學課程中建立的 **StorageLinkedService**。連結的服務會指向 Azure 儲存體帳戶，而資料集則會指定保留輸入和輸出資料儲存體中的容器、資料夾和檔案名稱。

按一下 [使用 Data Factory 編輯器] 索引標籤，切換到詳細說明此範本中使用的 JSON 屬性的文章。

在 **C:\\ADFGetStarted** 資料夾中，使用下列內容建立名為 **ADFTutorialARM.json** 的 JSON 檔案：

> [!IMPORTANT]
> 變更 **storageAccountName** 和 **storageAccountKey** 變數的值。也變更 **dataFactoryName**，因為名稱必須是唯一的。
> 
> 

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "TutorialDataFactoryARM",
            "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
            "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
            "apiVersion": "2015-10-01",
            "storageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDataset": "AzureBlobInput",
            "blobOutputDataset": "AzureBlobOutput",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "linkedservices",
                        "name": "[variables('storageLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "AzureStorage",
                            "typeProperties": {
                                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "linkedservices",
                        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "HDInsightOnDemand",
                            "typeProperties": {
                                "clusterSize": 4,
                                "version":  "3.2",
                                "timeToLive": "00:05:00",
                                "osType": "windows",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobInputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                                "typeProperties": {
                                    "fileName": "input.log",
                                    "folderPath": "adfgetstarted/inputdata",
                                    "format": {
                                        "type": "TextFormat",
                                        "columnDelimiter": ","
                                    }
                                },
                                "availability": {
                                    "frequency": "Month",
                                    "interval": 1
                                },
                                "external": true,
                                "policy": {}
                            }
                        },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobOutputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "published": false,
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                                "typeProperties": {
                                    "folderPath": "adfgetstarted/partitioneddata",
                                    "format": {
                                        "type": "TextFormat",
                                        "columnDelimiter": ","
                                    }
                                },
                                "availability": {
                                    "frequency": "Month",
                                    "interval": 1
                                }
                            }
                        },
                        {
                            "dependsOn": [
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
                            ],
                            "type": "datapipelines",
                            "name": "[variables('dataFactoryName')]",
                            "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "description": "My first Azure Data Factory pipeline",
                                "activities": [
                                    {
                                        "type": "HDInsightHive",
                                        "typeProperties": {
                                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                                            "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                                            "defines": {
                                                "inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                                                "partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                                            }
                                        },
                                        "inputs": [
                                            {
                                                "name": "AzureBlobInput"
                                            }
                                        ],
                                        "outputs": [
                                            {
                                                "name": "AzureBlobOutput"
                                            }
                                        ],
                                        "policy": {
                                            "concurrency": 1,
                                            "retry": 3
                                        },
                                        "scheduler": {
                                            "frequency": "Month",
                                            "interval": 1
                                        },
                                        "name": "RunSampleHiveActivity",
                                        "linkedServiceName": "HDInsightOnDemandLinkedService"
                                    }
                                ],
                                "start": "2016-04-01T00:00:00Z",
                                "end": "2016-04-02T00:00:00Z",
                                "isPaused": false
                            }
                        }
                ]
            }
        ]
    }

按一下 [使用 Data Factory 編輯器] 索引標籤，切換到詳細說明此範本中使用的 JSON 屬性的文章。

請注意：

* Data Factory 會以上述 JSON 為您建立「以 Windows 為基礎的」HDInsight 叢集。您也可以讓它建立**以 Linux 為基礎的** HDInsight 叢集。如需詳細資訊，請參閱 [HDInsight 隨選連結服務](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。
* 您可以使用**自己的 HDInsight 叢集**，不必使用隨選的 HDInsight 叢集。如需詳細資訊，請參閱 [HDInsight 連結服務](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)。
* HDInsight 叢集會在您於 JSON 中指定的 Blob 儲存體 (**linkedServiceName**) 建立**預設容器**。HDInsight 不會在刪除叢集時刪除此容器。這是設計的行為。在使用 HDInsight 隨選連結服務時，除非有現有的即時叢集 (**timeToLive**)，否則每當需要處理配量時，就會建立 HDInsight 叢集，並在處理完成時予以刪除。
  
    隨著處理的配量越來越多，您會在 Azure Blob 儲存體中看到許多容器。如果在疑難排解作業時不需要這些容器，建議您加以刪除以降低儲存成本。這些容器的名稱遵循下列模式："adf**yourdatafactoryname**-**linkedservicename**-datetimestamp"。請使用 [Microsoft 儲存體總管](http://storageexplorer.com/)之類的工具，來刪除 Azure Blob 儲存體中的容器。

如需詳細資訊，請參閱 [HDInsight 隨選連結服務](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。

> [!NOTE]
> 您可以在 [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json) 上找到可供建立 Azure Data Factory 的其他 Resource Manager 範本範例。
> 
> 

## 建立 Data Factory
1. 啟動 **Azure PowerShell** 並執行下列命令：
   * 執行 `Login-AzureRmAccount`，並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。
   * 執行 `Get-AzureRmSubscription` 以檢視此帳戶的所有訂用帳戶。
   * 執行 `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` 以選取您要使用的訂用帳戶。此訂用帳戶應該與您在 Azure 入口網站中使用的相同。
2. 執行下列命令，使用您在步驟 1 中建立的 Resource Manager 範本來部署 Data Factory 實體。
   
        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## 監視管線
1. 登入 [Azure 入口網站](https://portal.azure.com/)後，按一下 [瀏覽]，然後選取 [Data Factory]。![瀏覽 -> Data Factory](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. 在 [Data Factory] 刀鋒視窗中，按一下您建立的 Data Factory (**TutorialFactoryARM**)。
3. 在您 Data Factory 的 [Data Factory] 刀鋒視窗中，按一下 [圖表]。![圖表磚](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. 在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。
   
   ![圖表檢視](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png)
5. 在 [圖表檢視] 中，按兩下 **AzureBlobOutput** 資料集。您會看到目前正在處理的配量。
   
    ![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. 處理完成時，您會看到配量處於 [就緒] 狀態。建立隨選 HDInsight 叢集通常需要一些時間 (大約 20 分鐘)。因此，管線預計需要**大約 30 分鐘**的時間來處理配量。
   
    ![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)
7. 當配量處於**就緒**狀態時，檢查您 Blob 儲存體中 **adfgetstarted** 容器內 **partitioneddata** 資料夾的輸出資料。

如需如何使用 Azure 入口網站刀鋒視窗來監視您在本教學課程中建立的管線和資料集的指示，請參閱[監視資料集和管線](data-factory-monitor-manage-pipelines.md)。

您也可以使用「監視及管理應用程式」來監視您的資料管線。如需有關使用應用程式的詳細資訊，請參閱[使用監視應用程式來監視和管理 Azure Data Factory 管線](data-factory-monitor-manage-app.md)。

> [!IMPORTANT]
> 配量處理成功時就會刪除輸入檔案。因此，如果您想要重新執行配量或再次進行本教學課程，請將輸入檔案 (input.log) 上傳至 adfgetstarted 容器的 inputdata 資料夾。
> 
> 

## 可供建立閘道的 Resource Manager 範本
以下是可供在後端建立邏輯閘道的範例 Resource Manager 範本。在內部部署電腦或 Azure IaaS VM 上安裝閘道，並使用金鑰向 Data Factory 服務註冊此閘道。如需詳細資訊，請參閱[在內部部署和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)。

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "GatewayUsingArmDF",
            "apiVersion": "2015-10-01",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "eastus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "gateways",
                        "apiVersion": "[variables('apiVersion')]",
                        "name": "GatewayUsingARM",
                        "properties": {
                            "description": "my gateway"
                        }
                    }            
                ]
            }
        ]
    }

此範本會利用名為 GatewayUsingARM 的閘道建立名為 GatewayUsingArmDF 的 Data Factory。

## 另請參閱
| 主題 | 說明 |
|:--- |:--- |
| [資料轉換活動](data-factory-data-transformation-activities.md) |本文提供 Azure Data Factory 所支援的資料轉換活動清單 (例如您在本教學課程中使用的 HDInsight Hive 轉換)。 |
| [排程和執行](data-factory-scheduling-and-execution.md) |本文說明 Azure Data Factory 應用程式模型的排程和執行層面。 |
| [管線](data-factory-create-pipelines.md) |本文協助您了解 Azure Data Factory 中的管線和活動，以及如何使用這些來為您的案例或業務建構端對端的資料導向工作流程。 |
| [資料集](data-factory-create-datasets.md) |本文協助您了解 Azure Data Factory 中的資料集。 |
| [使用監視應用程式來監視和管理管線](data-factory-monitor-manage-app.md) |本文說明如何使用監視及管理應用程式，來監視、管理管線及進行偵錯。 |

<!---HONumber=AcomDC_0921_2016-->