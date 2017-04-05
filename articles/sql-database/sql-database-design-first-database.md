---
title: "設計您的第一個 Azure SQL Database | Microsoft Docs"
description: "了解如何設計您的第一個 Azure SQL Database。"
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/23/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 313bcf4fbc0ab7f251dd62b1e2151afef8392a55
ms.lasthandoff: 03/28/2017


---

# <a name="design-your-first-azure-sql-database"></a>設計您的第一個 Azure SQL Database

在本教學課程中，您將使用 Azure 入口網站在新伺服器上建立具有伺服器層級防火牆的資料庫。 您將接著使用 SQL Server Management Studio 來建立資料表、將資料載入到該資料表、查詢資料表，以及將索引新增到資料表。 最後，您將使用 SQL Database 服務的自動備份，將資料庫還原到您新增這個新資料表之前的時間點。

為了完成此教學課程，請確定您已安裝最新版的 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) 

## <a name="step-1---log-in-to-the-azure-portal"></a>步驟 1 - 登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="step-2---create-a-sql-database"></a>步驟 2：建立 SQL Database

Azure SQL Database 會使用一組定義的[計算和儲存體資源](sql-database-service-tiers.md)建立。 此資料庫建立於 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)和 [Azure SQL Database 邏輯伺服器](sql-database-features.md)內。 

請遵循下列步驟來建立包含 Adventure Works LT 範例資料的 SQL Database。 

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 從 [新增] 頁面中選取 [資料庫]，然後從 [資料庫] 頁面中選取 [SQL Database]。

3. 在 SQL Database 表單中填入必要資訊︰ 
   - 資料庫名稱︰提供資料庫名稱
   - 訂用帳戶︰選取您的訂用帳戶
   - 資源群組︰選取新的或現有的
   - 來源︰選取 [範例 (AdventureWorksLT)]
   - 伺服器︰建立新的伺服器 (**伺服器**名稱必須是全域唯一的)
   - 彈性集區︰針對此快速入門選取 [現在不要]
   - 定價層：選取 [20 DTU] 和 [250] GB 的儲存體
   - 定序︰匯入範例資料庫時無法變更此值 
   - 釘選到儀表板︰選取此核取方塊

      ![建立資料庫](./media/sql-database-get-started/create-database-s1.png)

