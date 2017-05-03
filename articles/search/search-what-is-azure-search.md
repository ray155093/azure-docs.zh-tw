---
title: "什麼是 Azure 搜尋服務 | Microsoft Docs"
description: "Azure 搜尋服務是受完整管理的託管雲端搜尋服務。 深入了解此功能概觀。"
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/24/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 3720aed95706f6e8dd9a28085a6de0bc1f756a9b
ms.lasthandoff: 04/26/2017

---
# <a name="what-is-azure-search"></a>何謂 Azure 搜尋服務？
Azure 搜尋服務是一項雲端搜尋即服務解決方案，可將伺服器和基礎結構管理委託給 Microsoft，讓您利用立即可用的服務來填入搜尋資料，然後用來在 Web 或行動應用程式中新增搜尋。 Azure 搜尋服務可讓您使用簡單的 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 或 [.NET SDK](search-howto-dotnet-sdk.md)，輕鬆地將強大的搜尋經驗新增至應用程式，而不需要管理搜尋基礎結構或成為搜尋專家。

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>在應用程式或網站中內嵌功能強大的搜尋體驗


### <a name="full-text-search-and-text-analysis"></a>全文檢索搜尋和文字分析

利用[簡單的查詢語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)就能建立查詢，包括邏輯運算子、片語搜尋運算子、後置運算子、優先順序運算子。 此外， [Lucene 查詢語法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 可以啟用模糊搜尋、鄰近搜尋、詞彙提升及規則運算式。 Azure 搜尋服務也支援[自訂語彙分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)，可讓您的應用程式利用語音比對和規則運算式，處理複雜的搜尋查詢。

### <a name="language-support"></a>語言支援

Azure 搜尋服務支援 [56 種不同語言](https://docs.microsoft.com/rest/api/searchservice/language-support)的語彙分析器。 由於同時使用 Lucene 分析器和 Microsoft 分析器 (經由 Office 和 Bing 中數年的自然語言處理改善)，因此「Azure 搜尋服務」能夠分析您應用程式搜尋方塊中的文字，以智慧方式處理語言專屬的語言學，包括動詞時態、性別、不規則複數名詞 (例如 ' mouse' 與 'mice')、拆解複合字、斷字 (針對沒有空格的語言) 等等。

### <a name="data-integration"></a>資料整合

您可以推送 JSON 資料結構以填入 Azure 搜尋服務索引。 此外，對於支援的資料來源，您可以使用[索引子](search-indexer-overview.md)，自動耙梳 Azure SQL Database、Azure DocumentDB 或 [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)，以同步化搜尋索引的內容與主要資料存放區。

**文件破解**支援[檢索主要檔案格式](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office、PDF 和 HTML 文件。

### <a name="search-experience"></a>搜尋體驗

+ 您可以在自動完成的搜尋列和預先輸入的查詢上啟用**搜尋建議**。 [建議您索引中的實際文件](https://docs.microsoft.com/rest/api/searchservice/suggesters) 。

+ 透過[單一查詢參數](https://docs.microsoft.com/azure/search/search-faceted-navigation)就能啟用**多面向導覽**。 Azure 搜尋服務會傳回多面向導覽結構，可做為類別清單背後的程式碼，用於自我引導篩選 (例如，依價格範圍或品牌來篩選類別目錄項目)。

+ **篩選** 可用來將多面向導覽納入應用程式的 UI、增強查詢編寫，以及根據使用者或開發人員指定的準則進行篩選。 使用 [OData 語法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)建立篩選器。

+ **搜尋結果醒目提示**[會將視覺化格式套用至搜尋結果中的相符關鍵字](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 您可以選擇哪些欄位傳回醒目提示的文字片段。

+ **簡單評分** 是 Azure 搜尋服務的主要優點。 [評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)用於將相關性模型化為文件本身中的值函數。 例如，您可能想要新推出的產品或折扣的產品，出現在搜尋結果中較高的位置。 您也可以根據您所追蹤並個別儲存的客戶搜尋喜好設定，使用標記進行個人化計分來建置計分設定檔。

+ **排序** 是透過索引結構描述來對多個欄位提供，然後使用單一搜尋參數在查詢階段進行切換。

+ **微調控制項** ，直接對您的搜尋結果進行 [分頁](search-pagination-page-layout.md) 和節流。  

### <a name="geosearch"></a>地理搜尋功能

Azure 搜尋服務以智慧方式處理、篩選和顯示地理位置。 可讓使用者根據指定位置的搜尋結果鄰近程度，或根據特定的地理區域來瀏覽資料。 本教學影片示範其運作方式︰ [Channel 9︰Azure 搜尋服務和地理空間資料](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)。

### <a name="cloud-service-advantages"></a>雲端服務的優點

+ **高可用性** 可確保相當可靠的搜尋服務體驗。 經過適當的調整， [Azure 搜尋服務可提供 99.9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

+ **受到完整管理** ，所以完全不需要基礎結構管理。 以兩種方式調整您的服務，即可針對您的需求量身訂做，以處理更多的文件儲存體、更高的查詢負載，或兩者。

### <a name="monitoring-and-reporting"></a>監視和報告

+ [收集和分析](search-traffic-analytics.md)**搜尋流量分析**，以便深入分析使用者在搜尋方塊中輸入的內容。

+ 自動會擷取每秒查詢次數、延遲和節流的計量，並在入口網站頁面中報告，不需要其他設定。 您也可以輕鬆地監視索引和文件計數，以便視需要調整容量。 

### <a name="tools-for-prototyping-and-inspection"></a>用於原型設計和檢查的工具

在入口網站中，您可以使用 [匯入資料] 精靈設定索引子、使用索引設計工具建立索引，以及使用 [搜尋總管] 直接從您帳戶的 Azure 入口網站查詢所有索引，以便測試查詢並修改評分設定檔。 您也可以開啟任何索引，以檢視其結構描述。

## <a name="how-it-works"></a>運作方式
### <a name="step-1-provision-service"></a>步驟 1：佈建服務
您可以使用 [Azure 入口網站](https://portal.azure.com/)或 [Azure 資源管理 API](https://msdn.microsoft.com/library/azure/dn832684.aspx) 來加速「Azure 搜尋服務」。

根據設定搜尋服務的方式，您將使用與其他 Azure 搜尋服務訂閱者共用的免費層服務，或供應資源專供您服務使用的 [付費層](https://azure.microsoft.com/pricing/details/search/) 。 佈建您的服務時，您也可以選擇裝載服務的資料中心區域。

根據您選擇的服務層而定，您可以在兩方面調整您的服務：1) 新增複本以提升您處理繁重查詢負載的能力，以及 2) 新增資料分割以增加可容納更多文件的儲存體。 您可以分開處理文件儲存體和查詢輸送量，進而自訂您的搜尋服務以滿足特定需求。

### <a name="step-2-create-index"></a>步驟 2：建立索引
您必須先定義 Azure 搜尋服務索引，才可以將您的內容上傳至 Azure 搜尋服務。 索引就像是資料庫資料表，其中保存您的資料並可接受搜尋查詢。 您可定義索引結構描述，以對應至您要搜尋的文件結構 (類似於資料庫中的欄位)。

您可以在 Azure 入口網站中，或[使用 .NET SDK](search-howto-dotnet-sdk.md) 或 [REST API](https://msdn.microsoft.com/library/azure/dn798941.aspx) 透過程式設計方式，建立這些索引的結構描述。 一旦定義索引之後，您就可以將資料上傳至 Azure 搜尋服務，接著編製索引。

### <a name="step-3-index-data"></a>步驟 3︰索引資料
定義索引的欄位和屬性之後，即可將您的內容上傳至索引。 您可以使用發送或提取模型，將資料上傳至索引。

提取模型是透過可以針對需要或排定的更新而設定的索引子提供的 (請參閱 [索引子作業 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn946891.aspx))，可讓您輕鬆地從裝載於 Azure VM 的 Azure DocumentDB、Azure SQL Database、Azure Blob 儲存體或 SQL Server 擷取資料和資料變更。

推送模式是透過 SDK 或 REST API 來提供，可用來將已更新的文件傳送到索引。 使用 JSON 格式，您幾乎可以從任何資料集發送資料。 如需載入資料的指引，請參閱[新增、更新或刪除文件](https://msdn.microsoft.com/library/azure/dn798930.aspx)或[如何使用 .NET SDK](search-howto-dotnet-sdk.md)。

### <a name="step-4-search"></a>步驟 4︰搜尋
填入您的 Azure Search 索引後，您現在可以透過 REST API 或 .NET SDK 使用簡單的 HTTP 要求對服務端點 [發出搜尋查詢](https://msdn.microsoft.com/library/azure/dn798927.aspx) 。

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

雖然在入口網站中可以執行許多工作，但 Azure 搜尋服務主要是給想要將搜尋功能整合到現有應用程式的開發人員。 有下列程式設計介面可用。

|平台 |說明 |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | 任何程式設計平台和語言 (包括 Xamarin、Java 和 JavaScript) 支援的 HTTP 指令|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API 的 .NET 包裝函式支援使用 C# 和以 .NET Framework 為目標的其他受管理程式碼語言，有效率地撰寫程式碼 |

## <a name="free-trial"></a>免費試用
Azure 訂閱者可以[在免費層中佈建服務](search-create-service-portal.md)。

如果您不是訂閱者，您可以[免費申請 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)：您會獲得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留帳戶並使用免費的 Azure 服務，例如「網站」。 除非您明確變更您的設定且同意付費，否則我們將不會從您的信用卡收取任何費用。

或者，您也可以[啟用 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)：您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。 

## <a name="watch-a-short-video"></a>觀看短片

搜尋引擎是行動裝置應用程式、Web 和公司資料存放區中推動資訊擷取的常用工具。 Azure 搜尋服務提供工具讓您創造類似大型商業網站上的搜尋體驗。 程式管理員 Liam Cavanagh 的這部 9 分鐘影片，說明整合搜尋引擎如何有益於您的應用程式、Azure 搜尋服務中的主要功能，以及一般工作流程的運作情形。 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 分鐘涵蓋主要功能和使用案例。
+ 3-4 分鐘涵蓋服務佈建。 
+ 4-6 分鐘涵蓋使用「匯入資料」精靈，以內建的房地產資料集建立索引。
+ 6-9 分鐘涵蓋「搜尋總管」和各種查詢。



