---
title: "使用圖形 API 來建置 Azure Cosmos DB Node.js 應用程式 | Microsoft Docs"
description: "提供可用來連線及查詢 Azure Cosmos DB 的 Node.js 程式碼範例。"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/13/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: d8a6a183d1acd7a06683ec2e402bd866cb5195f4
ms.contentlocale: zh-tw
ms.lasthandoff: 05/15/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-using-the-graph-api"></a>Azure Cosmos DB：使用圖形 API 來建置 Node.js 應用程式

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會示範如何使用 Azure 入口網站建立適用於圖形 API (預覽) 的 Azure Cosmos DB 帳戶、資料庫和圖形。 您會接著使用 OSS [Gremlin Node.js](https://aka.ms/gremlin-node) 驅動程式來建置和執行主控台應用程式。  

## <a name="prerequisites"></a>必要條件

* 您必須具備下列必要條件，才能執行此範例：
    * [Node.js](https://nodejs.org/en/) v0.10.29 版或更高版本
    * [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>新增圖形

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 Github 複製圖形 API 應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `cd` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. 然後在 Visual Studio 中開啟方案檔案。 

## <a name="review-the-code"></a>檢閱程式碼

讓我們快速檢閱應用程式中所發生的事情。 請開啟 `app.js` 檔案，您會發現這些程式碼行。

* 已建立 Gremlin 用戶端。

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        "https://<fillme>.graphs.azure.com", 
        { 
            "session": false, 
            "ssl": true, 
            "user": "/dbs/<db>/colls/<coll>",
            "password": "<authKey>"
        });
    ```

* 已使用 `client.execute` 方法執行一系列的 Gremlin 步驟。

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 在 [Azure 入口網站](http://portal.azure.com/)中，於您 Azure Cosmos DB 帳戶的左側瀏覽區中，按一下 [金鑰]，然後按一下 [讀寫金鑰]。 在下一個步驟中，您會使用畫面右側的複製按鈕，將 URI 和主要金鑰複製到 `app.js` 檔案。

    ![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/create-documentdb-dotnet/keys.png)

2. 從入口網站複製您的 URI 值 (使用 [複製] 按鈕)，並使它成為 config.js 中的 config.js 金鑰值。

    `config.endpoint = "GRAPHENDPOINT";`

3. 以 graphs.azure.com 取代 URI 的 documents.azure.com 部分。

4. 然後，從入口網站複製您的主要金鑰值，並使它成為 config.js 中的 config.primaryKey 值。 您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

    `config.primaryKey = "PRIMARYKEY";`

## <a name="run-the-console-app"></a>執行主控台應用程式

1. 開啟終端機視窗並 `cd` 至專案內含 package.json 檔案的安裝目錄。  

2. 執行 `npm install gremlin` 以安裝必要的 npm 模組。

3. 使用 [Cosmos DB Gremlin Fork](https://github.com/CosmosDB/gremlin-javascript) 中的原始程式碼取代 `node_modules\gremlin` 資料夾的內容，該分叉具有 Azure Cosmos DB 所需的 SSL 和 SASL 支援，但驅動程式目前不提供支援 (在驅動程式接受變更以前是暫時性支援)。

4. 在終端機中執行 `node app.js` 來啟動您的節點應用程式。

您現在可以返回 [資料總管]，以查看、查詢、修改及使用這項新資料。 

## <a name="browse-using-the-data-explorer"></a>使用資料總管進行瀏覽

您現在可以回到 Azure 入口網站中的 [資料總管]，瀏覽及查詢新的圖形資料。

* 在 [資料總管] 中，新的資料庫會出現在 [集合] 窗格中。 展開 [graphdb]、[graphcoll]，然後按一下 [圖形]。

    範例應用程式所產生的資料會顯示在 [圖形] 窗格中。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源： 

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 來建立圖形，以及如何執行應用程式。 您現在可以使用 Gremlin 來建置更複雜的查詢和實作強大的圖形周遊邏輯。 

> [!div class="nextstepaction"]
> [使用 Gremlin 進行查詢](tutorial-query-graph.md)
