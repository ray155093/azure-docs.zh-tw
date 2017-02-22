---
title: "使用 Data Factory 對 Oracle 往返移動資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 從內部部署 Oracle 資料庫來回移動資料。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: a4b067e732bccb01faa96f23dbfd2ed65b7711a0
ms.openlocfilehash: 62326da2e801a7c6e01d29e2298bd3552f331647


---
# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>使用 Azure Data Factory 對內部部署的 Oracle 往返移動資料
本文概述如何使用 Data Factory 複製活動將資料從 Oracle 移動與資料存放區之間往返移動。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

Data Factory 支援使用資料管理閘道連接至內部部署 Oracle 來源。 請參閱[資料管理閘道](data-factory-data-management-gateway.md)一文來了解資料管理閘道，以及[將資料從內部部署移動到雲端](data-factory-move-data-between-onprem-and-cloud.md)一文來取得設定資料管線的閘道以移動資料的逐步指示。

> [!NOTE]
> 即使 Oracle 裝載於 Azure IaaS VM 中，也必須要有閘道。 您可以將閘道安裝在與資料存放區相同或相異的 IaaS VM 上，只要閘道可以連線到資料庫即可。
>

## <a name="supported-versions-and-installation"></a>支援的版本和安裝
Oracle 連接器支援兩種驅動程式版本︰

- **適用於 Oracle 的 Microsoft 驅動程式**自 2.7 版起即隨附於資料管理閘道。 **建議**您使用此驅動程式。 如此一來，除了將閘道連線到 Oracle，就不需要安裝任何其他項目，而且您也能夠體驗更好的複製效能。 支援 Oracle Database 10g Release 2 版或更新版本。

    > [!NOTE]
    > 目前適用於 Oracle 的 Microsoft 驅動程式僅支援從 Oracle 複製資料，但不支援將資料寫入 Oracle。 請注意，[資料管理閘道診斷] 索引標籤中的測試連線功能不支援此驅動程式。 您可以選擇使用複製精靈來驗證連線。
    >

