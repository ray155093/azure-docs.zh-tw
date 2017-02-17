# 概觀
## [什麼是 SQL Database？](sql-database-technical-overview.md)
### [服務層](sql-database-service-tiers.md)
### [DTU 和 eDTU](sql-database-what-is-a-dtu.md)
### [DTU 基準測試概觀](sql-database-benchmark-overview.md)
### [資源限制](sql-database-resource-limits.md)
### [特性](sql-database-features.md)
### [SQL Database 常見問題集](sql-database-faq.md)
## 比較
### [比較 SQL Database 和 VM 上的 SQL](sql-database-paas-vs-sql-server-iaas.md)
### [T-SQL 差異](sql-database-transact-sql-information.md)
### [SQL 與 NoSQL 的比較](../documentdb/documentdb-nosql-vs-sql.md)
## [SQL Database 工具](sql-database-manage-overview.md)
## [SQL Database 教學課程](sql-database-explore-tutorials.md)
## [解決方案快速入門](sql-database-solution-quick-starts.md)
## 安全性
### [安全性概觀](sql-database-security-overview.md)
### [Azure SQL Database 適用的 Azure 資訊安全中心](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL 資訊安全中心](https://msdn.microsoft.com/library/azure/bb510589)
# 開始使用
## 資料庫和伺服器
### 學習
#### [伺服器](sql-database-server-overview.md)
#### [單一資料庫](sql-database-overview.md)
#### [多個資料庫](sql-database-elastic-scale-introduction.md)
##### 對應租用戶
###### [彈性用戶端程式庫](sql-database-elastic-database-client-library.md)
###### [分區對應管理員](sql-database-elastic-scale-shard-map-management.md)
###### [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)
###### [管理認證](sql-database-elastic-scale-manage-credentials.md)
###### [多分區查詢](sql-database-elastic-scale-multishard-querying.md)
##### 彈性集區 (資源集區)
###### [什麼是彈性集區？](sql-database-elastic-pool.md)
###### [何時該使用彈性集區](sql-database-elastic-pool-guidance.md)
###### [性集區價格](sql-database-elastic-pool-price.md)
##### 分區化資料庫
###### [彈性工具詞彙](sql-database-elastic-scale-glossary.md)
###### [在分區之間移動資料](sql-database-elastic-scale-overview-split-and-merge.md)
###### [彈性工具常見問題集](sql-database-elastic-scale-faq.md)
##### 彈性查詢 (跨資料庫查詢)
###### [什麼是彈性查詢？](sql-database-elastic-query-overview.md)
##### 彈性交易 (分散式交易)
###### [跨雲端資料庫的交易](sql-database-elastic-transactions-overview.md)
##### 彈性作業 (跨資料庫作業)
###### [什麼是彈性作業？](sql-database-elastic-jobs-overview.md)
#### [使用 Azure RemoteApp 連接到 SQL Database](sql-database-ssms-remoteapp.md)
#### [使用 Azure 自動化服務管理 SQL Database](sql-database-manage-automation.md)
### 建議事項
#### [使用 Azure 入口網站建立單一資料庫](sql-database-get-started.md)
#### [使用 PowerShell 建立單一資料庫](sql-database-get-started-powershell.md)
#### [使用 C# 建立單一資料庫](sql-database-get-started-csharp.md)
#### [建立分區化應用程式](sql-database-elastic-scale-get-started.md)
#### [在分區之間移動資料](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [開始使用彈性作業](sql-database-elastic-jobs-getting-started.md)
#### [開始使用彈性查詢](sql-database-elastic-query-getting-started-vertical.md)
#### [使用彈性查詢建立報表](sql-database-elastic-query-getting-started.md)
#### [對不同結構描述的資料庫執行查詢](sql-database-elastic-query-vertical-partitioning.md)
#### [跨相應放大的資料庫報告](sql-database-elastic-query-horizontal-partitioning.md)
## 移轉和移動資料
### 學習
#### [移轉資料庫](sql-database-cloud-migrate.md)
#### [異動複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [資料同步](sql-database-get-started-sql-data-sync.md)
#### [複製 SQL Database](sql-database-copy.md)
## 防火牆規則、驗證和授權
### 學習
#### [存取控制](sql-database-control-access.md)
#### [防火牆](sql-database-firewall-configure.md)
#### [管理登入](sql-database-manage-logins.md)
### 建議事項
#### [SQL 驗證和授權](sql-database-control-access-sql-authentication-get-started.md)
#### [Azure AD 驗證和授權](sql-database-control-access-aad-authentication-get-started.md)
## 防全和保護資料
### 學習
#### 稽核
##### [稽核](sql-database-auditing-get-started.md)
##### [下層用戶端支援與稽核適用的 IP 端點變更](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [威脅偵測](sql-database-threat-detection-get-started.md)
#### 加密資料
##### [Azure 金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md)
##### [透明資料加密](https://msdn.microsoft.com/library/azure/dn948096)
##### [資料行加密](https://msdn.microsoft.com/library/azure/ms179331)
#### 將資料加上遮罩
##### 動態資料遮罩
###### [Azure 入口網站](sql-database-dynamic-data-masking-get-started.md)
### 建議事項
#### [使用 Azure 入口網站進行動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)
##### [一律使用 Windows 憑證存放區加密](sql-database-always-encrypted.md)
## 業務持續性
### 學習
#### [概觀](sql-database-business-continuity.md)
#### [資料庫備份](sql-database-automated-backups.md)
#### [長期保留](sql-database-long-term-retention.md)
#### [使用備份進行資料庫復原](sql-database-recovery-using-backups.md)
#### [復原單一資料表](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [從資料中心中斷情況復原](sql-database-disaster-recovery.md)
#### [災害復原的驗證需求](sql-database-geo-replication-security-config.md)
#### [商務持續性設計案例](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [災害復原策略與彈性集區](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [輪流升級](sql-database-manage-application-rolling-upgrade.md)
#### [執行災害復原鑽研](sql-database-disaster-recovery-drills.md)
#### [主動式異地複寫概觀](sql-database-geo-replication-overview.md)
### 建議事項
#### [Azure 入口網站：備份與還原](sql-database-get-started-backup-recovery.md)
#### [PowerShell：庫備份與還原](sql-database-get-started-backup-recovery-powershell.md)
## 應用程式開發
### 學習
#### [資料庫應用程式開發概觀](sql-database-develop-overview.md)
#### [連線程式庫](sql-database-libraries.md)
#### [多租用戶 SaaS 應用程式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [使用資料列層級安全性調整多租用戶 SaaS 應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md)
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
#### [使用 Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [搭配使用用戶端程式庫與 Dapper](sql-database-elastic-scale-working-with-dapper.md)
### 客戶實作
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
#### [記憶體內部最佳化](sql-database-in-memory.md)
### 建議事項
#### [SQL Server 開發](https://msdn.microsoft.com/library/ms179422.aspx)
#### [採用記憶體內部 OLTP](sql-database-in-memory-oltp-migration.md)
## 監視和微調
### 學習
#### [單一資料庫](sql-database-single-database-monitor.md)
#### [SQL Database 建議程式概觀](sql-database-advisor.md)
#### [單一資料庫指導](sql-database-performance-guidance.md)
#### [效能深入資訊：Azure 入口網站](sql-database-performance.md)
#### [使用批次處理](sql-database-use-batching-to-improve-performance.md)
#### [擴充事件](sql-database-xevent-db-diff-from-svr.md)
## SQL Database V11
### [Web 和 Business 版本終止](sql-database-web-business-sunset-faq.md)
### [服務層級建議程式](sql-database-service-tier-advisor.md)
### [彈性集區評估工具](sql-database-elastic-pool-database-assessment-powershell.md)
### [升級至 V12](sql-database-v12-plan-prepare-upgrade.md)
#### [使用 Azure 入口網站升級](sql-database-upgrade-server-portal.md)
#### [使用 Powershell 升級](sql-database-upgrade-server-powershell.md)
# 作法
## 建立和管理
### [使用 Azure 入口網站管理 SQL Database](sql-database-manage-portal.md)
### [使用 PowerShell 管理 SQL Database](sql-database-manage-powershell.md)
### [使用 SSMS 管理 SQL Database](sql-database-manage-azure-ssms.md)
### 伺服器
#### [建立伺服器](sql-database-create-servers.md)
#### [檢視或更新伺服器設定](sql-database-view-update-server-settings.md)
### 單一資料庫
#### [建立單一資料庫](sql-database-create-databases.md)
#### [檢視或更新資料庫設定](sql-database-view-update-database-settings.md)
### 防火牆規則
#### [使用 Azure 入口網站建立防火牆規則](sql-database-configure-firewall-settings.md)
#### [使用 PowerShell 建立防火牆規則](sql-database-configure-firewall-settings-powershell.md)
#### [使用 REST API 建立防火牆規則](sql-database-configure-firewall-settings-rest.md)
#### [使用 T-SQL 建立防火牆規則](sql-database-configure-firewall-settings-tsql.md)
### 多個資料庫
#### [在用戶端應用程式中升級用戶端程式庫](sql-database-elastic-scale-upgrade-client-library.md)
#### 分區化資料庫
##### [安全性組態](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [新增分區](sql-database-elastic-scale-add-a-shard.md)
##### [修正分區對應問題](sql-database-elastic-database-recovery-manager.md)
##### [將現有的相應放大資料庫移轉至分區化資料庫](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [為分區對應管理員建立效能計數器](sql-database-elastic-database-perf-counters.md)
#### 彈性工作
##### [如何安裝彈性作業服務？](sql-database-elastic-jobs-service-installation.md)
##### [使用 PowerShell 建立及管理彈性作業](sql-database-elastic-jobs-powershell.md) 
##### [使用 Azure 入口網站建立及管理彈性作業](sql-database-elastic-jobs-create-and-manage.md)
##### [如何將彈性作業解除安裝？](sql-database-elastic-jobs-uninstall.md)
#### 彈性集區
##### [使用 Azure 入口網站建立](sql-database-elastic-pool-create-portal.md)
##### [使用 PowerShell 建立](sql-database-elastic-pool-create-powershell.md)
##### [用 C# 建立](sql-database-elastic-pool-create-csharp.md)
##### [使用 Azure 入口網站管理](sql-database-elastic-pool-manage-portal.md)
##### [使用 Powershell 管理](sql-database-elastic-pool-manage-powershell.md)
##### [使用 C# 管理](sql-database-elastic-pool-manage-csharp.md)
##### [使用 T-SQL 管理](sql-database-elastic-pool-manage-tsql.md)
##  驗證和授權
### [Azure AD 驗證](sql-database-aad-authentication.md)
### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
## 加密資料
### [透明資料加密](https://msdn.microsoft.com/library/azure/dn948096)
### [資料行加密](https://msdn.microsoft.com/library/azure/ms179331)
## 移轉資料庫
### 判斷相容性
#### [使用 SQL 套件公用程式判斷相容性](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [使用 SSMS 判斷相容性](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### 修正相容性問題
#### [使用 SSDT 修正相容性問題](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [使用 SSMS 修正相容性問題](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [使用 SMW 修正相容性問題](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [使用 SSMS 移轉精靈進行移轉](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## 監視與微調
### [查詢效能深入解析](sql-database-query-performance.md)
### [SQL Database 建議程式](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [相容性層級](sql-database-compatibility-level-query-performance-130.md)
### [效能微調秘訣](sql-database-troubleshoot-performance.md)
### 變更服務層和效能層級
#### [使用 Azure 入口網站變更服務層](sql-database-scale-up.md)
#### [使用 PowerShell 變更服務層](sql-database-scale-up-powershell.md)
### [建立警示](sql-database-insights-alerts-portal.md)
#### [監視記憶體內部 OLTP 儲存體](sql-database-in-memory-oltp-monitoring.md)
### 查詢存放區
#### [使用查詢存放區來監視效能](https://msdn.microsoft.com/library/dn817826.aspx)
#### [查詢存放區使用案例](https://msdn.microsoft.com/library/mt614796.aspx)
#### [操作查詢存放區](sql-database-operate-query-store.md)
### 擴充事件
#### [事件檔案目標代碼](sql-database-xevent-code-event-file.md)
#### [信號緩衝區目標代碼](sql-database-xevent-code-ring-buffer.md)
## 移動資料
### 複製 SQL Database
#### [使用 Azure 入口網站複製](sql-database-copy-portal.md)
#### [使用 PowerShell 複製](sql-database-copy-powershell.md)
#### [用 T-SQL 複製](sql-database-copy-transact-sql.md)
### 將資料庫匯出至 BACPAC 檔案
#### [使用 Azure 入口網站進行匯出](sql-database-export.md)
#### [使用 SSMS 進行匯出](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [使用 SQL 套件公用程式進行匯出](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [使用 Powershell 進行匯出](sql-database-export-powershell.md)
### 從 BACPAC 檔案匯入資料庫
#### [使用 Azure 入口網站進行匯入](sql-database-import.md)
#### [使用 Powershell 進行匯入](sql-database-import-powershell.md)
#### [使用 SSMS 進行匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [使用 SQL 套件公用程式進行匯入](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [使用 BCP 從 CSV 檔案載入](sql-database-load-from-csv-with-bcp.md)
## 查詢
### [使用 SSMS 進行查詢](sql-database-connect-query-ssms.md)
## 備份與還原
### 長期備份保留期
#### [設定長期備份保留期](sql-database-configure-long-term-retention.md)
#### [檢視復原服務保存庫中的備份](sql-database-view-backups-in-vault.md)
#### [從長期備份保留期還原](sql-database-restore-from-long-term-retention.md)
#### [除長期備份保留期](sql-database-long-term-retention-delete.md)
### 還原已刪除的資料庫
#### [使用 Azure 入口網站還原已刪除的資料庫](sql-database-restore-deleted-database-portal.md)
#### [使用 PowerShell 還原已刪除的資料庫](sql-database-restore-deleted-database-powershell.md)
### 還原時間點
#### [還原至某個時間點](sql-database-point-in-time-restore.md)
#### [檢視最早的還原點](sql-database-view-oldest-restore-point.md)
### [從異地備援備份還原](sql-database-geo-restore.md)
## 主動式異地複寫
### [使用 Azure 入口網站進行設定](sql-database-geo-replication-portal.md)
### [使用 PowerShell 進行設定](sql-database-geo-replication-powershell.md)
### [使用 T-SQL 進行設定](sql-database-geo-replication-transact-sql.md)
### [使用 Azure 入口網站進行容錯移轉](sql-database-geo-replication-failover-portal.md)
### [使用 PowerShell 進行容錯移轉](sql-database-geo-replication-failover-powershell.md)
### [使用 T-SQL 進行容錯移轉](sql-database-geo-replication-failover-transact-sql.md)
## 疑難排解
### [連接問題](sql-database-troubleshoot-common-connection-issues.md)
### [暫時性連接錯誤](sql-database-troubleshoot-connection.md)
### [診斷和防範](sql-database-connectivity-issues.md)
### [權限](sql-database-troubleshoot-permissions.md)
### [移動資料庫](sql-database-troubleshoot-moving-data.md)
# 參考
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (彈性資料庫)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# 相關參考
## SQL Database 管理程式庫
### [取得 SQL Database 管理程式庫套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server 驅動程式](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

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
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)


<!--HONumber=Feb17_HO1-->


