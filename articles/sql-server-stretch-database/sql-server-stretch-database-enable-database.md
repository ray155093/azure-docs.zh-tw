<properties
	pageTitle="為資料庫啟用 Stretch Database | Microsoft Azure"
	description="了解如何為資料庫設定 Stretch Database。"
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
	ms.date="08/05/2016"
	ms.author="douglasl"/>

# 為資料庫啟用 Stretch Database

若要為現有的資料庫設定 Stretch Database，請在 SQL Server Management Studio 中選取資料庫的 [工作 | Stretch | 啟用]，以開啟 [為資料庫啟用 Stretch] 精靈。您也可以使用 Transact-SQL 來為資料庫啟用 Stretch Database。

如果您選取個別資料表的 [工作 | Stretch | 啟用]，且尚未為資料庫啟用 Stretch Database，精靈將會為資料庫設定 Stretch Database，並在程序執行過程中讓您選取資料表。請遵循本主題中的步驟，而非 [Enable Stretch Database for a table (為資料表啟用 Stretch Database)](sql-server-stretch-database-enable-database.md) 中的步驟。

在資料庫或資料表上啟用 Stretch Database 需要 db\_owner 權限。在資料庫或資料表上啟用 Stretch Database 也需要 CONTROL DATABASE 權限。

 >   [AZURE.NOTE] 之後，如果您停用 Stretch Database，請記得停用資料表或資料庫的 Stretch Database 並不會刪除遠端物件。如果您想要刪除遠端資料表或遠端資料庫，您必須使用 Azure 管理入口網站加以卸除。遠端物件會繼續產生 Azure 成本，直到您手動刪除它們為止。

## 開始之前

-   在您為資料庫設定延伸功能之前，我們建議您執行 Stretch Database Advisor 以識別符合延伸功能資格的資料庫和資料表。Stretch Database Advisor 也能識別封鎖問題。如需詳細資訊，請參閱[為 Stretch Database 識別資料庫和資料表](sql-server-stretch-database-identify-databases.md)。

-   檢閱 [Stretch Database 的限制](sql-server-stretch-database-limitations.md)。

