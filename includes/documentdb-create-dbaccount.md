1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Jumpbar 中，按一下 [新增]，按一下 [資料庫]，然後按一下 [NoSQL (DocumentDB)]。
   
   ![Azure 入口網站的螢幕擷取畫面，其中反白顯示 [其他服務] 和 DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  
3. 在 [新增帳戶]  刀鋒視窗中，指定想要的 DocumentDB 帳戶組態。
   
    ![[新增 DocumentDB] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)
   
   * 在 [識別碼]  方塊中，輸入用來識別 DocumentDB 帳戶的名稱。  驗證 [識別碼] 時，[識別碼] 方塊中會出現綠色的核取記號。 [識別碼]  值會變成 URI 中的主機名稱。 此 [識別碼]  只能包含小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 請注意， *documents.azure.com* 會附加至您選擇的端點名稱後面，產生的結果會成為您的 DocumentDB 帳戶端點。
   * 在 [NoSQL API] 方塊中，選取 [DocumentDB]。  
   * 在 [訂用帳戶] 中，選取您要用於 DocumentDB 帳戶的 Azure 訂用帳戶。 如果您的帳戶只有一個訂用帳戶，預設會選取該帳戶。
   * 在 [資源群組] 中，選取或建立 DocumentDB 帳戶的資源群組。  依預設會建立新的資源群組。 如需詳細資訊，請參閱 [使用 Azure 入口網站管理 Azure 資源](../articles/azure-portal/resource-group-portal.md)。
   * 使用 [位置]  指定將代管您的 DocumentDB 帳戶的地理位置。 
4. 設定新的 DocumentDB 帳戶選項之後，按一下 [建立] 。 如果要檢查部署狀態，請查看通知中樞。  
   
   ![快速建立資料庫 - 通知中樞的螢幕擷取畫面，顯示正在建立 DocumentDB 帳戶](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  
   
   ![通知中樞的螢幕擷取畫面，顯示已成功建立 DocumentDB 帳戶並部署到資源群組 - 線上資料庫建立者通知](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)
5. 建立好的 DocumentDB 帳戶可立即以預設值來使用。 若要檢閱預設設定，請按一下 Jumpbar 中的 [NoSQL (DocumentDB)] 圖示，按一下您的新帳戶，然後按一下 [資源] 功能表中的 [預設一致性]。

   ![螢幕擷取畫面顯示如何在 Azure 入口網站中開啟您的 Azure DocumentDB 資料庫帳戶](./media/documentdb-create-dbaccount/azure-documentdb-database-open-account-portal.png)  

   DocumentDB 帳戶的預設一致性會設定為 [工作階段] 。  您可以藉由按一下資源功能表中的 [預設一致性]  ，調整預設一致性。 若要深入了解 DocumentDB 所提供的一致性層級，請參閱 [DocumentDB 中的一致性層級](../articles/documentdb/documentdb-consistency-levels.md)。

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md


<!--HONumber=Dec16_HO2-->


