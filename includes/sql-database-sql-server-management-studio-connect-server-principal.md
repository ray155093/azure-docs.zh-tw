

## <a name="connect-to-azure-sql-database-using-sql-server-authentication"></a>使用 SQL Server Authentication 連接到 Azure SQL Database
下列步驟示範如何使用 SSMS 連接到 Azure SQL Server 和 SQL Database。 如果您沒有伺服器和資料庫，請參閱 [在幾分鐘內建立 SQL Database](../articles/sql-database/sql-database-get-started.md) 加以建立。

1. 在 Windows 搜尋方塊中輸入 **Microsoft SQL Server Management Studio** ，然後按一下桌面應用程式。
2. 在 [連接到伺服器] 視窗中，輸入下列資訊 (如果已經執行 SSMS，請按一下 [連接] > [資料庫引擎] 以開啟 [連接到伺服器] 視窗)：
   
   * **伺服器類型**︰預設為資料庫引擎；請勿變更此值。
   * **伺服器名稱**：以下列格式輸入 Azure SQL Database 的完整名稱：*&lt;servername>*.**database.windows.net**
   * **驗證類型**︰本文說明如何使用 [SQL Server 驗證] 進行連接。 如需與 Azure Active Directory 連接的詳細資訊，請參閱[使用 Active Directory 整合式驗證進行連接](../articles/sql-database/sql-database-aad-authentication.md#connect-using-active-directory-integrated-authentication)、[使用 Active Directory 密碼驗證進行連接](../articles/sql-database/sql-database-aad-authentication.md#connect-using-active-directory-password-authentication)和[使用 Active Directory 通用驗證進行連接](../articles/sql-database/sql-database-ssms-mfa-authentication.md)。
   * **使用者名稱**︰輸入可存取伺服器上資料庫之使用者的名稱 (例如，您在建立伺服器時設定的「伺服器管理員」)。 
   * **密碼**：輸入指定使用者的密碼 (例如，您在建立伺服器時設定的「密碼」)。
     
       ![SQL Server Management Studio：連接到 SQL Database 伺服器](./media/sql-database-sql-server-management-studio-connect-server-principal/connect.png)
3. 按一下 [ **連接**]。
4. 根據預設，新的伺服器尚未定義 [防火牆規則](../articles/sql-database/sql-database-firewall-configure.md) ，所以用戶端一開始會遭到封鎖而無法連線。 如果您的伺服器還沒有可讓您特定的 IP 位址進行連接的防火牆規則，SSMS 會提示您建立伺服器層級的防火牆規則。
   
    按一下 [登入]  並建立伺服器層級的防火牆規則。 您必須是 Azure 系統管理員，才能建立伺服器層級的防火牆規則。
   
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-server-principal/newfirewallrule.png)
5. 成功連接到 Azure SQL Database 之後，[物件總管]  隨即開啟，您現在可以存取您的資料庫以 [執行管理工作或查詢資料](../articles/sql-database/sql-database-manage-azure-ssms.md)。
   
     ![新增伺服器層級防火牆](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-5.png)

## <a name="troubleshoot-connection-failures"></a>針對連接失敗進行疑難排解
連線失敗的最常見原因是伺服器名稱有誤和網路連線問題。 請記住，<*servername*> 是伺服器 (而不是資料庫) 的名稱，而您需要提供完整的伺服器名稱︰`<servername>.database.windows.net`

此外，確認使用者名稱和密碼不包含任何拼字錯誤或額外的空格 (使用者名稱不區分大小寫，但密碼需區分大小寫)。 

您可以使用伺服器名稱明確地設定通訊協定和連接埠號碼，如下所示︰ `tcp:servername.database.windows.net,1433`

網路連線問題也可能導致連線錯誤和逾時。 只要重試連線 (當您知道伺服器名稱、認證和防火牆規則均正確無誤時) 即可成功。

如需更多與連線問題有關的詳細資訊，請參閱[排解、診斷和防止 SQL Database 的 SQL 連接錯誤和暫時性錯誤](../articles/sql-database/sql-database-connectivity-issues.md)。

