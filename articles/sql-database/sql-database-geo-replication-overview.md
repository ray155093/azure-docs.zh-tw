<properties
	pageTitle="Azure SQL Database 的主動式異地複寫"
	description="主動式異地複寫可讓您在任何 Azure 資料中心中設定資料庫的 4 個複本。"
	services="sql-database"
	documentationCenter="na"
	authors="stevestein"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
   ms.workload="NA"
	ms.date="08/29/2016"
	ms.author="sstein" />

# 概觀︰SQL Database 主動式異地複寫

主動式異地複寫可讓您在相同或不同資料中心位置 (區域) 中設定最多 4 個可讀取的次要資料庫。在資料中心中斷或在無法連線至主要資料庫的情況下，便可使用次要資料庫進行查詢和容錯移轉。

>[AZURE.NOTE] 主動式異地複寫 (可讀取次要複本) 現在可供所有服務層中的所有資料庫使用。在 2017 年 4 月，不可讀取的次要類型將淘汰，而現有不可讀取的資料庫將自動升級為可讀取的次要複本。

 您可以使用 [Azure 入口網站](sql-database-geo-replication-portal.md)、[PowerShell](sql-database-geo-replication-powershell.md)、[Transact-SQL](sql-database-geo-replication-transact-sql.md) 或 [REST API - 建立或更新資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx)來設定主動式異地複寫。

> [AZURE.SELECTOR]
- [設定：Azure 入口網站](sql-database-geo-replication-portal.md)
- [設定：PowerShell](sql-database-geo-replication-powershell.md)
- [設定︰T-SQL](sql-database-geo-replication-transact-sql.md)

若您的主要資料庫因為任何原因而失敗，或只需要離線，您可以「容錯移轉」至任何次要資料庫。容錯移轉至其中一個次要資料庫啟動時，所有其他次要複本會自動連結至新的主要複本。

