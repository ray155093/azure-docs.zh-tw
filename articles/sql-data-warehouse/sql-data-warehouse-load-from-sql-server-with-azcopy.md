---
title: "將資料從 SQL Server 載入 Azure SQL 資料倉儲 (PolyBase) | Microsoft Docs"
description: "使用 bcp 將資料從 SQL Server 匯出到一般檔案、使用 AZCopy 將資料匯入至 Azure Blob 儲存體，以及使用 PolyBase 將資料擷取到 Azure SQL 資料倉儲中。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4d42786a-fb28-43c9-9c3b-72d19c0ecc11
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f3a4ad30d1aa0ec273b6b875b0d2d037005ac159


---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>將資料從 SQL Server 載入 Azure SQL 資料倉儲 (AZCopy)
使用 bcp 和 AZCopy 命令列公用程式，將資料從 SQL Server 載入至 Azure Blob 儲存體。 然後使用 PolyBase 或 Azure Data Factory 將資料載入 Azure SQL 資料倉儲中。 

## <a name="prerequisites"></a>必要條件
若要逐步執行本教學課程，您需要：

* SQL 資料倉儲資料庫
* 已安裝的 bcp 命令列公用程式
* 已安裝的 SQLCMD 命令列公用程式

> [!NOTE]
> 您可以從 [Microsoft 下載中心][Microsoft 下載中心]下載 bcp 和 sqlcmd 公用程式。
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>將資料匯入 SQL 資料倉儲
在本教學課程中，您將在 Azure SQL 資料倉儲中建立資料表，並將資料匯入資料表。

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>步驟 1：在 Azure SQL 資料倉儲中建立資料表
從命令提示字元，使用 sqlcmd 執行下列查詢，以在您的執行個體上建立資料表︰

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

> [!NOTE]
> 如需有關在 SQL 資料倉儲上建立資料表和 WITH 子句中可用選項的詳細資訊，請參閱[資料表概觀][資料表概觀]或 [CREATE TABLE 語法][CREATE TABLE 語法]。
> 
> 

### <a name="step-2-create-a-source-data-file"></a>步驟 2：建立來源資料檔
開啟 [記事本]，將下列幾行資料複製到新的文字檔，然後將此檔案儲存到本機暫存目錄 C:\Temp\DimDate2.txt。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 請務必記得 bcp.exe 不支援 UTF-8 檔案編碼。 使用 bcp.exe 時，請使用 ASCII 檔案或 UTF-16 編碼的檔案。
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>步驟 3：連接並匯入資料
在 bcp 中，您可以使用下列命令來連接並匯入資料 (請適當地取代其中的值)：

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

您可以使用 sqlcmd 執行下列查詢以確認資料已載入︰

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

這應該會傳回下列結果：

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>步驟 4：建立新載入資料的統計資料
Azure 資料倉儲尚未支援自動建立或自動更新統計資料。 為了獲得查詢的最佳效能，在首次載入資料，或是資料中發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。 如需統計資料的詳細說明，請參閱「開發」主題群組中的「[統計資料][統計資料]」主題。 以下是快速範例，說明如何在此範例中建立載入資料表的統計資料

在 sqlcmd 提示字元中執行下列 CREATE STATISTICS 陳述式：

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>從 SQL 資料倉儲匯出資料
在本教學課程中，您將從 Azure SQL 資料倉儲中的資料表建立資料檔案。 我們會將上面建立的資料匯出至新的資料檔案，稱為 DimDate2_export.txt。

### <a name="step-1-export-the-data"></a>步驟 1：匯出資料
在 bcp 公用程式中，您可以使用下列命令來連接並匯出資料 (請適當地取代其中的值)：

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
您可以開啟新的檔案來確認資料已正確匯出。 檔案中的資料應該符合以下文字：

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 由於分散式系統的本質，資料順序在不同 SQL 資料倉儲資料庫中可能不相同。 另一個選項是使用 bcp 的 **queryout** 函式來撰寫查詢擷取，而不是匯出整個資料表。
> 
> 

## <a name="next-steps"></a>後續步驟
如需載入的概觀，請參閱[將資料載入 SQL 資料倉儲][將資料載入 SQL 資料倉儲]。
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][SQL 資料倉儲開發概觀]。

<!--Image references-->

<!--Article references-->

[將資料載入 SQL 資料倉儲]: ./sql-data-warehouse-overview-load.md
[SQL 資料倉儲開發概觀]: ./sql-data-warehouse-overview-develop.md
[資料表概觀]: ./sql-data-warehouse-tables-overview.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE 語法]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft 下載中心]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Nov16_HO2-->