- **.NET 的 Oracle 資料提供者︰**您也可以選擇使用 Oracle 資料提供者，從 Oracle 複製資料/將資料複製到 Oracle。 此元件包含於 [適用於 Windows 的 Oracle 資料存取元件](http://www.oracle.com/technetwork/topics/dotnet/downloads/)中。 在安裝閘道的電腦上安裝適當版本 (32/64 位元)。 [Oracle Data Provider .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) 可以存取 Oracle Database 10g Release 2 或更新版本。

    如果您選擇「XCopy 安裝」，請依照 readme.htm 中的步驟進行。 建議您選擇含有 UI 的安裝程式 (非 XCopy 安裝)。

    安裝提供者之後，請使用 [服務] 小程式 (或) 資料管理閘道組態管理員，「重新啟動」您電腦上的資料管理閘道主機服務。  

## <a name="copy-data-wizard"></a>複製資料精靈
若要建立管線以將資料從 Oracle 資料庫複製到任何支援的接收資料存放區，最簡單的方式是使用複製資料精靈。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

下列範例提供您使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 來建立管線時，可使用的範例 JSON 定義。 這些範例示範如何將資料從 Oracle 資料庫複製到 Azure Blob 儲存體，以及複製其中的資料。 不過，您可以在 Azure Data Factory 中使用複製活動，將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>範例：從 Oracle 複製資料到 Azure Blob
此範例示範如何將資料從內部部署 Oracle 資料庫複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。  

此範例具有下列 Data Factory 實體：

1. [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)類型的連結服務。
3. [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) 做為來源和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 做為接收之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將資料從內部部署 Oracle 資料庫中的資料表移動到 Blob。 如需範例中使用的各種屬性的詳細資訊，請參閱範例後面幾節中的文件。

**Oracle 連結服務：**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob 儲存體連結服務：**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Oracle 輸入資料集：**

此範例假設您已在 Oracle 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。

設定 “external”: ”true” 會通知 Data Factory 服務：這是 Data Factory 外部的資料集而且不是由 Data Factory 中的活動所產生。

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
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
```

**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **OracleSource**，而 **sink** 類型設為 **BlobSink**。  利用 **oracleReaderQuery** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>範例：從 Azure Blob 複製資料到 Oracle
此範例示範如何將資料從 Azure Blob 儲存體複製到內部部署 Oracle 資料庫。 不過，您可以在 Azure Data Factory 中使用複製活動，**直接**從[這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所說的任何來源複製資料。  

此範例具有下列 Data Factory 實體：

1. [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)類型的連結服務。
3. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 做為來源和 [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) 做為接收之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將資料從 Blob移動到內部部署 Oracle 資料庫中的資料表。 如需範例中使用的各種屬性的詳細資訊，請參閱範例後面幾節中的文件。

**Oracle 連結服務：**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob 儲存體連結服務：**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Azure Blob 輸入資料集**

每小時從新的 Blob 挑選資料 (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。 資料夾路徑使用開始時間的年、月、日部分，而檔案名稱使用開始時間的小時部分。 “external”: “true” 設定會通知 Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
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
```

**Oracle 輸出資料集：**

此範例假設您已在 Oracle 資料表中建立資料表 "MyTable"。 請在 Oracle 中建立此資料表，其資料行的數目如您預期 Blob CSV 檔案要包含的數目。 此資料表會每小時加入新的資料列。

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **BlobSource**，而 **sink** 類型設為 **OracleSink**。  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="oracle-linked-service-properties"></a>Oracle 連結服務屬性
下表提供 Oracle 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| 類型 |類型屬性必須設為： **OnPremisesOracle** |是 |
| driverType | 指定要用來從 Oracle 複製資料/將資料複製到 Oracle 資料庫的驅動程式。 允許的值為 **Microsoft** 或 **ODP** (預設值)。 如需驅動程式詳細資料，請參閱[支援的版本和安裝](#supported-versions-and-installation)一節。 | 否 |
| connectionString | 針對 connectionString 屬性指定連接到 Oracle 資料庫執行個體所需的資訊。 請參閱以下範例。 | 是 |
| gatewayName | 用來連接到內部部署 Oracle 伺服器的閘道器名稱 |yes |

如需設定內部部署 Oracle 資料來源認證的詳細資訊，請參閱[利用資料管理閘道在內部部署來源和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)。

**範例︰使用 Microsoft 驅動程式**
```JSON
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**範例︰使用 ODP 驅動程式**

如需更多允許的格式，請參閱[此網站](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/)。
```JSON
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="oracle-dataset-type-properties"></a>Oracle 資料集類型屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (Oracle、Azure Blob、Azure 資料表等)。

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。 OracleTable 資料集類型的 typeProperties 區段具有下列屬性：

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| tableName |Oracle 資料庫中連結服務所參照的資料表名稱。 |否 (如果已指定 **OracleSource** 的 **oracleReaderQuery**) |

## <a name="oracle-copy-activity-type-properties"></a>Oracle 複製活動類型屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

> [!NOTE]
> 複製活動只會採用一個輸入，而且只產生一個輸出。
>
>

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

### <a name="oraclesource"></a>oracleReaderQuery
在複製活動中，如果來源的類型為 **OracleSource**，則 **typeProperties** 區段有下列可用屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| oracleReaderQuery |使用自訂查詢來讀取資料。 |SQL 查詢字串。 例如：select 若未指定，執行的 SQL 陳述式即為 select 若未指定，執行的 SQL 陳述式即為 select from MyTable * 若未指定，執行的 SQL 陳述式即為 select 若未指定，執行的 SQL 陳述式即為 select from MyTable <br/><br/> * 若未指定，執行的 SQL 陳述式即為 select 若未指定，執行的 SQL 陳述式即為 select from MyTable |否 (如果已指定 **dataset** 的 **tableName**) |

### <a name="oraclesink"></a>管線
**OracleSink** 支援下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| writeBatchTimeout |在逾時前等待批次插入作業完成的時間。 |時間範圍<br/><br/> 範例：00:30:00 (30 分鐘)。 |否 |
| writeBatchSize |當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中 |整數 (資料列數目) |否 (預設值：100) |
| sqlWriterCleanupScript |指定要讓「複製活動」執行的查詢，以便清除特定分割的資料。 |查詢陳述式。 |否 |
| sliceIdentifierColumnName |指定要讓「複製活動」以自動產生的分割識別碼填入的資料行名稱，這可在重新執行時用來清除特定分割的資料。 |資料類型為 binary(32) 之資料行的資料行名稱。 |否 |

## <a name="troubleshooting-tips"></a>疑難排解秘訣
### <a name="problem-1-net-framework-data-provider"></a>問題 1：.NET Framework Data Provider

您看到下列「錯誤訊息」：

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**可能的原因：**

1. 未安裝 .NET Framework Data Provider for Oracle。
2. .NET Framework Data Provider for Oracle 已安裝於 .NET Framework 2.0，而且在 .NET Framework 4.0 資料夾中找不到。

**解析/因應措施：**

1. 如果您尚未安裝 .NET Provider for Oracle，請 [安裝它](http://www.oracle.com/technetwork/topics/dotnet/downloads/) ，然後重試此案例。
2. 如果您即使在安裝提供者之後還是會收到錯誤訊息，請執行下列步驟：
   1. 從資料夾開啟 .NET 2.0 的電腦組態：<system disk>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config。
   2. 搜尋 **Oracle Data Provider for .NET**，而您應該能夠在 **system.data** -> **DbProviderFactories** 下方，找到如下列範例所示的項目：“<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />」的作法指南。
3. 將此項目複製到下列 v4.0 資料夾中的 machine.config 檔案 : <system disk>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config，並將版本變更為 4.xxx.x.x。
4. 執行 `gacutil /i [provider path]`，將 “<ODP.NET 安裝路徑>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll” 安裝到全域組件快取 (GAC)。## 疑難排解祕訣

### <a name="problem-2-datetime-formatting"></a>問題 2︰日期時間格式

您看到下列「錯誤訊息」：

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**解析/因應措施：**

您可能需要根據 Oracle 資料庫中日期的設定方式，調整複製活動中的查詢字串，如下列範例 (使用 to_date 函式) 所示︰

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Oracle 的類型對應
如同 [資料移動活動](data-factory-data-movement-activities.md) 一文所述，複製活動會使用下列 2 個步驟的方法，執行自動類型轉換，將來源類型轉換成接收類型：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 Oracle 移動資料時，會使用下列從 Oracle 資料類型到 .NET 類型的對應，反之亦然。

| Oracle 資料類型 | .NET Framework 資料類型 |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |String |
| CLOB |String |
| 日期 |DateTime |
| FLOAT |十進位 |
| INTEGER |十進位 |
| 間隔年至月 |Int32 |
| 間隔日至秒鐘 |時間範圍 |
| 長 |String |
| 長 RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| 數字 |十進位 |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| 時間戳記 |DateTime |
| 本地時區的時間戳記 |DateTime |
| 時區的時間戳記 |DateTime |
| 不帶正負號的整數 |數字 |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 使用 Microsoft 驅動程式時，不支援資料類型 **INTERVAL YEAR TO MONTH** 和 **INTERVAL DAY TO SECOND**。
>

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。



<!--HONumber=Feb17_HO1-->


