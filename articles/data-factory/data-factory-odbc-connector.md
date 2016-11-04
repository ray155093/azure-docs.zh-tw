---
title: 從 ODBC 資料存放區移動資料 | Microsoft Docs
description: 深入了解如何使用 Azure Data Factory 從 ODBC 資料存放區移動資料
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: jingwang

---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>使用 Azure Data Factory 從 ODBC 資料存放區移動資料
本文將概述如何使用 Azure Data Factory 中的複製活動將資料從內部部署 ODBC 資料存放區移動到另一個資料存放區。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

Data Factory 目前只支援將資料從內部部署 ODBC 資料存放區移到其他資料存放區。 不支援將資料從其他資料存放區移到內部部署 ODBC 資料存放區。

## <a name="enabling-connectivity"></a>啟用連線
Data Factory 服務支援使用資料管理閘道器連接至內部部署 ODBC 來源。 請參閱 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文來了解資料管理閘道和設定閘道的逐步指示。 即使 ODBC 資料存放區裝載於 Azure IaaS VM 中，仍請使用閘道來與其連接。 

您可以將閘道安裝在與 ODBC 資料存放區相同的內部部署機器或 Azure VM 上。 不過，建議您將閘道安裝在個別的機器/Azure IaaS VM 上，除了可避免發生資源爭用的情況之外，也可獲得較佳的效能。 當您在不同機器上安裝閘道器時，機器應該能夠存取具有 ODBC 資料存放區的機器。 

除了資料管理閘道器，您也需要在閘道器機器上的資料存放區安裝 ODBC 驅動程式。 

> [!NOTE]
> 如需連接/閘道器相關問題的疑難排解秘訣，請參閱 [針對閘道問題進行疑難排解](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) 。 
> 
> 

## <a name="copy-data-wizard"></a>複製資料精靈
要建立從 ODBC 來源複製資料的管線，最簡單的方法是使用複製資料精靈。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。 

以下範例提供可用來使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 建立管線的範例 JSON 定義。 這些範例示範如何將資料從 ODBC 來源複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動，將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores) 所說的任何接收器。

## <a name="sample:-copy-data-from-odbc-data-store-to-azure-blob"></a>範例：從 ODBC 資料存放區複製資料到 Azure Blob
此範例示範如何將資料從 ODBC 資料存放區複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores) 所說的任何接收器。  

此範例具有下列 Data Factory 實體：

