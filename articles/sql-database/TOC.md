# 概觀
## [什麼是 SQL Database？](sql-database-technical-overview.md)
### [服務層](sql-database-service-tiers.md)
### [資料庫交易單位](sql-database-what-is-a-dtu.md)
### [DTU 基準測試概觀](sql-database-benchmark-overview.md)
### [資源限制](sql-database-resource-limits.md)
### [特性](sql-database-features.md)
### [SQL Database 常見問題集](sql-database-faq.md)
## 優點
### [學習與調整](sql-database-learn-and-adapt.md)
### [即時調整](sql-database-scale-on-the-fly.md)
### [建置多租用戶應用程式](sql-database-build-multi-tenant-apps.md)
### [安全與保護](sql-database-helps-secures-and-protects.md)
### [在您的環境中運作](sql-database-works-in-your-environment.md)
## 比較
### [比較 SQL Database 和 VM 上的 SQL](sql-database-paas-vs-sql-server-iaas.md)
### [T-SQL 差異](sql-database-transact-sql-information.md)
### [SQL 與 NoSQL 的比較](../documentdb/documentdb-nosql-vs-sql.md)
## [SQL Database 工具](sql-database-manage-overview.md)
## [SQL Database 教學課程](sql-database-explore-tutorials.md)
## [解決方案快速入門](sql-database-solution-quick-starts.md)
## 安全性
### [Azure SQL Database 適用的 Azure 資訊安全中心](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL 資訊安全中心](https://msdn.microsoft.com/library/azure/bb510589)
# 開始使用
## 資料庫和伺服器
### 單一資料庫
#### 學習
##### [Azure 入口網站教學課程：使用 Azure 入口網站建立 Azure SQL 資料庫](sql-database-get-started.md)
#### 建議事項
##### [Azure 入口網站教學課程：使用 Azure 入口網站建立 Azure SQL 資料庫](sql-database-get-started.md)
##### [Azure 入口網站教學課程：使用 PowerShell 建立 Azure SQL 資料庫](sql-database-get-started-powershell.md)
##### [Azure 入口網站教學課程：使用 C# 建立 Azure SQL 資料庫](sql-database-get-started-csharp.md)
### 彈性資料庫集區
#### 學習
##### [彈性資料庫集區](sql-database-elastic-pool.md)
##### [何時該使用彈性資料庫集區](sql-database-elastic-pool-guidance.md)
##### [性集區價格](sql-database-elastic-pool-price.md)
#### 建議事項
##### [使用 Azure 入口網站建立](sql-database-elastic-pool-create-portal.md)
##### [使用 PowerShell 建立](sql-database-elastic-pool-create-powershell.md)
##### [用 C# 建立](sql-database-elastic-pool-create-csharp.md)
### 相應放大
#### 學習
##### [相應放大](sql-database-elastic-scale-introduction.md)
##### [彈性擴縮](sql-database-elastic-scale-get-started.md)
##### [建置可調整的雲端資料庫](sql-database-elastic-database-client-library.md)
##### [跨資料庫作業](sql-database-elastic-jobs-overview.md)
##### [彈性工具常見問題集](sql-database-elastic-scale-faq.md)
##### [彈性資料庫工具字彙](sql-database-elastic-scale-glossary.md)
##### [在向外延展的雲端資料庫之間移動資料](sql-database-elastic-scale-overview-split-and-merge.md)
#### 建議事項
##### [彈性工作](sql-database-elastic-jobs-getting-started.md)
##### [跨資料庫報告](sql-database-elastic-query-getting-started.md)
##### [跨資料庫查詢](sql-database-elastic-query-getting-started-vertical.md)
##### [將彈性工作解除安裝](sql-database-elastic-jobs-uninstall.md)
## 移轉和移動資料
### 學習
#### [移轉資料庫](sql-database-cloud-migrate.md)
#### [異動複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [資料同步](sql-database-get-started-sql-data-sync.md)
#### [複製 SQL Database](sql-database-copy.md)
### 建議事項
#### 判斷資料庫相容性
##### [SQL 套件公用程式](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### 修正資料庫相容性問題
##### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [SQL Azure 移轉精靈](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [SQL Server Management Studio 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### 複製資料庫
##### [使用 Azure 入口網站複製](sql-database-copy-portal.md)
##### [使用 PowerShell 複製](sql-database-copy-powershell.md)
##### [用 T-SQL 複製](sql-database-copy-transact-sql.md)
#### 將資料庫匯出至 BACPAC 檔案
##### [Azure 入口網站](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [SQL 套件公用程式](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### 從 BACPAC 檔案匯入資料庫
##### [Azure 入口網站](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [SQL 套件公用程式](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [使用 BCP 從 CSV 檔案載入](sql-database-load-from-csv-with-bcp.md)
## 驗證並授與存取權
### 學習
#### [概觀](sql-database-security.md)
#### [安全性方針](sql-database-security-guidelines.md)
#### [管理登入](sql-database-manage-logins.md)
### 建議事項
### [教學課程：SQL 驗證和存取](sql-database-get-started-security.md)
## 防全和保護資料
### 學習
#### 稽核
##### [稽核](sql-database-auditing-get-started.md)
##### [下層用戶端支援與稽核適用的 IP 端點變更](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [威脅偵測](sql-database-threat-detection-get-started.md)
#### 加密資料
##### [Azure 金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md)
##### [Windows 憑證存放區](sql-database-always-encrypted.md)
##### [透明資料加密](https://msdn.microsoft.com/library/azure/dn948096)
##### [資料行加密](https://msdn.microsoft.com/library/azure/ms179331)
#### 將資料加上遮罩
##### 動態資料遮罩
###### [Azure 入口網站](sql-database-dynamic-data-masking-get-started.md)
### 建議事項
#### [使用 Azure 入口網站進行動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)
## 業務持續性
### 學習
#### [概觀](sql-database-business-continuity.md)
#### [資料庫備份](sql-database-automated-backups.md)
#### [長期保留](sql-database-long-term-retention.md) 
#### [使用備份進行資料庫復原](sql-database-recovery-using-backups.md)
#### [災害復原的驗證需求](sql-database-geo-replication-security-config.md)
#### [商務持續性設計案例](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [災害復原策略與彈性集區](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [輪流升級](sql-database-manage-application-rolling-upgrade.md)
### 建議事項
#### 還原已刪除的資料庫
##### [Azure 入口網站](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### 還原時間點
##### [Azure 入口網站](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### 異地還原資料庫
##### [Azure 入口網站](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [單一資料表](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [從資料中心中斷情況復原](sql-database-disaster-recovery.md)
#### [執行災害復原鑽研](sql-database-disaster-recovery-drills.md)
### 複寫資料庫
#### [主動式異地複寫概觀](sql-database-geo-replication-overview.md)
#### 設定主動式異地複寫
##### [Azure 入口網站](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### 使用作用中異地複寫來進行容錯移轉
##### [Azure 入口網站](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## 應用程式開發
### 學習
#### [資料庫應用程式開發概觀](sql-database-develop-overview.md)
#### [連線程式庫](sql-database-libraries.md)
#### [多租用戶 SaaS 應用程式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [使用 ADO.NET 4.5 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [取得驗證應用程式所需的值](sql-database-client-id-keys.md)
### 建議事項
#### 與應用程式連線
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C 和 C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [透過 Visual Studio 連線](sql-database-connect-query.md)
#### [建置用戶端應用程式](https://www.microsoft.com/sql-server/developer-get-started)
#### [處理錯誤訊息](sql-database-develop-error-messages.md)
### 軟體即服務的客戶實作
#### [Daxko/CSI 軟體](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## 資料庫開發
### 學習
#### 暫存資料表
##### [暫存資料表](sql-database-temporal-tables.md)
##### [保留原則](sql-database-temporal-tables-retention-policy.md)
#### [JSON 資料](sql-database-json-features.md)
#### [記憶體內部](sql-database-in-memory.md)
### 建議事項
#### [SQL Server 開發](https://msdn.microsoft.com/library/ms179422.aspx)
## 監視和微調
### 學習
#### [單一資料庫](sql-database-single-database-monitor.md)
#### [SQL Database 建議程式概觀](sql-database-advisor.md)
#### [單一資料庫指導](sql-database-performance-guidance.md)
#### [Azure 入口網站中的工作負載深入解析](sql-database-performance.md)
#### [使用批次處理](sql-database-use-batching-to-improve-performance.md)
## SQL Database V11
### [Web 和 Business 版本終止](sql-database-web-business-sunset-faq.md)
### [服務層級建議程式](sql-database-service-tier-advisor.md)
### [彈性集區評估工具](sql-database-elastic-pool-database-assessment-powershell.md)
### [升級至 V12](sql-database-v12-plan-prepare-upgrade.md)
#### [使用 Azure 入口網站升級](sql-database-upgrade-server-portal.md)
#### [使用 Powershell 升級](sql-database-upgrade-server-powershell.md)
#### [定價層建議](sql-database-service-tier-advisor.md)
# 作法
## 建立和管理
### 伺服器和資料庫
#### [單一資料庫](sql-database-manage-portal.md)
#### [Azure 入口網站](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### 彈性資料庫集區
#### [使用 Azure 入口網站建立](sql-database-elastic-pool-create-portal.md)
#### [使用 PowerShell 建立](sql-database-elastic-pool-create-powershell.md)
#### [用 C# 建立](sql-database-elastic-pool-create-csharp.md)
#### [使用 Azure 入口網站管理](sql-database-elastic-pool-manage-portal.md)
#### [使用 Powershell 管理](sql-database-elastic-pool-manage-powershell.md)
#### [使用 C# 管理](sql-database-elastic-pool-manage-csharp.md)
#### [使用 T-SQL 管理](sql-database-elastic-pool-manage-tsql.md)
### 分區化資料庫
#### [使用分區對應管理員](sql-database-elastic-scale-shard-map-management.md)
#### [分割合併安全性組態](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [使用 Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [使用 Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)
#### [多租用戶資料列層級安全性](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [管理認證](sql-database-elastic-scale-manage-credentials.md)
#### [部署分割合併服務](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [新增分區](sql-database-elastic-scale-add-a-shard.md)
#### [使用 RecoveryManager 類別來修正分區對應問題](sql-database-elastic-database-recovery-manager.md)
###  驗證
#### [Azure AD 驗證](sql-database-aad-authentication.md)
#### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### 防火牆規則
#### [Azure 入口網站](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [REST API](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### 作業與自動化
#### [服務安裝](sql-database-elastic-jobs-service-installation.md)
#### [在 Azure 入口網站中建立及管理彈性工作](sql-database-elastic-jobs-create-and-manage.md)
#### [使用 PowerShell 建立及管理彈性作業](sql-database-elastic-jobs-powershell.md)
#### [升級用戶端程式庫](sql-database-elastic-scale-upgrade-client-library.md)
#### [使用 Azure 自動化服務管理 Azure SQL 資料庫](sql-database-manage-automation.md)
### [防火牆](sql-database-firewall-configure.md)
## 加密資料
### [一律加密的概觀](sql-database-always-encrypted.md)
### [透明資料加密](https://msdn.microsoft.com/library/azure/dn948096)
### [資料行加密](https://msdn.microsoft.com/library/azure/ms179331)
## 移轉
### 判斷相容性
#### [SQL 套件公用程式](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### 修正相容性問題
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SQL Azure 移轉精靈](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [使用 SQL Server Management Studio 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [使用異動複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [移轉現有的相應放大資料庫以便相應放大](sql-database-elastic-convert-to-use-elastic-tools.md)
## 監視與微調
### [查詢效能深入解析](sql-database-query-performance.md)
### [SQL Database 建議程式](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [相容性層級](sql-database-compatibility-level-query-performance-130.md)
### [分區對應管理員的效能計數器](sql-database-elastic-database-perf-counters.md)
### [效能微調秘訣](sql-database-troubleshoot-performance.md)
### 變更服務層和效能層級
#### [使用 Azure 入口網站](sql-database-scale-up.md)
#### [使用 PowerShell](sql-database-scale-up-powershell.md)
### [建立警示](sql-database-insights-alerts-portal.md)
### 記憶體內部 OLTP
#### [採用記憶體內部 OLTP](sql-database-in-memory-oltp-migration.md)
#### [監視記憶體內部 OLTP 儲存體](sql-database-in-memory-oltp-monitoring.md)
### 查詢存放區
#### [使用查詢存放區來監視效能](https://msdn.microsoft.com/library/dn817826.aspx)
#### [查詢存放區使用案例](https://msdn.microsoft.com/library/mt614796.aspx)
#### [操作查詢存放區](sql-database-operate-query-store.md)
### 擴充事件
#### [擴充事件](sql-database-xevent-db-diff-from-svr.md)
#### [事件檔案目標代碼](sql-database-xevent-code-event-file.md)
#### [信號緩衝區目標代碼](sql-database-xevent-code-ring-buffer.md)
## 移動資料
### [複製 SQL Database](sql-database-copy.md)
#### [Azure 入口網站](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### 將資料庫匯出至 BACPAC 檔案
#### [Azure 入口網站](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [SQL 套件公用程式](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### 從 BACPAC 檔案匯入資料庫
#### [Azure 入口網站](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [SQL 套件公用程式](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [使用 BCP 從 CSV 檔案載入](sql-database-load-from-csv-with-bcp.md)
### [在向外延展的雲端資料庫之間移動資料](sql-database-elastic-scale-overview-split-and-merge.md)
## 查詢
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [多分區查詢](sql-database-elastic-scale-multishard-querying.md)
### 跨資料庫查詢
#### [概觀](sql-database-elastic-query-overview.md)
#### [以不同結構描述進行跨資料庫查詢](sql-database-elastic-query-vertical-partitioning.md)
#### [跨資料庫報告](sql-database-elastic-query-horizontal-partitioning.md)
#### [跨雲端資料庫的分散式交易](sql-database-elastic-transactions-overview.md)
## 還原
### 還原已刪除的資料庫
#### [Azure 入口網站](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### 還原時間點
#### [Azure 入口網站](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### 異地還原
#### [Azure 入口網站](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [單一資料表](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [從資料中心中斷情況復原](sql-database-disaster-recovery.md)
### [執行災害復原鑽研](sql-database-disaster-recovery-drills.md)
## 複寫
### [主動式異地複寫概觀](sql-database-geo-replication-overview.md)
### 設定
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### 容錯移轉
#### [Azure 入口網站](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## 疑難排解
### [連接問題](sql-database-troubleshoot-common-connection-issues.md)
### [暫時性連接錯誤](sql-database-troubleshoot-connection.md)
### [診斷和防範](sql-database-connectivity-issues.md)
### [權限](sql-database-troubleshoot-permissions.md)
### [移動資料庫](sql-database-troubleshoot-moving-data.md)


# 參考
## [PowerShell](/powershell/azureps-cmdlets-docs/)
## [PowerShell 傳統](/powershell/servicemanagement/)
## [Java](/java/api/)
## [.NET](/dotnet/api/)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [Azure SQL Database Cmdlet](/powershell/resourcemanager/AzureRM.Sql/v1.0.12/AzureRM.Sql)
## [SQL Server Cmdlets](https://msdn.microsoft.com/library/mt740629.aspx)
## [REST](/rest/api/sql/)

## SQL Database 管理程式庫
### [SQL Database 管理程式庫參考](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [取得 SQL Database 管理程式庫套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server 驅動程式](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# 資源
## [價格](https://azure.microsoft.com/pricing/details/sql-database/)
## [MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [服務更新](https://azure.microsoft.com/updates/?service=sql-database)
## [SQL Server 工具](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [sqlcmd](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)




<!--HONumber=Nov16_HO4-->


