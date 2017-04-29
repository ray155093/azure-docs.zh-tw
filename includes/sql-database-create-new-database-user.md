

## <a name="create-new-database-user-using-ssms"></a>使用 SSMS 建立新的資料庫使用者
依照下列步驟，使用 SSMS 在現有的資料庫中建立新的資料庫使用者。 

這些步驟假設您使用 SSMS 連接到 [物件總管] 中的 SQL Database，而且以伺服器層級主體管理員身分或利用有權建立新使用者的使用者帳戶連接到 SQL Database 邏輯伺服器。 

1. 在 [物件總管] 中，展開 [資料庫] 節點，然後選取您要在其中建立新使用者帳戶的資料庫。
   
     ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)
2. 在選取的資料庫上按一下滑鼠右鍵，然後按一下 [查詢] 。
   
     ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)
3. 在查詢視窗中，編輯並使用下列 Transact-SQL 陳述式，在您的使用者資料庫中建立自主使用者。 
   
    ```CREATE USER user1 WITH PASSWORD ='p@ssw0rd1';
    ```
   
     ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-3.png)

