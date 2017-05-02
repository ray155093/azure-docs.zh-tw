---
title: "Data Factory (資料整合服務) 簡介 | Microsoft Docs"
description: "了解 Azure Data Factory 是什麼：一項雲端資料整合服務，用來協調以及自動移動和轉換資料。"
keywords: "資料整合、雲端資料整合、azure data factory 是什麼"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Azure Data Factory 服務 (雲端中的資料整合服務) 簡介
## <a name="what-is-azure-data-factory"></a>Azure 資料處理站是什麼？
在巨量資料的世界裡，現有資料是如何運用在商業經營上？ 是否有可能使用內部部署資料來源或其他不同資料來源的資料，來擴充雲端所產生的資料？ 因此，您需要的是一個平台，以便彙總並處理來自各種來源的資料。 Azure Data Factory 是雲端架構資料整合服務，用來協調以及自動**移動**和**轉換**資料。 您可以建立資料整合解決方案，以從不同資料存放區擷取輸入資料、轉換/處理資料，並將輸出資料發佈至資料存放區。 

![圖表︰Data Factory 概觀 (資料整合服務)](./media/data-factory-introduction/what-is-azure-data-factory.png)

**圖 1.** 從各種資料來源擷取資料，準備、轉換和分析資料，然後發佈立即可用的資料以供取用。


## <a name="what-does-it-offer"></a>它能提供什麼？ 
傳統上，資料整合專案的主軸都是圍繞在建立「擷取、轉換和載入 (ETL)」的程序，以從組織內的各種資料來源擷取資料，轉換資料以符合企業資料倉儲 (EDW) 的目標結構描述，然後將資料載入至 EDW，整個過程如下圖所示。 接著，系統會存取 EDW，將其作為 BI 分析解決方案的單一真實來源。

![傳統的 ETL](media/data-factory-introduction/traditional-etl.png)
**傳統的 ETL**

企業現今的資料態勢是，數量、多樣性與複雜性正以指數方式成長，如下圖所示。 不同形式和速度的內部部署資料與雲端架構資料，變得比以往更加多樣。 資料處理必須跨地理位置來進行，過程中還會結合開放原始碼軟體、商業解決方案和自訂處理服務，但這些技術的整合和維護不僅成本昂貴，而且難以施行。 靈活性是因應現今多變巨量資料態勢所需的特質，而這也是一個機會，您可以藉此機會將傳統的 EDW 與現代資訊生產系統所需的功能合併。 Azure Data Factory 是一個組合平台，可讓您跨越傳統 EDW 與多變的資料態勢進行工作，讓企業得以利用所有資料以據此做出決策。

![新的巨量資料態勢](media/data-factory-introduction/new-big-data-landscape.png)
**新的巨量資料態勢**

Azure Data Factory 服務可讓企業利用這種多樣性，它會提供一個平台供企業**將資料的處理、儲存和移動服務組合成資訊生產管線**，並讓企業管理受信任的資料資產。

Azure Data Factory 服務可讓您︰
- **輕鬆地使用不同的資料儲存和處理系統**。 

    企業會在有不同類型的資料位於不同的來源中。 建置資訊生產系統的第一步，就是連線到所有必要的資料和處理來源 (例如 SaaS 服務、檔案共用、FTP、Web 服務)，然後視需要將資料移動到集中位置以進行後續處理。

    沒有 Data Factory，企業必須建置自訂的資料移動元件或撰寫自訂服務，以整合這些資料來源和處理。 此類系統不僅昂貴，而且難以整合和維護，且通常缺乏企業等級的監視和警示功能，以及完全受管理的服務所能提供的控制力。

    有了 Data Factory，您就可以使用資料管線中的複製活動，將內部部署和雲端來源資料存放區內的資料全都移動到雲端中的集中資料存放區，以供進一步分析。 例如，您可以收集 Azure Data Lake Store 中的資料，之後使用 Azure Data Lake Analytics 計算服務來轉換資料。 或者，收集 Azure Blob 儲存體中的資料，之後使用 Azure HDInsight Hadoop 叢集來轉換資料。
