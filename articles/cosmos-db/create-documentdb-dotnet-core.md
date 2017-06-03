---
title: "Azure CosmosDB︰使用 .NET Core 和 DocumentDB API 建置 Web 應用程式 | Microsoft Docs"
description: "提供 .NET Core 程式碼範例，您可用來連線及查詢 Azure Cosmos DB DocumentDB API"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 9c0806a9edb52f0c05cced55edbda6b207398e3c
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-core-and-the-azure-portal"></a>Azure CosmosDB︰使用 .NET Core 和 Azure 入口網站建置 DocumentDB API Web 應用程式

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會示範如何使用 Azure 入口網站建立 Azure Cosmos DB 帳戶、文件資料庫和集合。 您會接著建置和部署以 [DocumentDB .NET Core API](documentdb-introduction.md) 為基礎的待辦事項清單 Web 應用程式，如下列螢幕擷取畫面所示。 

![具有範例資料的待辦事項應用程式](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

## <a name="prerequisites"></a>必要條件

如果尚未安裝 Visual Studio 2017，您可以下載並使用**免費的** [Visual Studio 2017 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-net-core"></a>安裝 .NET Core

使用 [.NET Core SDK](https://www.microsoft.com/net/download/core) 頁面上的指示來安裝 .NET Core。 SDK 適用於 Windows、MacOS 及 Linux。

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>新增集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>新增範例資料

[!INCLUDE [cosmos-db-create-sample-data](../../includes/cosmos-db-create-sample-data.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 Github 複製 DocumentDB API 應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `CD` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. 然後在 Visual Studio 中開啟方案檔案。 
    
## <a name="review-the-code"></a>檢閱程式碼

[!INCLUDE [cosmos-db-tutorial-review-code-dotnet](../../includes/cosmos-db-tutorial-review-code-dotnet.md)]

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 在 [Azure 入口網站](http://portal.azure.com/)中，於您 Azure Cosmos DB 帳戶的左側瀏覽區中，按一下 [金鑰]，然後按一下 [讀寫金鑰]。 在下一個步驟中，您將使用畫面右側的複製按鈕，將 URI 和主要金鑰複製到 web.config 檔案。

    ![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/create-documentdb-dotnet-core/keys.png)

2. 在 Visual Studio 2017 中，開啟 web.config 檔案。 

3. 從入口網站複製您的 URI 值 (使用 [複製] 按鈕)，並使它成為 web.config 中的端點金鑰值。 

    `<add key="endpoint" value="FILLME" />`

4. 然後，從入口網站複製您的主要金鑰值，並使它成為 web.confif 中的 authKey 值。 

    `<add key="authKey" value="FILLME" />`

您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

## <a name="run-the-web-app"></a>執行 Web 應用程式

1. 在 Visual Studio 中，於 [方案總管] 中的專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 套件]。 

2. 在 NuGet [瀏覽] 方塊中，輸入 *DocumentDB*。

3. 從結果中，安裝 **Microsoft.Azure.DocumentDB** 程式庫。 這會安裝 Azure Cosmos DB 套件以及所有相依性。

4. 按 CTRL + F5 來執行應用程式。 您的應用程式會顯示在瀏覽器中。 

5. 按一下瀏覽器中的 [新建]，然後在您的待辦事項應用程式中建立一些新工作。

   ![具有範例資料的待辦事項應用程式](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

您現在可以返回 [資料總管]，以查看、查詢、修改及使用這項新資料。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源：

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 建立集合，以及如何執行 Web 應用程式。 您現在可以將其他資料匯入到 Cosmos DB 帳戶。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)

