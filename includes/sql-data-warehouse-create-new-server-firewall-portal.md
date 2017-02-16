### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 入口網站中建立伺服器層級的防火牆規則

1. 在 SQL Server 刀鋒視窗的 [設定] 下，按一下 [防火牆] 來開啟 SQL Server 的防火牆刀鋒視窗。

    ![SQL Server 防火牆](./media/sql-data-warehouse-server-firewall/sql-server-firewall.png)
    
2. 檢閱顯示的用戶端 IP 位址，並使用自行選擇的瀏覽器來驗證這是否為您在網際網路上的 IP 位址 (問「我的 IP 位址是什麼)。 偶爾會因為各種原因而不相符。

    ![您的 IP 位址](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. 假設的 IP 位址相符，按一下工具列上的 [新增用戶端 IP]。

    ![新增用戶端 IP](./media/sql-data-warehouse-server-firewall/add-client-ip.png)

    > [!NOTE]
    > 您可以將 SQL Server (邏輯伺服器) 上的防火牆，針對單一 IP 位址或整個位址區段作開放。 開放防火牆可讓 SQL 系統管理員和使用者登入伺服器上，任何他們具備有效認證的資料庫。
    >

4. 按一下工具列上的 [儲存] 來儲存此項伺服器層級防火牆規則，然後按一下 [確定]。

    ![新增用戶端 IP](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

<!--HONumber=Jan17_HO3-->


