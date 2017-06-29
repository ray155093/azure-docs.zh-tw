1. 在新的視窗中，登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在左側窗格中，依序按一下 [新增]、[資料庫] 和 [Azure Cosmos DB]。
   
   ![Azure 入口網站資料庫窗格](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. 在 [新增帳戶] 刀鋒視窗中，指定您想要的 Azure Cosmos DB 帳戶設定。 

    在使用 Azure Cosmos DB 時，您可以選擇下列四種程式設計模型的其中一種︰Gremlin (圖形)、MongoDB、SQL (DocumentDB) 和資料表 (索引鍵-值)。 
    
    在本快速入門文章中，我們會針對 DocumentDB API 來進行程式設計，因此請在填寫表單時選擇 **SQL (DocumentDB)**。 但是，如果您有用於社交媒體應用程式的圖形資料、索引鍵/值 (資料表) 資料，或從 MongoDB 應用程式移轉而來的資料，請了解 Azure Cosmos DB 可以提供高度可用、全域分散式的資料庫服務平台來供您所有的任務關鍵性應用程式使用。

    在 [新增帳戶] 刀鋒視窗中填寫欄位，使用下列螢幕擷取畫面中的資訊 (作為指南) 填入欄位。 當您設定帳戶時，請選擇與螢幕擷取畫面中不符合的唯一值。 
 
    ![新的 [Azure Cosmos DB] 刀鋒視窗](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    設定|建議的值|說明
    ---|---|---
    ID|唯一值|您用來識別 Azure Cosmos DB 帳戶的唯一名稱。 documents.azure.com 這個字串會附加到您所提供的識別碼以建立 URI，因此，請使用可供辨識的唯一識別碼。 識別碼只能包含小寫字母、數字及連字號 (-) 字元，且必須包含 3 到 50 個字元。
    API|SQL (DocumentDB)|在本文稍後，我們會針對 [DocumentDB API](../articles/documentdb/documentdb-introduction.md) 來進行程式設計。|
    訂用帳戶|*您的訂用帳戶*|您要用於 Azure Cosmos DB 帳戶的 Azure 訂用帳戶。 
    資源群組|與識別碼相同的值|您帳戶的新資源群組名稱。 為求簡化，您可以使用和識別碼相同的名稱。 
    位置|最接近使用者的區域|用來主控 Azure Cosmos DB 帳戶的地理位置。 請選擇最接近使用者的位置，以便他們能以最快速度存取到資料。
4. 按一下 [建立]  來建立帳戶。
5. 在頂端工具列上，按一下 [通知] 以監視部署程序。

    ![Azure 入口網站的 [通知] 窗格](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  當部署完成時，從 [所有資源] 圖格開啟新的帳戶。 

    ![[所有資源] 圖格上的 DocumentDB 帳戶](./media/cosmos-db-create-dbaccount/all-resources.png)
 
