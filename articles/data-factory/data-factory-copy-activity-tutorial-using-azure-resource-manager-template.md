---
title: "教學課程：使用 Resource Manager 範本建立管線 | Microsoft Docs"
description: "在本教學課程中，您會使用 Azure Resource Manager 範本，建立具有複製活動的 Azure Data Factory 管線。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: f45c21e682f93de4dbe34b4ce07745225d548cb0
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>教學課程：使用 Azure Resource Manager 範本建立具有複製活動的管線
> [!div class="op_single_selector"]
> * [概觀和必要條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [複製精靈](data-factory-copy-data-wizard-tutorial.md)
> * [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 範本](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

本教學課程示範如何使用 Azure Resource Manager 範本建立和監視 Azure Data Factory。 Data Factory 中的管線會將資料從 Azure Blob 儲存體複製到 Azure SQL Database。

> [!NOTE]
> 本教學課程中的資料管線會將資料從來源資料存放區複製到目的地資料存放區。 它不會轉換輸入資料來產生輸出資料。 如需如何使用 Azure Data Factory 轉換資料的教學課程，請參閱[教學課程︰使用 Hadoop 叢集建置管線來轉換資料](data-factory-build-your-first-pipeline.md)。
> 
> 您可以將一個活動的輸出資料集設為另一個活動的輸入資料集，藉此鏈結兩個活動 (讓一個活動接著另一個活動執行)。 如需詳細資訊，請參閱[在 Data Factory 中排程和執行](data-factory-scheduling-and-execution.md)。 

## <a name="prerequisites"></a>必要條件
* 請檢閱[教學課程概觀和必要條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)並完成**必要**步驟。
* 按照 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 一文中的指示，在您的電腦上安裝最新版的 Azure PowerShell。 在本教學課程中，您可以使用 PowerShell 來部署 Data Factory 實體。 
* (選擇性) 若要了解 Azure Resource Manager 範本，請參閱 [撰寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md) 。

## <a name="in-this-tutorial"></a>本教學課程內容
在本教學課程中，您可以利用下列 Data Factory 實體建立資料處理站︰

| 實體 | 說明 |
| --- | --- |
| Azure 儲存體連結服務 |將您的 Azure 儲存體帳戶連結至 Data Factory。 Azure 儲存體是來源資料存放區，而 Azure SQL Database 是教學課程中複製活動的接收資料存放區。 它會指定包含複製活動之輸入資料的儲存體帳戶。 |
| Azure SQL Database 的連結服務 |將您的 Azure SQL Database 連結至 Data Factory。 它會指定包含複製活動之輸出資料的 Azure SQL Database。 |
| Azure Blob 輸入資料集 |是指 Azure 儲存體連結服務。 連結的服務是指 Azure 儲存體帳戶，而 Azure Blob 資料集則會指定保留輸入資料儲存體中的容器、資料夾和檔案名稱。 |
| Azure SQL 輸出資料集 |是指 Azure SQL 連結服務。 Azure SQL 連結服務是指 Azure SQL Server，而 Azure SQL 資料集會指定包含輸出資料的資料表名稱。 |
| Data Pipeline |管線有一個類型 Copy 的活動，會採用 Azure blob 資料集做為輸入和 Azure SQL 資料集做為輸出。 Copy 活動會將資料從 Azure Blob 複製到 Azure SQL Database。 |

資料處理站可以有一或多個管線。 其中的管線可以有一或多個活動。 兩種活動類型︰[資料移動活動](data-factory-data-movement-activities.md)和[資料轉換活動](data-factory-data-transformation-activities.md)。 在本教學課程中，您可以使用一個活動建立管線 (複製活動)。

![將 Azure Blob 複製到 Azure SQL Database](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

下節會提供完整的 Resource Manager 範本來定義 Data Factory 實體，如此您可以快速執行教學課程並測試範本。 若要了解每個 Data Factory 實體的定義方式，請參閱[範本中的 Data Factory 實體](#data-factory-entities-in-the-template)一節。

## <a name="data-factory-json-template"></a>Data Factory JSON 範本
用於定義資料處理站的最上層 Resource Manager 範本是︰ 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
在 **C:\ADFGetStarted** 資料夾中，使用下列內容建立名為 **ADFCopyTutorialARM.json** 的 JSON 檔案：

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureStorage",
              "description": "Azure Storage linked service",
              "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
              }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
              }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureBlob",
              "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Day",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Day",
                "interval": 1
              }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]",
              "[variables('azureSqlLinkedServiceName')]",
              "[variables('blobInputDatasetName')]",
              "[variables('sqlOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "activities": [
                {
                  "name": "CopyFromAzureBlobToAzureSQL",
                  "description": "Copy data frm Azure blob to Azure SQL",
                  "type": "Copy",
                  "inputs": [
                    {
                      "name": "[variables('blobInputDatasetName')]"
                    }
                  ],
                  "outputs": [
                    {
                      "name": "[variables('sqlOutputDatasetName')]"
                    }
                  ],
                  "typeProperties": {
                    "source": {
                      "type": "BlobSource"
                    },
                    "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                  },
                  "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                  }
                }
              ],
              "start": "2016-10-02T00:00:00Z",
              "end": "2016-10-03T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>參數 JSON
