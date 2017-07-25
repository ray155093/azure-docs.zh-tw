---
title: "使用圖形 API 來建置 Azure Cosmos DB Java 應用程式 | Microsoft Docs"
description: "提供 Java 程式碼範例，您可透過 Gremlin 用來連線及查詢 Azure Cosmos DB 中的圖形資料。"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: zh-tw
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB：使用圖形 API 來建置 Java 應用程式

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會示範如何使用 Azure 入口網站建立適用於圖形 API (預覽) 的 Azure Cosmos DB 帳戶、資料庫和圖形。 您會接著使用 OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) 驅動程式來建置和執行主控台應用程式。  

## <a name="prerequisites"></a>必要條件

* 您必須具備下列必要條件，才能執行此範例：
   * JDK 1.7+ (如果您沒有 JDK，請執行 `apt-get install default-jdk`)，並設定 `JAVA_HOME` 之類的環境變數
   * Maven (如果您沒有 Maven 則執行 `apt-get install maven`)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>新增圖形

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 Github 複製圖形 API (預覽) 應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `cd` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>檢閱程式碼

讓我們快速檢閱應用程式中所發生的事情。 請開啟 `Program.java` 檔案，您會發現這些程式碼行。 

* 已從 `src/remote.yaml` 中的組態初始化 Gremlin `Client`。

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* 已使用 `client.submit` 方法執行一系列的 Gremlin 步驟。

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>更新您的連接字串

1. 開啟 src/remote.yaml 檔案。 

3. 在 src/remote.yaml 檔案中填入您的 host、port、username、password、connectionPool 和 serializer 組態︰

    設定|建議的值|說明
    ---|---|---
    主機|[***.graphs.azure.com]|請看下方的螢幕擷取畫面。 這是 Azure 入口網站的 [概觀] 頁面上的 Gremlin URI 值，其以方括號括住並已移除尾端的 :443/。<br><br>此值也可以從 [金鑰] 索引標籤擷取，方法是移除 https://、將文件變更為圖形，並移除尾端的 :443/ 來使用 URI 值。
    連接埠|443|設為 443。
    使用者名稱|您的使用者名稱|`/dbs/<db>/colls/<coll>` 表單的資源，其中 `<db>` 是您的資料庫名稱，而 `<coll>` 是您的集合名稱。
    密碼|您的主要金鑰|請看下方的第二個螢幕擷取畫面。 這是您的主要金鑰，可以從 Azure 入口網站 [金鑰] 頁面的 [主鑰金鑰] 方塊中擷取。 使用方塊左側的 [複製] 按鈕來複製此值。
    ConnectionPool|{enableSsl: true}|SSL 的連線集區設定。
    serializer|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|設定此值，並在貼入此值時刪除任何 `\n` 分行符號。

    對於主機值，從 [概觀] 頁面複製 [Gremlin URI] 值：![在 Azure 入口網站的 [概觀] 頁面上檢視和複製 Gremlin URI 值](./media/create-graph-java/gremlin-uri.png)

    對於密碼值，從 [金鑰] 頁面複製 [主要金鑰]：![在 Azure 入口網站的 [金鑰] 頁面上檢視和複製主要金鑰](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>執行主控台應用程式

1. 在終端機中執行 `mvn package` 以安裝必要的 Java 套件。

2. 在終端機中執行 `mvn exec:java -D exec.mainClass=GetStarted.Program` 來啟動您的 Java 應用程式。

您現在可以返回 [資料總管]，以查看、查詢、修改及使用這項新資料。 

## <a name="browse-using-the-data-explorer"></a>使用資料總管進行瀏覽

您現在可以回到 Azure 入口網站中的 [資料總管]，瀏覽及查詢新的圖形資料。

* 在 [資料總管] 中，新的資料庫會出現在 [集合] 窗格中。 展開 [graphdb]、[graphcoll]，然後按一下 [圖形]。

    範例應用程式所產生的資料會顯示在 [圖形] 窗格中。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源： 

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 來建立圖形，以及如何執行應用程式。 您現在可以使用 Gremlin 來建置更複雜的查詢和實作強大的圖形周遊邏輯。 

> [!div class="nextstepaction"]
> [使用 Gremlin 進行查詢](tutorial-query-graph.md)


