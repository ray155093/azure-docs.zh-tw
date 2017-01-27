
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## <a name="create-a-new-azure-sql-database"></a>建立新的 Azure SQL Database
在 Azure 入口網站中使用下列步驟，可在新的或現有 Azure SQL Database 邏輯伺服器上建立新的 Azure SQL Database。

1. 如果目前未連線，請連線到 [Azure 入口網站](http://portal.azure.com)。
2. 按一下 [新增]，輸入 **SQL Database**，然後按一下 [SQL Database (新的資料庫)]。
   
     ![新資料庫](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)
3. 按一下 [新增] 。
   
     ![新資料庫](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)
4. 按一下 [建立]  ，以在 SQL Database 服務中建立新的資料庫。
   
     ![新資料庫](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)
5. 提供下列伺服器屬性的值：
   
   * 資料庫名稱
   * 訂用帳戶：僅適用於您有多個訂用帳戶時。
   * 資源群組：如果您剛開始使用，請使用邏輯伺服器的資源群組。
   * 選取來源：您可以選擇空白資料庫、範例資料或 Azure 資料庫備份。 若要使用 BCP 命令列工具來移轉內部部署 SQL Server 資料庫或載入資料，請參閱本文結尾處的連結。
   * 伺服器：新的或現有邏輯伺服器。
   * 伺服器管理員登入
   * 密碼
   * 定價層：如果您剛開始使用，請使用預設值 S0。
   * 定序︰僅適用於已選擇空白資料庫時。
     
        ![新資料庫](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)
6. 按一下 [建立] 。 您可以在通知區域中看到部署已開始。
   
    ![新資料庫](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)
7. 等待部署完成後，再繼續進行下一個步驟。
   
     ![新資料庫](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)



<!--HONumber=Jan17_HO3-->


