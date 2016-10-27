<properties
    pageTitle="SQL Database 服務的所有主題 | Microsoft Azure"
    description="http://azure.microsoft.com/documentation/articles/ 上名為 SQL Database 的 Azure 服務的所有主題表格 (標題與說明)。"
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>



# <a name="all-topics-for-azure-sql-database-service"></a>Azure SQL Database 服務的所有主題

本主題會列出每一個直接適用於 Azure **SQL Database** 服務的主題。 您可以使用 **Ctrl+F**，利用關鍵字搜尋本網頁，以尋找目前感興趣的主題。




## <a name="new"></a>新增

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 1 | [Daxko/CSI 使用 Azure 來加速其開發週期及提升其客戶服務和效能](sql-database-implementation-daxko.md) | 了解 Daxko/CSI 如何使用 SQL Database 來加速其開發週期及提升其客戶服務和效能 |
| 2 | [Azure 讓 GEP 全球化並更有效率](sql-database-implementation-gep.md) | 了解 GEP 如何使用 SQL Database 將觸角伸向更多全球客戶及提升效率 |
| 3 | [透過 Azure，SnelStart 以每月 1,000 個新 Azure SQL Database 的速度快速擴充其商務服務](sql-database-implementation-snelstart.md) | 了解 SnelStart 如何使用 SQL Database 以每月 1,000 個新 Azure SQL Database 的速度快速擴充其商務服務 |
| 4 | [Umbraco 使用 Azure SQL Database 為雲端中數千個租用戶快速佈建和調整服務](sql-database-implementation-umbraco.md) | 了解 Umbraco 如何使用 SQL Database 為雲端中數千個租用戶快速佈建和調整服務 |
| 5 | [使用 PowerShell 管理 Azure SQL Database](sql-database-manage-powershell.md) | 使用 PowerShell 管理 Azure SQL Database。 |
| 6 | [適用於與 SQL Database 和 SQL 資料倉儲搭配使用之 Azure AD MFA 的 SSMS 支援](sql-database-ssms-mfa-authentication.md) | 針對 SQL Database 和 SQL 資料倉儲，搭配使用 Multi-Factor Authentication 與 SSMS。 |
| 7 | [說明資料庫交易單位 (DTU) 和彈性資料庫交易單位 (eDTU)](sql-database-what-is-a-dtu.md) | 了解 Azure SQL Database 交易單元是什麼。 |


## <a name="updated-articles,-sql-database"></a>更新的文章, SQL Database

本節列出具有重大更新內容的最近更新文章。 針對每篇更新的文章，會顯示一段新增的 Markdown 文字概略程式碼片段。 文章的更新日期範圍是在 **2016-08-22** 到 **2016-10-05**。

