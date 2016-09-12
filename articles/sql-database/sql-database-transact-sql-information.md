<properties
   pageTitle="Azure SQL Database T-SQL 中不支援的項目 | Microsoft Azure"
   description="在 Azure SQL Database 中未完整支援 Transact-SQL 陳述式"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# Azure SQL Database Transact-SQL 差異


Microsoft SQL Server 和 Azure SQL Database 都支援應用程式相依的大部分 Transact-SQL 功能。應用程式之支援功能的部分清單如下：

- 資料類型。
- 運算子。
- 字串、算術，邏輯和指標函式。

不過，Azure SQL Database 的設計目的是將功能與任何在 **master** 資料庫上的相依性隔離。因此，SQL Database 並不適合，也不支援許多伺服器層級的活動。SQL Database 通常也不支援 SQL Server 中已過時的功能。

> [AZURE.NOTE]
本主題討論升級至目前的版本 SQL Database V12 時可用於 SQL Database 的功能。如需 V12 的詳細資訊，請參閱 [SQL Database V12 新功能](sql-database-v12-whats-new.md)。

下列各節列出部分支援的功能，以及完全不支援的功能。


## 在 SQL Database V12 中部分支援的功能

在對應的 SQL Server 2016 Transact-SQL 陳述式方面，SQL Database V12 支援部分而非全部的引數。例如，可以使用 CREATE PROCEDURE 陳述式，但無法使用 CREATE PROCEDURE 的所有選項。請參閱連結的語法主題，以了解每個陳述式支援區域的詳細資訊。

- 資料庫：[CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMV 通常可用於已公開推出的功能。
- 函式：[CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx)
- 登入：[CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- 預存程序：[CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- 資料表：[CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- 類型 (自訂)：[CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- 使用者：[CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- 檢視：[CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## 在 SQL Database 中不支援的功能

- 系統物件的定序
- 相關連接：端點陳述式、ORIGINAL\_DB\_NAME。SQL Database 不支援 Windows 驗證，但支援類似的 Azure Active Directory 驗證。某些驗證類型需要最新的 SSMS 版本。如需詳細資訊，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲](sql-database-aad-authentication.md)。
- 使用三個或四個組件名稱跨資料庫查詢。(使用[彈性資料庫查詢](sql-database-elastic-query-overview.md)支援跨資料庫唯讀查詢。)
- 跨資料庫擁有權鏈結、TRUSTWORTHY 設定
- 資料收集器
- 資料庫圖表
- Database Mail
- DATABASEPROPERTY (改用 DATABASEPROPERTYEX)
- EXECUTE AS 登入
- 加密：可延伸金鑰管理
- 事件服務：事件、事件通知、查詢通知
- 與資料庫檔案位置、大小和資料庫檔案 (由 Microsoft Azure 自動管理) 相關的功能。
- 與高可用性相關的功能，高可用性是透過您的 Microsoft Azure 帳戶管理：備份、還原、AlwaysOn、資料庫鏡像、記錄傳送、復原模式。如需詳細資訊，請參閱「Azure SQL Database 備份和還原」。
- 依賴在 SQL Database 上執行之記錄讀取器的功能：複寫、異動資料擷取。
- 依賴 SQL Server Agent 或 MSDB 資料庫的功能：工作、警示、運算子、以原則為基礎的管理、Database Mail、中央管理伺服器。
- FILESTREAM
- 函式：fn\_get\_sql、fn\_virtualfilestats、fn\_virtualservernodes
- 全域暫存資料表
- 硬體相關的伺服器設定：記憶體、背景工作執行緒、CPU 親和性、追蹤旗標等。改用服務層級。
- HAS\_DBACCESS
- KILL STATS JOB
- 連結的伺服器、OPENQUERY、OPENROWSET、OPENDATASOURCE、BULK INSERT 及 4 個組件名稱
- 主要/目標伺服器
- .NET Framework [SQL Server CLR 整合](http://msdn.microsoft.com/library/ms254963.aspx)
- 資源管理員
- 語意搜尋
- 伺服器認證。改用資料庫範圍認證。
- 伺服器層級項目：伺服器角色、IS\_SRVROLEMEMBER、sys.login\_token。雖然某些伺服器層級權限已由資料庫層級權限取代，但是無法使用伺服器層級權限。雖然某些伺服器層級 DMV 已由資料庫層級 DMV 取代，但是無法使用某些伺服器層級 DMV。
- 無伺服器 Express：localdb、使用者執行個體
- Service broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- sp\_configure 選項和 RECONFIGURE。有些選項可透過[變更資料庫範圍組態](https://msdn.microsoft.com/library/mt629158.aspx)來使用。
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- SQL Server 稽核。改用 SQL Database 稽核。
- SQL Server Profiler
- SQL Server 追蹤
- 追蹤旗標。某些追蹤旗標項目已移至相容性模式。
- Transact-SQL 偵錯
- 觸發程序：伺服器範圍或登入觸發程序
- USE 陳述式：若要將資料庫內容變更為不同的資料庫，您必須建立與新資料庫的連接。


## 完整 Transact-SQL 參考

如需 Transact-SQL 文法、使用方式和範例的詳細資訊，請參閱《SQL Server 線上叢書》中的＜[Transact-SQL 參考 (資料庫引擎)](https://msdn.microsoft.com/library/bb510741.aspx)＞。

### 關於「適用於」標記

Transact-SQL 參考包括從 SQL Server 版本 2008 到目前版本的相關主題。主題標題下方是圖示列，列出四個 SQL Server 平台並指出適用性。例如，可用性群組是在 SQL Server 2012 中導入。[建立可用性群組](https://msdn.microsoft.com/library/ff878399.aspx)主題指出陳述式會套用至 **SQL Server (從 2012 年開始)。陳述式不適用於 SQL Server 2008、SQL Server 2008 R2、Azure SQL Database、Azure SQL 資料倉儲或平行資料倉儲。

在某些情況下，一般主題的主旨可用於產品中，但產品之間會有些微的差異。依適當情況會在主題的中間點指出差異。

<!---HONumber=AcomDC_0831_2016-->