

## 授與新的資料庫使用者 db\_owner 權限
使用下列步驟來授與現有的資料庫使用者 db\_owner 權限

下列步驟假設您已連接到 SSMS 中 [物件總管] 中的 SQL Database，而且以伺服器層級主體管理員身分或利用有權授與使用者權限的使用者帳戶連接到 SQL Database 邏輯伺服器。

1. 在 [物件總管] 中，展開 [資料庫] 節點，然後選取具有您要授與 dbo 權限之使用者的資料庫。
   
     ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)
2. 在選取的資料庫上按一下滑鼠右鍵，然後按一下 [查詢]。
   
     ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)
3. 在查詢視窗中，編輯並使用下列 Transact-SQL 陳述式，對指定的使用者授與 dbo 權限。
   
    ```ALTER ROLE db_owner ADD MEMBER user1;
    ```
   
     ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)

<!---HONumber=AcomDC_0629_2016-->