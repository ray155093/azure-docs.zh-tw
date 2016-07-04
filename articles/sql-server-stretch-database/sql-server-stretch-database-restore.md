<properties
	pageTitle="還原已啟用延展功能的資料庫 |Microsoft Azure"
	description="了解如何還原已啟用延展功能的資料庫。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# 還原已啟用延展功能的資料庫

視需要還原已備份的資料庫，以從眾多類型的失敗、錯誤及災害進行復原。

如需有關備份的詳細資訊，請參閱[備份已啟用延展功能的資料庫](sql-server-stretch-database-backup.md)。

>   [AZURE.NOTE] 備份只是完整的高可用性及商務持續性解決方案的一部分。如需有關高可用性的詳細資訊，請參閱[高可用性解決方案](https://msdn.microsoft.com/library/ms190202.aspx)。

## 還原 SQL Server 資料
若要從硬體故障或損毀復原，請從備份還原已啟用延展功能的 SQL Server 資料庫。您可以繼續使用您目前使用的 SQL Server 還原方法。如需詳細資訊，請參閱[還原和復原概觀](https://msdn.microsoft.com/library/ms191253.aspx)。

還原 SQL Server 資料庫之後，您必須執行 **sys.sp\_rda\_reauthorize\_db** 預存程序來重新建立已啟用延展功能之 SQL Server 資料庫與遠端 Azure 資料庫之間的連線。如需詳細資訊，請參閱[還原 SQL Server 資料庫與遠端 Azure 資料庫之間的連線](#Restore-the-connection-between-the-SQL-Server-database-and-the-remote-Azure-database)。

## 還原您的遠端 Azure 資料

### 復原即時 Azure 資料庫
Azure 上的 SQL Server Stretch Database 服務會使用「Azure 儲存體快照」，至少每 8 小時建立一次所有即時資料的快照。這些快照會保留 7 天。這可讓您在擷取最新快照集的過去 7 天內，就有一個至少 21 個時間點可用來還原資料。

若要使用 Azure 入口網站將即時 Azure 資料庫還原到先前的時間點，請執行下列操作。

1. 登入 Azure 管理入口網站。
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Database]。
3. 瀏覽至您的資料庫，然後選取它。
4. 在資料庫刀鋒視窗頂端，按一下 [還原]。
5. 指定新的 [資料庫名稱]、選取 [還原點]，然後按一下 [建立]。
6. 資料庫還原程序將會開始，您可以使用 [通知] 來監視此程序。

### 復原已刪除的 Azure 資料庫
Azure 上的 SQL Server Stretch Database 會在卸除資料庫前建立資料庫快照，並將其保留 7 天。在此之後，就不會再保留來自即時資料庫的快照。這可讓您將已刪除的資料庫還原到其被刪除時的時間點。

若要使用 Azure 入口網站將已刪除的 Azure 資料庫還原到其被刪除時的時間點，請執行下列操作。

1. 登入 Azure 管理入口網站。
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Server]。
3. 瀏覽至您的伺服器，然後選取它。
4. 在伺服器的刀鋒視窗上，向下捲動至 [作業]，然後按一下 [已刪除的資料庫] 圖格。
5. 按一下您想要還原的已刪除資料庫。
5. 指定新的 [資料庫名稱]，然後按一下 [建立]。
6. 資料庫還原程序將會開始，您可以使用 [通知] 來監視此程序。

### 在不同 Azure 區域中復原 Azure 資料庫  
Azure 上的 SQL Server Stretch Database 服務會以非同步方式將快照複製到不同的 Azure 地理區域，以提升發生區域失敗時的復原能力。如果您因 Azure 區域失敗而無法存取資料庫，則可以將資料庫還原到其中一個異地備援快照集。

>   [AZURE.NOTE] 若要在不同的 Azure 區域中復原 Azure 資料庫，需要在復原後變更用戶端應用程式中的連接字串，而且可能導致永久性資料遺失。請只有在中斷可能持續很長一段時間時，才執行這類復原。

若要使用 Azure 入口網站，在不同的 Azure 區域中將 Azure 資料庫復原到先前的時間點，請執行下列操作。

1. 登入 Azure 管理入口網站。
2. 在畫面左側選取 [+新增]，然後依序選取 [資料和儲存體] 和 [SQL 資料倉儲]
3. 選取 [備份] 做為來源，然後選取您想要從中復原的異地備援備份
4. 指定其餘資料庫屬性，然後按一下 [建立]
5. 資料庫還原程序將會開始，您可以使用 [通知] 來監視此程序

在不同區域還原 Azure 資料庫之後，您必須執行 **sys.sp\_rda\_deauthorize\_db** 和 **sys.sp\_rda\_reauthorize\_db** 預存程序來重新建立已啟用延展功能之 SQL Server 資料庫與遠端 Azure 資料庫之間的連線。如需詳細資訊，請參閱[還原 SQL Server 資料庫與遠端 Azure 資料庫之間的連線](#Restore-the-connection-between-the-SQL-Server-database-and-the-remote-Azure-database)。

## 還原 SQL Server 資料庫與遠端 Azure 資料庫之間的連線

1.  如果您即將連接到已還原但名稱不同或在不同區域中的 Azure 資料庫，請執行 [sys.sp\_rda\_deauthorize\_db](https://msdn.microsoft.com/library/mt703716.aspx) 預存程序以與先前的 Azure 資料庫中斷連線。  

2.  執行 [sys.sp\_rda\_reauthorize\_db](https://msdn.microsoft.com/library/mt131016.aspx) 預存程序，以將已啟用延展功能的本機資料庫重新連接到 Azure 資料庫。

	-   請以 sysname 或 varchar(128) 值提供現有資料庫範圍認證。(請勿使用 varchar(max))。 您可以在 **sys.database\_scoped\_credentials** 檢視中查閱認證名稱。  

	-   指定是否要建立遠端資料的複本，並連接到該複本 (建議)。

    ```tsql  
    USE <Stretch-enabled database name>;
	GO
	EXEC sp_rda_reauthorize_db
	    @credential = N'<existing_database_scoped_credential_name>',
		@with_copy = 1 ;  
	GO
	```  

## 另請參閱

[Manage and troubleshoot Stretch Database (Stretch Database 的管理和疑難排解)](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[備份和還原 SQL Server 資料庫](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0622_2016-->