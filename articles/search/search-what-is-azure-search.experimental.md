---
title: "什麼是 Azure 搜尋服務 | Microsoft Docs"
description: "具有進階搜尋功能的搜尋引擎，支援自訂網站、應用程式及公司資料和檔案存放區中的搜尋列。"
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: heidist
experimental: true
experiment_id: heidist-experiment1-20170221
translationtype: Human Translation
ms.sourcegitcommit: f7e657906d24693dffd84ebbd348d1940f271931
ms.openlocfilehash: a4e28e17cd3ce2488bd292a4c5e95fb532526a56
ms.lasthandoff: 02/21/2017

---
# <a name="what-is-azure-search"></a>何謂 Azure 搜尋服務？

Azure 搜尋服務提供專用和可程式化的搜尋引擎，具有智慧型搜尋行為，可加強搜尋列來尋找網站、應用程式及公司檔案或資料存放區中的內容。 

![Azure 搜尋服務所支援的搜尋列](./media/search-what-is-azure-search/search-powered-by-azsearch3.png)

對於大部分 Web 和行動裝置應用程式，可靠的搜尋體驗絕對必要。 不論體驗背後的技術複雜性，全文檢索搜尋搭配自動完成的查詢詞彙、拼字修正，以及根據相同語意但不同字面的輸入 ("car" 和 "auto") 來比對，可說是最低要求。 調整性、可靠性及搜尋和後端資料存放區之間同步處理的操作需求，也同樣重要。 

Azure 搜尋服務提供廣泛的功能，讓您同時符合搜尋和操作需求。

![具備一般搜尋功能的搜尋列和自訂搜尋頁面](./media/search-what-is-azure-search/search-page-callouts3.png)

## <a name="how-it-works"></a>運作方式

若要使用 Azure 搜尋服務，請在您的 Azure 訂用帳戶中佈建免費或付費服務，建立和載入含有可搜尋內容的索引，然後呼叫 API 來發出搜尋要求並處理結果。 如果您想要大量的專用資源，則需要付費服務。

|層 |說明 |
|-----|------------|
|[免費](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) | 可快速佈建和用於小型工作負載的共用服務，通常做為學習和評估用途。 |
|[可計費](search-sku-tier.md) | 從基本到標準高密度，依容量分級的專用服務；採用不同定價層支援各種不同的部署組態。|

Azure 搜尋服務以 Microsoft 提供的受管理服務形式在雲端中執行。 可以在任何應用程式平台上與自訂程式碼整合。 您的完全受管理服務和私人內容無遠弗屆、可程式化、可調整且可復原。 