1. [OnPremisesOdbc](#odbc-linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)類型的連結服務。
3. [RelationalTable](#odbc-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [RelationalSource](#odbc-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每個小時將資料從 ODBC 資料存放區中的查詢結果複製到 Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。 

第一步是設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。 

**ODBC 連結服務** 此範例會使用 Basic 驗證。 請參閱 [ODBC 連結服務](#odbc-linked-service-properties) 章節以了解您可以使用的各種不同類型的驗證。 

    {
        "name": "OnPremOdbcLinkedService",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
                "userName": "username",
                "password": "password",
                "gatewayName": "mygateway"
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

**ODBC 輸入資料集**

此範例假設您已在 ODBC 資料庫中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。

設定 “external”: ”true” 會通知 Data Factory 服務：這是 Data Factory 外部的資料集而且不是由 Data Factory 中的活動所產生。

    {
        "name": "ODBCDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremOdbcLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }



**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

    {
        "name": "AzureBlobOdbcDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

此管線包含「複製活動」，該活動已設定為使用這些輸入和輸出資料集，並且排定為每小時執行。 在管線 JSON 定義中，已將 **source** 類型設為 **RelationalSource**，並將 **sink** 類型設為 **BlobSink**。 針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

    {
        "name": "CopyODBCToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "OdbcDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOdbcDataSet"
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
                    "name": "OdbcToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="odbc-linked-service-properties"></a>ODBC 連結服務屬性
下表提供 ODBC 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| 類型 |類型屬性必須設為： **OnPremisesOdbc** |是 |
| connectionString |連接字串的非存取認證部分和選擇性的加密認證。 請參閱下列幾節中的範例。 |是 |
| 認證 |以驅動程式特定「屬性-值」格式指定之連接字串的存取認證部分。 範例：“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”。 |否 |
| authenticationType |用來連接到 ODBC 資料存放區的驗證類型。 可能的值為：Anonymous 和 Basic。 |是 |
| username |如果您要使用 Basic 驗證，請指定使用者名稱。 |否 |
| password |指定您為使用者名稱所指定之使用者帳戶的密碼。 |否 |
| gatewayName |Data Factory 服務應該用來連接到 ODBC 資料存放區的閘道器名稱。 |是 |

如需為內部部署 ODBC 資料存放區設定認證的詳細資料，請參閱 [設定認證和安全性](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) 。

### <a name="using-basic-authentication"></a>使用基本驗證
    {
        "name": "odbc",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
                "userName": "username",
                "password": "password",
                "gatewayName": "mygateway"
            }
        }
    }

### <a name="using-basic-authentication-with-encrypted-credentials"></a>使用基本驗證與加密認證
您可以使用 [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (1.0 版的 Azure PowerShell) Cmdlet 或 [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 或更舊版的 Azure PowerShell) 來加密認證。  

    {
        "name": "odbc",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-anonymous-authentication"></a>使用匿名驗證
    {
        "name": "odbc",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
                "credential": "UID={uid};PWD={pwd}",
                "gatewayName": "mygateway"
            }
        }
    }



## <a name="odbc-dataset-type-properties"></a>ODBC 資料集類型屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。 **RelationalTable** 資料集類型的 typeProperties 區段 (包含 ODBC 資料集) 具有下列屬性

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| tableName |ODBC 資料存放區中資料表的名稱。 |是 |

## <a name="odbc-copy-activity-type-properties"></a>ODBC 複製活動類型屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。 

另一方面，活動的 **typeProperties** 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

在複製活動中，如果來源的類型為 **RelationalSource** (包括 ODBC)，則 typeProperties 區段中可使用下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| query |使用自訂查詢來讀取資料。 |SQL 查詢字串。 例如：select * from MyTable。 |是 |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odbc"></a>ODBC 的類型對應
如 [資料移動活動](data-factory-data-movement-activities.md) 一文所述，複製活動會藉由下列含有兩個步驟的方法，執行從來源類型轉換成接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 ODBC 資料存放區移動資料時，ODBC 資料類型會對應至 .NET 類型，如 [ODBC 資料類型對應](https://msdn.microsoft.com/library/cc668763.aspx) 主題中所述。

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="ge-historian-store"></a>GE Historian 存放區
您建立 ODBC 連結服務，將 [GE Proficy Historian (現為 GE Historian)](http://www.geautomation.com/products/proficy-historian) 資料存放區連結至 Azure Data Factory，如下例所示︰ 

    {
        "name": "HistorianLinkedService",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "connectionString": "DSN=<name of the GE Historian store>",
                "gatewayName": "<gateway name>",
                "authenticationType": "Basic",
                "userName": "<user name>",
                "password": "<password>"
            }
        }
    }

將「資料管理閘道」安裝在內部部署機器上，並向入口網站註冊該閘道。 安裝在內部部署電腦上的閘道會使用適用於 GE Historian 的 ODBC 驅動程式來連接到 GE Historian 資料存放區。 因此，如果尚未在閘道機器上安裝該驅動程式，請安裝它。 如需詳細資訊，請參閱 [啟用連線](#enabling-connectivity) 一節。

使用 Data Factory 方案中的 GE Historian 存放區之前，請先確認閘道器可否使用下節中的指示連接到資料存放區。 

如需在複製作業中將 ODBC 資料存放區用做來源資料存放區的詳細概觀，請從頭閱讀本文。  

## <a name="troubleshoot-connectivity-issues"></a>疑難排解連線問題
若要針對連線問題進行疑難排解，請使用 [資料管理閘道器組態管理員] 的 [診斷] 索引標籤。 

1. 啟動 **資料管理閘道器組態管理員**。 您可以直接執行 "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe" (或) 搜尋**閘道**，以尋找 **Microsoft 資料管理閘道**應用程式的連結，如下圖所示。 
   
    ![搜尋閘道器](./media/data-factory-odbc-connector/search-gateway.png)
2. 切換至 [診斷]  索引標籤。
   
    ![閘道診斷](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png) 
3. 選取資料存放區的 **類型** (連結的服務)。 
4. 指定 [驗證]，然後輸入用來連線到資料存放區的**認證** (或) 輸入**連接字串**。 
5. 按一下 [測試連線]  以測試資料存放區連線。 

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

<!--HONumber=Oct16_HO2-->


