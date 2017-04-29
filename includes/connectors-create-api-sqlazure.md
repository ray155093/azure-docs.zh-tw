### <a name="prerequisites"></a>必要條件
* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* 包含此連線資訊 (伺服器名稱、資料庫名稱和使用者名稱/密碼) 的 [Azure SQL Database](../articles/sql-database/sql-database-get-started.md)。 此資訊包含在 SQL Database 連接字串中：
  
    Server=tcp:*yoursqlservername*.database.windows.net,1433;Initial Catalog=*yourqldbname*;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  
    深入了解 [Azure SQL Database](https://azure.microsoft.com/services/sql-database)。

> [!NOTE]
> 當您建立 Azure SQL Database 時，您也可以建立 SQL 包含的範本資料庫。 
> 
> 

在於邏輯應用程式中使用您的 Azure SQL Database 之前，請先連線到您的 SQL Database。 您可以在 Azure 入口網站上，從邏輯應用程式內輕鬆完成此操作。  

請使用下列步驟來連線到您的 Azure SQL Database：  

1. 建立邏輯應用程式。 在 Logic Apps 設計工具中，新增一個觸發程序，然後新增一個動作。 從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "sql"。 選取其中一個動作︰  
   
    ![SQL Azure 連接建立步驟](./media/connectors-create-api-sqlazure/sql-actions.png)
2. 如果您之前尚未建立與 SQL Database 的任何連線，系統將會提示您輸入連線詳細資料：  
   
    ![SQL Azure 連接建立步驟](./media/connectors-create-api-sqlazure/connection-details.png) 
3. 輸入 SQL Database 詳細資料。 具有星號的屬性為必要項目。
   
   | 屬性 | 詳細資料 |
   | --- | --- |
   | 透過閘道連線 |讓此屬性保持未核取狀態。 連線到內部部署 SQL Server 時會使用此屬性。 |
   | 連線名稱 * |為連接器輸入任何名稱。 |
   | SQL Server 名稱 * |輸入伺服器名稱；也就是類似 *servername.database.windows.net* 的名稱。 伺服器名稱會顯示在 Azure 入口網站的 SQL Database 屬性中，並且也會顯示在連接字串中。 |
   | SQL Database 名稱 * |輸入提供給 SQL Database 的名稱。 這會列在連接字串的 SQL Database 屬性中︰Initial Catalog=yoursqldbname。 |
   | 使用者名稱 * |輸入 SQL Database 建立時所建立的使用者名稱。 這會列在 Azure 入口網站的 SQL Database 屬性中。 |
   | 密碼 * |輸入 SQL Database 建立時所建立的密碼。 |
   
    這些認證會用來授權邏輯應用程式連線並存取 SQL 資料。 完成後，連線詳細資料看起來類似下圖︰  
   
    ![SQL Azure 連接建立步驟](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. 選取 [ **建立**]。 
5. 請注意，已建立連線。 現在，請繼續進行您邏輯應用程式中的其他步驟： 
   
    ![SQL Azure 連接建立步驟](./media/connectors-create-api-sqlazure/table.png)

