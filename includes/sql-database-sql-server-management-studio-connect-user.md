## 以使用者身分連接到 Azure SQL Database

使用下列步驟，以使用者身分連接到採用 SSMS 的 Azure SQL Database。

1. 在 Windows 搜尋方塊中輸入 "Microsoft SQL Server Management Studio"，然後按一下桌面應用程式來啟動 SSMS。

2. 在 [連接到伺服器] 視窗中，輸入下列資訊：

- **伺服器類型**︰預設為資料庫引擎；請勿變更此值。
 - **伺服器名稱**：以下列格式輸入裝載 SQL Database 的伺服器名稱：&lt;servername>.**database.windows.net**
 - **驗證類型**︰如果您剛開始使用，請選取 [SQL 驗證]。如果您已對 SQL Database 邏輯伺服器啟用 Active Directory，您可以選取 [Active Directory 密碼驗證] 或 [Active Directory 整合式驗證]。
 - **使用者名稱**︰如果您選取 [SQL 驗證] 或 [Active Directory 密碼驗證]，請輸入有權存取伺服器上資料庫的使用者名稱。
 - **密碼**︰如果您選取 [SQL 驗證] 或 [Active Directory 密碼驗證]，請輸入指定使用者的密碼。
   
       ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-sql-server-management-studio-connect-user/connect-user-1.png)

3. 按一下 [選項] 來指定您要連接的資料庫。

      ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-sql-server-management-studio-connect-user/connect-user-2.png)
 
4. 在 [連接到資料庫] 中，選取您要連接的資料庫。

     ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-sql-server-management-studio-connect-user/connect-user-3.png)

5. 按一下 [連接]。
 
6. 如果您的用戶端 IP 位址沒有 SQL Database 邏輯伺服器的存取權，系統會提示您登入 Azure 帳戶並建立伺服器層級的防火牆規則。如果您是 Azure 訂用帳戶管理員，請按一下 [登入] 以建立伺服器層級的防火牆規則。如果不是，請管理員在您嘗試連接的資料庫中建立伺服器層級的防火牆規則或資料庫層級的防火牆規則。
 
      ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-sql-server-management-studio-connect-user/connect-user-4.png)
 
7. 如果您的認證授與您存取指定的資料庫，[物件總管] 會隨即開啟，而您現在可以執行管理工作或查詢資料 (視使用者權限而定)。
  
      ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-sql-server-management-studio-connect-user/connect-user-5.png)
      
 
 ## 疑難排解連接失敗

最常見的連接失敗原因是伺服器名稱 (請記得，<servername> 是邏輯伺服器而非資料庫的名稱)、使用者名稱或密碼錯誤，以及基於安全性理由不允許連接伺服器。

<!---HONumber=AcomDC_0504_2016-->