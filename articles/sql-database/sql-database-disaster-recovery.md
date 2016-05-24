<properties 
   pageTitle="SQL Database 嚴重損壞修復" 
   description="了解如何使用 Azure SQL Database 的作用中異地複寫和異地還原功能，從區域資料中心中斷或失敗情況復原資料庫。" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/10/2016"
   ms.author="elfish"/>

# 還原 Azure SQL Database 或容錯移轉到次要資料庫

Azure SQL Database 提供下列功能，以從中斷復原：

- [主動式異地複寫](sql-database-geo-replication-overview.md)
- [異地還原](sql-database-geo-restore.md)

若要了解如何為嚴重損壞情況做準備，以及何時可復原資料庫，請瀏覽我們的[業務續航力的設計](sql-database-business-continuity-design.md)頁面。

## 何時起始復原

復原作業會影響應用程式。它需要變更 SQL 連接字串，並且可能會導致永久的資料遺失。因此，只有在中斷情況可能持續超過應用程式的 RTO 時，才應該執行這項作業。將應用程式部署至生產環境之後，您應該定期監視應用程式健全狀況，並利用下列資料點判斷是否需要復原：

1.	從應用程式層到資料庫的連接發生永久性失敗。
2.	Azure 入口網站顯示有關區域中影響廣泛之事件的警示。
3.	Azure SQL Database 伺服器標示為已降級。 

視您應用程式的停機容忍度和可能的商務責任而定，您可以考慮下列復原選項。

## 等候服務復原

Azure 團隊會努力儘快還原服務可用性，但需視根本原因而言，有可能需要數小時或數天的時間。如果您的應用程式可以容忍長時間停機，您可以等待復原完成。在此情況下，您不需要採取任何動作。您可以在 [Azure 服務健康狀態儀表板](https://azure.microsoft.com/status/)上看見目前的服務狀態。在區域復原後，您應用程式的可用性將會還原。

## 容錯移轉至異地複寫的次要資料庫

如果您應用程式的停機可能會導致商務責任，您應該在應用程式中使用異地複寫的資料庫。這可讓應用程式在發生中斷時，快速還原不同區域的可用性。了解如何[設定異地複寫](sql-database-geo-replication-portal.md)。

若要還原資料庫的可用性，您必須使用其中一種支援的方法，開始容錯移轉到異地複寫的次要資料庫。


使用下列其中一份指南，容錯移轉至異地複寫的次要資料庫：

- [使用 Azure 入口網站容錯移轉至異地複寫的次要資料庫](sql-database-geo-replication-portal.md)
- [使用 PowerShell 容錯移轉至異地複寫的次要資料庫](sql-database-geo-replication-powershell.md)
- [使用 T-SQL 容錯移轉至異地複寫的次要資料庫](sql-database-geo-replication-transact-sql.md) 



## 使用異地還原進行復原

如果您應用程式的停機不會導致任何商務責任，您可以使用異地還原做為復原應用程式資料庫的方法。它會從其最新的異地備援備份建立資料庫的複本。

使用下列其中一份指南，將資料庫異地還原到新的區域︰

- [使用 Azure 入口網站將資料庫異地還原到新的區域](sql-database-geo-restore-portal.md)
- [使用 PowerShell 將資料庫異地還原到新的區域](sql-database-geo-restore-powershell.md) 


## 在復原之後設定資料庫

如果您使用異地還原選項的異地複寫容錯移轉，從中斷情況復原您的應用程式，您必須確定已正確設定新資料庫的連線，才能繼續執行正常的應用程式功能。以下工作檢查清單可協助您準備產生復原的資料庫。

### 更新連接字串

因為復原的資料庫將位於不同的伺服器，所以您必須更新您應用程式的連接字串以指向該伺服器。

如需變更連接字串的詳細資訊，請參閱[連接至 Azure SQL Database：重要的建議](sql-database-connect-central-recommendations.md)。

### 設定防火牆規則

您需要確認伺服器和資料庫上設定的防火牆規則符合主要伺服器與主要資料庫上設定的防火牆規則。如需詳細資訊，請參閱[如何：進行防火牆設定 (Azure SQL Database)](sql-database-configure-firewall-settings.md)。


### 設定登入和資料庫使用者

您需要確定應用程式使用的所有登入，都存在於主控已復原資料庫的伺服器上。如需詳細資訊，請參閱「如何在災害復原期間管理安全性」。如需詳細資訊，請參閱[異地複寫的安全性設定](sql-database-geo-replication-security-config.md)。

>[AZURE.NOTE] 如果您使用異地還原選項從中斷情況復原，您應該在 DR 演練期間設定伺服器防火牆規則和登入，以確定主要伺服器仍可用於擷取其組態。因為異地還原會使用資料庫備份，所以在中斷期間可能無法使用此伺服器層級組態。演練之後，您可以移除已還原的資料庫，但保留此伺服器與其組態，以供復原程序使用。如需 DR 演練的詳細資訊，請參閱[執行災害復原演練](sql-database-disaster-recovery-drills.md)。

### 設定遙測警示

您必須確定現有的警示規則設定已更新，才能對應至復原的資料庫和不同的伺服器。

如需資料庫警示規則的詳細資訊，請參閱[接收警示通知](../azure-portal/insights-receive-alert-notifications.md)和[追蹤服務健全狀況](../azure-portal/insights-service-health.md)。

### 啟用稽核

如果需要稽核才能存取您的資料庫，則您必須在資料庫復原之後啟用稽核。用戶端應用程式必須在 *.database.secure.windows.net 的模式中使用安全連接字串，才能有良好的稽核指標。如需詳細資訊，請參閱[開始使用 SQL 資料庫稽核](sql-database-auditing-get-started.md)。




## 其他資源


- [SQL Database 商務持續性和災害復原](sql-database-business-continuity.md)
- [時間點還原](sql-database-point-in-time-restore.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [異地複寫的安全性設定](sql-database-geo-replication-security-config.md)
- [SQL Database BCDR 常見問題集](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0511_2016-->