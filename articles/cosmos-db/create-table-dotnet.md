---
title: "使用資料表 API 來建置 Azure Cosmos DB .NET 應用程式 | Microsoft Docs"
description: "使用 .NET 來開始使用 Azure Cosmos DB 的資料表 API"
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 73b9448153ec520d77afd1bdb65b9694e7bf7b9e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB：使用資料表 API 來建置 .NET 應用程式

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會示範如何建立 Azure Cosmos DB 帳戶，並使用 Azure 入口網站在該帳戶內建立資料表。 然後，您會撰寫程式碼來插入、更新和刪除實體，並執行一些查詢。 您可以從 NuGet 下載 [Azure 儲存體預覽 SDK](https://aka.ms/premiumtablenuget)，此 SDK 具有與公用 [Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 相同的類別和方法簽章，但它也可以使用[資料表 API](table-introduction.md) (預覽版) 來連線到 Azure Cosmos DB 帳戶。 

## <a name="prerequisites"></a>必要條件

如果尚未安裝 Visual Studio 2017，您可以下載並使用**免費的** [Visual Studio 2017 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmosdb-create-dbaccount-table.md)]

## <a name="add-a-table"></a>新增資料表

[!INCLUDE [cosmosdb-create-table](../../includes/cosmosdb-create-table.md)]

## <a name="add-sample-data"></a>新增範例資料

您現在可以使用資料總管將資料新增至您的新資料表。

1. 在資料總管中，依序展開 **sample-database** 和 **sample-table**，按一下 [實體]，然後按一下 [新增實體]。
2. 現在，將資料新增至 PartitionKey 值方塊和 RowKey 值方塊，然後按一下 [新增實體]。

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    您現在可以在資料表中新增更多實體、編輯實體，或在資料總管中查詢資料。 資料總管也可供您調整輸送量，以及對資料表新增預存程序、使用者定義函式和觸發程序。

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 Github 複製 DocumentDB API 應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `cd` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. 然後在 Visual Studio 中開啟方案檔案。 

## <a name="review-the-code"></a>檢閱程式碼

讓我們快速檢閱應用程式中所發生的事情。 請開啟 DocumentDBRepository.cs 檔案，您會發現這些程式碼行會建立 Azure Cosmos DB 資源。 

* 已初始化 DocumentClient。

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* 已建立新資料庫。

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* 已建立新的圖形容器。

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 在 [Azure 入口網站](http://portal.azure.com/)中，於您 Azure Cosmos DB 帳戶的左側瀏覽區中，按一下 [金鑰]，然後按一下 [讀寫金鑰]。 在下一個步驟中，您將使用畫面右側的複製按鈕，將 URI 和主要金鑰複製到 app.config 檔案。

    ![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/create-documentdb-dotnet-core/keys.png)

2. 在 Visual Studio 中，開啟 app.config 檔案。 

3. 從入口網站複製您的 Azure Cosmos DB 帳戶名稱，並使它成為 app.config 中 PremiumStorageConnection 字串值的 AccountName 值。 在上面的螢幕擷取畫面中，帳戶名稱是 cosmos-db-quickstart。 您的帳戶名稱會顯示在入口網站頂端。

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMODB.documents.azure.com" />`

4. 然後，從入口網站複製您的主要金鑰值，並使它成為 PremiumStorageConnectionString 中的 AccountKey 值。 

    `AccountKey=AUTHKEY`

5. 最後，從入口網站的 [金鑰] 頁面複製 URI 值 (使用 [複製] 按鈕)，並讓它成為 PremiumStorageConnectionString 的 TableEndpoint 值。

    `TableEndpoint=https://COSMODB.documents.azure.com`

    您可以讓 StandardStorageConnectionString 保持原狀。

您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

## <a name="run-the-web-app"></a>執行 Web 應用程式

1. 在 Visual Studio 中，於 [方案總管] 中的專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 套件]。 

2. 在 NuGet 的 [瀏覽] 方塊中，輸入「WindowsAzure.Storage」，並勾選 [包括發行前版本] 方塊。 

3. 從結果中，安裝 **WindowsAzure.Storage** 程式庫。 這會安裝預覽版的 Azure Cosmos DB 資料表 API 套件以及所有相依性。

4. 按 CTRL + F5 來執行應用程式。

    主控台視窗會顯示將要新增至資料表的資料。 指令碼完成時，請關閉主控台視窗。 

您現在可以返回 [資料總管]，以查看、查詢、修改及使用這項新資料。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源： 

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用資料總管來建立資料表，以及如何執行應用程式。  現在，您可以使用資料表 API 來查詢您的資料。  

> [!div class="nextstepaction"]
> [使用資料表 API 來進行查詢](tutorial-query-table.md)