專用服務全年無休運作，級別達到 Microsoft Azure 支援的 [99.9% 服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

## <a name="how-it-compares"></a>比較的結果

有些雲端服務提供者提供自訂搜尋引擎，具有功能可加強自訂應用程式中的搜尋列。 另外有些是提供相似的基準功能，具備全文檢索搜尋、地理搜尋，且能夠處理搜尋輸入中若干程度的語意模糊。 是否最適合通常取決於[特殊化功能](#feature-drilldown)，或 API、工具和管理的容易性和整體簡單性。

相較於其他搜尋解決方案，針對 Azure 上的內容存放區和資料庫處理全文檢索搜尋工作負載時，Azure 搜尋服務的功能最強大，另外還提供一層必要的功能，以支援主要依賴搜尋內容導覽的應用程式。 

+ 索引層的 Azure 資料整合 (編目程式)
+ 集中管理的 Azure 入口網站
+ Azure 調整性、可靠性和世界級的可用性
+ 語言和自訂分析，還有分析器提供可靠的全文檢索搜尋，支援 56 種語言
+ 搜尋導向應用程式常用的核心功能，包括相關性、多面向、建議、同義字、地理搜尋等等 (列出如下)。

> [!Note]
> 完全支援非 Azure 資料來源。 您可以將任何 JSON 文件集合輸送至 Azure 搜尋服務索引。

### <a name="a-namefeature-drilldownafeature-drilldown"></a><a name="feature-drilldown"></a>深入探討功能

#### <a name="full-text-search-and-text-analysis"></a>全文檢索搜尋和文字分析

利用[簡單的查詢語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)就能建立查詢，包括邏輯運算子、片語搜尋運算子、後置運算子、優先順序運算子。 此外， [Lucene 查詢語法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 可以啟用模糊搜尋、鄰近搜尋、詞彙提升及規則運算式。 Azure 搜尋服務也支援[自訂語彙分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)，可讓您的應用程式利用語音比對和規則運算式，處理複雜的搜尋查詢。

#### <a name="language-support"></a>語言支援

Azure 搜尋服務支援 [56 種不同語言](https://docs.microsoft.com/rest/api/searchservice/language-support)的語彙分析器。 由於同時使用 Lucene 分析器和 Microsoft 分析器 (經由 Office 和 Bing 中數年的自然語言處理改善)，因此「Azure 搜尋服務」能夠分析您應用程式搜尋方塊中的文字，以智慧方式處理語言專屬的語言學，包括動詞時態、性別、不規則複數名詞 (例如 ' mouse' 與 'mice')、拆解複合字、斷字 (針對沒有空格的語言) 等等。

#### <a name="data-integration"></a>資料整合

您可以推送 JSON 資料結構以填入 Azure 搜尋服務索引。 此外，對於支援的資料來源，您可以使用[索引子](search-indexer-overview.md)，自動耙梳 Azure SQL Database、Azure DocumentDB 或 [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)，以同步化搜尋索引的內容與主要資料存放區。

**文件破解**支援[檢索主要檔案格式](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office、PDF 和 HTML 文件。

#### <a name="search-experience"></a>搜尋體驗

+ 您可以在自動完成的搜尋列和預先輸入的查詢上啟用**搜尋建議**。 [建議您索引中的實際文件](https://docs.microsoft.com/rest/api/searchservice/suggesters) 。

+ 透過[單一查詢參數](https://docs.microsoft.com/azure/search/search-faceted-navigation)就能啟用**多面向導覽**。 Azure 搜尋服務會傳回多面向導覽結構，可做為類別清單背後的程式碼，用於自我引導篩選 (例如，依價格範圍或品牌來篩選類別目錄項目)。

+ **篩選** 可用來將多面向導覽納入應用程式的 UI、增強查詢編寫，以及根據使用者或開發人員指定的準則進行篩選。 使用 [OData 語法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)建立篩選器。

+ **搜尋結果醒目提示**[會將視覺化格式套用至搜尋結果中的相符關鍵字](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 您可以選擇哪些欄位傳回醒目提示的文字片段。

+ **簡單評分** 是 Azure 搜尋服務的主要優點。 [評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)用於將相關性模型化為文件本身中的值函數。 例如，您可能想要新推出的產品或折扣的產品，出現在搜尋結果中較高的位置。 您也可以根據您所追蹤並個別儲存的客戶搜尋喜好設定，使用標記進行個人化計分來建置計分設定檔。

+ **排序** 是透過索引結構描述來對多個欄位提供，然後使用單一搜尋參數在查詢階段進行切換。

+ **微調控制項** ，直接對您的搜尋結果進行 [分頁](search-pagination-page-layout.md) 和節流。  

#### <a name="geosearch"></a>地理搜尋功能

Azure 搜尋服務以智慧方式處理、篩選和顯示地理位置。 可讓使用者根據指定位置的搜尋結果鄰近程度，或根據特定的地理區域來瀏覽資料。 本教學影片示範其運作方式︰ [Channel 9︰Azure 搜尋服務和地理空間資料](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)。

#### <a name="cloud-service-advantages"></a>雲端服務的優點

+ **高可用性** 可確保相當可靠的搜尋服務體驗。 經過適當的調整， [Azure 搜尋服務可提供 99.9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

+ **受到完整管理** ，所以完全不需要基礎結構管理。 以兩種方式調整您的服務，即可針對您的需求量身訂做，以處理更多的文件儲存體、更高的查詢負載，或兩者。

#### <a name="monitoring-and-reporting"></a>監視和報告

+ [收集和分析](search-traffic-analytics.md)**搜尋流量分析**，以便深入分析使用者在搜尋方塊中輸入的內容。

+ 自動會擷取每秒查詢次數、延遲和節流的計量，並在入口網站頁面中報告，不需要其他設定。 您也可以輕鬆地監視索引和文件計數，以便視需要調整容量。 

#### <a name="tools-for-prototyping-and-inspection"></a>用於原型設計和檢查的工具

在入口網站中，您可以使用 [匯入資料] 精靈設定索引子、使用索引設計工具建立索引，以及使用 [搜尋總管] 直接從您帳戶的 Azure 入口網站查詢所有索引，以便測試查詢並修改評分設定檔。 您也可以開啟任何索引，以檢視其結構描述。

## <a name="how-to-get-started"></a>如何開始使用

從免費服務開始，然後[使用內建的資料範例建立和查詢索引](search-get-started-portal.md)。 您可以使用入口網站完成所有工作，這是試用 Azure 搜尋服務的快速方法，不必撰寫任何程式碼。

1. Azure 訂閱者可以[在免費層中佈建服務](search-create-service-portal.md)。

  非訂閱者可以[免費註冊 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，並利用免費信用額度來試用付費的 Azure 服務。 當您用完信用額度之後，您可以保留帳戶並使用免費的 Azure 服務，例如網站。 除非您明確變更您的設定且同意付費，否則我們絕對不會從您的信用卡收取任何費用。

  或者，[啟用 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。 您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。 

2. 在[內建的房地產範例資料集](search-get-started-portal.md#create-index)上執行 [匯入資料] 精靈。

  這個精靈可以從大多數 Azure 資料來源產生並載入索引。 對於精靈未明確支援的資料來源，則需要撰寫程式碼。

3. 使用[搜尋總管](search-get-started-portal.md#query-index)來查詢索引。

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

雖然在入口網站中可以執行許多工作，但 Azure 搜尋服務主要是給想要將搜尋功能整合到現有應用程式的開發人員。 有下列程式設計介面可用。

|平台 |說明 |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | 任何程式設計平台和語言 (包括 Xamarin、Java 和 JavaScript) 支援的 HTTP 指令|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API 的 .NET 包裝函式支援使用 C# 和以 .NET Framework 為目標的其他受管理程式碼語言，有效率地撰寫程式碼 |

## <a name="watch-a-short-video"></a>觀看短片

搜尋引擎是行動裝置應用程式、Web 和公司資料存放區中推動資訊擷取的常用工具。 Azure 搜尋服務提供工具讓您創造類似大型商業網站上的搜尋體驗。 程式管理員 Liam Cavanagh 的這部 9 分鐘影片，說明整合搜尋引擎如何有益於您的應用程式、Azure 搜尋服務中的主要功能，以及一般工作流程的運作情形。 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 分鐘涵蓋主要功能和使用案例。
+ 3-4 分鐘涵蓋服務佈建。 
+ 4-6 分鐘涵蓋使用「匯入資料」精靈，以內建的房地產資料集建立索引。
+ 6-9 分鐘涵蓋「搜尋總管」和各種查詢。


