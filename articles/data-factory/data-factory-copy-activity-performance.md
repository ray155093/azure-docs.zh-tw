<properties
	pageTitle="複製活動的效能及微調指南 | Microsoft Azure"
	description="了解在 Azure Data Factory 中透過複製活動的資料移動效能會受到哪些主要因素的影響。"
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
	ms.date="08/09/2016"
	ms.author="spelluru"/>


# 複製活動的效能及微調指南
本文說明在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素。它也列出在內部測試期間所觀察到的效能，並討論要讓複製活動效能最佳化的各種方式。

透過複製活動，您將可以取得高水準的資料移動輸送量，如下列範例所示︰

- 在 3 小時內，從內部部署檔案系統和 Azure Blob 儲存體，內嵌 1 TB 資料到 Azure Blob 儲存體 (也就是 @ 100 MBps)
- 在 3 小時內，從內部部署檔案系統和 Azure Blob 儲存體，內嵌 1 TB 資料到 Azure Data Lake Store (也就是 @ 100 MBps)
- 在 3 小時內，從 Azure Blob 儲存體，內嵌 1 TB 資料到 Azure SQL 資料倉儲 (也就是 @ 100 MBps)

請參閱下列章節，深入了解複製活動的效能以及加以進一步改善的微調秘訣。

> [AZURE.NOTE] 如果您大致來說並不熟悉複製活動，請先參閱[資料移動活動](data-factory-data-movement-activities.md)再通讀本文。

## 效能微調步驟
以下列出我們建議您執行，以透過複製活動對您的 Azure Data Factory 解決方案進行效能調整的一般步驟。

