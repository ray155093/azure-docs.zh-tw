---
title: "教學課程：開始使用 Azure Data Lake Analytics U-SQL 語言 | Microsoft Docs"
description: "使用此教學課程來了解 Azure Data Lake Analytics U-SQL 語言。"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: dc8c7beaf5b8e8d4f5467ffe22390c41f446d787
ms.lasthandoff: 03/04/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>教學課程：開始使用 Azure Data Lake 分析 U-SQL 語言
U-SQL 語言結合了 SQL 的所有優點，可運用您自有程式碼的運算式能力來處理任何規模的資料。 透過 U-SQL 的可調整分散式查詢功能，您可以有效率地分析各關聯式存放區 (Azure SQL Database) 中的資料。 使用 U-SQL，您可以藉由在讀取時套用結構描述並插入自訂邏輯和 UDF，來處理非結構化資料。 此外，U-SQL 所含有的擴充性可讓您細微控制如何大規模執行。 若要深入了解 U-SQL 背後的設計原理，請參閱 Visual Studio 部落格文章[簡介 U-SQL – 讓巨量資料的處理變簡單的語言](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)。

U-SQL 有幾點不同於 ANSI SQL 或 T-SQL。 例如，SELECT 等關鍵字必須全部是大寫字母。

 其在 SELECT 子句和 WHERE 述詞內的類型系統和運算式語言為 C#。 這表示，資料類型是 C# 類型且使用 C# NULL 語意，而述詞內的比較作業會遵循 C# 語法 (例如 == "foo")。 這也表示，值全都是 .NET 物件，因此您可以輕鬆地使用任何方法來操作物件 (例如 "f o o o".Split(' '))。