建立名為 **ADFCopyTutorialARM-Parameters.json** 的 JSON 檔案，其中包含 Azure Resource Manager 範本的參數。 

> [!IMPORTANT]
> 針對此參數檔案中的 **storageAccountName** 和 **storageAccountKey** 參數指定您 Azure 儲存體帳戶的名稱和金鑰。  
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the Azure SQL server>" },
        "databaseName": { "value": "<Name of the Azure SQL database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
    }
}
```

> [!IMPORTANT]
> 您可能需要不同的參數 JSON 檔案，以供可與相同 Data Factory JSON 範本共同使用的開發、測試和生產環境使用。 使用 Power Shell 指令碼，您可以在這些環境中自動部署 Data Factory 實體。  
> 
> 

## <a name="create-data-factory"></a>建立 Data Factory
1. 啟動 **Azure PowerShell** 並執行下列命令：
   * 執行下列命令並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。
    ```PowerShell
    Login-AzureRmAccount       
    ```  
   * 執行下列命令以檢視此帳戶的所有訂用帳戶。
    ```PowerShell
    Get-AzureRmSubscription
    ```   
   * 執行下列命令以選取您要使用的訂用帳戶。 
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```    
2. 執行下列命令，使用您在步驟 1 中建立的 Resource Manager 範本來部署 Data Factory 實體。

    ```PowerShell   
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>監視管線

1. 使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左功能表的 [Data Factory] \(或) 按一下 [更多服務] 然後按一下 [智慧 + 分析] 類別下的 [Data Factory]。
   
    ![Data Factory 功能表](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. 在 [Data factory] 頁面上，搜尋並尋找您的 Data Factory。 
   
    ![搜尋 Data Factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. 按一下您的 Azure Data Factory。 您會看到 Data Factory 的首頁。
   
    ![Data Factory 首頁](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. 按一下 [圖表] 圖格以查看您 Data Factory 的圖表檢視。
   
    ![Data Factory 的圖表檢視](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. 在 [圖表檢視] 中，按兩下 **SQLOutputDataset** 資料集。 您會看到該配量的狀態。 複製作業完成時，您將狀態設定為 [就緒]。
   
    ![輸出配量處於就緒狀態](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. 當配量處於 [就緒] 狀態時，請確認資料已複製到 Azure SQL Database 中的 **emp** 資料表。

如需如何使用 Azure 入口網站刀鋒視窗來監視您在本教學課程中建立的管線和資料集的指示，請參閱 [監視資料集和管線](data-factory-monitor-manage-pipelines.md) 。

您也可以使用「監視及管理應用程式」來監視您的資料管線。 如需有關使用應用程式的詳細資訊，請參閱 [使用監視應用程式來監視和管理 Azure Data Factory 管線](data-factory-monitor-manage-app.md) 。

## <a name="data-factory-entities-in-the-template"></a>範本中的 Data Factory 實體
### <a name="define-data-factory"></a>定義資料處理站
您可以在 Resource Manager 範本中定義資料處理站，如下列範例所示︰  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```

dataFactoryName 定義為： 

```json
"dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
```

根據資源群組識別碼，它是唯一的字串。  

### <a name="defining-data-factory-entities"></a>定義 Data Factory 實體
下列的 Data Factory 實體定義於 JSON 範本中︰ 

