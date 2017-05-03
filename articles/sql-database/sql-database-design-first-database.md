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
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/30/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 0d02954829ebac9275c014f7dac7e1ec423b0fc1
ms.lasthandoff: 04/21/2017


---

# <a name="design-your-first-azure-sql-database"></a>設計您的第一個 Azure SQL Database

在本教學課程中，您會建置一個大學資料庫來追蹤學生成績和課程註冊。 本教學課程展示如何使用 [Azure 入口網站](https://portal.azure.com/)和 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)，在 Azure SQL Database 邏輯伺服器上建立 Azure SQL Database、將資料表新增至資料庫、將資料載入至資料表，以及查詢資料庫。 此外亦會展示如何使用 SQL Database [還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)功能，將資料庫還原至較早的時間點。

為了完成此教學課程，請確定您已安裝最新版的 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)。 

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-blank-sql-database-in-azure"></a>在 Azure 中建立空白 SQL Database

Azure SQL Database 會使用一組定義的[計算和儲存體資源](sql-database-service-tiers.md)建立。 此資料庫建立於 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)和 [Azure SQL Database 邏輯伺服器](sql-database-features.md)內。 

遵循以下步驟來建立空白 SQL 資料庫。 

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 從 [新增] 頁面中選取 [資料庫]，然後從 [資料庫] 頁面中選取 [SQL Database]。 

    ![建立空白資料庫](./media/sql-database-design-first-database/create-empty-database.png)

3. 使用下列資訊填寫 SQL Database 表單，如上圖所示︰     

   - 資料庫名稱︰**mySampleDatabase**
   - 資源群組︰**myResourceGroup**
   - 來源：**空白資料庫**

