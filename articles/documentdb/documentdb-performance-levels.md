---
title: "DocumentDB 中的效能等級 | Microsoft Docs"
description: "了解 DocumentDB 中的效能等級如何可讓您依每個集合為基礎保留輸送量。"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: ed44ca2076860128b175888748cdaa8794c2310d
ms.openlocfilehash: 237a92713ee8dca72a09550c47519189f2fd23cc


---
# <a name="performance-levels-and-pricing-tiers-in-documentdb"></a>DocumentDB 中的效能層級和定價層
本文提供 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)中的效能層級概觀。

閱讀本文後，您將能夠回答下列問題：  

* 什麼是效能等級？
* 如何為資料庫帳戶保留輸送量？
* 如何處理效能等級？
* 效能等級的收費如何？

## <a name="introduction-to-performance-levels"></a>效能等級的簡介
在標準 DocumentDB 帳戶下建立的每個 DocumentDB 集合將會佈建相關聯的效能等級。 在資料庫中的每一個集合可以有不同的效能等級，讓您為經常存取的集合指定較多的輸送量，以及為較不常存取的集合指定較少的輸送量。 

DocumentDB 同時支援**使用者定義**的效能等級和**預先定義**的效能等級，如下表所示。  使用者定義的效能可讓您以 100 RU/秒單位保留輸送量，並具有無限制的儲存體，而預先定義的三個效能層級已指定輸送量選項，以及 10 GB 的儲存配額。 下表比較**使用者定義**的效能與**預先定義**的效能。

|效能類型|詳細資料|輸送量|儲存體|版本|API|
|----------------|-------|----------|-------|-------|----|
|使用者定義的效能|使用者設定 100 RU/秒單位的輸送量|無限|無限|V2|API 2015-12-16 和更新版本|
|預先定義的效能|10 GB 保留儲存體。<br><br>S1 = 250 RU/秒<br>S2 = 1000 RU/秒<br>S3 = 2500 RU/秒|2500 RU/s|10 GB|V1|任意|

輸送量會對每個集合保留，並且可供該集合所專用。 輸送量的測量是根據[要求單位 (RU)](documentdb-request-units.md)，可識別執行各種 DocumentDB 資料庫作業所需的資源量。