- **將資料轉換成受信任的資訊**。 

    當資料存在於雲端中的集中式資料存放區之後，您想要編寫和部署資料管線，在可維護且可控制的排程中可靠地產生轉換的資料，以將信任的資料饋送至生產環境。 Azure Data Factory 中的資料轉換會由轉換活動 (例如 Hive、Pig、MapReduce、Azure Machine Learning Batch 執行) 來執行，以及由自訂 C# 活動 (執行於 Azure HDInsight Hadoop 叢集、Azure Machine Learning VM 或 Azure Batch VM 集區上) 來執行。
- **集中監視資料管線**。

    透過多元的資料產品組合，提供可靠且完整的儲存、處理和資料移動服務檢視。 Data Factory 可協助您快速評估端對端資料管線健全狀況、固定點問題，以及視需要採取更正動作。 以視覺方式追蹤資料歷程和各資料來源之間的關係。 從單一監控儀表板查看工作執行、系統健全狀況和相依性的完整歷程。

## <a name="how-does-it-work"></a>運作方式 
Azure Data Factory 有三個資訊生產階段︰

![三個資訊生產階段](media/data-factory-introduction/three-information-production-stages.png)

- **連線和收集**︰在這個階段，系統會將各種資料來源的資料收集在同一個位置。
- **轉換和擴充**︰在這個階段，系統會處理或轉換所收集的資料。
- **發佈**。 在這個階段，系統會發佈資料，讓資料可供 BI 工具、分析工具和其他應用程式取用。

## <a name="key-components"></a>重要元件
Azure 訂用帳戶可能會有一或多個 Azure Data Factory 執行個體 (或資料處理站)。 Azure Data Factory 是由四個重要元件所組成，這些元件會一起運作，以提供平台供您撰寫簡單到複雜的資料移動和轉換協調流程，來形成您的資料流程。

### <a name="activity"></a>活動
活動會定義在您資料上執行的動作。 例如，您可以使用複製活動將資料從某個資料存放區複製到另一個資料存放區。 同樣地，您可以使用在 Azure HDInsight 叢集上執行 Hive 查詢的 Hive 活動，來轉換或分析您的資料。 Data Factory 支援兩種活動類型︰資料移動活動和資料轉換活動。

每個活動可以有零個或多個輸入資料集，並且會產生一個或多個輸出資料集。 


### <a name="data-movement-activities"></a>資料移動活動
Data Factory 中的複製活動會將資料從來源資料存放區複製到接收資料存放區。 Data Factory 支援下列資料存放區。 可將來自任何來源的資料寫入任何接收器。 按一下資料存放區，即可了解如何將資料複製到該存放區，以及從該存放區複製資料。

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

如需詳細資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)文章。

### <a name="data-transformation-activities"></a>資料轉換活動
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

如需詳細資訊，請參閱[資料轉換活動](data-factory-data-transformation-activities.md)文章。

如果您需要將資料移入/移出「複製活動」不支援的資料存放區，或使用您自己的邏輯轉換資料，請建立 **自訂 .NET 活動**。 如需有關建立及使用自訂活動的詳細資料，請參閱 [在 Azure Data Factory 管線中使用自訂活動](data-factory-use-custom-activities.md)。

### <a name="pipeline"></a>管線
管線是一組活動。 管線中的活動會合作執行一項工作。 例如，管線可能包含一組活動，以從 Azure Blob 擷取資料，然後對 HDInsight 叢集執行 Hive 查詢以分割記錄資料。 這麼做的好處是，您可以將這些活動作為一個集合來進行管線，而不是個別管理每個活動。 例如，您可以部署管線並為其排程，而非獨立地對每個活動進行這些工作。

