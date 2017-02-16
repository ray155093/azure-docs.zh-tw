---
title: "DocumentDB 規模和效能測試 | Microsoft Docs"
description: "了解如何執行 Azure DocumentDB 的相關規模和效能測試"
keywords: "效能測試"
services: documentdb
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 6b82132b24fb2be3f0d8ab5112c712c27edb733e
ms.openlocfilehash: 4d7754e73abedb704b81fa0001e1957422bde1b3


---
# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Azure DocumentDB 的效能和規模測試
效能和規模測試是應用程式開發過程中的關鍵步驟。 對許多應用程式來說，資料庫層對整體效能和延展性具有相當重大的影響，因此是效能測試的重要元件。 [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 是為了能夠彈性延展及獲得可預測的效能而建置，因此非常適合需要高效能資料庫層的應用程式。 

這篇文章適合做為針對其 DocumentDB 工作負載實作效能測試套件或針對高效能應用程式案例評估 DocumentDB 之開發人員的參考。 主要是著重在隔離的資料庫效能測試，但也包含適用於實際執行應用程式的最佳做法。

閱讀本文後，您將能夠回答下列問題：   

* 哪裡可以找到可供進行 Azure DocumentDB 效能測試的範例 .NET 用戶端應用程式？ 
* 如何藉由 Azure DocumentDB 從我的用戶端應用程式達到高輸送量層級？

若要開始使用程式碼，請從 [DocumentDB 效能測試範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)下載專案。 

> [!NOTE]
> 此應用程式的目標，在於示範透過少數用戶端電腦發揮 DocumentDB 更好效能的最佳作法。 而不是要示範可以無限制調整的服務尖峰容量。
> 
> 

如果您要尋找用戶端設定選項，以改善 DocumentDB 效能，請參閱 [DocumentDB 效能祕訣](documentdb-performance-tips.md)。

## <a name="run-the-performance-testing-application"></a>執行效能測試應用程式
若要開始使用，最快的方法就是依以下步驟所述，編譯並執行下面的 .NET 範例。 您也可以檢閱原始程式碼，然後對自己的用戶端應用程式實作類似的組態。

**步驟 1：** 從 [DocumentDB 效能測試範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)下載專案，或建立 GitHub 儲存機制分支。

**步驟 2：** 修改 App.config 中 EndpointUrl、AuthorizationKey、CollectionThroughput 及 DocumentTemplate (選擇性) 的設定。

> [!NOTE]
> 以高輸送量佈建集合之前，請參閱 [價格頁面](https://azure.microsoft.com/pricing/details/documentdb/) 以估算每個集合的成本。 DocumentDB 對儲存體和輸送量是以小時為基礎獨立計費，因此您可以藉由在測試後刪除或降低 DocumentDB 集合的輸送量來節省成本。
> 
> 

**步驟 3：** 從命令列編譯並執行主控台應用程式。 您應該會看到如以下的輸出：

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**步驟 4 (如有必要)：** 從工具回報的輸送量 (RU/秒) 應該等於或大於佈建的集合輸送量。 如果情況並非如此，向上微調 DegreeOfParallelism 可協助您達到該限制。 如果來自用戶端應用程式的輸送量達持平狀態，在相同或不同機器上啟動多個應用程式執行個體將可協助您在各個不同的執行個體達到所佈建的限制。 如果您需要協助進行這個步驟，請撰寫電子郵件寄至 askdocdb@microsoft.com 或在 [Azure 入口網站](https://portal.azure.com)提出支援票證。

讓應用程式處於執行狀態之後，您便可以嘗試不同的[索引編製原則](documentdb-indexing-policies.md)和[一致性層級](documentdb-consistency-levels.md)，以了解它們對輸送量和延遲的影響。 您也可以檢閱原始程式碼，然後對自己的測試套件或實際執行應用程式實作類似的組態。

## <a name="next-steps"></a>後續步驟
在這篇文章中，我們探討了如何使用 .NET 主控台應用程式來執行 DocumentDB 的相關效能和規模測試。 如需有關使用 DocumentDB 的其他資訊，請參閱下面的連結。

* [DocumentDB 效能測試範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [用以改善 DocumentDB 效能的用戶端設定選項](documentdb-performance-tips.md)
* [DocumentDB 中的伺服器端資料分割](documentdb-partition-data.md)
* [DocumentDB 集合和效能等級](documentdb-performance-levels.md)
* [MSDN 上的 DocumentDB .NET SDK 文件](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB .NET 範例](https://github.com/Azure/azure-documentdb-net)
* [有關效能秘訣的 DocumentDB 部落格](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)




<!--HONumber=Jan17_HO3-->


