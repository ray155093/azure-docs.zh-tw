---
title: "Azure DocumentDB 常見的使用案例和 NoSQL 案例 | Microsoft Docs"
description: "深入了解 DocumentDB 的前五個使用案例：使用者產生的內容、事件記錄、目錄資料、使用者喜好設定資料，和物聯網 (Internet of Things，IoT)。"
services: documentdb
author: h0n
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: hawong
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 9a0ba106b4c2aded8aaac673dfba3610bd4ca0fb
ms.lasthandoff: 03/24/2017


---

# <a name="common-documentdb-use-cases"></a>常見的 DocumentDB 使用案例
本文提供幾個常見的 DocumentDB 使用案例概觀。  本文中的建議可以做為使用 DocumentDB 開發應用程式的起點。   

閱讀本文後，您將能夠回答下列問題： 

* DocumentDB 有哪些常見使用案例？
* 使用 DocumentDB 作為零售應用程式的優點有哪些？
* 使用 DocumentDB 做為物聯網 (Internet of Things，IoT) 系統的資料存放區有何優點？
* 使用 DocumentDB 作為 Web 和行動裝置應用程式的優點有哪些？

## <a name="common-use-cases-for-documentdb"></a>DocumentDB 的常見使用案例
Azure DocumentDB 是 NoSQL 資料庫的一般用途，其廣泛運用在應用程式和使用案例。 它很適合用於需要低毫秒級回應時間，而且必須快速調整的應用程式。 以下是讓 DocumentDB 非常適合用於高效能應用程式的一些特性。

* DocumentDB 會原生分割您的資料以實現高可用性和延展性。
* DocumentDB 具有以 SSD 支持的儲存體，可提供低延遲的毫秒級回應時間。
* DocumentDB 支援最終、工作階段和限定過期等一致性層級，因此能提供高性價比。 
* DocumentDB 有彈性的資料友善計價模式，可針對儲存體和輸送量單獨計價。
* DocumentDB 的保留輸送量模式可讓您以讀取/寫入數量，而非基礎硬體的 CPU/記憶體/IOPs 來做為思考方向。
* DocumentDB 的設計可讓您調整為每日數十億個要求級數的巨量要求。

對於需要低回應時間，而且必須處理大量讀取和寫入的 Web 應用程式、行動應用程式、遊戲應用程式和 IoT 應用程式來說，這些特性很有助益。 

## <a name="iot-and-telematics"></a>IoT 和遠距通訊
IoT 使用案例在如何內嵌、處理和儲存資料方面通常共用一些模式。  首先，這些系統需要內嵌於各種地區設定的裝置感應器中暴增的資料量。 接著，這些系統會處理並分析資料流資料，以衍生即時的資訊分析。 然後將資料封存到冷儲存體，進行批次分析。 Microsoft Azure 提供適用於 IoT 使用案例的豐富服務，包括 Azure 事件中樞、Azure DocumentDB、Azure 串流分析、Azure 通知中樞、Azure 機器學習服務、Azure HDInsight 及 PowerBI。 

![Azure DocumentDB IoT 參考架構](./media/documentdb-use-cases/documentdb-iot.png)

Azure 事件中樞可以擷取暴增的資料量，因為它提供高輸送量資料擷取和低延遲。 您可以將需要處理以取得即時資訊分析的內嵌資料，使用漏斗方式倒入 Azure 串流分析以進行即時分析。 您可以將資料載入 DocumentDB 以進行臨機操作查詢。 將資料載入 DocumentDB 之後，這些資料便可提供查詢。  DocumentDB 中的資料可以當做參考資料使用，以做為即時分析的一部分。 此外，您還可以進一步地調整和處理資料，方法是將 DocumentDB 資料連線到 HDInsight，以進行 Pig、Hive 或 Map/Reduce 工作。  接著，調整過的資料會被載回 DocumentDB 進行報告。   

