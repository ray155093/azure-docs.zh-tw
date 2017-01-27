---
title: "Azure SQL 資料倉儲 - 快速入門教學課程 | Microsoft Docs"
description: "Azure SQL 資料倉儲的快速入門教學課程。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: fe9de0ffad3fe5d4acbf3caf2f08101f6a13daaf
ms.openlocfilehash: 12f500c01671799612b0d1988e0d07bbfda600da


---
# <a name="get-started-with-sql-data-warehouse"></a>開始使用 SQL 資料倉儲

Azure SQL 資料倉儲的快速入門教學課程。 本教學課程會教導您將資料佈建和載入到 SQL 資料倉儲的基本概念，以及一些關於調整大小、暫停和微調的基本概念。 

**預估完成時間：** 75 分鐘

## <a name="prerequisites"></a>必要條件


### <a name="sign-up-for-microsoft-azure"></a>註冊 Microsoft Azure
如果您還沒有 Microsoft Azure 帳戶，則必須註冊帳戶才能使用這項服務。 如果您已經有帳戶，則可以跳過此步驟。 

1. 瀏覽至帳戶頁面 [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. 建立免費的 Azure 帳戶，或購買帳戶。
3. 遵循指示進行

### <a name="install-appropriate-sql-client-driver-and-tools"></a>安裝適當的 SQL 用戶端驅動程式和工具

大部分的 SQL 用戶端工具都可以使用 JDBC、ODBC 或 ADO.net 連線到 Azure SQL 資料倉儲。 由於產品複雜性高且 SQL 資料倉儲支援大量 T-SQL 功能，因此並非每一個用戶端應用程式都會與 SQL 資料倉儲完全相容。

如果您執行 Windows 作業系統，建議您使用 [Visual Studio] 或 [SQL Server Management Studio]。


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>建立 Azure SQL 資料倉儲

> [!NOTE]
> 建立 SQL 資料倉儲可能會導致新的可計費服務。  如需詳細資訊，請參閱 [SQL 資料倉儲價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。
>


### <a name="create-a-sql-data-warehouse"></a>建立 SQL 資料倉儲
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [新增] > [資料庫] > [SQL 資料倉儲]。

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. 填寫部署詳細資料

    **資料庫名稱**︰挑選您想要的名稱。 如果您有多個 SQL DW 執行個體，建議您在名稱中包含詳細資料，例如其區域、環境等，例如 *mydw-westus-1-test*

    **訂用帳戶**：您的 Azure 訂用帳戶

    **資源群組**︰建立新的群組 (或者，如果您計劃將 Azure SQL 資料倉儲用於其他服務，則可以使用現有群組)
    > [!NOTE]
    > 資源群組中的服務應具有相同的存留期。 資源群組適合用來管理資源，例如界定存取控制和樣板化部署的範圍。 您可以[在此](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)深入了解 Azure 資源群組和最佳作法
    >

    **來源**：空白資料庫

    **伺服器**︰選取您在[必要條件]中建立的伺服器。

    **定序**：保留預設定序 SQL_Latin1_General_CP1_CI_AS

    **選取效能**︰建議您保持使用標準的 400 DWU

4. 選擇 [釘選到儀表板]****
    ![釘選到儀表板](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. 稍候一會，讓 Azure SQL 資料倉儲進行部署！ 正常來說，此程序需要幾分鐘的時間。 當執行個體部署完成時，入口網站會通知您。 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>透過 SQL Server (邏輯伺服器) 連線到 Azure SQL 資料倉儲

本教學課程使用 SQL Server Management Studio 來連線到 SQL 資料倉儲。 您可以使用其他工具，透過下列支援連接器連線到 SQL 資料倉儲︰ADO.NET、JDBC、ODBC 和 PHP。 請記住，非 Microsoft 支援工具的功能可能會受限。


### <a name="get-connection-information"></a>取得連線資訊

若要連線到 SQL 資料倉儲，您必須透過您在[必要條件]中所建立的 SQL Server (邏輯伺服器) 進行連線。

1. 從儀表板選取 SQL 資料倉儲，或在您的資源中進行搜尋。

    ![SQL 資料倉儲儀表板](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. 尋找邏輯伺服器的完整名稱。

    ![選取伺服器名稱](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. 開啟 SSMS，並使用物件總管以您在[必要條件]中所建立的認證連線到此伺服器

    ![以 SSMS 連線](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

如果一切正常，您現在應該已連線到 SQL Server (邏輯伺服器) 執行個體了。 您可以使用伺服器認證，以資料庫擁有者的身分對該伺服器上的任何資料庫進行驗證。 不過，最佳作法是，您應該針對每個資料庫建立個別的登入和使用者。 請在[建立 SQL 資料倉儲的使用者](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse)中探索如何建立使用者。 

## <a name="create-a-user-for-sql-data-warehouse"></a>建立 SQL 資料倉儲的使用者

### <a name="why-create-a-separate-user"></a>為何要建立個別的使用者？

我們會以上一個步驟中所得到的伺服器認證，使用 SQL Server (邏輯伺服器) 的連線來為我們的 SQL 資料倉儲建立新的使用者。 您可能會基於兩個主要原因而想要為 SQL DW 建立個別的使用者/登入。

1.  貴組織的使用者應該使用不同的帳戶來驗證。 如此一來，您可以限制授與應用程式的權限，並降低惡意活動的風險。

2. 根據預設，您目前用來連線的伺服器管理員登入，會使用較小的資源類別。 資源類別可協助您控制指定給查詢的記憶體配置以及 CPU 週期。 **smallrc** 中的使用者會獲得較小量的記憶體，且可利用較高的並行存取能力。 相反地，指派給 **xlargerc** 的使用者會獲得大量的記憶體，因此可並行執行的查詢較少。 為了以可將壓縮最佳化的方式載入資料，您會想要確保載入資料的使用者屬於更大的資源類別。 請[在此](./sql-data-warehouse-develop-concurrency.md#resource-classes)深入了解資源類別：

### <a name="creating-a-user-of-a-larger-resource-class"></a>建立屬於更大資源類別的使用者

1. 在伺服器的**主要**資料庫上進行查詢

    ![主要資料庫上的新增查詢](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![主要資料庫上的新增查詢&1;](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. 建立伺服器登入和使用者

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. 查詢 SQL DataWarehouse 資料庫、依伺服器登入建立新的資料庫使用者 
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. 對使用者授與 DB 控制權
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > 如果資料庫名稱中有連字號，請務必以括弧括住！ 
    >

5. 將資料庫使用者新增至 **xlargerc** 資源類別角色
    ```sql
    EXEC sp_addrolemember 'xlargerc', 'LoadingUser';
    ```

6. 使用新認證登入資料庫

    ![使用新登入進行登入](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>載入資料

### <a name="defining-external-data"></a>定義外部資料
1. 建立主要金鑰並定義外部資料來源

    ```sql
    CREATE MASTER KEY;

    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. 定義外部檔案格式

    ```CREATE EXTERNAL FILE FORMAT``` 命令可用來指定所要載入之外部資料的格式。 針對紐約公用計程車資料，我們使用了兩種格式將資料儲存在 Azure Blob 儲存體中

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  建立外部檔案格式的結構描述

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. 建立外部資料表。 這些資料表會參考 HDFS 或 Azure Blob 儲存體中儲存的資料。 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
    (
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
    )
    WITH
    (
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>Create Table as Select (CTAS)

5. 從外部資料表將資料載入 SQL 資料倉儲執行個體。 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > 您將會載入數 GB 的資料，並將其壓縮成高效能的叢集資料行存放區索引。 請執行下列 DMV 查詢，然後喝杯咖啡或吃些點心，等候 Azure SQL 資料倉儲完成一些繁重的工作。
    >

6. 建立新的查詢，並透過此動態管理檢視 (DMV) 觀看資料的載入

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. 檢視所有系統查詢

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. 輕鬆地看著資料順利載入至 Azure SQL 資料倉儲

    ![看著資料載入](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>查詢資料 

### <a name="scan-query-with-scaling"></a>透過調整大小掃描查詢

讓我們來了解調整大小會如何影響查詢速度。

在開始之前，讓我們先將營運規模下調為 100 DWU，以便了解單一計算節點本身的可能執行效能。

1. 移至入口網站，並選取 SQL 資料倉儲執行個體

2. 在 [SQL 資料倉儲] 刀鋒視窗中選取 [調整]。 

    ![從入口網站調整 DW](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. 將效能列縮小到 100 DWU，然後按一下 [儲存]。

    ![調整並儲存](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. 等候調整作業完成。

    > [!NOTE]
    > 請注意，調整大小作業會**刪除**目前正在執行的查詢，並阻止新的查詢執行。
    >
    
5. 對車程資料執行掃描作業，選取所有資料行的前&1; 百萬個項目。 如果您想要進展快一點，可放心地選取較少的資料列。

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

記下執行這項作業所花費的時間。

6. 將執行個體上調為 400 DWU。 請記住，每上調 100 DWU 就會再新增一個計算節點至您的 Azure SQL 資料倉儲。

7. 再次執行查詢！ 您應該會發現顯著差異。 

> [!NOTE]
> Azure SQL 資料倉儲是大量平行處理 (MPP) 平台。 可在不同節點間平行處理工作的查詢和作業，將會體驗到 Azure SQL 資料倉儲的真正能力。
>

### <a name="join-query-with-statistics"></a>聯結查詢與統計資料

1. 執行聯結了日期資料表與車程資料表的查詢

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    如您所料，當您在不同節點之間隨機處理資料時，尤其是像這樣的查詢聯結案例，需要耗費更長的時間進行查詢。

2. 我們來看看，當我們執行下列命令對所聯結的資料行建立統計資料時，此查詢會有何不同︰

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL DW 不會自動管理您的統計資料。 統計資料對於查詢的效能很重要，因此強烈建議您建立和更新統計資料。
    > 
    > **對牽涉聯結的資料行、WHERE 子句中使用的資料行、在 GROUP BY 中找到的資料行加以統計資料，可以獲得最大效益。**
    >

3. 再次從必要條件執行查詢，並觀察效能差異。 雖然查詢效能的差異幅度不會和上調規模一樣巨大，但您應該會發現速度有所增加。 

## <a name="next-steps"></a>後續步驟

您現在已準備就緒，可以開始查詢和探索。 請查看我們的最佳作法或提示。

如果您當天的探索已完成，請務必暫停您的執行個體！ 在生產環境中，您可以藉由暫停和調整大小以符合商務需求來省下大額成本。

![暫停](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>實用內容

[並行和工作負載管理]

[Azure SQL 資料倉儲最佳做法]

[查詢監視]

[建立大規模關聯式資料倉儲的 10 大最佳作法]

[將資料移轉至 Azure SQL 資料倉儲]


[並行和工作負載管理]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Azure SQL 資料倉儲最佳做法]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[查詢監視]: sql-data-warehouse-manage-monitor.md
[建立大規模關聯式資料倉儲的 10 大最佳作法]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[將資料移轉至 Azure SQL 資料倉儲]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[必要條件]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO3-->