> [!NOTE]
> 您可以透過 [SDK](documentdb-sdk-dotnet.md) 或 [Azure 入口網站](https://portal.azure.com/)調整集合的效能等級。 效能層級變更預期會在 3 分鐘內完成。
> 
> 

## <a name="setting-performance-levels-for-collections"></a>設定集合的效能等級
一旦建立集合，將保留集合根據指定效能等級的 RU 完整配置。

請注意，同時使用使用者定義和預先定義的效能等級時，DocumentDB 會根據保留的輸送量運作。 藉由建立集合，已保留某個應用程式，並對其就保留的輸送量收費，不論該輸送量中有多少在使用中。 使用使用者定義的效能等級時，儲存體是根據耗用量計量，但是使用預先定義的效能等級時，會在集合建立時保留 10 GB 的儲存體。  

建立集合之後，您可以使用 [SDK](documentdb-sdk-dotnet.md) 或 [Azure 入口網站](https://portal.azure.com/)來修改效能層級和/或輸送量。

> [!IMPORTANT]
> DocumentDB 標準集合是依每小時費率收費，並且您建立的每個集合將被收取最少一小時的使用量。
> 
> 

如果您將集合的效能等級調整在一個小時內，您仍會被收取在該小時期間所設定最高的效能等級。 例如，如果您在上午 8:53 增加某個集合的效能等級，您會被從上午 8:00 開始收取新的層級費用。 同樣地，如果您在上午 8:53 減少效能等級，則會在上午 9:00 套用新的費率。

要求單位是根據設定的效能等級保留給每個集合。 要求單位消耗量是以每秒的速率來計算。 如果應用程式的速率超過集合上佈建的要求單位速率 (或效能等級)，便會受到節流控制，直到該速率降到該集合的預留層級以下。 如果您的應用程式需要較高等級的輸送量，您可以增加每個集合的效能等級。

> [!NOTE]
> 當您的應用程式超過一個或多個集合的效能等級時，要求將會依每個集合為基礎降低。 這表示某些應用程式的要求可能會成功，而其他的可能受到節流控制。 建議您在受到節流控制時新增少量的重試次數，以便處理要求流量突然暴增的情況。
> 
> 

## <a name="working-with-performance-levels"></a>處理效能等級
DocumentDB 集合可讓您根據查詢模式和應用程式的效能需求來群組資料。 藉由 DocumentDB 的自動索引和查詢支援，在相同集合內共置異質文件是相當常見的。 決定是否應該使用不同集合的重要考量包括：

* 查詢 – 集合是查詢執行的範圍。 如果您需要跨一組文件進行查詢，最有效率的讀取模式是來自共置在單一集合的文件。
* 交易 - 所有交易的範圍為單一集合。 如果您有必須在單一預存程序或觸發中更新的文件，它們必須儲存在相同集合內。 更具體來說，集合內的分割索引鍵是交易界限。 如需詳細資訊，請參閱 [DocumentDB 中的分割](documentdb-partition-data.md) 。
* 效能隔離 - 集合具有相關聯的效能等級。 這可確保每個集合透過保留的 RU 具有可預測的效能。 資料可以根據存取頻率，使用不同的效能等級配置到不同的集合。

> [!IMPORTANT]
> 務必了解將根據在您的應用程式內建立的集合數目，向您收取完整的標準費率。
> 
> 

建議您的應用程式使用少量的集合，除非您具有大量儲存體或輸送量的需求。 確定您更加了解建立新集合的應用程式模式。 您可以選擇將集合建立保留做為在您的應用程式外部處理的管理動作。 同樣地，調整集合的效能等級也將變更對集合收取的小時費率。 如果您的應用程式動態調整等級，您應該監控集合的效能等級。

## <a name="a-idchanging-performance-levels-using-the-azure-portalachange-from-s1-s2-s3-to-user-defined-performance"></a><a id="changing-performance-levels-using-the-azure-portal"></a>從 S1、S2、S3 變更為使用者定義的效能
請遵循下列步驟，在 Azure 入口網站中從使用預先定義的輸送量層級變更為使用使用者定義的輸送量層級。 藉由使用使用者定義的輸送量層級，您可以調整輸送量以符合需求。 如果您仍在使用 S1 帳戶，只須按幾下滑鼠，就可以將預設輸送量從 250 RU/s 增加至 400 RU/s。 請注意，一旦您將集合從 S1、S2 或 S3 集合移到標準 (使用者定義)，即不能移回至 S1、S2 或 S3，不過，您隨時可以修改標準集合的輸送量。

如需與使用者定義的輸送量和預先定義的輸送量相關的價格變化詳細資訊，請參閱部落格文章 [DocumentDB：新價格選項的所有使用須知](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureDocumentDB/ChangeDocumentDBCollectionPerformance/player]
> 
> 

1. 在 [**Azure 入口網站**](https://portal.azure.com)中，按一下 [NoSQL (DocumentDB)]，然後選取要修改的 DocumentDB 帳戶。 
 
    如果 **NoSQL (DocumentDB)** 不在動態工具列中，按一下 >，捲動到 [資料庫]，選取 [NoSQL (DocumentDB)] ，然後選取 DocumentDB 帳戶。  

2. 在 [資源] 功能表上，於 [集合] 下，按一下 [級別]，從下拉式清單選取要修改的集合，然後按一下 [定價層]。 使用預先定義的輸送量的帳戶具有 S1、S2 或 S3 定價層。  在 [選擇定價層] 刀鋒視窗中按一下 [標準] 來變更使用者定義的輸送量，然後按一下 [選取] 以儲存變更。

    ![[設定] 刀鋒視窗的螢幕擷取畫面，其中顯示可供變更輸送量值的位置](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

3. 回到 [調整] 刀鋒視窗中，[定價層] 已變更為 [標準]，並且 [輸送量 (RU/秒)] 方塊會顯示預設值 400。 將輸送量設定為介於 400 到 10,000 個 [要求單位](documentdb-request-units.md)/秒 (RU/秒)。 頁面底部的 [估計每月帳單] 會自動更新以提供每月成本估計。 按一下 [確定] 儲存變更。

    如果您判斷您需要更多輸送量 (大於 10,000 RU/秒) 或更多儲存體 (大於 10 GB)，您可以建立資料分割的集合。 若要建立資料分割的集合，請參閱 [建立集合](documentdb-create-collection.md)。

> [!NOTE]
> 變更集合的效能層級最多需要 2 分鐘。
> 
> 

## <a name="changing-performance-levels-using-the-net-sdk"></a>使用 .NET SDK 變更效能層級
變更集合的效能層級的另一個選項是透過我們的 SDK。 本節只涵蓋使用我們的 [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) 來變更集合的效能層級，但程序類似於我們的其他 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 如果您不熟悉我們的 .NET SDK，請瀏覽我們的 [開始使用教學課程](documentdb-get-started.md)。

以下是變更供應項目輸送量為每秒 50000 要求單位的程式碼片段：

    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);

    // Set the throughput to S2
    offer = new Offer(offer);
    offer.OfferType = "S2";

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);



> [!NOTE]
> 以每秒 10000 要求單位佈建的集合，可以隨時在使用使用者定義的輸送量和預先定義的輸送量 (S1、S2、S3) 的供應項目之間移轉。 以超過每秒 10000 要求單位佈建的集合不能轉換為預先定義的輸送量等級。
> 
> 

請造訪 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) 以檢視其他範例，並深入了解我們提供的方法：

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## <a name="a-idchange-throughputachanging-the-throughput-of-a-collection"></a><a id="change-throughput"></a>變更集合的輸送量
如果您已經在使用使用者定義的效能，您可以透過下列方式變更集合的輸送量。 如果您需要從 S1、S2 或 S3 效能層級 (預先定義的效能) 變更為使用者定義的效能，請參閱 [從 S1、S2、S3 變更為使用者定義的效能](#changing-performance-levels-using-the-azure-portal)。

1. 在 [**Azure 入口網站**](https://portal.azure.com)中，按一下 [NoSQL (DocumentDB)]，然後選取要修改的 DocumentDB 帳戶。    
2. 在 [資源] 功能表中，於 [集合] 下，按一下 [級別]，從下拉式清單選取要修改的集合。
3. 在 [輸送量 (RU/秒)] 方塊中，輸入新的輸送量層級。 
   
    頁面底部的 [估計每月帳單] 會自動更新以提供每月成本估計。 按一下 [確定] 儲存變更。

    如果您不確定要增加多少輸送量，請參閱[估計輸送量需求](documentdb-request-units.md#estimating-throughput-needs)和[要求單位計算機](https://www.documentdb.com/capacityplanner)。

## <a name="troubleshooting"></a>疑難排解

如果在 [選擇定價層] 刀鋒視窗中看不到在 可在 S1、S2 或 S3 效能層級之間變更的選項，請按一下 [檢視全部] 來顯示標準、S1、S2 和 S3 效能層級。 如果您使用標準定價層，即無法在 S1、S2 和 S3 間變更。

![[選擇您的定價層] 刀鋒視窗螢幕擷取畫面，並將 [檢視全部] 反白顯示](./media/documentdb-performance-levels/azure-documentdb-database-view-all-performance-levels.png)

一旦將集合從 S1、S2 或 S3 變更為標準，即不能改回 S1、S2 或 S3。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure DocumentDB 的價格和管理資料，請探索這些資源：

* [DocumentDB 價格](https://azure.microsoft.com/pricing/details/documentdb/)
* [在 DocumentDB 中模型化資料](documentdb-modeling-data.md)
* [在 DocumentDB 中分割資料](documentdb-partition-data.md)
* [要求單位](http://go.microsoft.com/fwlink/?LinkId=735027)

若要深入了解 DocumentDB，請參閱 Azure DocumentDB [文件](https://azure.microsoft.com/documentation/services/documentdb/)。

若要開始使用 DocumentDB 的相關規模和效能測試，請參閱 [Azure DocumentDB 的相關效能和規模測試](documentdb-performance-testing.md)。

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png



<!--HONumber=Jan17_HO2-->


