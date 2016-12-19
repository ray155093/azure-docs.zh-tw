---
title: "使用 Data Factory 從 Amazon Simple Storage Service 移動資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 從 Amazon Simple Storage Service (S3) 移動資料。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: c2350ae447ccebf1a6b85a563e7fa1d7c12b16d7
ms.openlocfilehash: 05a9466ba2a2d4a495d2e9a4f3ca4c9d08ddcadb


---
# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>使用 Azure Data Factory 從 Amazon Simple Storage Service 移動資料
本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料從 Amazon Simple Storage Service (S3) 移到另一個資料存放區。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動來移動資料時的一般概觀，以及所支援的來源/接收資料存放區清單。  

Data Factory 目前只支援將資料從 Amazon S3 移到其他資料存放區，而不支援將資料從其他資料存放區移到 Amazon S3。

## <a name="required-permissions"></a>所需的權限
若要從 Amazon S3 複製資料，請確定您已獲得下列權限︰

* 適用於 Amazon S3 物件作業的 **s3:GetObject** 和 **s3:GetObjectVersion**
* 適用於 Amazon S3 貯體作業的 **s3:ListBucket** 和 **s3:ListAllMyBuckets** (僅用於複製精靈)

您可以從[在原則中指定權限](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)找到 Amazon S3 權限的完整清單和詳細資料。

## <a name="copy-data-wizard"></a>複製資料精靈
若要建立從 Amazon S3 複製資料的管線，最簡單的方法就是使用複製資料精靈。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

下列範例提供您使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 來建立管線時，可使用的範例 JSON 定義。 它將示範如何將資料從 Amazon S3 複製到「Azure Blob 儲存體」。 不過，您可以將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>範例：將資料從 Amazon S3 複製到 Azure Blob
此範例示範如何將資料從 Amazon S3 複製到「Azure Blob 儲存體」。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。  

此範例具有下列 Data Factory 實體：

* [AwsAccessKey](#linked-service-properties)類型的連結服務。
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)類型的連結服務。
* [AmazonS3](#dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
* 具有使用 [FileSystemSource](#copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將資料從 Amazon S3 複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Amazon S3 連結服務**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
            }
        }
    }

**Azure 儲存體連結服務**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Amazon S3 輸入資料集**

設定 **"external": true** 會告知 Data Factory 服務該資料集是 Data Factory 外部的資料集，而不是由 Data Factory 中的活動所產生。 在不是由管線中的活動所產生的輸入資料集上，請將此屬性設定為 true。

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }



**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **FileSystemSource**，而 **sink** 類型設為 **BlobSink**。

    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>連結服務屬性
下表提供 Amazon S3 (**AwsAccessKey**) 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| accessKeyID |密碼存取金鑰的識別碼。 |string |是 |
| secretAccessKey |密碼存取金鑰本身。 |加密的密碼字串 |是 |

## <a name="dataset-type-properties"></a>資料集類型屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 所有資料集類型 (Azure SQL、Azure Blob、Azure 資料表等) 的結構、可用性及原則等區段都相似。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。 **AmazonS3** 類型之資料集 (包括 Amazon S3 資料集) 的 typeProperties 區段具有下列屬性

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| bucketName |S3 貯體名稱。 |string |是 |
| key |S3 物件索引鍵。 |string |否 |
| prefix |S3 物件索引鍵的前置詞。 系統會選取索引鍵以此前置詞開頭的物件。 只有當索引鍵空白時才適用。 |string |否 |
| version |如果已啟用 S3 版本設定功能，則為 S3 物件的版本。 |string |否 |
| format |支援下列格式類型：**TextFormat**、**AvroFormat**、**JsonFormat**、**OrcFormat**、**ParquetFormat**。 將格式下的 **type** 屬性設定為這些值其中之一。 如需詳細資料，請參閱[指定 TextFormat](#specifying-textformat)、[指定 AvroFormat](#specifying-avroformat)、[指定 JsonFormat](#specifying-jsonformat)、[指定 OrcFormat](#specifying-orcformat)、[指定 ParquetFormat](#specifying-parquetformat) 各節。 如果您想要在以檔案為基礎的存放區之間依原樣複製檔案 (二進位複本)，您可以在輸入和輸出資料集定義中略過格式區段。 |否 | |
| compression |指定此資料的壓縮類型和層級。 支援的類型為：**GZip**、**Deflate** 和 **BZip2**，而支援的層級為：**最佳**和**最快**。 **AvroFormat** 或 **OrcFormat** 格式的資料目前不支援壓縮設定。 如需詳細資訊，請參閱 [壓縮支援](#compression-support) 一節。 |否 | |

> [!NOTE]
> bucketName + key 可指定 S3 物件的位置，其中貯體是 S3 物件的根容器，而索引鍵是 S3 物件的完整路徑。
>
>

### <a name="sample-dataset-with-prefix"></a>prefix 的相關範例資料集
    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-with-version"></a>version 的相關範例資料集
    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>S3 的動態路徑
在此範例中，我們針對 Amazon S3 資料集內的 key 和 bucketName 屬性使用固定的值。

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

您可以藉由使用系統變數 (例如 SliceStart)，讓 Data Factory 在執行階段動態地計算 key 和 bucketName 的值。

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

您也可以對 Amazon S3 資料集的 prefix 屬性執行相同的操作。 如需支援的函式和變數清單，請參閱 [Data Factory 函式與系統變數](data-factory-functions-variables.md) 。

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="copy-activity-type-properties"></a>複製活動類型屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

另一方面，活動的 **typeProperties** 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

當複製活動中的來源類型為 **FileSystemSource** (包括 Azure S3) 時，typeProperties 區段中會有下列可用屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| 遞迴 |指定是否要以遞迴方式列出目錄下的 S3 物件。 |true/false |否 |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

* [複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ，以取得使用「複製活動」來建立管線的逐步指示。



<!--HONumber=Nov16_HO3-->