1. [Azure 儲存體連結服務](#azure-storage-linked-service)
2. [Azure SQL 連結服務](#azure-sql-database-linked-service)
3. [Azure blob 資料集](#azure-blob-dataset)
4. [Azure SQL 資料集](#azure-sql-dataset)
5. [具有複製活動的管線](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure 儲存體連結服務
在此區段中指定您 Azure 儲存體帳戶的名稱和金鑰。 如需用來定義 Azure 儲存體連結服務之 JSON 屬性的詳細資料，請參閱 [Azure 儲存體連結服務](data-factory-azure-blob-connector.md#azure-storage-linked-service)。 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```

connectionString 會使用 storageAccountName 和 storageAccountKey 參數。 使用組態檔傳遞這些參數的值。 定義也會使用在範本中定義的變數︰azureStroageLinkedService 和 dataFactoryName。 

#### <a name="azure-sql-database-linked-service"></a>Azure SQL Database 的連結服務
在此區段中指定 Azure SQL 伺服器名稱、資料庫名稱、使用者名稱和使用者密碼。 如需用來定義 Azure SQL 連結服務之 JSON 屬性的詳細資料，請參閱 [Azure SQL 連結服務](data-factory-azure-sql-connector.md#linked-service-properties)。  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlDatabase",
          "description": "Azure SQL linked service",
          "typeProperties": {
            "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
          }
    }
}
```

connectionString 會使用 sqlServerName、databaseName、sqlServerUserName 和 sqlServerPassword 參數，其值會使用組態檔傳遞。 定義也會使用下列來自範本的參數：azureSqlLinkedServiceName、dataFactoryName。

#### <a name="azure-blob-dataset"></a>Azure Blob 資料集
您可以指定 blob 容器、資料夾和包含輸入資料之檔案的名稱。 請參閱 [Azure Blob 資料集屬性](data-factory-azure-blob-connector.md#dataset-properties)，以取得用來定義 Azure Blob 資料集之 JSON 屬性的詳細資訊。 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]",
      "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Day",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Azure SQL 資料集
指定存放來自 Azure Blob 儲存體之複製資料的 Azure SQL Database 中的資料表名稱。 請參閱 [Azure SQL 資料集屬性](data-factory-azure-sql-connector.md#dataset-properties)，以取得用來定義 Azure SQL 資料集之 JSON 屬性的詳細資訊。 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Day",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>Data Pipeline
定義將資料從 Azure blob 資料集複製到 Azure SQL 資料集的管線。 請參閱[管線 JSON](data-factory-create-pipelines.md#pipeline-json)，以取得用來在此範例中定義管線的 JSON 元素之描述。 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('azureSqlLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "activities": [
        {
              "name": "CopyFromAzureBlobToAzureSQL",
              "description": "Copy data frm Azure blob to Azure SQL",
              "type": "Copy",
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('sqlOutputDatasetName')]"
            }
              ],
              "typeProperties": {
                "source": {
                      "type": "BlobSource"
                },
                "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                }
              },
              "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
              }
        }
          ],
          "start": "2016-10-02T00:00:00Z",
          "end": "2016-10-03T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>重複使用範本
在教學課程中，您可以建立定義 Data Factory 實體的範本和傳遞參數值的範本。 管線會將資料從 Azure 儲存體帳戶複製到透過參數指定的 Azure SQL Database。 若要使用相同的範本將 Data Factory 實體部署至不同的環境，您可以針對每個環境建立參數檔案，並在部署到該環境時使用它。     

範例：  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

請注意，第一個命令會使用開發環境的參數檔案，第二個會使用測試環境的參數檔案，而第三個會使用生產環境的參數檔案。  

您也可以重複使用範本來執行重複的工作。 例如，您需要使用一個或多個管線建立許多資料處理站，這些管線會實作相同的邏輯，但每個資料處理站會使用不同的儲存體和 SQL Database 帳戶。 在此案例中，您會在具有不同參數檔案的相同環境中 (開發、測試或生產) 使用相同的範本來建立資料處理站。   

## <a name="see-also"></a>另請參閱
| 主題 | 說明 |
|:--- |:--- |
| [管線](data-factory-create-pipelines.md) |本文協助您了解 Azure Data Factory 中的管線和活動。 |
| [資料集](data-factory-create-datasets.md) |本文協助您了解 Azure Data Factory 中的資料集。 |
| [排程和執行](data-factory-scheduling-and-execution.md) |本文說明 Azure Data Factory 應用程式模型的排程和執行層面。 |