4. 完成時按一下 [建立]。 佈建需要幾分鐘的時間。
5. SQL Database 部署完成後，在儀表板上選取 [SQL Database] 或從左側功能表中選取 [SQL Database]，然後在 [SQL Database] 頁面上按一下新的資料庫。 資料庫的 [概觀] 頁面隨即開啟，其中會顯示完整伺服器名稱 (例如 **mynewserver20170313.database.windows.net**)，並提供進一步的組態選項。

      ![新的 SQL Database](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="step-3---create-a-server-level-firewall-rule"></a>步驟 3 - 建立伺服器層級防火牆規則

SQL Database 服務會建立防火牆來防止外部應用程式和工具連線至您的伺服器和資料庫。 請遵循下列步驟來為您的 IP 位址建立 [SQL Database 伺服器層級防火牆規則](sql-database-firewall-configure.md)，以便讓外部連線能夠穿過 SQL Database 防火牆。 

1. 在資料庫的工具列上按一下 [設定伺服器防火牆]。 SQL Database 伺服器的 [防火牆設定] 頁面隨即開啟。 

      ![伺服器防火牆規則](./media/sql-database-get-started/server-firewall-rule.png) 

2. 依據按一下工具列上的 [新增用戶端 IP] 和 [儲存]。 系統便會為目前的 IP 位址建立伺服器層級防火牆規則。

3. 依序按一下 [確定] 和 [X] 以關閉 [防火牆設定] 頁面。

您現在可以使用 SQL Server Management Studio 或您選擇的其他工具來連線至資料庫及其伺服器。

## <a name="step-4---get-connection-information"></a>步驟 4 - 取得連線資訊

在 Azure 入口網站中取得 Azure SQL Database 伺服器的完整伺服器名稱。 透過 SQL Server Management Studio，您可使用此完整伺服器名稱連接到您的伺服器。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在 Azure 入口網站中您資料庫的 [基本資訊] 窗格中，找到後複製 [伺服器名稱]。

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="step-5---connect-to-the-server-using-ssms"></a>步驟 5 - 使用 SSMS 來連線到伺服器

使用 SQL Server Management Studio (SSMS) 建立對 Azure SQL Database 伺服器的連線。

1. 在 Windows 搜尋方塊中輸入 **SSMS**，然後按一下 **Enter** 以開啟 SSMS。

2. 在 [連接到伺服器] 對話方塊中，輸入下列資訊：
   - **伺服器類型**：指定資料庫引擎
   - **伺服器名稱**︰輸入您的完整伺服器名稱，例如 **mynewserver20170313.database.windows.net**
   - **驗證**：指定 SQL Server 驗證
   - **登入**︰輸入您的伺服器管理帳戶
   - **密碼**：輸入伺服器管理帳戶的密碼
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. 按一下 [ **連接**]。 [物件總管] 視窗隨即在 SSMS 中開啟。 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. 在 [物件總管] 中，展開 [資料庫]，然後展開 [mySampleDatabase] 以檢視範例資料庫中的物件。

## <a name="step-6---create-and-query-a-table"></a>步驟 6 - 建立及查詢資料表 
1. 在 [物件總管] 中，於 **mySampleDatabase** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。 隨即開啟已連線到您資料庫的空白查詢視窗。
2. 在查詢視窗中，執行下列查詢︰

   ```sql 
   CREATE TABLE [dbo].[Students]
   (
     [student_id] int, 
     [name] varchar(100),
     [age] int,
     [email] varchar(100),
     [AddressID] int REFERENCES [SalesLT].[Address] (AddressID)
   );
   ```

   完成查詢之後，您便已在資料庫中建立名為 Students 的空資料表。

3. 在 SSMS 查詢視窗中，執行下列查詢︰ 

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   ```

   Students 資料表未傳回任何資料。

## <a name="step-7---load-data-into-the-table"></a>步驟 7 - 將資料載入到資料表 
1. 開啟命令提示字元視窗。

2. 執行下列 PowerShell 命令，以將範例文字檔下載到您目前的目錄。

   ```powershell
   powershell -command "& { (New-Object Net.WebClient).DownloadFile('https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData.txt', 'SampleStudentData.txt'); echo 'Download complete' }" 
   ``` 

3. 完成此操作時，執行下列命令以將 1000 個資料列插入到 Student 資料表中，其中以適用於您環境的值取代 **ServerName**、**DatabaseName**、**UserName** 及 **Password** 的值。

   ```bcp
   bcp Students in SampleStudentData.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

您現在已將範例資料載入到您先前建立的資料表。

## <a name="step-8---add-an-index-to-a-table"></a>步驟 8 - 將索引新增到資料表
若要使在資料表中搜尋特定的值變得更有效率，請在 Students 資料表上建立索引。 索引會將資料加以組織，使得現在必須查看所有資料，才能找到特定的值。

1. 在 SSMS 查詢視窗中，執行下列查詢︰

   ```sql 
   CREATE NONCLUSTERED INDEX IX_Age ON Students (age);
   ```

2. 在 SSMS 查詢視窗中，執行下列查詢︰

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   WHERE age > 20
   ```

   此查詢會傳回超過 20 歲之學生的名字、年齡及電子郵件。

## <a name="step-9---restore-a-database-to-a-point-in-time"></a>步驟 9：將資料庫還原到某個時間點 
Azure 中的資料庫具有每隔 5-10 分鐘自動建立的[連續備份](sql-database-automated-backups.md)。 這些備份可讓您將資料庫還原到先前的時間點。 如果將資料庫還原到不同的時間點，就會從您指定的時間點 (在您服務層的保留期間內) 開始，在與原始資料庫相同的伺服器中建立重複的資料庫。 下列步驟會將範例資料庫還原到新增 **Students** 資料表之前的時間點。 

1. 在資料庫的 [SQL Database] 頁面上，按一下工具列上的 [還原]。 [還原] 頁面隨即開啟。

    <img src="./media/sql-database-design-first-database/restore.png" alt="restore" style="width: 780px;" />

2. 在 [還原] 表單中填入必要資訊︰
    * 資料庫名稱︰提供資料庫名稱 
    * 時間點：選取 [還原] 表單上的 [時間點] 索引標籤 
    * 還原點：選取一個在變更資料庫之前的時間
    * 目標伺服器︰還原資料庫時，您無法變更此值 
    * 彈性資料庫集區：選取 [無]  
    * 定價層：選取 [20 DTU] 和 [250 GB] 的儲存體。

    <img src="./media/sql-database-design-first-database/restore-point.png" alt="restore-point" style="width: 780px;" />

3. 按一下 [確定] 以將資料庫還原到新增 **Students** 資料表之前的時間點。

## <a name="next-steps"></a>後續步驟 
如需一般工作的 PowerShell 範例，請參閱 [SQL Database PowerShell 範例](sql-database-powershell-samples.md)

