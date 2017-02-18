---
title: "Azure SQL Database T-SQL 中不支援的項目 | Microsoft Docs"
description: "在 Azure SQL Database 中未完整支援  Transact-SQL 陳述式"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 3f9077733725174f1eed61d37d544e4f36822f6e
ms.openlocfilehash: d935571ccd18bc15baa000fb8c07fed11b66ba6c


---
# <a name="azure-sql-database-transact-sql-differences"></a>Azure SQL Database Transact-SQL 差異   
Microsoft SQL Server 和 Azure SQL Database 都支援應用程式相依的大部分 Transact-SQL 功能。 例如，資料類型、運算子、字串、算術、邏輯和資料指標函式等核心 SQL 元件皆可從 SQL Server 運作無差異。

## <a name="why-some-transact-sql-is-not-supported"></a>為什麼不支援某些 Transact-SQL
Azure SQL Database 的設計目的是將功能與在 master 資料庫和作業系統上的相依性隔離。 因此，SQL Database 並不適合許多伺服器層級的活動。 Transact-SQL 陳述式通常無法使用，如果它們是設定伺服器層級選項、作業系統元件，或指定檔案系統設定。 當使用者資料庫以外的功能為必要時，通常會從 SQL Database 或從另一個 Azure 功能或服務以其他方式提供適合的替代方案。 

例如，永遠開啟會取代作用中主動式異地複寫。 基於這個理由，SQL Database 不支援與可用性群組相關的任何 Transact-SQL 陳述式，且不支援與永遠開啟相關的動態管理檢視。  

如需 SQL Database 支援和不支援的功能清單，請參閱 [Azure SQL Database 的考量、指導方針和功能](sql-database-features.md)。

SQL Server 中已被取代的語法在 SQL Database 中通常不受支援。

## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>在 SQL Database 中部分支援的 Transact-SQL 語法
在對應的 SQL Server 2016 Transact-SQL 陳述式方面，SQL Database 支援部分而非全部的引數。 例如，可以使用 `CREATE PROCEDURE` 陳述式，但無法使用 `CREATE PROCEDURE` 的所有選項。 在此描述完整的語法會很令人困惑且多餘。 請參閱連結的語法主題，以了解每個陳述式支援區域的詳細資訊。

- 資料庫：[CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER DATABASE](https://msdn.microsoft.com/library/ms174269.aspx)   
- 函式：[CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)   
- 登入：[CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)   
- 預存程序：[CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)   
- 資料表︰[CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)   
- 類型 (自訂)： [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)   
- 使用者：[CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)   
- 檢視：[CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>在 SQL Database 中不支援的 Transact-SQL 語法   
除了與 [Azure SQL Database 考量、指導方針和功能](sql-database-features.md)中所述的不支援功能相關之 Transact-SQL 陳述式之外，不支援下列陳述式和陳述式群組。
- 系統物件的定序
- 相關連接：端點陳述式、`ORIGINAL_DB_NAME`。 SQL Database 不支援 Windows 驗證，但支援類似的 Azure Active Directory 驗證。 某些驗證類型需要最新的 SSMS 版本。 如需詳細資訊，請參閱 [使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲](sql-database-aad-authentication.md)。
- 使用三個或四個組件名稱跨資料庫查詢。 (使用[彈性資料庫查詢](sql-database-elastic-query-overview.md)支援跨資料庫唯讀查詢。)
- 跨資料庫擁有權鏈結，`TRUSTWORTHY` 設定
- `DATABASEPROPERTY` 請改用 `DATABASEPROPERTYEX`。
- `EXECUTE AS LOGIN` 請改用 'EXECUTE AS USER'。
- 除了可延伸金鑰管理之外還支援加密
- 事件服務：事件、事件通知、查詢通知
- 與資料庫檔案位置、大小和資料庫檔案 (由 Microsoft Azure 自動管理) 相關的語法。
- 與透過 Microsoft Azure 帳戶管理的高可用性相關的語法。 這包括備份、還原、永遠開啟、資料庫鏡像、記錄傳送、修復模式的語法。
- 依賴在 SQL Database 上不適用之記錄讀取器的語法：複寫、異動資料擷取。 SQL Database 可以是推送複寫文章的訂閱者。
- 依賴 SQL Server Agent 或 MSDB 資料庫的語法︰警示、運算子、中央管理伺服器。 改用指令碼，例如 Azure PowerShell。
- 函式：`fn_get_sql`、`fn_virtualfilestats`、`fn_virtualservernodes`
- 全域暫存資料表
- 與硬體相關伺服器設定相關的語法：記憶體、背景工作執行緒、CPU 親和性、追蹤旗標等。改用服務層級。
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`、`OPENROWSET`、`OPENDATASOURCE`、`BULK INSERT` 和四部分名稱
- .NET Framework [SQL Server CLR 整合](http://msdn.microsoft.com/library/ms254963.aspx)
- 語意搜尋
- 伺服器認證。 改用資料庫範圍認證。
- 伺服器層級項目︰伺服器角色、`IS_SRVROLEMEMBER`、`sys.login_token`。 雖然某些伺服器層級權限已由資料庫層級權限取代，但是無法使用伺服器層級權限的 `GRANT`、`REVOKE` 和 `DENY`。 一些有用的伺服器層級 DMV 有相同的資料庫層級 DMV。
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` 選項和 `RECONFIGURE`。 有些選項可透過 [變更資料庫範圍組態](https://msdn.microsoft.com/library/mt629158.aspx)來使用。
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server 稽核。 改用 SQL Database 稽核。
- SQL Server 追蹤
- 追蹤旗標。 某些追蹤旗標項目已移至相容性模式。
- Transact-SQL 偵錯
- 觸發程序：伺服器範圍或登入觸發程序
- `USE` 陳述式：若要將資料庫內容變更為不同的資料庫，您必須建立與新資料庫的連接。

## <a name="full-transact-sql-reference"></a>完整 Transact-SQL 參考
如需 Transact-SQL 文法、使用方式和範例的詳細資訊，請參閱《SQL Server 線上叢書》中的＜ [Transact-SQL 參考 (資料庫引擎)](https://msdn.microsoft.com/library/bb510741.aspx) ＞。 

### <a name="about-the-applies-to-tags"></a>關於「適用於」標記
Transact-SQL 參考包括從 SQL Server 版本 2008 到目前版本的相關主題。 主題標題下方是圖示列，列出四個 SQL Server 平台並指出適用性。 例如，可用性群組是在 SQL Server 2012 中導入。 [建立可用性群組](https://msdn.microsoft.com/library/ff878399.aspx) 主題指出陳述式會套用至 **SQL Server (從 2012 年開始)**。 陳述式不適用於 SQL Server 2008、SQL Server 2008 R2、Azure SQL Database、Azure SQL 資料倉儲或平行資料倉儲。

在某些情況下，一般主題的主旨可用於產品中，但產品之間會有些微的差異。 依適當情況會在主題的中間點指出差異。



<!--HONumber=Nov16_HO5-->