### <a name="datasets"></a>資料集
資料集代表資料存放區中的資料結構，其只會指出或參考您要在活動中作為輸入或輸出的資料。 例如，Azure Blob 資料集會指定管線應從中讀取資料之 Azure Blob 儲存體中的 Blob 容器和資料夾。 

### <a name="linked-services"></a>連結的服務
連結的服務非常類似連接字串，後者會定義 Data Factory 要連線到外部資源所需的連線資訊。 這麼說吧：資料集代表資料的結構，連結的服務則會定義與資料來源的連線。  Data Factory 中的連結服務，有兩個用途：

* 用來代表 **資料存放區** ，其中包含 (但不限於) 內部部署 SQL Server、Oracle 資料庫、檔案共用或 Azure Blob 儲存體帳戶。 如需支援的資料存放區清單，請參閱 [資料移動活動](#data-movement-activities) 一節。
* 用來代表可裝載活動執行的 **計算資源** 。 例如，HDInsightHive 活動會在 HDInsight Hadoop 叢集上執行。 如需支援的計算環境清單，請參閱[資料轉換活動](#data-transformation-activities)一節。

### <a name="relationship-between-data-factory-entities"></a>Data Factory 實體之間的關聯性
![圖表︰Data Factory 概觀 (雲端資料整合服務) - 重要概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**圖 2.** 資料集、活動、管線和連結服務之間的關聯性。

## <a name="supported-regions"></a>支援區域
您目前可以在 [美國西部]、[美國東部] 和 [北歐] 區域建立 Data Factory。 不過，Data Factory 可以存取其他 Azure 區域的資料存放區和計算資料，以在資料存放區之間移動資料或使用計算服務處理資料。

Azure Data Factory 本身不會儲存任何資料。 它可讓您建立資料驅動的流程，以協調[所支援資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)之間的資料移動，以及使用[計算服務](data-factory-compute-linked-services.md)在其他區域或內部部署環境中處理資料。 它也可讓您使用程式設計方式和 UI 機制 [監視和管理工作流程](data-factory-monitor-manage-pipelines.md) 。

即使 Azure Data Factory 只能在**美國西部**、**美國東部**和**北歐**區域使用，但 Data Factory 中支援資料移動的服務可在[全球](data-factory-data-movement-activities.md#global)好幾個區域中使用。 如果資料存放區位於防火牆後面，則會改由內部部署環境中所安裝的 [資料管理閘道](data-factory-move-data-between-onprem-and-cloud.md) 移動資料。

如需範例，讓我們假設您的計算環境 (例如 Azure HDInsight 叢集和 Azure 機器學習服務) 即將用盡西歐區域的資源。 您可以在北歐建立和利用 Azure Data Factory 執行個體，並用它排程您在西歐區域之計算環境上的工作。 只要幾毫秒的時間，Data Factory 就能觸發計算環境上的作業，但執行計算環境上作業所需的時間則不會改變。

未來我們計畫讓 Azure 在每個地理位置都能支援 Azure Data Factory。

## <a name="next-steps"></a>後續步驟
若要了解如何建置具有資料管線的 Data Factory，請遵循下列教學課程中的逐步指示：

| 教學課程 | 說明 |
| --- | --- |
| [在兩個雲端資料存放區之間移動資料](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |在本教學課程中，您會建立具有管線的 Data Factory，以從 Blob 儲存體 **移動資料** 至 SQL Database。 |
| [使用 Hadoop 叢集轉換資料](data-factory-build-your-first-pipeline.md) |在本教學課程中，您會在 Azure HDInsight (Hadoop) 叢集上執行 Hive 指令碼，以建立您的第一個 Azure Data Factory 與用來 **處理資料** 的資料管線。 |
| [使用資料管理閘道，在內部部署資料存放區與雲端資料存放區之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) |在本教學課程中，您會建置具有管線的 Data Factory，以從**內部部署** SQL Server 資料庫**移動資料**至 Azure Blob。 在逐步解說中，您會在電腦上安裝及設定資料管理閘道。 |