4. 按一下 [伺服器] 為您的新資料庫建立及設定新的伺服器。 填寫指定全域唯一伺服器名稱的**新伺服器表單**提供伺服器系統管理員登入的名稱，然後指定您所選擇的密碼。 

    ![建立資料庫伺服器](./media//sql-database-design-first-database/create-database-server.png)
5. 按一下 [選取] 。

6. 按一下 [定價層] 指定新資料庫的服務層和效能等級。 針對此快速入門，選取 [20 DTUs (20 個 DTU)] 和 [250] GB 儲存體。

    ![建立資料庫-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. 按一下 [套用]。  

8. 按一下 [建立] 即可佈建資料庫。 佈建完成所需時間約 1.5 分。 

9. 在工具列上，按一下 [通知] 以監視部署程序。

    ![通知](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>建立伺服器層級防火牆規則

Azure SQL Database 受防火牆保護。 依預設，伺服器與其內部資料庫的所有連線皆會遭拒。 遵循以下步驟建立 [SQL Database 伺服器層級防火牆規則](sql-database-firewall-configure.md)，以讓您的伺服器允許來自用戶端 IP 位址的連線。 

1. 部署完成之後，按一下左側功能表中的 [SQL Database]，然後按一下 **SQL Database** 頁面上的新資料庫 **mySampleDatabase**。 資料庫的概觀頁面隨即開啟，其中會顯示完整伺服器名稱 (例如 **mynewserver20170313.database.windows.net**)，並提供進一步的組態選項。

      ![伺服器防火牆規則](./media/sql-database-design-first-database/server-firewall-rule.png) 

2. 如先前映像所示，按一下工具列上的 [設定伺服器防火牆]。 SQL Database 伺服器的 [防火牆設定] 頁面隨即開啟。 

3. 依據按一下工具列上的 [新增用戶端 IP] 和 [儲存]。 系統便會為目前的 IP 位址建立伺服器層級防火牆規則。

      ![設定伺服器防火牆規則](./media/sql-database-design-first-database/server-firewall-rule-set.png) 

4. 依序按一下 [確定] 和 [X] 以關閉 [防火牆設定] 頁面。

您現在可以使用 SQL Server Management Studio 或您選擇的其他工具來連線至資料庫及其伺服器。

> [!NOTE]
> SQL Database 會透過連接埠 1433 通訊。 如果您嘗試從公司網路進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 1433，否則將無法連線至 Azure SQL Database 伺服器。
>

## <a name="get-connection-information"></a>取得連線資訊

在 Azure 入口網站中取得 Azure SQL Database 伺服器的完整伺服器名稱。 透過 SQL Server Management Studio，您可使用此完整伺服器名稱連接到您的伺服器。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在 Azure 入口網站中您資料庫的 [基本資訊] 窗格中，找到後複製 [伺服器名稱]。

    ![連線資訊](./media/sql-database-connect-query-ssms/connection-information.png) 

## <a name="connect-to-your-database-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 連線至您的資料庫

使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) 建立對 Azure SQL Database 伺服器的連線。

1. 開啟 SQL Server Management Studio。

2. 在 [連接到伺服器] 對話方塊中，輸入下列資訊：
   - **伺服器類型**：指定資料庫引擎
   - **伺服器名稱**︰輸入您的完整伺服器名稱，例如 **mynewserver20170313.database.windows.net**
   - **驗證**：指定 SQL Server 驗證
   - **登入**︰輸入您的伺服器管理帳戶
   - **密碼**：輸入伺服器管理帳戶的密碼


   <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. 按一下 [連接到伺服器] 對話方塊中的 [選項]。 在 [連線到資料庫] 區段中，輸入 **mySampleDatabase** 以連線到這個資料庫。

   ![連線到伺服器上的 DB](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. 按一下 [ **連接**]。 [物件總管] 視窗隨即在 SSMS 中開啟。 

5. 在 [物件總管] 中，展開 [資料庫]，然後展開 [mySampleDatabase] 以檢視範例資料庫中的物件。

   ![資料庫物件](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>在資料庫中建立資料表 

使用四個資料表建立資料庫結構描述，其會使用 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) 建立大學的學生管理系統模型：

- Person
- 課程
- 學生
- 建立大學專屬學生管理系統的信用額度

下圖顯示這些資料表是如何彼此相互關聯。 在這當中有部分資料表會參考其他資料表的資料欄。 例如，[Student (學生)] 資料表會參考 [Person (人員)] 資料表的 [PersonId] 資料欄。 研究圖表，以了解在本教學課程中資料表彼此關連的方式。 如需深入了解建立有效資料庫資料表的方式，請參閱[建立有效資料庫資料表 ](https://msdn.microsoft.com/library/cc505842.aspx)。 如需選擇資料類型的相關資訊，請參閱[資料類型 (英文)](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql)。

> [!NOTE]
> 您亦可使用 [SQL Server Management Studio 中的資料表設計工具](https://msdn.microsoft.com/library/hh272695.aspx)，建立和設計您的資料表。 

![資料表關聯性](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. 在 [物件總管] 中，於 **mySampleDatabase** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。 隨即開啟已連線到您資料庫的空白查詢視窗。

2. 在查詢視窗中，執行下列查詢以在資料庫中建立四個資料表︰ 

   ```sql 
   -- Create Person table

    CREATE TABLE Person
    (
      PersonId      INT IDENTITY PRIMARY KEY,
      FirstName     NVARCHAR(128) NOT NULL,
      MiddelInitial NVARCHAR(10),
      LastName      NVARCHAR(128) NOT NULL,
      DateOfBirth   DATE NOT NULL
    )
   
   -- Create Student table
 
    CREATE TABLE Student
    (
      StudentId INT IDENTITY PRIMARY KEY,
      PersonId  INT REFERENCES Person (PersonId),
      Email     NVARCHAR(256)
    )
    
   -- Create Course table
 
    CREATE TABLE Course
    (
      CourseId  INT IDENTITY PRIMARY KEY,
      Name      NVARCHAR(50) NOT NULL,
      Teacher   NVARCHAR(256) NOT NULL
    ) 

   -- Create Credit table
 
    CREATE TABLE Credit
    (
      StudentId   INT REFERENCES Student (StudentId),
      CourseId    INT REFERENCES Course (CourseId),
      Grade       DECIMAL(5,2) CHECK (Grade <= 100.00),
      Attempt     TINYINT,
      CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
      (
        StudentId, CourseId, Grade, Attempt
      )
    )
   ```

![建立資料表](./media/sql-database-design-first-database/create-tables.png)

3. 在 SQL Server Management Studio 物件總管中展開「資料表」節點，以查看您建立的資料表。

   ![建立 ssms 資料表](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>將資料載入到資料表

1. 在 [下載] 資料夾中建立一個名為 **SampleTableData** 的資料夾，以存放您資料庫的範例資料。 

2. 以滑鼠右鍵按一下下列連結，將其儲存至 **SampleTableData** 資料夾。 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. 開啟命令提示字元視窗，並瀏覽至 SampleTableData 資料夾。

4. 執行下列命令以將範例資料列插入至資料表中，其中以適用於您環境的值取代 **ServerName**、**DatabaseName**、**UserName** 及 **Password** 的值。
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

您現在已將範例資料載入至先前建立的資料表。

## <a name="query-the-tables"></a>查詢資料表

執行下列查詢，以從資料庫資料表中擷取資訊。 如需深入了解關於撰寫 SQL 查詢的資訊，請參閱[撰寫 SQL 查詢 (英文)](https://technet.microsoft.com/library/bb264565.aspx)。 第一個查詢會聯結所有四個資料表，以尋找由「Dominick Pope」授課且成績高於全班 75% 學生的所有學生。 第二個查詢會連結所有四個資料表，以尋找「Noe Coleman」曾註冊的所有課程。

1. 在 [SQL Server Management Studio] 查詢視窗中，執行下列查詢︰

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

    SELECT  person.FirstName,
        person.LastName,
        course.Name,
        credit.Grade
    FROM  Person AS person
        INNER JOIN Student AS student ON person.PersonId = student.PersonId
        INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
        INNER JOIN Course AS course ON credit.CourseId = course.courseId
    WHERE course.Teacher = 'Dominick Pope' 
        AND Grade > 75
   ```

2. 在 [SQL Server Management Studio] 查詢視窗中，執行下列查詢︰

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

    SELECT  course.Name,
        course.Teacher,
        credit.Grade
    FROM  Course AS course
        INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
        INNER JOIN Student AS student ON student.StudentId = credit.StudentId
        INNER JOIN Person AS person ON person.PersonId = student.PersonId
    WHERE person.FirstName = 'Noe'
        AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>將資料庫還原至先前的時間點 

假設您不小心刪除了資料表。 這是您無法輕易復原的情況。 Azure SQL Database 可讓您返回至最長過去 35 天內的任何時間點，並將此時間點還原至新資料庫。 您可使用此資料庫來復原已刪除的資料。 下列步驟會將範例資料庫還原至新增資料表之前的時間點。

1. 在資料庫的 [SQL Database] 頁面上，按一下工具列上的 [還原]。 [還原] 頁面隨即開啟。

   ![還原](./media/sql-database-design-first-database/restore.png)

2. 在 [還原] 表單中填入必要資訊︰
    * 資料庫名稱︰提供資料庫名稱 
    * 時間點：選取 [還原] 表單上的 [時間點] 索引標籤 
    * 還原點：選取一個在變更資料庫之前的時間
    * 目標伺服器︰還原資料庫時，您無法變更此值 
    * 彈性資料庫集區：選取 [無]  
    * 定價層：選取 [20 DTU] 和 [250 GB] 的儲存體。

   ![還原點](./media/sql-database-design-first-database/restore-point.png)

3. 按一下 [確定]，以將資料庫[還原至新增資料表之前的時間點](sql-database-recovery-using-backups.md#point-in-time-restore)。 若將資料庫還原至不同的時間點，系統即會從您指定的時間點 (在您[服務層](sql-database-service-tiers.md)的保留期限內) 開始，在與原始資料庫相同的伺服器中建立重複的資料庫。

## <a name="next-steps"></a>後續步驟 

如需一般工作的 PowerShell 範例，請參閱 [SQL Database PowerShell 範例](sql-database-powershell-samples.md)

