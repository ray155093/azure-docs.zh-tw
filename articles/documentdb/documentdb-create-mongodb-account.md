<properties 
    pageTitle="建立具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶 | Microsoft Azure" 
    description="了解如何建立具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶，現在可供預覽。" 
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>


# <a name="how-to-create-a-documentdb-account-with-protocol-support-for-mongodb-using-the-azure-portal"></a>如何使用 Azure 入口網站，建立具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶

若要建立具有 MongoDB 的通訊協定支援的 Azure DocumentDB 帳戶，您必須：

- 具有 Azure 帳戶。 您可以取得 [免費 Azure 帳戶](https://azure.microsoft.com/free/) (若您尚無此帳戶)。

## <a name="create-the-account"></a>建立帳戶  

若要建立具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶，請執行下列步驟。

1. 在新的視窗中，登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下 [新增]、[資料 + 儲存體]、[檢視全部]、然後搜尋 [DocumentDB 通訊協定] 的 [資料 + 儲存體] 類別。 按一下 [DocumentDB - Protocol Support for MongoDB] 。

    ![[Marketplace] 和 [資料 + 儲存體] 搜尋刀鋒視窗的螢幕擷取畫面 (醒目顯示 [DocumentDB - MongoDB (Mongo 資料庫) 的通訊協定支援])](./media/documentdb-create-mongodb-account/marketplacegallery2.png)

3. 或者，在 [儲存體] 類別底下的 [資料 + 儲存體] 類別，按一下 [更多]，然後按一下 [載入更多] 一或多次，以顯示 [DocumentDB - Protocol Support for MongoDB]。 按一下 [DocumentDB - Protocol Support for MongoDB] 。

    ![[Marketplace] 和 [資料 + 儲存體] 刀鋒視窗的螢幕擷取畫面 (醒目顯示 [DocumentDB - MongoDB (Mongo 資料庫) 的通訊協定支援])](./media/documentdb-create-mongodb-account/marketplacegallery1.png)

4. 在 [DocumentDB - Protocol Support for MongoDB (預覽)] 刀鋒視窗中，按一下 [建立] 以啟動預覽註冊程序。

    ![Azure 入口網站中的 [DocumentDB - MongoDB 的通訊協定支援] 刀鋒視窗](./media/documentdb-create-mongodb-account/marketplacegallery3.png)

5. 在 [DocumentDB 帳戶] 刀鋒視窗中，按一下 [註冊使用預覽版]。 閱讀資訊，然後按一下 [確定]。

    ![Azure 入口網站中 [DocumentDB - MongoDB 的通訊協定支援] 的 [立即註冊使用預覽版] 刀鋒視窗](./media/documentdb-create-mongodb-account/registerforpreview.png)

6.  接受預覽條款之後，您會返回 [建立] 刀鋒視窗。  在 [DocumentDB 帳戶]  刀鋒視窗中，指定想要的帳戶組態。

    ![[新增具有 MongoDB 的通訊協定支援的 DocumentDB] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)


    - 在 [識別碼]  方塊中，輸入用來識別帳戶的名稱。  驗證 [識別碼] 時，[識別碼] 方塊中會出現綠色的核取記號。 [識別碼]  值會變成 URI 中的主機名稱。 此 [識別碼]  只能包含小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 請注意， *documents.azure.com* 會附加至您選擇的端點名稱後面，產生的結果將成為您的帳戶端點。

    - 針對 [訂用帳戶] ，選取您要用於帳戶的 Azure 訂用帳戶。 如果您的帳戶只有一個訂用帳戶，預設會選取該帳戶。

    - 在 [資源群組] 中，選取或建立帳戶的資源群組。  根據預設，將會選擇 Azure 訂用帳戶之下現有的資源群組。  不過，您可以選擇建立新的資源群組，以便在其中加入帳戶。 如需詳細資訊，請參閱 [使用 Azure 入口網站管理 Azure 資源](resource-group-portal.md)。

    - 使用 [位置]  指定裝載帳戶的地理位置。
   
    - 勾選 [釘選到儀表板]  

7.  一旦設定了新的帳戶選項，請按一下 [建立] 。  建立帳戶可能需要數分鐘。  若要檢查狀態，您可以監視「開始面板」上的進度。  
    ![「開始面板」上 [建立] 圖格的螢幕擷取畫面 - 線上資料庫建立者](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)  

    或者，您也可以從 [通知] 中樞監視進度。  

    ![快速建立資料庫 - 通知中樞的螢幕擷取畫面，顯示正在建立 DocumentDB 帳戶](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)  

    ![通知中樞的螢幕擷取畫面，顯示已成功建立 DocumentDB 帳戶並部署到資源群組 - 線上資料庫建立者通知](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)

8.  建立好的帳戶可立即以預設值來使用。 

    ![[預設帳戶] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-create-mongodb-account/defaultaccountblades.png)
    

## <a name="next-steps"></a>後續步驟


- 了解如何 [連接](documentdb-connect-mongodb-account.md) 到具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶。

 



<!--HONumber=Oct16_HO2-->


