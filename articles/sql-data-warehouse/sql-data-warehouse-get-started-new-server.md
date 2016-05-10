<properties
   pageTitle="在 Azure 入口網站中建立 SQL 資料倉儲資料庫 | Microsoft Azure"
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
   ms.date="05/03/2016"
   ms.author="lodipalm;"/>

# 建立新的邏輯伺服器

在 SQL Database 和 SQL 資料倉儲中，每個資料庫都會指派給一部伺服器，而每部伺服器會指派給一個地理位置。此伺服器稱為邏輯 SQL Server。

> [AZURE.NOTE] <a name="note"></a>邏輯 SQL Server：
  >
  > + 提供一致的方式在相同的地理位置設定多個資料庫。
  > + 不是內部部署伺服器的實體硬體。它是服務軟體的一部分。這就是為什麼我們將其稱為「邏輯」伺服器。
  > + 可以裝載多個資料庫，而不會影響其效能。
  > + 在其名稱中使用小寫 s。SQL **s**erver 是 Azure 邏輯伺服器，而 SQL **S**erver 則是 Microsoft 的內部部署資料庫產品。

1. 按一下 [伺服器] > [建立新伺服器]。伺服器不會收取費用。如果您已經有想要使用的 V12 邏輯 SQL 伺服器，請選擇現有的伺服器，然後移至下一個步驟。

    ![建立新伺服器](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. 填入**新伺服器**資訊。

	- **伺服器名稱**：輸入邏輯伺服器的名稱。這對於各個地理位置而言都是唯一的。
	- **伺服器系統管理員帳戶**：輸入伺服器系統管理員帳戶的使用者名稱。
	- **密碼**：輸入伺服器系統管理員密碼。
	- **位置**：選擇您的伺服器的地理位置。若要減少資料傳輸時間，最好找出您的伺服器，其地理位置靠近此資料庫將存取的其他資料資源。
	- **建立 V12 伺服器**：[是] 是 SQL 資料倉儲的唯一選項。
	- **允許 Azure 服務存取伺服器**：SQL 資料倉儲一律會核取這個選項。

    >[AZURE.NOTE] 請務必將伺服器名稱、伺服器系統管理員名稱及密碼儲存於他處。您需要此資訊才能登入伺服器。

3. 按一下 [確定] 以儲存邏輯 SQL 伺服器組態設定並返回 [SQL 資料倉儲] 刀鋒視窗。

    ![設定新的伺服器](./media/sql-data-warehouse-get-started-provision/configure-server.png)

<!---HONumber=AcomDC_0504_2016-->