-   Stretch Database 會將資料移轉至 Azure。因此您必須擁有 Azure 帳戶，以及計費的訂用帳戶。若要取得 Azure 帳戶，請[按一下這裡](http://azure.microsoft.com/pricing/free-trial/)。

-   您需要具備連接和登入資訊，才能建立新的 Azure 伺服器或選取現有 Azure 伺服器。

## <a name="EnableTSQLServer"></a>必要條件：在伺服器上啟用 Stretch Database
在您可以在資料庫或資料表上啟用 Stretch Database 之前，您必須先在本機伺服器上啟用它。此操作需要 sysadmin 或 serveradmin 權限。

-   如果您擁有必要的系統管理權限，[為資料庫啟用 Stretch] 精靈將會為伺服器設定 Stretch。

-   如果您沒有必要的權限，在您執行精靈之前，系統管理員必須透過執行 **sp\_configure** 來手動啟用該選項，否則便需由系統管理員執行該精靈。

若要在伺服器上手動啟用 Stretch Database，請執行 **sp\_configure** 並開啟 [遠端資料封存] 選項。下列範例會藉由將 [遠端資料封存] 選項的值設為 1 來啟用它。

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
如需詳細資訊，請參閱 [設定遠端資料封存伺服器組態選項](https://msdn.microsoft.com/library/mt143175.aspx) 以及 [sp\_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx)。

## <a name="Wizard"></a>使用精靈以在資料庫上啟用 Stretch Database
如需有關「為資料庫啟用 Stretch 精靈」的資訊 (包括您必須輸入的資訊及必須做的選擇)，請參閱[開始執行為資料庫啟用 Stretch 精靈](sql-server-stretch-database-wizard.md)。

## <a name="EnableTSQLDatabase"></a>使用 Transact-SQL 在資料庫上啟用 Stretch Database
在您可以在個別資料表上啟用 Stretch Database 之前，您必須先在資料庫上啟用它。

在資料庫或資料表上啟用 Stretch Database 需要 db\_owner 權限。在資料庫或資料表上啟用 Stretch Database 也需要 CONTROL DATABASE 權限。

1.  在您開始之前，請選擇現有 Azure 伺服器來接收 Stretch Database 所移轉的資料，或建立新的 Azure 伺服器。

2.  在 Azure 伺服器上，利用 SQL Server 的 IP 位址範圍，來建立能讓 SQL Server 與遠端伺服器通訊的防火牆規則。

    您可以嘗試從 SQL Server Management Studio (SSMS) 中的 [物件總管] 連接至 Azure 伺服器，以輕鬆找到您需要的值並建立防火牆規則。SSMS 會開啟下列對話方塊，其中已包含必要的 IP 位址值，以協助您建立規則。

	![在 SSMS 中建立防火牆規則][FirewallRule]

3.  若要為 SQL Server 資料庫設定 Stretch Database，該資料庫必須擁有資料庫主要金鑰。資料庫主要金鑰能保護 Stretch Database 用來連線到遠端資料庫的認證。以下範例會建立新的資料庫主要金鑰。

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
	GO
    ```

    如需資料庫主要金鑰的詳細資訊，請參閱 [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) 及[建立資料庫主要金鑰](https://msdn.microsoft.com/library/aa337551.aspx)。

4.  當您為資料庫設定 Stretch Database 時，您必須為 Stretch Database 提供認證，以供內部部署 SQL Server 與遠端 Azure 伺服器之間進行通訊使用。您有兩個選擇。

    -   您可以提供系統管理員認證。

        -   如果您是透過執行精靈啟用 Stretch Database，您可以在那時建立認證。

        -   如果您打算透過執行 **ALTER DATABASE** 來啟用 Stretch Database，您必須在執行 **ALTER DATABASE** 啟用 Stretch Database 之前手動建立認證。

		以下範例會建立新的認證。

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

		如需認證的詳細資訊，請參閱 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx)。建立認證需要 ALTER ANY CREDENTIAL 權限。

    -   您可以使用同盟服務帳戶，讓 SQL Server 與 Azure 伺服器在下列條件全數成立時進行通訊。

        -   SQL Server 執行個體正在其下執行的服務帳戶為網域帳戶。

        -   網域帳戶屬於其 Active Directory 為與 Azure Active Directory 同盟的網域。

        -   遠端 Azure 伺服器已設定為支援 Azure Active Directory 驗證。

        -   SQL Server 執行個體正在其下執行的服務帳戶，在遠端 Azure 伺服器上必須被設定為 dbmanager 或 sysadmin 帳戶。

5.  若要為資料庫設定 Stretch Database，請執行 ALTER DATABASE 命令。

    1.  針對 SERVER 引數，請提供現有 Azure 伺服器的名稱，包括名稱的 `.database.windows.net` 部分 - 例如：`MyStretchDatabaseServer.database.windows.net`。

    2.  提供現有系統管理員認證並搭配 CREDENTIAL 引數，或是指定 FEDERATED\_SERVICE\_ACCOUNT = ON。下列範例提供現有的認證。

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## 後續步驟
-   [為資料表啟用 Stretch Database](sql-server-stretch-database-enable-table.md) 以啟用其他資料表。

-   [監視 Stretch Database](sql-server-stretch-database-monitor.md) 以查看資料移轉狀態。

-   [Pause and resume Stretch Database (暫停和繼續 Stretch Database)](sql-server-stretch-database-pause.md)

-   [Manage and troubleshoot Stretch Database (Stretch Database 的管理和疑難排解)](sql-server-stretch-database-manage.md)

-   [備份已啟用 Stretch 的資料庫](sql-server-stretch-database-backup.md)

## 另請參閱

[識別適用於 Stretch Database 的資料庫和資料表。](sql-server-stretch-database-identify-databases.md)

[ALTER DATABASE SET 選項 (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png

<!---HONumber=AcomDC_0810_2016---->