如需使用 DocumentDB、EventHubs 和 Storm 的 IoT 解決方案範例，請參閱 [GitHub 上的 hdinsight-storm-examples 儲存機制](https://github.com/hdinsight/hdinsight-storm-examples/)。

如需有關適用於 IoT 的 Azure 產品詳細資訊，請參閱[建立您的物聯網](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx)。 

## <a name="retail-and-marketing"></a>零售和行銷
DocumentDB 通常用在儲存目錄資料的零售產業中。 目錄資料使用方式案例涉及儲存和查詢一組實體屬性，例如人員、地點和產品。  目錄資料的一些範例包括使用者帳戶、產品目錄、IoT 的裝置註冊，和材料表系統。  這項資料的屬性可能會有所不同，而且可以隨時間變更，以符合應用程式需求。  

請細想汽車零件供應商產品目錄的範例。 除了所有零件共用的通用屬性外，每個零件還可能會有自己的屬性。  此外，用於特定零件的屬性可能在明年推出新的模型時變更。  做為 JSON 文件存放區，DocumentDB 支援彈性的結構描述和階層式資料，因此很適合用來儲存產品目錄資料。

![Azure DocumentDB 零售目錄參考架構](./media/documentdb-use-cases/documentdb-retail-catalog.png)

 此外，DocumentDB 中儲存的資料可以與 HDInsight 整合，透過 Pig、Hive 或 Map/Reduce 工作進行巨量資料分析。 如需適用於 DocumentDB 的 Hadoop Connector 詳細資訊，請參閱 [使用 DocumentDB 與 HDInsight 執行 Hadoop 工作](documentdb-run-hadoop-with-hdinsight.md)。

 ![Azure DocumentDB 零售訂單參考架構](./media/documentdb-use-cases/documentdb-retail-orders.png)
## <a name="gaming"></a>玩遊戲
資料庫層是遊戲應用程式的重要元件。 現今的遊戲會在行動/主控台用戶端進行圖形處理，但依賴雲端來提供自訂和個人化的內容，如遊戲中的統計資料、社交媒體整合和得分排行榜。 遊戲的讀取和寫入通常需要單一毫秒延遲，以在遊戲中提供更吸引人的體驗。 在新遊戲推出和功能更新期間，遊戲資料庫必須快速且要能處理大量激增的要求率。

DocumentDB 已獲得 [The Walking Dead: No Man's Land](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) ([Next Games](http://www.nextgames.com/) 所製作) 和 [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/) 之類的遊戲所採用。 DocumentDB 可為遊戲開發人員提供下列優點︰

* DocumentDB 可彈性調高或調降效能。 這可讓遊戲藉由單一 API 呼叫就能處理同一時間區區十幾名玩家到數百萬名玩家的更新設定檔和統計資料。
* DocumentDB 支援毫秒讀取和寫入，可避免玩家玩遊戲時發生延遲現象。
* DocumentDB 的自動編製索引可針對多個不同的屬性進行即時篩選，例如依玩家的內部識別碼、GameCenter、Facebook、Google ID 找出玩家，或根據玩家的公會成員資格進行查詢。 不用建置複雜的索引或分區化基礎結構就可做到這些事。
* 透過彈性的結構描述更加輕易地實作社交功能，包括遊戲中的交談訊息、玩家的公會成員資格、已完成的挑戰、得分排行榜和社交關係圖。
* DocumentDB 做為受管理的平台即服務 (PaaS) 時只需要最少的設定和管理工作就能快速重覆作業，因此能縮短上市時間。

![Azure DocumentDB 遊戲參考架構](./media/documentdb-use-cases/documentdb-gaming-architecture.png)

## <a name="web-and-mobile-applications"></a>Web 與行動應用程式
DocumentDB 常用在 Web 和行動裝置應用程式，而且非常適合用於模型化社交網路互動，與協力廠商服務整合，以及用於建立豐富的個人化體驗。 DocumentDB SDK 可以用來建置使用熱門 [Xamarin 架構](documentdb-mobile-apps-with-xamarin.md)的豐富 iOS 和 Android 應用程式。  

### <a name="social-applications"></a>社交應用程式
DocumentDB 的常見使用案例是針對 Web 和行動應用程式儲存和查詢使用者產生的內容 (UGC)，尤其是社交媒體應用程式。 一些 UGC 範例包括對談、推文、部落格文章、評等和註解。 通常，社交媒體應用程式中的 UGC 會結合使用自由格式文字、屬性、標記和不受固定結構限制的關聯性。 內容 (例如聊天、註解和貼文) 可以儲存在 DocumentDB 中，而無需要求關聯式對應層的轉換或複雜物件。  開發人員在反覆查看應用程式程式碼時，可以輕鬆地新增或修改資料屬性以符合需求，進而加快開發的速度。  

與第三方社交網路整合的應用程式必須回應這些網路不斷變更的結構描述。 由於 DocumentDB 依預設會自動編製資料的索引，您可以隨時查詢資料。 因此，這些應用程式可以有彈性地根據其各自的需求擷取投影。

許多社交應用程式是以全球的規模運作，而且可能出現無法預期的使用模式。 調整資料存放區的彈性十分重要，因為應用程式層會進行調整以符合使用需求。  您可以透過在 DocumentDB 帳戶下新增其他資料分割區來進一步向外延伸。  此外，您也可以跨多個區域建立其他 DocumentDB 帳戶。 如需 DocumentDB 服務區域可用性，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

![Azure DocumentDB Web 應用程式參考架構](./media/documentdb-use-cases/documentdb-web.png)

### <a name="personalization"></a>個人化
現今，現代應用程式具備複雜的檢視和體驗。 這些是通常是動態的，根據使用者喜好設定或情緒和品牌需求量身訂做。 因此，應用程式必須要能夠有效地擷取個人化設定，以快速呈現 UI 項目和體驗。 

JSON 是呈現 UI 配置資料的有效格式，因為它不只是輕量，還可以輕鬆地透過 JavaScript 解譯。 DocumentDB 提供可微調的一致性層級，允許快速讀取和低延遲寫入。 因此，在 DocumentDB 中將包括個人化設定的 UI 配置資料儲存為 JSON 文件，是在不同的線路上取得這項資料的有效方法。

## <a name="next-steps"></a>後續步驟
若要開始使用 DocumentDB，您可以建立[帳戶](https://azure.microsoft.com/pricing/free-trial/)，然後依照我們的[學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)執行，以了解 DocumentDB 並找到所需的資訊。 

或者，如果您想要閱讀更多有關使用 DocumentDB 的客戶，您也可以使用下列客戶案例：

* [Sulekha.com](https://customers.microsoft.com/en-US/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india)。 Sulekha 使用 Azure DocumentDB 連接印度的客戶和企業。
* [NewOrbit](https://customers.microsoft.com/en-US/story/neworbit-takes-flight-with-azure-documentdb)。 NewOrbit 使用 Azure DocumentDB 而大展鴻圖。
* [Affinio](https://customers.microsoft.com/en-US/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale)。 Affinio 從 AWS 切換為 Azure DocumentDB，以操控大規模的社交資料。
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)。 The Walking Dead: No Man's Land 遊戲一舉成為 Azure DocumentDB 所支援的第一款遊戲。
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)。 Halo 5 如何使用 Azure DocumentDB 實作社交遊戲。
* [Cortana Analytics 資源庫](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/)。 Cortana Analytics 資源庫 - 以 Azure DocumentDB 為基礎所建置的可調整社群網站。
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602)。 只需幾分鐘的時間，前置整合器即可使用富彈性的雲端技術來提供跨國企業的全球資訊分析。
* [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639)。 為新聞加入智慧功能，提供以參加的公民為對象的資訊。 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653)。 為取得全球的一致性色彩，主要品牌會尋求 SGS 的協助。 而 SGS 會求助於 Azure。
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608)。 全球領導者 Telenor 借助雲端之力加快啟動速度。 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667)。 未來存放區的運作基礎會是快速搜尋和簡單資料流程。
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet)。 以 Azure 為基礎的軟體平台，將企業與客戶之間的障礙細分
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases)。 Weka 的智能冰櫃可改善疫苗管理，讓更多人受到保護免於疾病危害
* [Orange Tribes](https://customers.microsoft.com/en-US/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth)。 那個美食應用程式除了滿足視覺和味蕾享受，還有更多功能。
* [Real Madrid](https://customers.microsoft.com/en-US/story/real-madrid-brings-the-stadium-closer-to-450-million-f)。 Real Madrid 使用 Microsoft 雲端服務，為全球 4.5 億球迷帶來最即時的精采賽事。
* [Tuku](https://customers.microsoft.com/en-US/story/tuku-makes-car-buying-fun-with-help-from-azure-services)。 利用 Azure 服務的協助，TUKU 讓購車有樂趣 