1.	**建立基準。** 在開發階段，透過複製活動依據具代表性的範例資料測試您的管線。您可以利用 Azure Data Factory 的[切割模型](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices)來限制您所使用的資料量。

	藉由**監視及管理應用程式**收集執行時間和效能特性︰按一下 Data Factory 首頁上的 [監視和管理] 圖格，選取樹狀檢視中的 [輸出資料集]，然後在 [活動時段] 清單中選取複製活動回合。您應該會在 [活動時段] 清單中看到複製活動的持續時間，並在右邊的 [活動時段總管] 視窗看到複製資料的大小和輸送量。若要深入了解應用程式，請參閱[使用監視及管理應用程式來監視及管理 Azure Data Factory 管線](data-factory-monitor-manage-app.md)。
	
	![活動執行詳細資料](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

	您可以比較您的案例的效能和組態，根據內部的觀察值發佈於下方的複製活動[效能參考](#performance-reference)。
2. **效能診斷與最佳化**：如果您觀察到的效能值低於預期，您必須找出效能瓶頸並執行最佳化，以消除或減少瓶頸的影響。效能診斷的完整說明不在本文的討論之列，但我們會列出一些常見的考量，如下所示。
	- [來源](#considerations-on-source)
	- [接收](#considerations-on-sink)
	- [序列化/還原序列化](#considerations-on-serializationdeserialization)
	- [壓縮](#considerations-on-compression)
	- [資料行對應](#considerations-on-column-mapping)
	- [資料管理閘道](#considerations-on-data-management-gateway)
	- [其他考量](#other-considerations)
	- [平行複製](#parallel-copy)
	- [雲端資料移動單位](#cloud-data-movement-units)

3. **將組態擴充至您的整體資料**：如果您對執行結果及效能感到滿意，您可以將資料集定義和管線作用期間涵蓋至圖片中的整體資料。

## 效能參考
> [AZURE.IMPORTANT] **免責聲明：**以下發佈的資料僅供指引和高階計劃之用。它假設頻寬、硬體、組態等要素皆處於最佳水準。這部分僅供參考之用。您所看到的資料移動輸送量會受到多個變數的影響。請參閱後續章節，以了解如何進行調整，並針對您的資料移動需求達到更理想的效能。這項資料會在新增效能改進功能時進行更新。

![效能矩陣](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

注意事項：

- 輸送量的計算公式如下：[從來源讀取的資料大小]/[複製活動執行持續時間]
- [TPC-H](http://www.tpc.org/tpch/) 資料集已運用在上述數據的計算上。
- 如果使用 Microsoft Azure 資料存放區，來源和接收器會位於相同的 Azure 區域中。
- **cloudDataMovementUnits** 設為 1，且未指定 **parallelCopies**。
- 如果是混合式 (內部部署至雲端或雲端至內部部署) 資料移動，則會使用下列組態，將資料管理閘道器 (單一執行個體) 裝載在不同於內部部署資料存放區的電腦上。請注意，若閘道器上僅執行單一活動，複製作業將只會取用這部電腦一小部分的 CPU/記憶體資源和網路頻寬。
	<table>
	<tr>
		<td>CPU</td>
		<td>32 核心 2.20GHz Intel Xeon® E5-2660 v2</td>
	</tr>
	<tr>
		<td>記憶體</td>
		<td>128 GB</td>
	</tr>
	<tr>
		<td>網路</td>
		<td>網際網路介面：10Gbps；內部網路介面：40Gbps</td>
	</tr>
	</table>

## 平行複製
想要增強複製作業的輸送量並減少資料移動時間，其中一種方法是**在複製活動回合內以平行方式**從來源讀取資料和/或將資料寫入至目的地。
 
請注意，此設定不同於活動定義中的**並行**屬性。並行屬性可決定**並行複製活動回合**的數目，這些回合會在執行階段一起執行，以處理來自不同活動時段 (1-2 AM、2-3 AM、3-4 AM，依此類推) 的資料。在執行歷程載入時，這個屬性非常有用。然而，此處所討論的平行複製功能則適用於**單一活動回合**。

讓我們看一下**範例案例**︰請試想以下有多個過往配量需要處理的範例。Data Factory 服務會對每個配量執行一個複製活動執行個體 (活動回合)。

- 第 1 個活動時段 (1 AM - 2 AM) 的資料配量 ==> 活動回合 1
- 第 2 個活動時段 (2 AM - 3 AM) 的資料配量 ==> 活動回合 2
- 第 2 個活動時段 (3 AM - 4 AM) 的資料配量 ==> 活動回合 3
- 等等。

在此範例中，若**並行**設定為 **2**，則可讓**活動回合 1** 和**活動回合 2** **並行**複製兩個活動時段的資料，以改善資料移動效能。不過，如果有多個檔案與活動回合 1 相關聯，則一次只會從來源複製一個檔案到目的地。

### parallelCopies
您可以使用 **parallelCopies** 屬性，來指出您想要複製活動使用的平行處理原則。簡單地說，您可以將此屬性視為複製活動內，以平行方式從來源讀取和/或寫入至接收資料存放區中的執行緒數目上限。

對於每個複製活動回合，Azure Data Factory 會明智地決定要用來將資料從來源資料存放區，複製到目的地資料存放區的平行複製數目。它會使用的預設平行複製數目取決於來源和接收的類型︰

來源和接收 |	由服務決定的預設平行複製計數
------------- | -------------------------------------------------
在**檔案型存放區** (Azure Blob、Azure Data Lake、內部部署檔案系統、內部部署 HDFS) 之間複製資料 | **1 到 32** 之間的任何值，取決於**檔案大小**和用來在兩個雲端資料存放區之間複製資料的**雲端資料移動單位的數值** (其定義請參閱下一節)，(或) 用於混合式複製的實體閘道器電腦組態 (複製資料至內部部署資料存放區/從內部部署資料存放區複製資料)
將資料從**任何來源資料存放區複製到 Azure 資料表** | 4
所有其他來源和接收組 | 1

在大多數情況下，預設行為應可提供最佳輸送量。不過，您也可以藉由指定 **parallelCopies** 屬性的值來覆寫預設值，以便控制資料存放區電腦上的負載，或調整複製效能。該值必須介於 **1 (含) 到 32 (含)**。在執行階段，複製活動會選擇小於或等於設定值的值，以提供最佳效能。

	"activities":[  
	    {
	        "name": "Sample copy activity",
	        "description": "",
	        "type": "Copy",
	        "inputs": [{ "name": "InputDataset" }],
	        "outputs": [{ "name": "OutputDataset" }],
	        "typeProperties": {
	            "source": {
	                "type": "BlobSource",
	            },
	            "sink": {
	                "type": "AzureDataLakeStoreSink"
	            },
	            "parallelCopies": 8
	        }
	    }
	]

請注意：

- 在複製檔案型存放區之間的資料時，平行處理原則會發生在檔案層級；換句話說，單一檔案中不會進行區塊化。在執行階段用於複製作業的實際平行複製數目，將不會超過您擁有的檔案數目。如果複製行為是 mergeFile，則不會利用平行處理原則。
- 在為 parallelCopies 屬性指定值時，請務必考慮其將為來源和接收資料存放區增加的負載，以及如果是混合式複製，尤其是當您有多個活動或對相同資料存放區執行相同活動的並行回合時，請務必考慮其將為閘道器增加的負載。如果您注意到資料存放區或閘道器已無法應付負載，請減少 parallelCopies 值以減輕負載。
- 將資料從非檔案型存放區複製到檔案型存放區時，即使已指定 parallelCopies 屬性，也會遭到忽略，並且不會利用任何平行處理原則。

> [AZURE.NOTE] 若要在進行混合式複製時利用 parallelCopies 功能，您必須使用 1.11 版或更新版本的資料管理閘道器。

### 雲端資料移動單位
**雲端資料移動單位**是一項量值，代表用來執行雲端到雲端複製作業的 Azure Data Factory 服務中，單一單位的能力 (CPU、記憶體和網路資源配置的組合)。在進行混合式複製時，它並不會派上用場。根據預設，Azure Data Factory 服務會使用一個雲端資料移動單位來執行一個複製活動回合。您可以藉由指定 **cloudDataMovementUnits** 屬性的值來覆寫此預設值。此時，只有在**兩個 Azure Blob 儲存體之間**或從 **Azure Blob 儲存體到 Azure Data Lake Store** 複製資料時，**才會支援** CloudDataMovementUnits 設定，而且此設定只會在您有多個要個別複製的檔案，且其大小大於或等於 16 MB 時生效。

如果您要複製許多較大型的檔案，設定較高的 **parallelCopies** 屬性值可能無法改善效能，因為單一雲端資料移動單位的資源有所限制。在這種情況下，您可能會想要使用更多個雲端資料移動單位，以便以高輸送量複製大量資料。若要指定您想要複製活動使用的雲端資料移動單位數目，請如下所示設定 **cloudDataMovementUnits** 屬性的值︰

	"activities":[  
	    {
	        "name": "Sample copy activity",
	        "description": "",
	        "type": "Copy",
	        "inputs": [{ "name": "InputDataset" }],
	        "outputs": [{ "name": "OutputDataset" }],
	        "typeProperties": {
	            "source": {
	                "type": "BlobSource",
	            },
	            "sink": {
	                "type": "AzureDataLakeStoreSink"
	            },
	            "cloudDataMovementUnits": 4
	        }
	    }
	]

cloudDataMovementUnits 屬性的**允許值**是︰1 (預設值)、2、4 和 8。如果您需要更多雲端資料移動單位以提高輸送量，請連絡 [Azure 支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。在執行階段用於複製作業的**雲端資料移動單位的實際數目**，將會等於或小於設定的值，視要從符合大小準則的來源複製的檔案數目。

> [AZURE.NOTE] parallelCopies (若有指定) 應該大於或等於 cloudDataMovementUnits；當 cloudDataMovementUnits 大於 1 時，平行資料移動就會分散給該複製活動回合的 cloudDataMovementUnits，因而提高輸送量。

在 **cloudDataMovementUnits** 設定為 2、4 和 8 的情況下複製多個大型檔案時，效能可達到＜效能參考＞一節所述之參考數字的 2x (2 倍)、4x 及 7x。

請參閱這裡的[範例使用案例](#case-study---parallel-copy)，以更好地利用上述 2 個屬性來增強資料移動輸送量。
 
請**務必**要記住，您將必須根據複製作業的總時間付費。因此，若過去某複製作業使用 1 個雲端單位花費 1 小時，現在使用 4 個雲端單位花費 15 分鐘，兩種的整體費用幾乎相同。再以另一個案例為例：假設您在某複製活動回合中使用 4 個雲端單位：第 1 個雲端單位費時 10 分鐘、第 2 個單位費時 10 分鐘、第 3 個單位費時 5 分鐘、第 4 個單位費時 5 分鐘。整個複製 (資料移動) 作業以複製的時間總計計費，等於 10 + 10 + 5 + 5 = 30 分鐘。是否使用 **parallelCopies** 對計費沒有任何影響。

## 分段複製
從來源資料存放區將資料複製到接收資料存放區時，您可能會使用 Azure Blob 儲存體做為過渡暫存存放區。暫存功能在下列情況下特別有用︰

1.	**有時需要一段時間，以透過慢速網路連接執行混合式資料移動 (也就是在內部部署資料存放區至雲端資料存放區，反之亦然)。** 為了提升這類資料移動的效能，您可以壓縮內部部署資料，透過寫入至雲端中的暫存資料存放區，然後在將其載入至目的地資料存放區之前在暫存存放區中解壓縮資料，減少移動資料的時間。
2.	**由於 IT 原則，您不想要在您的防火牆中開啟 80 和 443 以外的連接埠。** 例如，從內部部署資料存放區將資料複製到 Azure SQL Database 接收或 Azure SQL 資料倉儲接收時，必須針對 Windows 防火牆和公司防火牆啟用連接埠 1433 上的輸出 TCP 通訊。在這類案例中，您可以利用資料管理閘道器先將資料複製到暫存 Azure Blob 儲存體，這會透過 Http(s) 也就是透過連接埠 443 進行，並接著將資料從暫存 Blob 儲存體載入到 SQL Database 或 SQL 資料倉儲。在這種流程中，連接埠 1433 並不需要啟用。
3.	**從各種資料存放區透過 PolyBase 將資料擷取至 Azure SQL 資料倉儲。** Azure SQL 資料倉儲提供 PolyBase 做為高輸送量機制，將大量資料載入 SQL 資料倉儲。不過，這需要來源資料位於 Azure Blob 儲存體，而且符合其他額外的條件。從 Azure Blob 儲存體以外的資料存放區載入資料時，您可以透過過渡暫存 Azure Blob 儲存體複製資料，在這種情況下，Azure Data Factory 會對資料執行必要的轉換，以確保它符合 PolyBase 的需求，然後使用 PolyBase 將資料載入 SQL 資料倉儲。如需詳細資訊和範例，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。

### 分段複製的運作方式
當您啟用暫存功能時，資料會先從來源資料存放區複製到暫存資料存放區 (自備)，接著再從暫存資料存放區複製到接收資料存放區。Azure Data Factory 會自動管理您的 2 階段流程，也會在資料移動完成之後，清除暫存儲存體的暫存資料。

在來源和接收資料存放區都在雲端中且不是利用資料管理閘道器的**雲端複製案例**中，複製作業是由 **Azure Data Factory 服務**執行。

![分段複製 - 雲端案例](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

而在**混合式複製案例**中，其中來源是內部部署，而接收在雲端中，從來源資料存放區至暫存資料存放區的資料移動是由**資料管理閘道器**執行，而從暫存資料存放區至接收資料存放區的資料移動是由 **Azure Data Factory 服務**執行。反轉流程也支援透過暫存將資料從雲端存放區複製到內部部署資料存放區。

![分段複製 - 混合式案例](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

當您使用暫存存放區啟用資料移動時，您可以指定是否要在從來源資料存放區將資料移動至過渡/暫存資料存放區之前壓縮資料，並且在從過渡/暫存資料存放區將資料移動至接收資料存放區之前解壓縮資料。

目前不支援在具有暫存存放區的兩個內部部署資料存放區之間複製資料，將會在近期之內啟用。

### 組態
您可以在複製活動上設定 **enableStaging** 設定，指定您是否想要讓資料在載入至目的地資料存放區之前，暫存在 Azure Blob 儲存體中。當您將 enableStaging 設定為 true 時，您需要指定下列資料表所列的其他屬性。而且您需要建立 Azure 儲存體或 Azure 儲存體 SAS 連結服務，做為暫存 (如果您還沒有)。

屬性 | 說明 | 預設值 | 必要
--------- | ----------- | ------------ | --------
enableStaging | 指定您是否要透過過渡暫存存放區複製資料。 | False | 否
linkedServiceName | 指定 [AzureStoage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 或 [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 連結服務的名稱，代表您將用來做為過渡暫存存放區的 Azure 儲存體。<br/><br/> 請注意，具有 SAS (共用存取簽章) 的 Azure 儲存體不能用於透過 PolyBase 將資料載入至 Azure SQL 資料倉儲。它可以用於其他所有案例。 | N/A | 是，當 enableStaging 設為 true。 
路徑 | 指定 Azure Blob 儲存體中的路徑，該儲存體包含分段資料。如果未提供路徑，服務會建立容器來儲存暫存資料。<br/><br/> 您不需要指定路徑，除非您使用具有 SAS 的 Azure 儲存體，或有暫存資料必須存在的強烈需求。 | N/A | 否
enableCompression | 指定資料在從來源資料存放區移至接收資料存放區時，是否應該壓縮，以減少在網路上傳輸的資料量。 | False | 否

以下是具有上述屬性的「複製活動」的範例定義︰

	"activities":[  
	{
		"name": "Sample copy activity",
		"type": "Copy",
		"inputs": [{ "name": "OnpremisesSQLServerInput" }],
		"outputs": [{ "name": "AzureSQLDBOutput" }],
		"typeProperties": {
			"source": {
				"type": "SqlSource",
			},
			"sink": {
				"type": "SqlSink"
			},
	    	"enableStaging": true,
			"stagingSettings": {
				"linkedServiceName": "MyStagingBlob",
				"path": "stagingcontainer/path",
				"enableCompression": true
			}
		}
	}
	]


### 計費影響
請注意，將會根據複製持續時間的兩個階段和其複製類型，分別向您計費，這表示︰

- 在雲端複製期間使用暫存時 (從雲端資料存放區將資料複製到其他雲端資料存放區，例如，Azure Data Lake 至 Azure SQL 資料倉儲)，計費為 [步驟 1 和步驟 2 的複製持續時間的總和] x [雲端複製單位價格]
- 在混合式複製期間使用暫存時 (從內部部署資料存放區將資料複製到雲端資料存放區，例如，內部部署 SQL Server 資料庫至 Azure SQL 資料倉儲)，計費為 [混合式複製持續時間] x [混合式複製單位價格] + [雲端複製持續時間] x [雲端複製單位價格]


## 來源的考量
### 一般
請確定基礎資料存放區未被執行於其上的其他工作負載全面佔據，這包括 (但不限於) 複製活動。

針對 Microsoft 資料存放區，請參閱資料存放區特定的[監視和微調主題](#appendix-data-store-performance-tuning-reference)，這可協助您了解資料存放區的效能特性、最小化回應時間和最大化輸送量。

如果您要從 **Azure Blob 儲存體**複製資料至 **Azure SQL 資料倉儲**，請考慮啟用 **PolyBase** 以提升效能。如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse)。


### 以檔案為基礎的資料存放區
*(包括 Azure Blob、Azure 資料湖、內部部署檔案系統)*

- **平均檔案大小和檔案計數**：複製活動會依檔案傳送資料檔案。在要移動的資料量相同的前提下，如果資料包含大量的小型檔案，其整體輸送量將會低於少量的大型檔案，因為每個檔案都需要啟動程序階段。因此，可能的話，請將小型檔案合併為較大的檔案，以提高輸送量。
- **檔案格式和壓縮**：請參閱[序列化/還原序列化的考量](#considerations-on-serializationdeserialization)和[壓縮的考量](#considerations-on-compression)小節，以了解可改善效能的其他方法。
- 此外，對於必須使用**資料管理閘道器**的**內部部署檔案系統**案例，請參閱[閘道器的考量](#considerations-on-data-management-gateway)一節。

### 關聯式資料存放區
*(包括 Azure SQL Database、Azure SQL 資料倉儲、SQL Server Database、Oracle 資料庫、MySQL 資料庫、DB2 資料庫、Teradata 資料庫、Sybase 資料庫、PostgreSQL 資料庫)*

- **資料模式**：資料表結構描述對複製輸送量會有影響。若要複製相同的資料量，較大的資料列大小將會有優於較小資料列大小的效能，因為資料庫可以更有效率地擷取包含較少資料列的較少資料批次。
- **查詢或預存程序**：最佳化您在複製活動來源中指定的查詢或預存程序邏輯，以更有效率地擷取資料。
- 此外，對於必須使用**資料管理閘道器**的**內部部署關聯式資料庫** (例如 SQL Server 和 Oracle)，請參閱[閘道器的考量](#considerations-on-data-management-gateway)一節。

## 接收器的考量

### 一般
請確定基礎資料存放區未被執行於其上的其他工作負載全面佔據，這包括 (但不限於) 複製活動。

針對 Microsoft 資料存放區，請參閱資料存放區特定的[監視和微調主題](#appendix-data-store-performance-tuning-reference)，這可協助您了解資料存放區的效能特性、最小化回應時間和最大化輸送量。

如果您要從 **Azure Blob 儲存體**複製資料至 **Azure SQL 資料倉儲**，請考慮啟用 **PolyBase** 以提升效能。如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse)。


### 以檔案為基礎的資料存放區
*(包括 Azure Blob、Azure 資料湖、內部部署檔案系統)*

- **複製行為**：如果您從另一個以檔案為基礎的資料存放區複製資料，複製活動會透過 "copyBehavior" 屬性提供三種類型的行為：保留階層、扁平化階層，以及合併檔案。保留或扁平化階層只會造成些微甚至沒有效能負荷，而合併檔案則會導致額外的效能負荷。
- **檔案格式和壓縮**：請參閱[序列化/還原序列化的考量](#considerations-on-serializationdeserialization)和[壓縮的考量](#considerations-on-compression)小節，以了解可改善效能的其他方法。
- 對於 **Azure Blob**，我們目前只支援以區塊 Blob 來最佳化資料傳送和輸送量。
- 此外，對於必須使用**資料管理閘道器**的**內部部署檔案系統**案例，請參閱[閘道器的考量](#considerations-on-data-management-gateway)一節。

### 關聯式資料存放區
*(包括 Azure SQL Database、Azure SQL 資料倉儲、SQL Server Database)*

- **複製行為**：根據為 "sqlSink" 設定的屬性，複製活動會以不同的方式將資料寫入目的地資料庫中：
	- 根據預設，資料移動服務會使用大量複製 API 以附加模式插入資料，而提供最佳效能。
	- 如果您在接收器中設定了預存程序，資料庫將會依資料列來套用資料列 (而不是大量載入)，因此效能會大幅降低。如果資料較大，在適用的情況下，請考慮改為使用 "sqlWriterCleanupScript" 屬性 (如下所示)。
	- 如果您設定 "sqlWriterCleanupScript" 屬性，在每個複製活動執行時，服務會先觸發指令碼，再使用大量複製 API 插入資料。例如，若要以最新的資料覆寫整個資料表，您可以先指定指令碼以刪除所有記錄，再從來源大量載入新資料。
- **資料模式和批次大小**：
	- 資料表結構描述對複製輸送量會有影響。若要複製相同的資料量，較大的資料列大小將會有優於較小資料列大小的效能，因為資料庫可以更有效率地認可較少的資料批次。
	- 複製活動會以一系列的批次插入資料，其中，批次中包含的資料列數可使用 “writeBatchSize” 屬性來設定。如果您的資料具有較小的資料列，您可以將 "writeBatchSize" 屬性設為較高的值，以減少批次的額外負荷，並增加輸送量。如果您的資料的資料列較，在增加 writeBatchSize 時請多加留意 – 較大的值可能會導致複製因資料庫的超載而失敗。
- 此外，對於必須使用**資料管理閘道器**的**內部部署關聯式資料庫** (例如 SQL Server 和 Oracle)，請參閱[閘道器的考量](#considerations-on-data-management-gateway)一節。


### NoSQL 存放區
*(包括 Azure 資料表、Azure DocumentDB)*

- 針對 **Azure 資料表**：
	- **資料分割**：將資料寫入至交錯的資料分割，會大幅降低效能。您可以選擇依資料分割索引鍵來排序您的來源資料，使資料能在分割後有效率地插入資料分割中，或者，您可以調整邏輯，將資料寫入單一資料分割中。
- 針對 **Azure DocumentDB**：
	- **批次大小**："writeBatchSize" 屬性會指出要 DocumentDB 服務建立文件的平行要求數目。增加 "writeBatchSize" 時，您可預期有更好的效能，因為對 DocumentDB 傳送了更多的平行要求。不過，在寫入至 DocumentDB 時必須注意節流問題 (「要求率偏高」錯誤訊息)。發生節流的因素有很多，包括文件的大小，文件中的詞彙數目，以及目標集合的索引編製原則等。若要達到更高的複製輸送量，請考慮使用更好的集合 (例如 S3)。

## 序列化/還原序列化的考量
如果您的輸入資料集或輸出資料集是檔案，就可能發生序列化和還原序列化。目前，複製活動可支援 Avro 和文字 (例如 CSV 和 TSV) 資料格式。

**複製行為：**

- 在以檔案為基礎的資料存放區之間複製檔案時：
	- 如果輸入和輸出資料集使用相同的檔案格式設定，或者都沒有這些設定，資料移動服務將會執行二進位複製，而不執行任何序列化/還原序列化。因此，其輸送量應會優於來源/接收器檔案格式設定不相同的案例。
	- 如果輸入和輸出資料集都是以文字格式，而只編碼類型不同，則資料移動服務只會執行編碼轉換，而不執行任何序列化/還原序列化，而產生略高於二進位複製的效能負荷。
	- 如果輸入和輸出資料集有不同的檔案格式或不同的組態 (例如分隔符號)，則資料移動服務會將來源資料還原序列化，以串流、轉換，和再序列化為所需的輸出格式。這將會導致遠高於前述案例的效能負荷。
- 對 (從) 不是以檔案為基礎的資料存放區複製檔案時 (例如，從以檔案為基礎的存放區複製到關聯式存放區)，將必須執行序列化和還原序列化步驟，而這將會導致很高的效能負荷。

**檔案格式：**檔案格式的選擇可能會影響複製效能。例如，Avro 是一種壓縮二進位格式，可將中繼資料和資料儲存在一起，並且廣泛支援在 Hadoop 生態系統中進行處理和查詢。不過，Avro 的序列化/還原序列化代價較高，因為會導致低於文字格式的複製輸送量。在選擇以何種檔案格式與處理流程搭配使用時，應有整體考量，首先要考量何種形式的資料會儲存在來源資料存放區中或從外部系統擷取，再考量最理想的儲存、分析處理和查詢格式，以及資料應以何種格式匯出到資料超市中，以供報告和視覺化工具使用。有些時候，在整體分析程序的考量下，讀取和寫入效能次佳的檔案格式，反而會是較適用的。

## 壓縮的考量
如果您的輸入或輸出資料集是檔案，您可以將複製活動設定為在資料寫入至目的地時執行壓縮或解壓縮。啟用壓縮時，您必須在 I/O 與 CPU 之間進行取捨：壓縮資料須耗用額外的計算資源，但另一方面卻可降低網路 I/O 和儲存體用量，這取決於您的資料是否可讓您提升整體複製輸送量。

**轉碼器：**支援 GZIP、BZIP2 和 Deflate 壓縮類型。這三種類型都可供 Azure HDInsight 進行處理。每種壓縮轉碼器各有其獨特之處。例如，BZIP2 的複製輸送量最低，但卻有最佳的 Hive 查詢效能，因為可劃分處理；GZIP 可提供最均衡的選項，也最常被使用。您應選擇您的端對端案例最適用的轉碼器。

**層級：**對於每個壓縮轉碼器，您可以從兩個面向來選擇 - 壓縮速度最快和壓縮效能最佳。最快速的壓縮選項能以最快速度壓縮資料，但產生的檔案不一定經過最理想的壓縮。最佳化的壓縮選項會花費較長的壓縮時間，但產生最少量的資料。您可以測試這兩個選項，以查看何者在您的案例中可提供更好的整體效能。

**考量事項：**在內部部署存放區與雲端之間複製大型資料時 (此時公司網路與 Azure 之間的頻寬通常是限制因素)，如果您想要讓輸入資料集和輸出資料集都保有未壓縮的形式，您可以考慮使用**暫時性 Azure Blob** 進行壓縮。更具體來說，您可以將單一複製活動分成兩個複製活動：第一個複製活動以壓縮形式從來源複製到暫時性或預備 Blob，第二個複製活動則從預備環境複製壓縮的資料，並在寫入至接收器時加以解壓縮。

## 資料行對應的考量
複製活動中的 “columnMappings” 屬性可用來將所有或部分的輸入資料行對應至輸出資料行。從來源讀取資料之後，資料移動服務必須先對資料執行資料行對應，再將其寫入至接收器。這項額外處理會降低複製輸送量。

如果您的來源資料存放區是可查詢的 (例如 Azure SQL/SQL Server 之類的關聯式存放區，或 Azure 資料表/Azure DocumentDB 之類的 NoSQL 存放區)，您可以考慮將資料行篩選/重新排序邏輯深入至查詢屬性，而不使用資料行對應，如此將會在從來源資料存放區讀取資料期間執行預測，而大幅提高效率。

## 資料管理閘道器的考量
如需閘道器設定的建議，請參閱[使用資料管理閘道器的考量](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway)。

**閘道器電腦環境：**建議您使用專用的電腦來裝載資料管理閘道器。請使用 PerfMon 之類的工具，檢查您的閘道器電腦在複製作業期間的 CPU、記憶體和頻寬使用量。如果 CPU、記憶體或網路頻寬出現瓶頸，請改用更強大的電腦。

**並行複製活動執行：**資料管理閘道器的單一執行個體可以同時為多個複製活動執行提供服務。也就是說，一個閘道器可以同時執行多個複製作業 (並行作業數目會根據閘道器電腦的硬體組態來計算)。額外的作業會排入佇列中，直到閘道器加以取用或作業逾時，視何者先發生。若要避免閘道器上的資源爭用，您可以預備活動的排程，以減少同時排入佇列的複製作業數量，或考慮將負載劃分到多個閘道器上。


## 其他考量
如果要複製的資料很大，您可以調整您的商務邏輯，使用 Azure Data Factory 的切割機制進一步分割資料，並且更頻繁地排程複製活動，以縮減每個複製活動執行的資料大小。

請密切留意資料集數目，以及複製活動是否在任何時間點觸及相同的資料存放區。大量的並行複製作業可能會導致資料存放區出現瓶頸，並導致效能降低，複製作業內部重試，在某些情況下甚至導致執行失敗。

## 案例研究 – 從內部部署 SQL Server 複製到 Azure Blob
**案例：**建置從內部部署 SQL Server 將資料以 CSV 格式複製到 Azure Blob 的管線。為了加快複製速度，CSV 檔案依指定會以 BZIP2 格式進行壓縮。

**測試和分析：**可觀察到，複製活動的輸送量低於 2 MB/s，遠低於效能基準。

**效能分析和微調：**為了排解效能問題，我們將先逐步了解處理及移動資料的方式：

1.	**讀取資料：**閘道器開啟對 SQL Server 的連接，並傳送查詢。SQL Server 透過內部網路將資料流傳送至閘道器，以進行回應。
2.	閘道器將資料流**序列化**為 CSV 格式，並將資料**壓縮**為 BZIP2 資料流。
3.	**寫入資料：**閘道器透過網際網路將 BZIP2 資料流上傳至 Azure Blob。

如您所見，資料正在進行處理，並以串流序列的方式移動：SQL Server -> LAN -> 閘道器 -> WAN -> Azure Blob，**整體效能受限於管線的最小輸送量**。

![資料流](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

效能瓶頸可能是下列一或多個因素：

1.	**來源：**SQL Server 本身的輸送量偏低，因為負載過重。
2.	**資料管理閘道器：**
	1.	**LAN：**閘道器的位置遠離 SQL Server，因此只有低頻寬連線
	2.	**閘道器電腦上的負載**已達到其限制，而無法執行下列動作：
		1.	**序列化：**將資料流序列化為 CSV 時，輸送量偏低
		2.	**壓縮：**選擇了緩慢的壓縮轉碼器 (例如 BZIP2，採用 Core i7，速度為 2.8 MB/s)
	3.	**WAN：**公司網路與 Azure 之間的頻寬偏低 (例如，T1 = 1544 kbps、T2 = 6312 kbps)
4.	**接收器：**Azure Blob 具有低輸送量 (但不太可能發生，因為其 SLA 保證至少有 60 MB/s)。

在此情況下，BZIP2 資料壓縮可能會拖慢整個管線。改用 GZIP 壓縮轉碼器，可以緩解此瓶頸。


## 案例研究 - 平行複製  

**案例 I：**從內部部署檔案系統複製 1000 個 1 MB 的檔案至 Azure Blob 儲存體

**分析和效能微調︰**假設您已在四核心電腦上安裝資料管理閘道器，Data Factory 預設會使用 16 個平行複製，以並行方式從檔案系統中將檔案移至 Azure Blob。這應該會導致良好的輸送量。如果您想要的話，也可以明確指定平行複製計數。在複製大量的小型檔案時，平行複製可藉由更有效率地利用所涉及的資源，而對輸送量大有幫助。

![案例 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**案例 II：**從 Azure Blob 儲存體複製 20 個 Blob (每個 Blob 有 500 MB) 到 Azure Data Lake Store Analysis 並微調效能。

**分析和效能微調︰**在此案例中，Data Factory 預設會使用一個複製 (parallelCopies 為 1) 以及使用一個雲端資料移動單位，將資料從 Azure Blob 複製到 Azure Data Lake。您所觀察到的輸送量將會接近上面的[效能參考](#performance-reference)一節所述。

![案例 2](./media/data-factory-copy-activity-performance/scenario-2.png)

當個別檔案的大小大於數十 MB 且總數量很大時，增加 parallelCopies 並不會提升複製效能，因為單一雲端資料移動單位的資源有所限制。相反地，您應該指定更多個雲端資料移動單位，以取得更多用來執行資料移動的資源。請不要指定 parallelCopies 屬性的值，以便讓 Data Factory 為您處理平行處理原則。在此案例中，將 cloudDataMovementUnits 指定為 4 會讓輸送量變成大約 4 倍。

![案例 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## 資料存放區效能微調參考
以下是幾個支援的資料存放區所適用的一些效能監視及調整參考：

- Azure 儲存體 (包括 Azure Blob 和 Azure 資料表)：[Azure 儲存體的擴充性目標](../storage/storage-scalability-targets.md)和 [Azure 儲存體效能和擴充性檢查清單](../storage//storage-performance-checklist.md)
- Azure SQL Database：您可以[監視效能](../sql-database/sql-database-service-tiers.md#monitoring-performance)，並檢查資料庫交易單位 (DTU) 百分比。
- Azure SQL 資料倉儲：其能力會以資料倉儲單位 (DWU) 來測量。請參閱 [SQL 資料倉儲的彈性效能和調整功能](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
- Azure DocumentDB：[DocumentDB 中的效能層級](../documentdb/documentdb-performance-levels.md)。
- 內部部署 SQL Server：[效能的監視與微調](https://msdn.microsoft.com/library/ms189081.aspx)。
- 內部部署檔案伺服器：[檔案伺服器的效能微調](https://msdn.microsoft.com/library/dn567661.aspx)

<!---HONumber=AcomDC_0810_2016-->