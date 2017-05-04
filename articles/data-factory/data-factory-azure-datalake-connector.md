---
title: "從 Azure Data Lake Store 來回移動資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 從 Azure Data Lake Store 來回移動資料"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 3f0575a170eb20d136858bedc1f87d4f4375c812
ms.lasthandoff: 04/15/2017


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>使用 Azure Data Factory 從 Azure Data Lake Store 來回移動資料
本文說明如何使用 Azure Data Factory 中的「複製活動」，將資料移進/移出 Azure Data Lake Store。 本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文提供使用複製活動來移動資料的一般概觀。

您可以將資料從任何支援的來源資料存放區複製到 Azure Data Lake Store，或從 Azure Data Lake Store 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表格。  

> [!NOTE]
> 請先建立 Azure Data Lake Store 帳戶，再透過複製活動建立管線，以在 Azure Data Lake Store 中移入/移出資料。 若要了解 Azure Data Lake Store，請參閱 [開始使用 Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)。

## <a name="supported-authentication-types"></a>支援的驗證類型
Azure Data Lake Store 連接器支援「服務主體」驗證和「使用者認證」(OAuth)驗證。 尤其是進行排程的資料複製時，建議您使用前者，避免使用後者而發生權杖到期行為。 參閱組態詳細資料中的[連結服務屬性](#linked-service-properties)區段。

## <a name="getting-started"></a>開始使用
您可以藉由使用不同的工具/API，建立內含複製活動的管線，以將資料移進/移出 Azure Data Lake Store。

建立管線的最簡單方式就是使用「複製精靈」。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

您也可以使用下列工具來建立管線︰**Azure 入口網站**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 範本**、**.NET API** 及 **REST API**。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

不論您是使用工具還是 API，都需執行下列步驟來建立將資料從來源資料存放區移到接收資料存放區的管線：

1. 建立**連結服務**，將輸入和輸出資料存放區連結到資料處理站。
2. 建立**資料集**，代表複製作業的輸入和輸出資料。
3. 建立**管線**，其中含有以一個資料集作為輸入、一個資料集作為輸出的複製活動。

使用精靈時，精靈會自動為您建立這些 Data Factory 實體 (已連結的服務、資料集及管線) 的 JSON 定義。 使用工具/API (.NET API 除外) 時，您需使用 JSON 格式來定義這些 Data Factory 實體。  如需相關範例，其中含有用來將資料複製到 Azure Data Lake Store (或從 Azure Data Lake Store 複製資料) 之 Data Factory 實體的 JSON 定義，請參閱本文的 [JSON 範例](#json-examples)一節。

下列各節提供 JSON 屬性的相關詳細資料，這些屬性是用來定義 Azure Data Lake Store 特定的 Data Factory 實體：

## <a name="linked-service-properties"></a>連結服務屬性
已連結的服務會將資料存放區連結到 Data Factory。 您需建立 **AzureDataLakeStore** 類型的已連結服務，以將 Azure Data Lake Store 連結到 Data Factory。 下表提供 Azure Data Lake Store 連結服務專屬 JSON 元素的描述，並可在「服務主體」和「使用者認證」驗證之間擇一。

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | type 屬性必須設為： **AzureDataLakeStore** | 是 |
| dataLakeStoreUri | 指定有關 Azure Data Lake Store 帳戶的資訊。 它的格式如下：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 所屬的 Azure 訂用帳戶識別碼。 | 接收 (Sink) 的必要項目 |
| resourceGroupName | Data Lake Store 所屬的 Azure 資源群組名稱。 | 接收 (Sink) 的必要項目 |

### <a name="using-service-principal-authentication-recommended"></a>使用服務主體驗證 (建議)
若要使用服務主體驗證，請在 Azure Active Directory (AAD) 中註冊應用程式實體，並授與它 Data Lake Store 的存取權。 請參閱[服務對服務驗證](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)以取得詳細步驟。 請記錄下列值：「應用程式識別碼」、「應用程式金鑰」及「租用戶識別碼」。 您會將此資訊用於定義已連結的服務。

> [!IMPORTANT]
> 如果您使用複製精靈來撰寫數據管道，請務必為服務主體至少授與 Data Lake Store 帳戶存取控制 (IAM) 中的「讀者」角色，並至少授與針對 Data Lake Store 根 ("/") 及其子系的「讀取+執行」權限。 否則，您可能會看到「提供的認證不正確」錯誤。
>
> 在您建立/更新 AAD 中的服務主體之後，變更可能需要幾分鐘才會實際生效。 首先，請再次檢查服務主體和 Data Lake Store ACL 設定。 如果您仍然看見錯誤：「提供的認證無效」，請稍候片刻並再試一次。

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是 |

**範例：使用服務主體驗證**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="using-user-credential-authentication"></a>使用使用者認證驗證
或者，您也可以藉由指定下列屬性，使用使用者認證驗證從 Data Lake Store 複製，或是複製到 Data Lake Store：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 授權 | 按一下 [Data Factory 編輯器] 中的 [授權] 按鈕，然後輸入您的認證，此動作會將自動產生的授權 URL 指派給此屬性。 | 是 |
| sessionId | OAuth 授權工作階段的 OAuth 工作階段識別碼。 每個工作階段識別碼都是唯一的，只能使用一次。 當您使用 Data Factory 編輯器時便會自動產生此設定。 | 是 |

**範例︰使用使用者認證驗證**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>權杖到期
您使用 [授權] 按鈕所產生的授權碼在一段時間後會到期。 請參閱下表以了解不同類型的使用者帳戶的到期時間。 當驗證的「權杖到期時」，您可能會看見下列錯誤訊息：

```
"Credential operation error: invalid_grant - AADSTS70002: Error validating credentials. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z".
```

| 使用者類型 | 到期時間 |
|:--- |:--- |
| 不受 Azure Active Directory 管理的使用者帳戶 (@hotmail.com、@live.com 等)。 |12 小時 |
| 受 Azure Active Directory (AAD) 管理的使用者帳戶 |最後一次執行配量後的 14 天。 <br/><br/>如果以 OAuth 式連結服務為基礎的配量至少每 14 天執行一次，則為 90 天。 |

如果您在此權杖的到期時間之前變更密碼，權杖會立即到期，且您會看到本節所述的錯誤。

如果要避免/解決此錯誤，請在**權杖到期**時使用 [授權] 按鈕重新授權，然後重新部署連結服務。 您也可以使用下一節中的程式碼以程式設計方式產生 **sessionId** 和 **authorization** 屬性的值：

#### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>若要以程式設計方式產生 sessionId 與 authorization 的值

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
請參閱 [AzureDataLakeStoreLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、[AzureDataLakeAnalyticsLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)和 [AuthorizationSessionGetResponse 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)主題，以取得在程式碼中使用的 Data Factory 類別的詳細資訊。 針對程式碼中使用的 WindowsFormsWebAuthenticationDialog 類別，將參考新增至 **2.9.10826.1824** 版的 **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll**。

## <a name="dataset-properties"></a>資料集屬性
若要指定資料集以代表「Azure Data Lake Store」中的輸入資料，您需將資料集的類型屬性設定成：AzureDataLakeStore。 請將資料集的 **linkedServiceName** 屬性設定成 Azure Data Lake Store 已連結服務的名稱。 如需定義資料集的 JSON 區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。 每個資料集類型的 **TypeProperties** 區段都不同，可提供資料存放區中資料的位置、格式等相關資訊。 **AzureDataLakeStore** 類型資料集的 typeProperties 區段具有下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| folderPath |Azure Data Lake Store 中容器與資料夾的路徑。 |是 |
| fileName |Azure Data Lake Store 中的檔案名稱。 fileName 是選擇性的，而且區分大小寫。 <br/><br/>如果您指定檔案名稱，活動 (包括複製) 適用於特定的檔案。<br/><br/>如果您未指定 fileName，複製會包含 folderPath 中的所有檔案以做為輸入資料集。<br/><br/>沒有為輸出資料集指定 fileName 時，所產生的檔案名稱會是下列格式：Data<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |否 |
| partitionedBy |partitionedBy 是選擇性的屬性。 您可以用來指定時間序列資料的動態 folderPath 和 filename。 例如，folderPath 可針對每小時的資料進行參數化。 如需詳細資訊和範例，請參閱 [使用 partitionedBy 屬性](#using-partitionedby-property) 一節。 |否 |
| format | 支援下列格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 將格式下的 **type** 屬性設定為這些值其中之一。 如需詳細資訊，請參閱[文字格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)章節。 <br><br> 如果您想要在以檔案為基礎的存放區之間**依原樣複製檔案** (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。 |否 |
| compression | 指定此資料的壓縮類型和層級。 支援的類型為：**GZip**、**Deflate**、**BZip2** 及 **ZipDeflate**。 支援的層級為：**Optimal** 和 **Fastest**。 如需詳細資訊，請參閱 [Azure Data Factory 中的檔案和壓縮格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

### <a name="using-partitionedby-property"></a>使用 partitionedBy 屬性
如上一節所述，您可以使用 **partitionedBy** 屬性、[Data Factory 函式及系統變數](data-factory-functions-variables.md)，來指定時間序列資料的動態 folderPath 和檔案名稱。

請參閱[建立資料集](data-factory-create-datasets.md)和[排程和執行](data-factory-scheduling-and-execution.md)等文章，以了解時間序列資料集、排程和配量的詳細資訊。

#### <a name="sample-1"></a>範例 1

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
在此範例中，{Slice} 會取代成 Data Factory 系統變數 SliceStart 的值 (使用指定的格式 (YYYYMMDDHH))。 SliceStart 是指配量的開始時間。 每個配量的 folderPath 都不同。 例如：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>範例 2
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
在此範例中，SliceStart 的年、月、日和時間會擷取到 folderPath 和 fileName 屬性所使用的個別變數。

## <a name="copy-activity-properties"></a>複製活動屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

而活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 若為複製活動，這些屬性會根據來源和接收的類型而有所不同

**AzureDataLakeStoreSource** 支援下列屬性 **typeProperties** 區段：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| 遞迴 |表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 |True (預設值)、False |否 |

**AzureDataLakeStoreSink** 支援下列屬性 **typeProperties** 區段：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| copyBehavior |指定複製行為。 |<b>PreserveHierarchy</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><br/><b>FlattenHierarchy</b>：來源資料夾的中所有檔案都會建立在目標資料夾的第一個層級中。 建立的目標檔案會具有自動產生的名稱。<br/><br/><b>MergeFiles</b>：將來源資料夾的所有檔案合併為一個檔案。 如果已指定檔案/Blob 名稱，合併檔案名稱會是指定的名稱；否則，就會是自動產生的檔案名稱。 |否 |

## <a name="supported-file-and-compression-formats"></a>支援的檔案和壓縮格式
請參閱 [Azure Data Factory 中的檔案和壓縮格式](data-factory-supported-file-and-compression-formats.md)文章了解詳細資訊。

## <a name="json-examples"></a>JSON 範例
以下範例提供可用來使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 建立管線的範例 JSON 定義。 它們會示範如何將資料複製到 Azure Data Lake Store 和 Azure Blob 儲存體，以及複製其中的資料。 不過，您可以將資料從任何來源**直接**複製到任何支援的接收器。 如需詳細資訊，請參閱[使用複製活動來移動資料](data-factory-data-movement-activities.md)中的＜支援的資料存放區和格式＞一節。  
## <a name="example-copy-data-from-azure-blob-to-azure-data-lake-store"></a>範例：將資料從 Azure Blob 複製到 Azure Data Lake Store
下列範例顯示︰

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)類型的連結服務。
2. [AzureDataLakeStore](#linked-service-properties)類型的連結服務。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureDataLakeStore](#dataset-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 和 [AzureDataLakeStoreSink](#copy-activity-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例每小時會將時間序列資料從 Azure Blob 儲存體複製到 Azure Data Lake Store。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure 儲存體連結服務：**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Azure Data Lake Store 已連結的服務**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> 如需組態詳細資料，請參閱 [Azure Data Lake Store 連結服務屬性](#linked-service-properties)一節中的步驟。
>

**Azure Blob 輸入資料集：**

每小時從新的 Blob 挑選資料 (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。 資料夾路徑使用開始時間的年、月、日部分，而檔案名稱使用開始時間的小時部分。 “external”: “true” 設定會通知 Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

```JSON
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
```

**Azure Data Lake Store 輸出資料集：**

此範例會將資料複製到 Azure Data Lake Store。 每個小時會將新資料複製到 Data Lake Store。

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**具有 Blob 來源和 Azure Data Lake Store 接收器的管線中複製活動：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設定為 **BlobSource**，且 **sink** 類型設定為 **AzureDataLakeStoreSink**。

```json
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
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

## <a name="example-copy-data-from-azure-data-lake-store-to-azure-blob"></a>範例：將資料從 Azure Data Lake Store 複製到 Azure Blob
下列範例顯示︰

1. [AzureDataLakeStore](#linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)類型的連結服務。
3. [AzureDataLakeStore](#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [AzureDataLakeStoreSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 之複製活動的[管線](data-factory-create-pipelines.md)

此範例每小時會將時間序列資料從 Azure Data Lake Store 複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure Data Lake Store 連結服務：**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> 如需組態詳細資料，請參閱 [Azure Data Lake Store 連結服務屬性](#linked-service-properties)一節中的步驟。
>

**Azure 儲存體連結服務：**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Data Lake 輸入資料集：**

設定 **"external": true** 會通知 Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

```json
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
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
```
**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```JSON
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

**具有 Azure Data Lake Store 來源和 Blob 接收器的管線中複製活動：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設定為 **AzureDataLakeStoreSource**，且 **sink** 類型設定為 **BlobSink**。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

您也可以在複製活動定義中，將來自來源資料集的資料行與來自接收資料集的資料行對應。 如需詳細資料，請參閱[在 Azure Data Factory 中對應資料集資料行](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

