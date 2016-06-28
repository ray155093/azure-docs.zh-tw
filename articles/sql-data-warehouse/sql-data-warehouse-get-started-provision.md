<properties
   pageTitle="在 Azure 入口網站中建立 SQL 資料倉儲 | Microsoft Azure"
   description="了解如何在 Azure 入口網站中建立 Azure SQL 資料倉儲"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# 建立 Azure SQL 資料倉儲

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

在本教學課程中，您將使用 Azure 入口網站來建立包含 AdventureWorksDW 範例資料庫的 SQL 資料倉儲。


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 按一下 [+新增] > [資料 + 儲存體] > [SQL 資料倉儲]。

    ![建立](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. 在 [SQL 資料倉儲] 刀鋒視窗中，填入所需的資訊，然後按 [建立] 來建立。

    ![建立資料庫](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **伺服器**︰我們建議您先選取您的伺服器。您可以選取現有的伺服器，或[建立一個新的伺服器](./sql-data-warehouse-get-started-new-server.md)。 

	- **資料庫名稱**︰將用來參考 SQL 資料倉儲的名稱。對伺服器而言，它必須是唯一的。
	
    - **效能**：我們建議從 400 DWU 開始。您可以將滑桿向左邊或向右移動，以調整資料倉儲的效能，在建立之後相應增加或相應減少。若要深入了解 DWU，請參閱[調整](./sql-data-warehouse-manage-compute-overview.md)相關文件或我們的[價格頁面](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

    - **訂用帳戶**：選取此 SQL 資料倉儲將會計費的訂用帳戶。

    - **資源群組**：主要為了協助您管理 Azure 資源集合。深入了解[資源群組](../azure-portal/resource-group-portal.md)。

    - **選取來源**：按一下 [選取來源] > [範例]。因為此時只有一個可用的範例資料庫，所以當您選取 [範例] 時，Azure 會以 AdventureWorksDW 自動填入 [選取範例] 選項。

4. 按一下 [建立] 來建立您的 SQL 資料倉儲。

5. 等候幾分鐘的時間，您的 SQL 資料倉儲就會準備就緒。完成之後，您應該會回到 [Azure 入口網站](https://portal.azure.com)。您可以在儀表板上尋找您的 SQL 資料倉儲，其列在您的 SQL 資料庫之下，或在您用來建立它的資源群組中。

    ![入口網站檢視](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL Database 建立伺服器](../../includes/sql-database-create-new-server-firewall-portal.md)]

## 後續步驟

既然您已建立 SQL 資料倉儲，您已準備好[連接](./sql-data-warehouse-connect-overview.md)和開始查詢。

若要將資料載入 SQL 資料倉儲，請參閱[載入概觀](./sql-data-warehouse-overview-load.md)。

如果您嘗試將現有的資料庫移轉至 SQL 資料倉儲，請參閱[移轉概觀](./sql-data-warehouse-overview-migrate.md)或使用[移轉公用程式](./sql-data-warehouse-migrate-migration-utility.md)。

<!---HONumber=AcomDC_0622_2016-->