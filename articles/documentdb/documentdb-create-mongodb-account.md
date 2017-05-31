---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-create-account
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4b2ae4a8d3b93b5c27e180a5875ae9e90483068a
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017



---

# <a name="create-an-azure-cosmos-db-account-with-mongodb-api"></a>使用 MongoDB API 建立 Azure Cosmos DB 帳戶
Azure Cosmos DB 資料庫現在可做為針對 MongoDB 所撰寫之應用程式的資料存放區。 若要使用這項功能，您需要 Azure 帳戶和 Azure Cosmos DB 帳戶。 本教學課程將逐步引導您完成建立 Azure Cosmos DB 帳戶來與 MongoDB 應用程式搭配使用的程序。 

您可以使用 Azure 入口網站或使用 Azure CLI 搭配 Azure Resource Manager 範本，來建立支援 MongoDB 的 Azure Cosmos DB 帳戶。 本文說明如何使用 Azure 入口網站來建立支援 MongoDB 的 Azure Cosmos DB 帳戶。 若要使用 Azure CLI 搭配 Azure Resource Manager 來建立帳戶，請參閱[使用 Azure CLI 2.0 自動進行 Azure Cosmos DB 帳戶管理](documentdb-automation-resource-manager-cli.md)。

## <a name="prerequisite"></a>必要條件
一個 Azure 帳戶。 如果您沒有 Azure 帳戶，可以立即建立一個[免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
## <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

1. 在網際網路瀏覽器中，登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側導覽中，按一下 [Azure Cosmos DB]。

    ![入口網站左側導覽中反白顯示 DocumentDB NoSQL 項目的螢幕擷取畫面](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. 或者，按一下 [更多服務 >]，在頂端搜尋列中輸入 **DocumentDB**，然後按一下 [Azure Cosmos DB]。

    ![[更多服務] 刀鋒視窗中搜尋 DocumentDB NoSQL 項目的螢幕擷取畫面](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. 在 [Azure Cosmos DB] 刀鋒視窗上方，按一下頂端動作列上的 [+ 新增]。

    ![Cosmos DB 資源刀鋒視窗上 [新增] 按鈕的螢幕擷取畫面](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. 在 [Azure Cosmos DB 帳戶] 刀鋒視窗中，指定想要的帳戶組態。

   ![[新增具有 MongoDB 的通訊協定支援的 Azure Cosmos DB] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - 在 [識別碼]  方塊中，輸入用來識別帳戶的名稱。  驗證 [識別碼] 時，[識別碼] 方塊中會出現綠色的核取記號。 [識別碼]  值會變成 URI 中的主機名稱。 此 [識別碼]  只能包含小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 請注意， *documents.azure.com* 會附加至您選擇的端點名稱後面，產生的結果將成為您的帳戶端點。

    - 針對 [API]，選取 [MongoDB]。 這會指定您想要用來與 Azure Cosmos DB 資料庫互動的通訊 API。

    - 針對 [訂用帳戶] ，選取您要用於帳戶的 Azure 訂用帳戶。 如果您的帳戶只有一個訂用帳戶，預設會選取該帳戶。

    - 在 [資源群組] 中，選取或建立帳戶的資源群組。  根據預設，將會選擇 Azure 訂用帳戶之下現有的資源群組。  不過，您可以選擇建立新的資源群組，以便在其中加入帳戶。 如需詳細資訊，請參閱 [使用 Azure 入口網站管理 Azure 資源](../azure-portal/resource-group-portal.md)。

    - 使用 [位置]  指定裝載帳戶的地理位置。

6. 一旦設定了新的帳戶選項，請按一下 [建立] 。  建立帳戶可能需要數分鐘。

   您可以從 [通知] 中樞監視進度。  

   ![[通知] 中樞的螢幕擷取畫面，其中顯示正在建立 Azure Cosmos DB 帳戶](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. 若要存取您的新帳戶，請按一下左側功能表上的 [Azure Cosmos DB]。 在您的一般 DocumentDB 及具有 Mongo 通訊協定支援之 DocumentDB 的帳戶清單中，按一下您新帳戶的名稱。
8. 您的 Azure Cosmos DB 帳戶現在已經可以與 MongoDB 應用程式搭配使用。

   ![[預設帳戶] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>後續步驟
* 了解如何 [連接](documentdb-connect-mongodb-account.md) 到具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶。