如需 U-SQL 的詳細資訊，請參閱 [U-SQL 語言參考](http://go.microsoft.com/fwlink/p/?LinkId=691348)。

### <a name="prerequisites"></a>必要條件
如果您尚未這麼做，請閱讀並完成[教學課程：使用適用於 Visual Studio 的 Data Lake 工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。 在完成此教學課程後，請返回本文。

在此教學課程中，您使用下列 U-SQL 指令碼執行了 Azure Data Lake Analytics 作業：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

此指令碼沒有任何轉換步驟。 它會從原始程式檔 SearchLog.tsv 讀取資料，為其建立結構描述，並將資料列集回寫到 SearchLog-first-u-sql.csv 檔案。

請注意 [持續時間] 欄位中資料類型旁邊的問號。 該符號表示 [持續時間] 欄位可能是 null。

在指令碼中，您會發現下列概念和關鍵字︰

* 資料列集變數：每個會產生資料列集的查詢運算式都可以指派給變數。 在指令碼中，U-SQL 會遵循 T-SQL 變數命名模式 (例如 @searchlog)。

 >[!NOTE]
 >指派作業不會強制執行。 它只會指定運算式，以便您能夠建置更複雜的運算式。
* EXTRACT︰使用此關鍵字，您可以在讀取時定義結構描述。 結構描述是透過每個資料行的資料行名稱和 C# 型別名稱配對來加以指定。 結構描述會使用所謂的擷取器 (例如 Extractors.Tsv()) 來擷取 .tsv 檔案。 您可以開發自訂擷取器。
* OUTPUT：此關鍵字會抓取資料列集並將其序列化。 Outputters.Csv() 會將逗號分隔檔寫入到指定位置。 您也可以開發自訂輸出器。

 >[!NOTE]
 >兩個路徑都是相對路徑。 您也可以使用絕對路徑。 例如：    
 >     adl://\<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
 >
 >您必須使用絕對路徑，才能存取所連結儲存體帳戶中的檔案。  儲存在連結 Azure 儲存體帳戶中之檔案的語法是：wasb://\<BlobContainerName>@\<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

 >[!NOTE]
 >目前不支援具有公用 Blob 或公用容器存取權限的 Azure Blob 儲存體容器。

## <a name="use-scalar-variables"></a>使用純量變數
您也可以使用純量變數以方便維護指令碼。 前述 U-SQL 指令碼也可以撰寫成：

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>轉換資料列集
使用 **SELECT** 來轉換資料列集：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE 子句使用 [C# 布林運算式](https://msdn.microsoft.com/library/6a71f45d.aspx)。 您可以使用 C# 運算式語言來建置自己的運算式和函式。 您甚至可以將它們與邏輯結合 (AND) 和邏輯分離 (OR) 做結合，以執行更複雜的篩選。

下列指令碼使用 DateTime.Parse() 方法和邏輯結合。

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >第二個查詢會對第一個資料列集的結果起作用，因而建立兩個篩選條件的組合。 您也可以重複使用變數名稱，因為它們是語彙範圍型名稱。

## <a name="aggregate-rowsets"></a>彙總資料列集
U-SQL 提供您已熟悉使用的 ORDER BY、GROUP BY 和各種彙總語法。

下列查詢會尋找每個區域的總持續時間，然後按順序顯示前五大持續時間。

U-SQL 資料列集不會保留它們的順序以供下一次查詢使用。 因此，若要對輸出排序，您需要將 ORDER BY 新增至 OUTPUT 陳述式：

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

U-SQL 的 ORDER BY 子句必須搭配 FETCH 子句一起用在 SELECT 運算式中。

U-SQL 的 HAVING 子句可以用來將輸出限制為符合 HAVING 條件的群組：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>聯結資料
U-SQL 提供常見的聯結運算子，例如 INNER JOIN、LEFT/RIGHT/FULL OUTER JOIN、SEMI JOIN，不僅可聯結資料表，還可以連結任何資料列集 (即使是由檔案所產生的資料列集)。

下列指令碼聯結了搜尋日誌與廣告印象日誌，並提供我們特定日期的查詢字串的廣告。

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL 只支援符合規範的 ANSI 聯結語法：Rowset1 JOIN Rowset2 ON 述詞。 「不」支援舊有的 FROM Rowset1, Rowset2 WHERE 述詞語法。
JOIN 中的述詞必須是等號比較聯結且沒有運算式。 如果您想要使用運算式，請將它加入前一個資料列集的 select 子句。 如果您想要進行不同的比較，則可將它移至 WHERE 子句。

## <a name="create-databases-table-valued-functions-views-and-tables"></a>建立資料庫、資料表值函式、檢視和資料表
在 U-SQL 中，您可以在資料庫和結構描述的內容中使用資料，而不一定要讀取或寫入檔案。

每個 U-SQL 指令碼在執行時，都會有預設資料庫 (主要) 與預設結構描述 (DBO) 做為其預設內容。 您可以建立自己的資料庫或結構描述。 若要變更內容，請使用 USE 陳述式。

### <a name="create-a-tvf"></a>建立 TVF
在先前的 U-SQL 指令碼中，您重複使用 EXTRACT 來從相同原始程式檔進行讀取。 使用 U-SQL 資料表值函式 (TVF)，您就可以封裝資料以供日後重複使用。  

下列指令碼會在預設資料庫和結構描述中建立名為 *Searchlog()* 的 TVF。

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

下列指令碼會示範如何使用先前的指令碼中定義的 TVF：

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>建立檢視
如果您只有一個想要抽取但不要從中建立參數的查詢運算式，您可以建立檢視而不是資料表值函式。

下列指令碼會在預設資料庫和結構描述中建立名為 *SearchlogView* 的檢視：

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

下列指令碼示範如何使用定義的檢視：

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>建立資料表
和關聯式資料庫資料表一樣，使用 U-SQL 可讓您使用預先定義的結構描述建立資料表，或建立資料表以從填入資料表的查詢推斷結構描述 (也就是 CREATE TABLE AS SELECT 或 CTAS)。

使用下列指令碼建立一個資料庫和兩個資料表：

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SearchLogDb;
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    DISTRIBUTED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>查詢資料表
您可以運用和查詢資料檔案一樣的方式來查詢資料表 (例如，上一個指令碼所建立的資料表)。 您現在可以直接參考資料表名稱，而不必使用 EXTRACT 建立資料列集。

若要從資料表進行讀取，請修改您先前使用的轉換指令碼：

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

 >[!NOTE]
 >您目前無法在用來建立資料表的相同指令碼中，對該資料表執行 SELECT。

## <a name="conclusion"></a>結論
本教學課程只說明了 U-SQL 一小部分的內容。 因為範圍有限，本教學課程並未討論 U-SQL 的其他眾多好處。 例如，您可以：

* 使用 CROSS APPLY 將局部字串、陣列和對應解除封裝到資料列。
* 操作經過分割的各組資料 (檔案集和經過分割的資料表)。
* 以 C# 開發使用者定義的運算子，例如擷取器、輸出器、處理器和使用者定義的彙總器。
* 使用 U-SQL 視窗函式。
* 使用檢視、資料表值函式和預存程序管理 U-SQL 程式碼。
* 在您的處理節點上執行任意的自訂程式碼。
* 連接到 SQL Database 並同盟這些資料庫和 U-SQL 與 Azure Data Lake 資料的查詢。

## <a name="see-also"></a>另請參閱
* [Microsoft Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* [使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
* [針對Azure Data Lake Analytics 工作使用 U-SQL 視窗函式](data-lake-analytics-use-window-functions.md)
* [使用 Azure 入口網站監視和疑難排解 Azure Data Lake Analytics 作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>讓我們知道您的想法
* [提交要求功能](http://aka.ms/adlafeedback)
* [在論壇上取得協助](http://aka.ms/adlaforums)
* [提供關於 U-SQL 的意見反應](http://aka.ms/usqldiscuss)