| &nbsp; | 文章 | 更新的文字、程式碼片段 | 更新時機 |
| --: | :-- | :-- | :-- |
| 8 | [使用 PowerShell 管理 Azure SQL Database](sql-database-command-line-tools.md) | 使用 (https://msdn.microsoft.com/library/azure/mt759837.aspx) Cmdlet 來為 SQL Database 及相關的 Azure 資源建立資源群組。 * $resourceGroupName = "resourcegroup1" $resourceGroupLocation = "northcentralus" New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation * 如需詳細資訊，請參閱搭配使用 Azure PowerShell 與 Azure Resource Manager (../powershell-azure-resource-manager.md)。 如需範例指令碼，請參閱建立 SQL Database PowerShell 指令碼 (sql-database-get-started-powershell.md create-a-sql-database-powershell-script)。 **建立 SQL Database 伺服器** 使用 New-AzureRmSqlServer 建立 SQL Database 伺服器 (https://msdn.microsoft.com/library/azure/mt603715.aspx) Cmdlet。 將 *server1* 取代為您伺服器的名稱。 伺服器名稱在所有 Azure SQL Database 伺服器必須是唯一的。 如果該伺服器名稱已被使用，您會收到錯誤訊息。 此命令可能需要幾分鐘的時間才能完成。 結果 | 2016-09-14 |
| 9 | [使用 PowerShell 複製 Azure SQL Database](sql-database-copy-powershell.md) |   SQL Database 來源 (要複製的現有資料庫)  --  $sourceDbName = "db1"  $sourceDbServerName = "server1"  $sourceDbResourceGroupName = "rg1"  SQL database copy (the new db to be created)  --  $copyDbName = "db1_copy"  $copyDbServerName = "server2"  $copyDbResourceGroupName = "rg2"  將資料庫複製到相同的伺服器  --  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName  將資料庫複製到不同的伺服器  --  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName  將資料庫複製到彈性資料庫集區  --  $poolName = "pool1"  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyReso | 2016-09-08 |
| 10 | [使用 C# 來建立彈性資料庫集區](sql-database-elastic-pool-create-csharp.md) |   Console.WriteLine("Server firewall...");  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);  Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);  Console.WriteLine("Elastic pool...");  ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);  Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);  Console.WriteLine("Database...");  DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);  Console.WriteLine("Database: " + dbr.Database.Id);  Console.WriteLine("Press any key to continue...");  Console.ReadKey();  }  static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa | 2016-09-14 |
| 11 | [用來識別彈性資料庫集區適用資料庫的 PowerShell 指令碼](sql-database-elastic-pool-database-assessment-powershell.md) | ** 針對彈性資料庫集區的候選項目，我們排除 master 和已在集區中的任何資料庫。您可以視需要將更多資料庫新增到下列排除清單** $ListOfDBs = Get-AzureRmSqlDatabase -ServerName $servername.Split('.') 0  -ResourceGroupName $ResourceGroupName  /  Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")} $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword" $destinationTableName = "resource_stats_output" ** Create a table in output database for metrics collection** $sql = " IF  NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U')) BEGIN Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_ | 2016-09-29 |
| 12 | [SQL Database 教學課程：使用 Azure 入口網站在幾分鐘內建立 SQL Database](sql-database-get-started.md) |   ! 新的 SQL Database 1 (./media/sql-database-get-started/sql-database-new-database-1.png) 3. 按一下 [SQL Database]  來開啟 [SQL Database] 刀鋒視窗。 此刀鋒視窗上的內容會視您的訂用帳戶和您現有的物件 (例如現有的伺服器) 數目而定。  ! 新的 SQL Database 2 (./media/sql-database-get-started/sql-database-new-database-2.png) 4. 在 [資料庫名稱]  文字方塊中，提供您第一個資料庫的名稱 - 例如 "my-database"。 綠色核取記號指示您提供的名稱有效。  ! 新的 SQL Database 3 (./media/sql-database-get-started/sql-database-new-database-3.png) 5. 如果您有多個訂用帳戶，請選取一個訂用帳戶。 6. 在 [資源群組] 底下，按一下 [新建] 並提供您第一個資源群組的名稱 - 例如 "my-resource-group"。 綠色核取記號指示您提供的名稱有效。  ! 新的 SQL Database 4 (./media/sql-database-get-started/sql-database-new-database-4.png) 7. 在 **選取來源*下 | 2016-09-08 |
| 13 | [試用 SQL Database：透過 SQL Database Library for .NET 使用 C#; 建立 SQL Database](sql-database-get-started-csharp.md) | ** 建立用來存取資源的服務主體**下列 PowerShell 指令碼會建立 Active Directory (AD) 應用程式以及驗證 C# 應用程式所需的服務主體。 指令碼會輸出先前 C 範例所需的值。 如需詳細資訊，請參閱使用 Azure PowerShell 建立用來存取資源的服務主體 (../resource-group-authenticate-service-principal.md)。  登入 Azure。  Add-AzureRmAccount  如果有多個訂用帳戶，請取消註解並設定為您要使用的訂用帳戶。  $subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"  Set-AzureRmContext -SubscriptionId $subscriptionId  對您的新 AAD 應用程式提供這些值。  $appName 是您應用程式的顯示名稱，在您的目錄中必須是唯一的。  $uri 不需要是實際的 URI。  $secret 是您所建立的密碼。  $appName = "{app-name}"  $uri = "http://{app-name}"  $secret = "{app-password}"  建立 AAD 應用程式  $azureAdApplication = New-AzureRmADApp | 2016-09-23 |
| 14 | [使用 Azure 入口網站管理 Azure SQL Database](sql-database-manage-portal.md) | 若要檢視或更新您的資料庫設定，請按一下 [SQL Database] 刀鋒視窗上所需的設定︰! SQL Database 設定 (./media/sql-database-manage-portal/settings.png) ** 如何尋找 SQL Database 完整伺服器名稱？** 若要檢視您的資料庫伺服器名稱，請按一下 [SQL Database] 刀鋒視窗上的 [概觀]，然後記下伺服器名稱︰! SQL Database 設定 (./media/sql-database-manage-portal/server-name.png) ** 如何管理防火牆規則以控制我的 SQL Server 和 Database 的存取權？** 若要檢視、建立或更新防火牆規則，請按一下 [SQL Database] 刀鋒視窗上的 [設定伺服器防火牆]。 如需詳細資訊，請參閱使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則 (sql-database-configure-firewall-settings.md)。 ! 防火牆規則 (./media/sql-database-manage-portal/sql-database-firewall.png) ** 如何變更 SQL Database 服務層或效能等級？** 若要更新 SQL Database 的服務層或效能等級，請按一下 ** 定價層 ( | 2016-09-20 |





## <a name="get-started"></a>開始使用

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 15 | [使用 Azure SQL Database 以隔離和高效率方式建置多租用戶應用程式](sql-database-build-multi-tenant-apps.md) | 了解 Azure SQL Database 如何建置多租用戶的應用程式 |
| 16 | [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md) | 了解如何使用 SQL Server Management Studio (SSMS) 在 Azure 上連接到 SQL Database。 然後，使用 TRANSACT-SQL (T-SQL) 執行範例查詢。 |
| 17 | [使用 .NET (C#) 連接到 SQL Database](sql-database-develop-dotnet-simple.md) | 使用這個快速入門中的範例程式碼建置現代應用程式，這個應用程式使用 C#，並受到具有 Azure SQL Database 的雲端中之強大關聯式資料庫的支援。 |
| 18 | [使用 Azure 入口網站來建立新的彈性資料庫集區](sql-database-elastic-pool-create-portal.md) | 如何將可調整的彈性資料庫集區新增至 SQL Database 組態，以便更輕鬆地進行多個資料庫的系統管理與資源共用。 |
| 19 | [探索 Azure SQL Database 教學課程](sql-database-explore-tutorials.md) | 了解 SQL Database 特性與功能 |
| 20 | [SQL Database 教學課程：使用 Azure 入口網站在幾分鐘內建立 SQL Database](sql-database-get-started.md) | 了解如何設定 SQL Database 邏輯伺服器、伺服器防火牆規則、SQL Database 和範例資料。 此外，了解如何連接用戶端工具、設定使用者以及設定資料庫防火牆規則。 |
| 21 | [Azure SQL Database 安全防護](sql-database-helps-secures-and-protects.md) | 了解 SQL Database 如何協助安全防護 |
| 22 | [Azure SQL Database 學習 &amp; 調整](sql-database-learn-and-adapt.md) | 了解 SQL Database 如何學習與調整 |
| 23 | [選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | 了解哪一個雲端 SQL Server 選項適合您的應用程式：Azure SQL (PaaS) Database 或 Azure 虛擬機器上雲端中的 SQL Server。 |
| 24 | [Azure SQL Database 即時調整](sql-database-scale-on-the-fly.md) | 了解 SQL Database 如何進行即時調整 |
| 25 | [探索 Azure SQL Database 解決方案快速入門](sql-database-solution-quick-starts.md) | 了解 Azure SQL Database 解決方案 |
| 26 | [Azure SQL Database 可在您的環境中運作](sql-database-works-in-your-environment.md) | 了解 SQL Database 如何協助安全防護 |



## <a name="build-an-app"></a>建置應用程式

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 27 | [排解、診斷和防止 SQL Database 的 SQL 連接錯誤和暫時性錯誤](sql-database-connectivity-issues.md) | 了解如何在 Azure SQL Database 中排解、診斷和防止 SQL 連接錯誤或暫時性錯誤。  |
| 28 | [使用 Visual Studio 連接到 SQL Database](sql-database-connect-query.md) | 以 C# 撰寫程式以查詢和連接到 SQL Database。 有關 IP 位址、連接字串、安全登入和免費 Visual Studio 的資訊。 |
| 29 | [針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md) | 從 ADO.NET 至 Azure SQL Database V12 的用戶端連線有時會略過 Proxy 並直接與資料庫互動。 1433 以外的連接埠變得重要。 |
| 30 | [SQL Database 用戶端應用程式的 SQL 錯誤碼：資料庫連線錯誤和其他問題](sql-database-develop-error-messages.md) | 了解 SQL Database 用戶端應用程式的 SQL 錯誤碼，例如常見的資料庫連線錯誤、資料庫副本問題，以及一般錯誤。  |
| 31 | [在 Windows 上使用 PHP 連接到 SQL Database](sql-database-develop-php-simple.md) | 提供可從 Windows 用戶端連接到 Azure SQL Database 的範例 PHP 程式，並提供用戶端所需之必要軟體元件的連結。 |
| 32 | [試用 SQL Database：透過 SQL Database Library for .NET 使用 C#; 建立 SQL Database](sql-database-get-started-csharp.md) | 嘗試用 SQL Database 開發 SQL 和 C# 應用程式，然後使用 SQL Database Library for .NET 以 C# 建立 Azure SQL Database。 |
| 33 | [開始使用 Azure SQL Database 中的 JSON 功能](sql-database-json-features.md) | Azure SQL Database 可讓您剖析、查詢及格式化採用「JavaScript 物件標記法」(JSON) 的資料。 |
| 34 | [針對 Azure SQL Database 連線問題進行疑難排解](sql-database-troubleshoot-common-connection-issues.md) | 找出並解決 Azure SQL Database 常見之連接錯誤的步驟。 |
| 35 | [連接到 SQL Database 時發生「目前無法使用伺服器上的資料庫」錯誤](sql-database-troubleshoot-connection.md) | 針對應用程式連接到 SQL Database 時發生的「目前無法使用伺服器上的資料庫」錯誤進行。 |



## <a name="develop"></a>開發

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 36 | [取得驗證應用程式以從程式碼存取 SQL Database 的所需值](sql-database-client-id-keys.md) | 建立服務主體以從程式碼存取 SQL Database。 |
| 37 | [在 Windows 上搭配 JDBC 使用 Java 連接到 SQL Database](sql-database-develop-java-simple.md) | 提供可用來連接到 Azure SQL Database 的 Java 程式碼範例。 這個範例使用在 Windows 用戶端電腦上執行的 JDBC。 |
| 38 | [使用 Node.js 連接到 SQL Database](sql-database-develop-nodejs-simple.md) | 提供可用來連接到 Azure SQL Database 的 Node.js 程式碼範例。 |
| 39 | [SQL Database 開發概觀](sql-database-develop-overview.md) | 深入了解連接至 SQL Database 的應用程式的可用連線庫和最佳作法。 |
| 40 | [使用 Python 連接到 SQL Database](sql-database-develop-python-simple.md) | 提供可用來連接到 Azure SQL Database 的 Python 程式碼範例。 |
| 41 | [使用 Ruby 連接到 SQL Database](sql-database-develop-ruby-simple.md) | 提供您可執行以連接到 Azure SQL Database 的 Ruby 程式碼範例。 |
| 42 | [開始使用 Azure SQL Database 中的時態表](sql-database-temporal-tables.md) | 了解如何開始使用 Azure SQL Database 中的時態表。 |



## <a name="performance-and-scale"></a>效能和延展性

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 43 | [SQL Database 建議程式](sql-database-advisor.md) | Azure SQL Database 建議程式會提供可改善現有 SQL Database 查詢效能的建議。 |
| 44 | [使用 Azure 入口網站的 SQL Database 建議程式](sql-database-advisor-portal.md) | 您可以在 Azure 入口網站中，使用 Azure SQL Database 建議程式來檢閱和實作適用於您現有 SQL Database 的建議，這些建議可以改善現有的查詢效能。 |
| 45 | [Azure SQL Database 基準測試概觀](sql-database-benchmark-overview.md) | 本主題說明用來測量 Azure SQL Database 效能的「Azure SQL Database 基準測試」。 |
| 46 | [何時使用彈性資料庫集區？](sql-database-elastic-pool-guidance.md) | 彈性資料庫集區是一組彈性資料庫共用的可用資源集合。 本文件提供指引，幫助您評估對一組資料庫使用彈性資料庫集區的合適性。 |
| 47 | [開始使用彈性資料庫工具](sql-database-elastic-scale-get-started.md) | Azure SQL Database 彈性資料庫工具功能的基本解說，包括易於執行的範例應用程式。 |
| 48 | [SQL Database 常見問題集](sql-database-faq.md) | 客戶詢問有關雲端資料庫與 Azure SQL Database、Microsoft 的關聯式資料庫管理系統 (RDBMS) 以及資料庫作為雲端中服務的一般問題的解答。 |
| 49 | [在 SQL Database 中開始使用 In-Memory (預覽)](sql-database-in-memory.md) | SQL 記憶體內部技術大幅提升交易和分析工作負載的效能。 了解如何利用這些技術。 |
| 50 | [使用記憶體內部 OLTP (預覽) 改善 SQL Database 中的應用程式效能](sql-database-in-memory-oltp-migration.md) | 採用記憶體內部 OLTP 來改善現有 SQL Database 的交易效能。 |
| 51 | [監視記憶體內部 OLTP 儲存體](sql-database-in-memory-oltp-monitoring.md) | 估計和監視 XTP 記憶體內儲存體使用量、容量；解決容量錯誤 41823 |
| 52 | [操作 Azure SQL Database 中的查詢存放區](sql-database-operate-query-store.md) | 了解如何操作 Azure SQL Database 中的查詢存放區 |
| 53 | [SQL Database 效能深入解析](sql-database-performance.md) | Azure SQL Database 會提供可協助您找出可改善目前查詢效能區域的效能工具。 |
| 54 | [Azure SQL Database 和單一資料庫效能](sql-database-performance-guidance.md) | 本文可協助您判斷要為您的應用程式選擇哪個服務層。 文中也會建議微調應用程式以充分利用 Azure SQL Database 的方式。 |
| 55 | [Azure SQL Database 查詢效能深入解析](sql-database-query-performance.md) | 查詢效能監視可識別 Azure SQL Database 的大部分 CPU 取用的查詢。 |
| 56 | [變更 SQL Database 的服務層級和效能等級 (定價層)](sql-database-scale-up.md) | 變更 Azure SQL Database 的服務層級和效能等級說明如何相應增加或減少您的 SQL Database。 變更 Azure SQL Database 的定價層。 |
| 57 | [監視 Azure SQL Database 中的資料庫效能](sql-database-single-database-monitor.md) | 了解使用 Azure 工具和動態管理檢視來監視您的資料庫的選項。 |
| 58 | [SQL Database 效能調整秘訣](sql-database-troubleshoot-performance.md) | 透過評估和改進來調整 Azure SQL Database 效能的秘訣。 |
| 59 | [如何使用批次處理來改善 SQL Database 應用程式效能](sql-database-use-batching-to-improve-performance.md) | 本主題提供批次處理資料庫作業可大幅改善 Azure SQL Database 應用程式之速度和延展性的證據。 雖然這些批次處理技術適用於任何 SQL Server 資料庫，但本文的重點在於 Azure。 |



## <a name="tools-for-scaling-out"></a>相應放大的工具

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 60 | [多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md) | 本文討論雲端環境中執行的多租用戶資料庫應用程式需要考慮的需求和通用資料架構模式，以及這些模式相關的各種利弊取捨。 其中也說明 Azure SQL Database 搭配其彈性集區和彈性工具，如何在無需妥協的情況下滿足這些需求。 |
| 61 | [轉換現有的資料庫以使用彈性資料庫工具](sql-database-elastic-convert-to-use-elastic-tools.md) | 建立分區對應管理員來轉換分區化資料庫，以使用彈性資料庫工具 |
| 62 | [建置可調整的雲端資料庫](sql-database-elastic-database-client-library.md) | 使用彈性資料庫用戶端程式庫建置可調整的 .NET 資料庫應用程式 |
| 63 | [分區對應管理員的效能計數器](sql-database-elastic-database-perf-counters.md) | ShardMapManager 類別與資料相依路由效能計數器 |
| 64 | [使用彈性資料庫工具加入分區](sql-database-elastic-scale-add-a-shard.md) | 如何使用 Elastic Scale API 將新的分區新增至分區集。 |
| 65 | [部署分割合併服務](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | 使用彈性資料庫工具來分割及合併 |
| 66 | [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md) | 如何在 .NET 應用程式中將 ShardMapManager 類別用於資料相依路由 (Azure SQL Database 的彈性資料庫的一項功能) |
| 67 | [彈性資料庫工具常見問題集](sql-database-elastic-scale-faq.md) | 有關 Azure SQL Database 彈性延展的常見問題集。 |
| 68 | [彈性資料庫工具字彙](sql-database-elastic-scale-glossary.md) | 彈性資料庫工具所用詞彙的解釋 |
| 69 | [使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md) | 軟體即服務 (SaaS) 開發人員可以輕鬆地使用這些工具在雲端中建立彈性的可擴充資料庫 |
| 70 | [用來存取彈性資料庫用戶端程式庫的認證](sql-database-elastic-scale-manage-credentials.md) | 如何設定彈性資料庫應用程式的正確認證層級 (管理員到唯讀) |
| 71 | [多分區查詢](sql-database-elastic-scale-multishard-querying.md) | 使用彈性資料庫用戶端程式跨分區執行查詢。 |
| 72 | [在向外延展的雲端資料庫之間移動資料](sql-database-elastic-scale-overview-split-and-merge.md) | 說明如何使用彈性資料庫 API 透過自我託管服務操作分區和移動資料。 |
| 73 | [使用分區對應管理員相應放大資料庫](sql-database-elastic-scale-shard-map-management.md) | 如何使用彈性資料庫用戶端程式庫 ShardMapManager |
| 74 | [分割合併安全性設定](sql-database-elastic-scale-split-merge-security-configuration.md) | 設定加密的 x409 憑證 |
| 75 | [將應用程式升級以使用最新的彈性資料庫用戶端程式庫](sql-database-elastic-scale-upgrade-client-library.md) | 使用 Nuget 升級應用程式和程式庫 |
| 76 | [搭配使用彈性資料庫用戶端程式庫與 Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | 使用彈性資料庫用戶端程式庫與和 Entity Framework 來編寫資料庫 |
| 77 | [搭配使用彈性資料庫用戶端程式庫與 Dapper](sql-database-elastic-scale-working-with-dapper.md) | 搭配使用彈性資料庫用戶端程式庫與 Dapper。 |
| 78 | [使用彈性資料庫工具和資料列層級安全性的多租用戶應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md) | 了解如何搭配資料列層級安全性使用彈性資料庫工具建置應用程式，且讓此應用程式在 Azure SQL Database 上具有可支援多租用戶分區的高度可擴充資料層。 |



## <a name="elastic-jobs"></a>彈性工作

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 79 | [使用彈性工作建立和管理相應放大的 Azure SQL Database (預覽)](sql-database-elastic-jobs-create-and-manage.md) | 逐步解說如何建立和管理彈性資料庫工作。 |
| 80 | [開始使用彈性資料庫工作](sql-database-elastic-jobs-getting-started.md) | 如何使用彈性資料庫工作 |
| 81 | [管理相應放大的雲端資料庫](sql-database-elastic-jobs-overview.md) | 說明彈性資料庫工作服務 |
| 82 | [使用 PowerShell 建立和管理 SQL Database 彈性資料庫工作 (預覽)](sql-database-elastic-jobs-powershell.md) | 用來管理 Azure SQL Database 集區的 PowerShell |
| 83 | [安裝彈性資料庫工作概觀](sql-database-elastic-jobs-service-installation.md) | 逐步解說如何安裝彈性工作功能。 |
| 84 | [解除安裝彈性資料庫工作元件](sql-database-elastic-jobs-uninstall.md) | 如何解除安裝彈性資料庫工作工具 |



## <a name="elastic-pools"></a>彈性集區

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 85 | [什麼是 Azure 彈性集區？](sql-database-elastic-pool.md) | 使用集區管理數百或數千個資料庫。 針對一組效能單位的一個價格可以分散在集區。 您可以隨意將資料庫移入或移出。 |
| 86 | [使用 C# 來建立彈性資料庫集區](sql-database-elastic-pool-create-csharp.md) | 使用 C# 資料庫開發技術在 Azure SQL Database 中建立可調整的彈性資料庫集區，讓您可以跨多個資料庫共用資源。 |
| 87 | [使用 PowerShell 建立新的彈性資料庫集區](sql-database-elastic-pool-create-powershell.md) | 了解如何藉由建立可調整的彈性資料庫集區來管理多個資料庫，進而使用 PowerShell 來相應放大Azure SQL Database 資源。 |
| 88 | [用來識別彈性資料庫集區適用資料庫的 PowerShell 指令碼](sql-database-elastic-pool-database-assessment-powershell.md) | 彈性資料庫集區是一組彈性資料庫共用的可用資源集合。 本文件提供 Powershell 指令碼，幫助您評估對一組資料庫使用彈性資料庫集區的合適性。 |
| 89 | [透過 C# 監視和管理彈性資料庫集區](sql-database-elastic-pool-manage-csharp.md) | 使用 C# 資料庫開發技術管理 Azure SQL Database 彈性資料庫集區。 |
| 90 | [使用 Azure 入口網站監視和管理彈性資料庫集區](sql-database-elastic-pool-manage-portal.md) | 了解如何使用 Azure 入口網站和 SQL Database 的內建智慧功能來管理、監視及準確估量可調整的彈性資料庫集區，以期能最佳化資料庫效能和管理成本。 |
| 91 | [透過 PowerShell 監視和管理彈性資料庫集區](sql-database-elastic-pool-manage-powershell.md) | 了解如何使用 PowerShell 管理彈性資料庫集區。 |
| 92 | [使用 Transact-SQL 監視和管理彈性資料庫集區](sql-database-elastic-pool-manage-tsql.md) | 使用 T-SQL 在彈性集區中建立 Azure SQL Database。 或使用 T-SQL 將資料庫移入和移出集區。 |
| 93 | [彈性資料庫集區的計費和定價資訊](sql-database-elastic-pool-price.md) | 彈性資料庫集區專屬定價資訊。 |



## <a name="elastic-query"></a>彈性查詢

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 94 | [跨相應放大雲端資料庫報告 (預覽)](sql-database-elastic-query-getting-started.md) | 如何使用跨資料庫的資料庫查詢 |
| 95 | [開始使用跨資料庫查詢 (垂直資料分割) (預覽)](sql-database-elastic-query-getting-started-vertical.md) | 如何使用具有垂直分割資料庫的彈性資料庫查詢 |
| 96 | [跨相應放大的雲端資料庫報告 (預覽)](sql-database-elastic-query-horizontal-partitioning.md) | 如何設定水平資料分割的彈性查詢 |
| 97 | [Azure SQL Database 彈性資料庫查詢概觀 (預覽)](sql-database-elastic-query-overview.md) | 彈性查詢功能的概觀 |
| 98 | [對不同結構描述的雲端資料庫執行查詢 (預覽)](sql-database-elastic-query-vertical-partitioning.md) | 如何透過垂直資料分割設定跨資料庫查詢 |



## <a name="elastic-transaction"></a>彈性交易

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 99 | [跨雲端資料庫的分散式交易](sql-database-elastic-transactions-overview.md) | Azure SQL Database 的彈性資料庫交易概觀 |



## <a name="backup-and-recovery"></a>備份與復原

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 100 | [SQL Database 備份](sql-database-automated-backups.md) | 深入了解可讓您將 Azure SQL Database 回復到先前時間點，或將資料庫複製到地理區域中新資料庫 (最多 35 天) 的 SQL Database 內建資料庫備份。 |
| 101 | [使用 Azure SQL Database 的商務持續性概觀](sql-database-business-continuity.md) | 了解 Azure SQL Database 如何支援雲端商務持續性和資料庫復原，以及如何協助維持任務關鍵性雲端應用程式持續執行。 |
| 102 | [如何從 Azure SQL Database 備份還原單一資料表](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | 了解如何從 Azure SQL Database 備份還原單一資料表。 |
| 103 | [使用 SQL Database 的作用中異地複寫設計雲端災害復原應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | 了解如何使用異地複寫針對商務持續性計劃設計雲端災害復原方案，以用於搭配 Azure SQL Database 備份應用程式資料。 |
| 104 | [還原 Azure SQL Database 或容錯移轉到次要資料庫](sql-database-disaster-recovery.md) | 了解如何使用 Azure SQL Database 的作用中異地複寫和異地還原功能，從區域資料中心中斷或失敗情況復原資料庫。 |
| 105 | [執行嚴重損壞修復演練](sql-database-disaster-recovery-drills.md) | 了解使用 Azure SQL Database 執行嚴重損壞修復演練的指引和最佳作法，以協助確保您的關鍵性商務應用程式在失敗和中斷時可迅速復原。 |
| 106 | [使用 SQL Database 彈性集區之應用程式的災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | 了解如何選擇正確的容錯移轉模式來設計災害復原的雲端解決方案。 |
| 107 | [使用 RecoveryManager 類別來修正分區對應問題](sql-database-elastic-database-recovery-manager.md) | 使用 RecoveryManager 類別來解決分區對應的問題 |
| 108 | [匯入 BACPAC 檔案以建立 Azure SQL Database](sql-database-import.md) | 匯入現有的 BACPAC 檔案以建立 Azure SQL Database。 |
| 109 | [使用 Azure 入口網站將 Azure SQL Database 還原至先前的時間點](sql-database-point-in-time-restore-portal.md) | 將 Azure SQL Database 還原至先前的時間點。 |
| 110 | [使用 PowerShell 將 Azure SQL Database 還原至先前的時間點](sql-database-point-in-time-restore-powershell.md) | 將 Azure SQL Database 還原至先前的時間點 |
| 111 | [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md) | 還原時間點, Microsoft Azure SQL Database, 還原資料庫, 復原資料庫, Azure 傳統入口網站, Azure 傳統入口網站 |
| 112 | [使用自動資料庫備份復原 Azure SQL Database](sql-database-recovery-using-backups.md) | 了解還原時間點，其可讓您復原 Azure SQL Database 到先前的時間 (最多 35 天)。 |
| 113 | [還原使用 Azure 入口網站的已刪除 Azure SQL Database](sql-database-restore-deleted-database-portal.md) | 還原已刪除的 Azure SQL Database (Azure 入口網站)。 |
| 114 | [使用 PowerShell 還原已刪除的 Azure SQL Database](sql-database-restore-deleted-database-powershell.md) | 還原已刪除的 Azure SQL Database (PowerShell)。 |
| 115 | [將資料庫還原至先前的時間點、還原已刪除的資料庫，或從資料中心中斷情況復原](sql-database-troubleshoot-backup-and-restore.md) | 了解如何使用 Azure SQL Database 中的備份和複本，從錯誤和中斷情況復原雲端資料庫。 |



## <a name="migrate"></a>移轉

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 116 | [使用 SqlPackage 將的 SQL Server Database 匯出到 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Microsoft Azure SQL Database, 資料庫移轉, 匯出資料庫, 匯出 BACPAC 檔案, sqlpackage |
| 117 | [使用 SQL Server Management Studio 將 SQL Server 資料庫匯出到 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Microsoft Azure SQL Database, 資料庫移轉, 匯出資料庫, 匯出 BACPAC 檔案, 匯出資料層應用程式精靈 |
| 118 | [使用 SqlPackage 從 BACPAC 檔案匯入 SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 匯入 BACPAC 檔案, sqlpackage |
| 119 | [使用 SSMS 從 BACPAC 匯入到 SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Microsoft Azure SQL Database、資料庫部署、資料庫移轉、匯入資料庫，匯出資料庫、移轉精靈 |
| 120 | [使用「將資料庫部署到 Microsoft Azure Database 精靈」將 SQL Database 移轉到 SQL Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Microsoft Azure SQL Database, 資料庫移轉, Microsoft Azure 資料庫精靈 |
| 121 | [使用異動複寫將 SQL Server 資料庫移轉到 Azure SQL Database](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 異動複寫 |
| 122 | [使用 SqlPackage.exe 判斷 SQL Database 相容性](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure SQL Database, 資料庫移轉, SQL Database 相容性, SqlPackage |
| 123 | [在移轉至 Azure SQL Database 之前，先使用 SQL Server Management Studio 來判斷 SQL Database 相容性](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure SQL Database, 資料庫移轉, SQL Database 相容性, 匯出資料層應用程式精靈 |
| 124 | [在移轉至 Azure SQL Database 之前，先使用「SQL Azure 移轉精靈」來修正 SQL Server 資料庫相容性問題](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure SQL Database, 資料庫移轉, 資料庫相容性, SQL Azure 移轉精靈 |
| 125 | [使用適用於 Visual Studio 的 SQL Server Data Tools 將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Azure SQL Database, 資料庫移轉, 資料庫相容性, SQL Azure 移轉精靈, SSDT |
| 126 | [在移轉至 SQL Database 之前，使用 SQL Server Management Studio 修正 SQL Server 資料庫相容性問題](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure SQL Database, 資料庫移轉, 資料庫相容性, SQL Azure 移轉精靈 |
| 127 | [使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案](sql-database-export-powershell.md) | 使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案 |
| 128 | [使用 PowerShell 匯入 BACPAC 檔案以建立 Azure SQL Database](sql-database-import-powershell.md) | 使用 PowerShell 匯入 BACPAC 檔案以建立 Azure SQL Database |
| 129 | [將資料從 CSV 載入 Azure SQL 資料倉儲 (一般檔案)](sql-database-load-from-csv-with-bcp.md) | 對於較小的資料大小，請使用 bcp 將資料匯入 Azure SQL Database。 |
| 130 | [在伺服器之間、訂用帳戶之間移動資料庫，以及將資料庫移入或移出 Azure](sql-database-troubleshoot-moving-data.md) | 複製、移動和移轉 Azure SQL Database 中的資料和資料庫的簡單步驟。 |



## <a name="move-data-in-and-out"></a>資料移入和移出

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 131 | [SQL Server 資料庫移轉至雲端 SQL Database](sql-database-cloud-migrate.md) | 了解將內部部署 SQL Server 資料庫移轉至雲端 Azure SQL Database 的相關做法。 請先使用資料庫移轉工具測試相容性再進行資料庫移轉。 |
| 132 | [複製 Azure SQL Database](sql-database-copy.md) | 建立 Azure SQL Database 的複本 |
| 133 | [使用 Azure 入口網站將 Azure SQL Database 封存到 BACPAC 檔案](sql-database-export.md) | 使用 Azure 入口網站將 Azure SQL Database 封存到 BACPAC 檔案 |



## <a name="security"></a>安全性

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 134 | [使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲](sql-database-aad-authentication.md) | 了解如何使用 Azure Active Directory 驗證連接到 SQL Database |
| 135 | [一律加密：保護 SQL Database 中的機密資料，並將加密金鑰儲存在 Windows 憑證存放區中](sql-database-always-encrypted.md) | 在數分鐘內開始保護您 SQL 資料庫中的機密資料。 |
| 136 | [一律加密：保護 SQL Database 中的機密資料，並將加密金鑰儲存在「Azure 金鑰保存庫」中](sql-database-always-encrypted-azure-key-vault.md) | 在數分鐘內開始保護您 SQL 資料庫中的機密資料。 |
| 137 | [SQL Database - 下層用戶端支援與針對「稽核」的 IP 端點變更](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | 了解 SQL Database 下層用戶端支援與針對「稽核」的 IP 端點變更。 |
| 138 | [使用 PowerShell 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-powershell.md) | 了解如何設定存取 Azure SQL Database 之 IP 位址的防火牆。 |
| 139 | [使用 REST API 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-rest.md) | 了解如何設定存取 Azure SQL Database 之 IP 位址的防火牆。 |
| 140 | [使用 T-SQL 設定 Azure SQL Database 伺服器層級和資料庫層級防火牆規則](sql-database-configure-firewall-settings-tsql.md) | 了解如何設定存取 Azure SQL Database 之 IP 位址的防火牆。 |
| 141 | [開始使用 SQL Database 動態資料遮罩 (Azure 入口網站)](sql-database-dynamic-data-masking-get-started.md) | 如何開始在 Azure 入口網站中使用 SQL 資料庫動態資料遮罩 |
| 142 | [開始使用 SQL Database 動態資料遮罩 (Azure 傳統入口網站)](sql-database-dynamic-data-masking-get-started-portal.md) | 如何開始在 Azure 傳統入口網站中使用 SQL 資料庫動態資料遮罩 |
| 143 | [設定 Azure SQL Database 防火牆規則 \- 概觀](sql-database-firewall-configure.md) | 了解如何以伺服器層級和資料庫層級防火牆規則設定 SQL Database 防火牆，以管理存取權。 |
| 144 | [SQL Database 教學課程：建立 SQL Database 使用者帳戶來存取和管理資料庫](sql-database-get-started-security.md) | 了解如何建立使用者帳戶來存取和管理資料庫。 |
| 145 | [SQL Database 驗證和授權：授與存取](sql-database-manage-logins.md) | 深入了解 SQL Database 安全性管理，特別是如何透過伺服器層級主體帳戶管理資料庫存取與登入安全性。 |
| 146 | [Azure SQL Database 安全性方針和限制](sql-database-security-guidelines.md) | 了解與安全性相關的 Microsoft Azure SQL Database 方針和限制。 |
| 147 | [開始使用 SQL Database 威脅偵測](sql-database-threat-detection-get-started.md) | 如何開始在 Azure 入口網站中使用 SQL Database 威脅偵測 |
| 148 | [如何在 Azure SQL Database 中執行重設管理密碼等一般管理工作](sql-database-troubleshoot-permissions.md) | 描述如何在 SQL Database 中執行一般管理工作。 例如，重設管理密碼，授與和移除存取權。 |



## <a name="manage-your-database"></a>管理您的資料庫

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 149 | [開始使用 SQL Database 稽核](sql-database-auditing-get-started.md) | 開始使用 SQL Database 稽核 |
| 150 | [使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md) | 了解如何設定存取 Azure SQL Server 之 IP 位址的防火牆。 |
| 151 | [使用 Azure 入口網站複製 Azure SQL Database](sql-database-copy-portal.md) | 建立 Azure SQL Database 的複本 |
| 152 | [使用 Azure 自動化管理 Azure SQL 資料庫](sql-database-manage-automation.md) | 了解如何使用 Azure 自動化服務大規模地管理 Azure SQL 資料庫。 |
| 153 | [概觀：SQL Database 的管理工具](sql-database-manage-overview.md) | 比較管理 Azure SQL Database 的工具和選項 |
| 154 | [使用動態管理檢視監視 Azure SQL Database](sql-database-monitoring-with-dmvs.md) | 了解如何使用動態管理檢視監視 Microsoft Azure SQL Database 來偵測和診斷常見的效能問題。 |
| 155 | [保護您的 SQL Database](sql-database-security.md) | 了解 Azure SQL Database 和 SQL Server 安全性，包括雲端和 SQL Server 內部部署之間在驗證、授權、連線安全性、加密和法規遵循等各方面的差異。 |



## <a name="extended-events"></a>擴充事件

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 156 | [SQL Database 中擴充事件的事件檔案目標程式碼](sql-database-xevent-code-event-file.md) | 提供 PowerShell 和 Transact-SQL 的兩階段程式碼範例，示範 Azure SQL Database 上擴充事件中的事件檔案目標。 此案例必須要有 Azure 儲存體。 |
| 157 | [SQL Database 中擴充事件的信號緩衝區目標程式碼](sql-database-xevent-code-ring-buffer.md) | 提供 Transact-SQL 程式碼範例，可在 Azure SQL Database 中輕鬆又快速使用信號緩衝區目標。 |
| 158 | [SQL Database 中的擴充事件](sql-database-xevent-db-diff-from-svr.md) | 描述 Azure SQL Database 中的擴充事件 (XEvents)，以及事件工作階段與 Microsoft SQL Server 中的事件工作階段有如何的些微不同。 |



## <a name="geo-replication"></a>異地複寫

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 159 | [使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉](sql-database-geo-replication-failover-portal.md) | 使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉 |
| 160 | [使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉](sql-database-geo-replication-failover-powershell.md) | 使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉 |
| 161 | [使用 Transact-SQL 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉](sql-database-geo-replication-failover-transact-sql.md) | 使用 Transact-SQL 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉 |
| 162 | [概觀︰SQL Database 作用中異地複寫](sql-database-geo-replication-overview.md) | 作用中異地複寫可讓您在任何 Azure 資料中心中設定資料庫的 4 個複本。 |
| 163 | [使用 Azure 入口網站為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-portal.md) | 使用 Azure 入口網站為 Azure SQL Database 設定異地複寫 |
| 164 | [使用 PowerShell 為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-powershell.md) | 使用 PowerShell 為 Azure SQL Database 設定作用中異地複寫 |
| 165 | [如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md) | 本主題說明在資料庫還原或容錯移轉之後管理安全性的安全性考量。 |
| 166 | [使用 Transact-SQL 為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-transact-sql.md) | 使用 Transact-SQL 為 Azure SQL Database 設定異地複寫 |
| 167 | [使用 Azure 入口網站從異地備援備份異地還原 Azure SQL Database](sql-database-geo-restore-portal.md) | 從異地備援備份 (Azure 入口網站) 異地還原 Azure SQL Database。 |
| 168 | [使用 PowerShell 從異地備援備份還原 Azure SQL Database](sql-database-geo-restore-powershell.md) | 從異地備援備份將 Azure SQL Database 異地還原至新的伺服器 |



## <a name="upgrade"></a>升級

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 169 | [改善 Azure SQL Database 中相容性層級 130 的查詢效能](sql-database-compatibility-level-query-performance-130.md) | 步驟和工具，可供判斷哪個相容性層級最適合您在 Azure SQL Database 或 Microsoft SQL Server 上的資料庫 |
| 170 | [使用 SQL Database 作用中異地複寫管理雲端應用程式的輪流升級](sql-database-manage-application-rolling-upgrade.md) | 了解如何使用 Azure SQL Database 異地複寫以支援雲端應用程式的線上升級。 |
| 171 | [使用 Azure 入口網站升級至 Azure SQL Database V12](sql-database-upgrade-server-portal.md) | 說明如何使用 Azure 入口網站升級至 Azure SQL Database V12，包括如何升級 Web 和商務資料庫，以及如何將 V11 伺服器的資料庫直接移轉至彈性資料庫集區來升級 V11 伺服器。 |
| 172 | [使用 PowerShell 升級至 Azure SQL Database V12](sql-database-upgrade-server-powershell.md) | 說明如何使用 PowerShell 升級至 Azure SQL Database V12，包括如何升級 Web 和商務資料庫，以及如何將 V11 伺服器的資料庫直接移轉至彈性資料庫集區來升級 V11 伺服器。 |
| 173 | [規劃和準備升級至 SQL Database V12](sql-database-v12-plan-prepare-upgrade.md) | 說明升級至 Azure SQL Database V12 版本的相關準備和限制。 |
| 174 | [SQL Database V12 新功能](sql-database-v12-whats-new.md) | 說明在雲端使用 Azure SQL Database 的業務系統為何可在立即升級至 V12 版本之後受益。 |
| 175 | [Web 和 Business 版本終止常見問題集](sql-database-web-business-sunset-faq.md) | 了解 Azure SQL Web 和 Business 資料庫將淘汰，並了解新服務層的特性和功能。 |



## <a name="tools"></a>工具

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 176 | [使用 PowerShell 管理 Azure SQL Database](sql-database-command-line-tools.md) | 使用 PowerShell 管理 Azure SQL Database。 |
| 177 | [SQL Database 教學課程：將 Excel 連接到 Azure SQL Database 並建立報告](sql-database-connect-excel.md) | 了解如何將 Microsoft Excel 連接到雲端的 Azure SQL Database。 將資料匯入 Excel 中進行報告和資料探索。 |
| 178 | [建立 SQL Database 並使用 PowerShell Cmdlet 執行一般資料庫設定工作](sql-database-get-started-powershell.md) | 了解如何使用 PowerShell 來建立 SQL Database。 透過 PowerShell cmdlet 可以管理一般資料庫設定工作。 |
| 179 | [開始使用 Azure SQL 資料同步 (預覽)](sql-database-get-started-sql-data-sync.md) | 本教學課程可協助您開始使用 Azure SQL 資料同步 (預覽)。 |
| 180 | [使用 SQL Server Management Studio 管理 Azure SQL Database](sql-database-manage-azure-ssms.md) | 了解如何使用 SQL Server Management Studio 管理 SQL Database 伺服器和資料庫。 |
| 181 | [使用 Azure 入口網站管理 Azure SQL Database](sql-database-manage-portal.md) | 了解如何在 Azure 傳統入口網站中使用 Azure 入口網站管理雲端中的關聯式資料庫。 |
| 182 | [使用 PowerShell 變更 SQL Database 的服務層級級和效能等級 (定價層)](sql-database-scale-up-powershell.md) | 變更 Azure SQL Database 的服務層級和效能等級說明如何使用 PowerShell 相應增加或減少您的 SQL Database。 使用 PowerShell 變更 Azure SQL Database 的定價層。 |
| 183 | [在 Azure RemoteApp 中使用 SQL Server Management Studio 來連接到 SQL Database](sql-database-ssms-remoteapp.md) | 使用本教學課程來了解如何在連線到 SQL Database 時，在 Azure RemoteApp 中使用 SQL Server Management Studio 以維護安全性和效能 |



## <a name="reference"></a>參考

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 184 | [SQL Database 和 SQL Server 的連線庫](sql-database-libraries.md) | 列出用戶端程式可用來連接到 Azure SQL Database 或 Microsoft SQL Server 之每個驅動程式的最低版本號碼。 連結所提供的驅動程式版本資訊，是由社群 (而不是由 Microsoft) 所發行。 |
| 185 | [Azure SQL Database 資源限制](sql-database-resource-limits.md) | 此頁面描述一些 Azure SQL Database 常見的資源限制。 |
| 186 | [Azure SQL Database Transact-SQL 差異](sql-database-transact-sql-information.md) | 在 Azure SQL Database 中未完整支援  Transact-SQL 陳述式 |



## <a name="miscellaneous"></a>其他資訊

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 187 | [使用 PowerShell 複製 Azure SQL Database](sql-database-copy-powershell.md) | 使用 PowerShell 建立 Azure SQL Database 的複本 |
| 188 | [使用 Transact-SQL 複製 Azure SQL Database](sql-database-copy-transact-sql.md) | 使用 Transact-SQL 建立 Azure SQL Database 的複本 |
| 189 | [Azure SQL Database 一般限制與方針](sql-database-general-limitations.md) | 此頁面描述 Azure SQL Database 的部分一般限制，以及互通性與支援的範圍。 |
| 190 | [SQL Database 定價層建議](sql-database-service-tier-advisor.md) | 在 Azure 入口網站中變更定價層時，提供的各種定價層建議所建議的層是最適合執行現有 Azure SQL Database 的工作負載。 定價層會說明 SQL Database 的服務層級和效能等級。 |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>額外項目

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>其他 Azure 服務的相關文章

- [在 Azure 中備份您的 Azure App Service](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>文件工具

- [Azure SQL Database 的更新宣佈](http://azure.microsoft.com/updates/?service=sql-database)

- [使用篩選器搜尋 Microsoft Azure 的所有文件類型](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>學習路徑圖

- [所有 Microsoft Azure 服務的學習路徑圖](http://azure.microsoft.com/documentation/learning-paths/)

- [學習路徑：學習 Azure SQL Database](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [學習路徑：SQL Database 彈性調整](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->





<!--HONumber=Oct16_HO2-->