您可以使用 [Azure 入口網站](sql-database-geo-replication-failover-portal.md)、[PowerShell](sql-database-geo-replication-failover-powershell.md)、[TRANSACT-SQL](sql-database-geo-replication-failover-transact-sql.md)、[REST API - 計劃的容錯移轉](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx) 或 [REST API - 非計劃的容錯移轉](https://msdn.microsoft.com/library/azure/mt582027.aspx)，容錯移轉至次要資料庫。


> [AZURE.SELECTOR]
- [容錯移轉︰Azure 入口網站](sql-database-geo-replication-failover-portal.md)
- [容錯移轉︰PowerShell](sql-database-geo-replication-failover-powershell.md)
- [容錯移轉︰T-SQL](sql-database-geo-replication-failover-transact-sql.md)

容錯移轉之後，請確認已在新的主要資料庫上設定伺服器和資料庫的驗證需求。如需詳細資訊，請參閱[災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。


主動式異地複寫功能會實作可在相同 Microsoft Azure 區域或不同區域 (異地備援) 內提供資料庫備援的機制。主動式異地複寫以非同步方式從資料庫將已認可的交易複寫至不同伺服器上最多四個資料庫複本，而且使用讀取認可快照隔離 (RCSI) 進行隔離。當設定主動式異地複寫時，會在指定的伺服器上建立次要資料庫。原始的資料庫會變成主要資料庫。主要資料庫會以非同步方式將已認可的交易複寫到每一個次要資料庫。複寫的項目僅限完整的交易。雖然次要資料可能會在任何指定時間點稍微落後主要資料庫，但是次要資料保證絕對不會含有部分交易。在[商務持續性概觀](sql-database-business-continuity.md)中可以找到特定的 RPO 資料。

主動式異地複寫的主要優點之一，是它提供了具有極短復原時間的資料庫層級災害復原解決方案。當您將次要資料庫放在不同區域中的伺服器上，就能夠為應用程式中加入最大的恢復能力。跨區域備援可讓應用程式從天然災害、災難性人為錯誤或惡意行為所造成的全部或部分資料中心永久遺失復原。下圖顯示在進階資料庫上設定的主動式異地複寫範例，其在美國中北部區域有主要資料庫，在美國中南部區域有次要資料庫。

![異地複寫關聯性](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

另一個主要優點是次要資料庫可讀取，而且可用來卸載唯讀工作負載，例如報告作業。如果您只想使用次要資料庫進行負載平衡，您可以在與主要資料庫相同的區域中建立它。不過，在相同的區域建立次要資料庫不會增加應用程式對於災難性失敗的恢復能力。

可以使用主動式異地覆寫的其他情況包括：

- **資料庫移轉**：您可以使用主動式異地覆寫，以最少的停機時間將資料庫從一部伺服器移轉到另一個線上伺服器。
- **應用程式升級**：您可以在應用程式升級期間建立額外的次要資料庫做為容錯回復複本。

若要達到真正的業務持續性，新增資料中心之間的資料庫備援只是解決方案的一部分。在災難性失敗後要端對端復原應用程式 (服務) 需要復原構成服務的所有元件和任何相依的服務。這些元件的範例包括用戶端軟體 (例如自訂 JavaScript 的瀏覽器)、web 前端、儲存體和 DNS。所有元件都必須對相同的失敗具有恢復功能，並且在應用程式的復原時間目標 (RTO) 內可供使用。因此，您需要識別所有相依服務並了解其提供的保證與功能。然後，您必須採取適當步驟以確保服務功能在它所依賴的服務容錯移轉期間都正常。如需有關設計災害復原解決方案的詳細資訊，請參閱[使用主動式異地複寫設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## 主動式異地複寫功能
主動式異地覆寫功能提供下列基本功能：

- **自動非同步複寫**︰您只能藉由加入至現有資料庫來建立次要資料庫。次要資料庫只能建立在不同的 Azure SQL Database 伺服器。一旦建立之後，次要資料庫就要填入從主要資料庫複製的資料。這個程序稱為植入。建立並植入次要資料庫之後，主要資料庫的更新會以非同步方式自動複製到次要資料庫。非同步複寫表示交易會先在主要資料庫上受到認可，才會複製到次要資料庫。

- **多個次要資料庫**：兩個或以上的次要資料庫可增加主要資料庫和應用程式的備援和保護層級。如果有多個次要資料庫存在，即使其中一個次要資料庫失敗，應用程式仍會受到保護。如果只有一個次要資料庫卻失敗了，應用程式會暴露在更高的風險中，直到建立新的次要資料庫。

- **可讀取的次要資料庫**：應用程式可以存取次要資料庫，使用用於存取主要資料庫的相同安全性主體或不同安全性主體進行唯讀作業。在快照集隔離模式中執行次要資料庫，以確保主要資料庫更新的複寫 (記錄重播) 不會被次要資料庫上執行的查詢延遲。

>[AZURE.NOTE] 如果有從主要資料庫接收的結構描述更新，則次要資料庫上的記錄重播會延遲，因為它需要次要資料庫上的結構描述鎖定。

- **彈性集區資料庫的主動式異地複寫**：您可以為任何彈性資料庫集區中的任何資料庫設定主動式異地複寫。次要資料庫可以在其他彈性資料庫集區中。對於一般資料庫，只要服務層相同，次要資料庫可以是彈性資料庫集區，反之亦然。

- **次要資料庫的可設定效能層級**︰可以使用比主要資料庫低的效能層級建立次要資料庫。主要和次要資料庫必須有相同的服務層。對於具有高資料庫寫入活動的應用程式不建議使用這個選項，因為增加的複寫延遲會提高容錯移轉後遺失重要資料的風險。此外，在容錯移轉之後應用程式的效能會受到影響，直到新的主要資料庫升級至較高的效能層級。在 Azure 入口網站上的記錄 IO 百分比圖表，提供預估次要資料庫承受複寫負載所需的最少效能層級的好方法。例如，如果您的主要資料庫是 P6 (1000 DTU) 和其記錄 IO 百分比為 50%，則次要資料庫必須至少是 P4 (500 DTU)。您也可以使用 [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) 或 [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) 資料庫檢視來擷取記錄 IO 資料。如需 SQL Database 效能層級的詳細資訊，請參閱 [SQL Database 選項和效能](sql-database-service-tiers.md)。

- **使用者控制的容錯移轉和容錯回復**：次要資料庫可以隨時透過應用程式或使用者的明確動作，切換到主要角色。在實際的中斷期間，應該使用「非計劃性」選項，這樣會立即將次要升級為主要。當失敗的主要資料庫復原，並且可再次使用時，系統會自動將復原的主要資料庫標示為次要資料庫，並讓它與新的主要資料庫保持更新。由於複寫的非同步本質，如果主要資料庫在將最新的變更複寫至次要資料庫之前失敗，則非計劃的容錯移轉期間會有少量的資料遺失。當具有多個次要資料庫的主要資料庫容錯移轉時，系統會自動重新設定複寫關聯性，並且將剩餘的次要資料庫連結至新升級的主要資料庫，而不需要任何使用者介入。解決造成容錯移轉的中斷之後，可能想要讓應用程式返回主要區域。若要這麼做，應該使用「計劃」選項叫用容錯移轉命令。

- **保持認證和防火牆規則同步**︰我們建議針對異地複寫資料庫使用[資料庫防火牆規則](sql-database-firewall-configure.md)，這樣一來，這些規則可以使用資料庫複寫，以確保所有次要資料庫具有與主要資料庫相同的防火牆規則。此方法不需要客戶手動設定及維護同時裝載主要和次要資料庫之伺服器上的防火牆規則。同樣地，針對資料存取使用[自主資料庫使用者](sql-database-manage-logins.md)，確保主要與次要資料庫永遠具有相同的使用者認證，在容錯移轉時不會因為登入和密碼不相符而有任何中斷。使用額外的 [Azure Active Directory](../active-directory/active-directory-whatis.md)，客戶可以管理主要和次要資料庫的使用者存取，並且排除在資料庫中管理認證的需求。

## 防止重要資料遺失
由於廣域網路的高度延遲，連續複製採用非同步複寫機制。如果發生失敗，非同步複寫導致部分資料遺失是無法避免的。不過，有些應用程式可能會要求資料不能遺失。若要保護這些重大更新，應用程式開發人員可以在認可交易後立即呼叫 [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx) 系統程序。呼叫 **sp\_wait\_for\_database\_copy\_sync** 會封鎖呼叫執行緒，直到最後認可的交易複寫到次要資料庫。此程序會等候，直到次要資料庫認可所有加入佇列的交易。**sp\_wait\_for\_database\_copy\_sync** 以特定的連續複製連結為範圍。任何具備主要資料庫連接權限的使用者都可以呼叫此程序。

>[AZURE.NOTE] **sp\_wait\_for\_database\_copy\_sync** 程序呼叫所造成的延遲可能會相當可觀。延遲會取決於當時交易記錄長度的大小，而且此呼叫不會傳回，直到複寫整個記錄。請避免呼叫此程序，除非絕對必要。

## 以程式設計方式管理主動式異地複寫

如前所述，主動式異地複寫可使用 Azure PowerShell 和 REST API，以程式設計的方式管理。下表描述可用的命令集。

- **Azure Resource Manager API 和角色型安全性**︰主動式異地複寫包含一組可管理的 [Azure Resource Manager API](https://msdn.microsoft.com/library/azure/mt163571.aspx)，包括[以 Azure Resource Manager 為基礎的 PowerShell Cmdlet](sql-database-geo-replication-powershell.md)。這些 API 需要使用資源群組，並支援以角色為基礎的安全性 (RBAC)。如需如何實作存取角色的詳細資訊，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

>[AZURE.NOTE] 只有在使用以 [Azure Resource Manager](../resource-group-overview.md) 為基礎的 [Azure SQL REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) 和 [Azure SQL Database PowerShell Cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx) 時，才支援主動式異地複寫的許多新功能。(傳統) REST API (https://msdn.microsoft.com/library/azure/dn505719.aspx) 和 [Azure SQL Database (傳統) Cmdlet](https://msdn.microsoft.com/library/azure/dn546723.aspx) 支援回溯相容性，因此建議使用以 Azure Resource Manager 為基礎的 API。


### Transact-SQL

|命令|說明|
|-------|-----------|
|[ALTER DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx)|使用 ADD SECONDARY ON SERVER 引數，針對現有資料庫建立次要資料庫並開始資料複寫|
|[ALTER DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx)|使用 FAILOVER 或 FORCE\_FAILOVER\_ALLOW\_DATA\_LOSS，將次要資料庫切換為主要資料庫以便開始容錯移轉
|[ALTER DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx)|使用 REMOVE SECONDARY ON SERVER，來終止 SQL Database 和指定次要資料庫間的資料複寫。|
|[sys.geo\_replication\_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx)|針對 Azure SQL Database 邏輯伺服器上的每個資料庫，傳回所有結束複寫連結的相關資訊。|
|[sys.dm\_geo\_replication\_link\_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx)|針對指定的 SQL Database，取得上次複寫時間、上次複寫延遲，以及複寫連結的其他相關資訊。|
|[sys.dm\_operation\_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx)|顯示所有資料庫作業的狀態，包括複寫連結的狀態。|
|[sp\_wait\_for\_database\_copy\_sync (Azure SQL Database)](https://msdn.microsoft.com/library/dn467644.aspx)|導致應用程式等候，直到作用中次要資料庫複寫並認可所有認可的交易為止。|
||||

### PowerShell

|Cmdlet|說明|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/zh-TW/library/azure/mt603648.aspx)|取得一或多個資料庫。|
|[New-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|針對現有資料庫建立次要資料庫並開始資料複寫。|
|[Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/zh-TW/library/mt619393.aspx)|將次要資料庫切換為主要資料庫以開始容錯移轉。|
|[Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/zh-TW/library/mt603457.aspx)|終止 SQL Database 和指定次要資料庫間的資料複寫。|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|取得 Azure SQL Database 和資源群組或 SQL Server 之間的異地複寫連結。|
||||

### REST API

|API|說明|
|---|-----------|
|[Create or Update Database (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|建立、更新或還原主要或次要資料庫。|
|[取得建立或更新資料庫狀態](https://msdn.microsoft.com/library/azure/mt643934.aspx)|在建立作業期間傳回狀態。|
|[將次要資料庫設定為主要資料庫 (計劃性容錯移轉)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|在異地複寫關聯性中升級次要資料庫以做為新的主要資料庫。|
|[將次要資料庫設定為主要資料庫 (非計劃的容錯移轉)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|強制容錯移轉至次要資料庫，並將次要資料庫設定為主要資料庫。|
|[取得複寫連結](https://msdn.microsoft.com/library/azure/mt600929.aspx)|取得異地複寫關聯性中指定 SQL Database 的所有複寫連結。它會擷取 sys.geo\_replication\_links 目錄檢視中顯示的資訊。|
|[取得複寫連結](https://msdn.microsoft.com/library/azure/mt600778.aspx)|取得異地複寫關聯性中指定 SQL Database 的特定複寫連結。它會擷取 sys.geo\_replication\_links 目錄檢視中顯示的資訊。|
||||



## 後續步驟

- 如需商務持續性概觀和案例，請參閱[商務持續性概觀](sql-database-business-continuity.md)
- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
- 若要了解如何使用自動備份進行復原，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
- 若要了解如何使用自動備份進行封存，請參閱[資料庫複製](sql-database-copy.md)。
- 若要深入了解新的主要伺服器和資料庫的驗證需求，請參閱[災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。

<!---HONumber=AcomDC_0907_2016-->