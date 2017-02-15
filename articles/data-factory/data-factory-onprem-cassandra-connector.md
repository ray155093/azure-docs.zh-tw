---
title: "使用 Data Factory 從 Cassandra 移動資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 從內部部署的 Cassandra 資料庫移動資料。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 21ec253e35b31af770cacb9747210deb4b9f5fa0


---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>使用 Azure Data Factory 從內部部署的 Cassandra 資料庫移動資料
本文概述如何在 Azure Data Factory 使用複製活動，將資料從內部部署的 Cassandra 資料庫，複製到 [支援的來源與接收](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 一節的 [接收] 欄底下列出的任何資料存放區。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

Data Dactory 目前只支援將資料從 Cassandra 資料庫移動到 [支援的接收資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)，但不支援將資料從其他資料存放區移動到 Cassandra 資料庫。

## <a name="prerequisites"></a>必要條件
如果是能夠連接到您內部部署 Cassandra 資料庫的 Azure Data Factory 服務，您就必須安裝下列項目：

* 位於裝載資料庫的同一部電腦上或個別電腦上的資料管理閘道 2.0 或更新版本，可避免與資料庫競用資源。 資料管理閘道是一套透過安全且可管理的方式，將內部部署資料來源連結至雲端服務的軟體。 如需資料管理閘道的詳細資訊，請參閱 [在內部部署和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文。

    當您安裝閘道時，它會自動安裝用來連線至 Cassandra 資料庫的 Microsoft Cassandra ODBC 驅動程式。

> [!NOTE]
> 如需連接/閘道器相關問題的疑難排解秘訣，請參閱 [針對閘道問題進行疑難排解](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) 。
>
>

## <a name="copy-data-wizard"></a>複製資料精靈
若要建立管線以將資料從 Cassandra 資料庫複製到任何支援的接收資料存放區，最簡單的方式是使用複製資料精靈。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

下列範例提供您使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 來建立管線時，可使用的範例 JSON 定義。 這些範例示範如何將資料從 Cassandra 資料庫複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動，將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。   

## <a name="sample-copy-data-from-cassandra-to-blob"></a>範例︰將資料從 Cassandra 複製到 Blob
範例會每隔一小時就把 Cassandra 資料庫的資料複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。 您可以使用 Azure Data Factory 中的複製活動，把資料直接複製到 [資料移動活動](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 一文中所述的任何接收器。

* [OnPremisesCassandra](#onpremisescassandra-linked-service-properties)類型的連結服務。
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)類型的連結服務。
* [CassandraTable](#cassandratable-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
* 具有使用 [CassandraSource](#cassandrasource-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

**Cassandra 連結服務**

此範例使用 **Cassandra** 連結服務。 如需此連結服務所支援的屬性，請參閱 [Cassandra 連結服務](#onpremisescassandra-linked-service-properties) 一節。  

```JSON
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
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

**Cassandra 輸入資料集**

```JSON
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
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
```

將 **external** 設定為 **true** 會通知 Data Factory 服務：這是 Data Factory 外部的資料集而且不是由 Data Factory 中的活動所產生。

**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。

```JSON
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **CassandraSource**，而 **sink** 類型設為 **BlobSink**。

如需 RelationalSource 支援的屬性清單，請參閱 [RelationalSource 類型屬性](#cassandrasource-type-properties) 。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

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

## <a name="onpremisescassandra-linked-service-properties"></a>OnPremisesCassandra 連結服務屬性
下表提供 Cassandra 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| 類型 |類型屬性必須設為： **OnPremisesCassandra** |是 |
| 主機 |一或多個 Cassandra 伺服器 IP 位址或主機名稱。<br/><br/>指定以逗號分隔的 IP 位址或主機名稱清單，以同時連線到所有伺服器。 |是 |
| 連接埠 |Cassandra 伺服器用來接聽用戶端連線的 TCP 連接埠。 |否，預設值：9042 |
| authenticationType |基本或匿名 |是 |
| username |指定使用者帳戶的使用者名稱。 |是，如果 authenticationType 設定為 [基本]。 |
| password |指定使用者帳戶的密碼。 |是，如果 authenticationType 設定為 [基本]。 |
| gatewayName |用來連線到內部部署 Cassandra 資料庫的閘道器名稱。 |是 |
| encryptedCredential |由閘道加密認證。 |否 |

## <a name="cassandratable-properties"></a>CassandraTable 屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。 **CassandraTable** 類型資料集的 typeProperties 區段有下列屬性

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| keyspace |Cassandra 資料庫中的 Keyspace 或結構描述名稱。 |是 (如果未定義 **CassandraSource** 的**查詢**)。 |
| tableName |Cassandra 資料庫中資料表的名稱。 |是 (如果未定義 **CassandraSource** 的**查詢**)。 |

## <a name="cassandrasource-type-properties"></a>CassandraSource 類型屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

如果來源類型為 **CassandraSource**，則 typeProperties 區段可使用下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| query |使用自訂查詢來讀取資料。 |SQL-92 查詢或 CQL 查詢。 請參閱 [CQL 參考資料](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)。 <br/><br/>在使用 SQL 查詢時，指定 **keyspace name.table 名稱** 來代表您想要查詢的資料表。 |否 (如果已定義資料集上的 tableName 和 keyspace)。 |
| consistencyLevel |一致性層級可指定必須先有多少複本回應讀取要求，才會將資料傳回用戶端應用程式。 Cassandra 會檢查要讓資料滿足讀取要求的指定複本數目。 |ONE、TWO、THREE、QUORUM、ALL、LOCAL_QUORUM、EACH_QUORUM、LOCAL_ONE。 如需詳細資訊，請參閱 [設定資料一致性](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) 。 |否。 預設值為 ONE。 |

### <a name="type-mapping-for-cassandra"></a>Cassandra 的類型對應
| Cassandra 類型 | 以 .Net 為基礎的類型 |
| --- | --- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |BOOLEAN |
| DECIMAL |DECIMAL |
| DOUBLE |DOUBLE |
| FLOAT |單一 |
| INET |String |
| INT |Int32 |
| TEXT |String |
| 時間戳記 |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |DECIMAL |

> [!NOTE]
> 如需集合類型 (對應、集、清單等)，請參閱 [使用虛擬資料表處理 Cassandra 集合類型](#work-with-collections-using-virtual-table) 一節。
>
> 不支援使用者定義的類型。
>
> 二進位資料行與字串資料行的長度不能超過 4000。
>
>

## <a name="work-with-collections-using-virtual-table"></a>使用虛擬資料表處理集合
Azure Data Factory 會使用內建的 ODBC 驅動程式來連線到 Cassandra 資料庫並從中複製資料。 對於對應、集和清單在內的集合類型，此驅動程式會將資料重新標準化為對應的虛擬資料表。 具體來說，如果資料表包含任何集合資料行，則此驅動程式會產生下列虛擬資料表︰

* **基底資料表**，其中包含與實際資料表相同的資料 (集合資料行除外)。 基底資料表使用與它所代表的實際資料表相同的名稱。
* 每個集合資料行的 **虛擬資料表** ，以展開巢狀資料。 代表集合的虛擬資料表會使用實際資料表名稱、分隔字元 “vt”和資料行名稱來命名。

虛擬資料表會參考實際資料表中的資料，讓驅動程式得以存取反正規化的資料。 如需詳細資訊，請參閱＜範例＞一節。 您可以藉由查詢和聯結虛擬資料表來存取 Cassandra 集合的內容。

您可以利用 [複製精靈](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) ，以直觀方式檢視 Cassandra 資料庫中的資料表清單 (包括虛擬資料表)，並預覽其中的資料。 您也可以在複製精靈中建構查詢，並進行驗證以查看結果。

### <a name="example"></a>範例
例如，以下的「ExampleTable」就是 Cassandra 資料庫資料表，其中包含名為「pk_int」的整數主索引鍵資料行、名為「值」的文字資料行、「清單」資料行、「對應」資料行和「集」資料行 (名為「StringSet」)。

| pk_int | 值 | 列出 | 對應 | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"sample value 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"sample value 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

驅動程式會產生多個代表這個單一資料表的虛擬資料表。 虛擬資料表中的外部索引鍵資料行會參考實際資料表中的主索引鍵資料行，並指出虛擬資料表資料列所對應的實際資料表資料列。

第一個虛擬資料表是名為「ExampleTable」的基底資料表，如下表所示。 基底資料表包含與原始資料庫資料表相同的資料，但集合除外，集合會被此資料表省略，而在其他虛擬資料表中展開。

| pk_int | 值 |
| --- | --- |
| 1 |"sample value 1" |
| 3 |"sample value 3" |

下表顯示會重新標準化「清單」、「對應」與「StringSet」資料行中資料的虛擬資料表。 名稱結尾為「_index」或「_key」的資料行代表資料在原始清單或對應內的位置。 名稱結尾為「_value」的資料行包含從集合展開的資料。

#### <a name="table-exampletablevtlist"></a>資料表「ExampleTable_vt_List」：
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>資料表「ExampleTable_vt_Map」：
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |具有使用  |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>資料表「ExampleTable_vt_StringSet」：
| pk_int | StringSet_value |
| --- | --- |
| 1 |具有使用  |
| 1 |b |
| 1 |C |
| 3 |具有使用  |
| 3 |E |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。



<!--HONumber=Nov16_HO3-->


