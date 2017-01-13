---
title: "從 FTP 伺服器移動資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 從 FTP 伺服器移動資料。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: f27d978eab3aba52ee9a51a5f284b592e3edd6af


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>使用 Azure Data Factory 從 FTP 伺服器移動資料
本文概述如何使用 Azure Data Factory 中的複製活動，將 FTP 伺服器中的資料移動到支援的接收資料存放區。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md)一文，該文呈現使用複製活動移動資料的一般概觀以及支援作為來源/接收的資料存放區清單。

Data Factory 目前只支援將資料從 FTP 伺服器移到其他資料存放區，而不支援將資料從其他資料存放區移到 FTP 伺服器。 它支援內部部署和雲端 FTP 伺服器。

如果您要將資料從**內部部署** FTP 伺服器移到雲端資料存放區 (範例：Azure Blob 儲存體)，請安裝並使用資料管理閘道。 資料管理閘道是安裝在內部部署電腦上的用戶端代理程式，允許雲端服務連接至內部部署資源。 如需資料管理閘的詳細資料，請參閱[資料管理閘道](data-factory-data-management-gateway.md)。 請參閱[在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文來了解設定和使用閘道的逐步指示。 即使伺服器位於 Azure IaaS 虛擬機器 (VM) 上，您還是可以使用閘道連接至 FTP 伺服器。

您可以將閘道安裝在與 FTP 伺服器相同的內部部署電腦或 Azure IaaS VM 上。 不過，建議您將閘道安裝在個別的電腦或個別的 Azure IaaS VM 上，除了可避免發生資源爭用的情況之外，也可獲得較佳的效能。 當您在不同電腦上安裝閘道時，電腦應該能夠存取 FTP 伺服器。

## <a name="copy-data-wizard"></a>複製資料精靈
要建立從 FTP 伺服器複製資料的管線，最簡單的方法是使用複製資料精靈。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

以下範例提供可用來使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 建立管線的範例 JSON 定義。

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>範例：將資料從 FTP 伺服器複製至 Azure Blob
此範例示範如何將資料從 FTP 伺服器複製至 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。  

此範例具有下列 Data Factory 實體：

* [FtpServer](#ftp-linked-service-properties) 類型的連結服務。
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)類型的連結服務。
* [FileShare](#fileshare-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
* 具有使用 [FileSystemSource](#ftp-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

範例會每隔一小時就把 FTP 伺服器的資料複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**FTP 連結服務** 此範例會利用純文字使用者名稱和密碼來使用基本驗證。 您也可以使用下列其中一種方式：

* 匿名驗證
* 基本驗證與加密認證
* 透過 SSL/TLS 的 FTP (FTPS)

請參閱 [FTP 連結服務](#ftp-linked-service-properties)一節，來了解您可以使用的不同驗證類型。

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
**Azure 儲存體連結服務**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**FTP 輸入資料集** 此資料集指的是 FTP 資料夾 `mysharedfolder` 和 `test.csv` 檔案。 管線會將檔案複製至目的地。

設定 "external": "true" 會通知 Data Factory 服務：這是 Data Factory 外部的資料集而且不是由 Data Factory 中的活動所產生。

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
```


**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **FileSystemSource**，而 **sink** 類型設為 **BlobSink**。

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```

## <a name="ftp-linked-service-properties"></a>FTP 連結服務屬性
下表提供 FTP 連結服務專屬 JSON 元素的說明。

| 屬性 | 說明 | 必要 | 預設值 |
| --- | --- | --- | --- |
| 類型 |類型屬性必須設定為 FtpServer |是 |&nbsp; |
| 主機 |FTP 伺服器的名稱或 IP 位址 |是 |&nbsp; |
| authenticationType |指定驗證類型 |是 |基本或匿名 |
| username |可存取 FTP 伺服器的使用者 |否 |&nbsp; |
| password |使用者 (使用者名稱) 的密碼 |否 |&nbsp; |
| encryptedCredential |用來存取 FTP 伺服器的加密認證 |否 |&nbsp; |
| gatewayName |連接至內部部署 FTP 伺服器的「資料管理閘道」閘道 |否 |&nbsp; |
| 連接埠 |FTP 伺服器所接聽的連接埠 |否 |21 |
| enableSsl |指定是否使用透過 SSL/TLS 的 FTP 通道 |否 |true |
| enableServerCertificateValidation |指定是否在使用透過 SSL/TLS 的 FTP 通道時啟用伺服器 SSL 憑證驗證 |否 |true |

### <a name="using-anonymous-authentication"></a>使用匿名驗證

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>使用純文字的使用者名稱和密碼進行基本驗證

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Using port, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>針對驗證和閘道使用 encryptedCredential

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

如需設定內部部署 FTP 資料來源認證的詳細資訊，請參閱[利用資料管理閘道在內部部署來源和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)。

[!INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>FTP 複製活動類型屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 就複製活動而言，類型屬性會根據來源和接收的類型而有所不同。

[!INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

* [複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ，以取得使用「複製活動」來建立管線的逐步指示。



<!--HONumber=Nov16_HO3-->


