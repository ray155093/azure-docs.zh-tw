---
title: "將 Azure Data Lake Analytics 中的 U-SQL 指令碼參數化 | Microsoft Docs"
description: "了解如何將 Azure Data Lake Analytics 中的 U-SQL 指令碼參數化。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/10/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6318fa8b14d8bb7d650522e2d96a5b1417afe3df
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="parameterizing-u-sql-scripts"></a>將 U-SQL 指令碼參數化

使用 U-SQL 指令碼時，將這些指令碼參數化可便於使用。 參數可讓本文在保持不變的情況下，仍可透過傳送個別的參數值來控制指令碼的執行。 其中一個典型的案例就是指令碼處理某個時間範圍的資料，而開發人員可能想要將開始日期和結束日期的相關指令碼參數化。 

將指令碼參數化的方法有兩種：
* 指令碼允許在其上方安全地新增參數。 U-SQL 支援此方法。
* 作業提交 API 允許呼叫端傳送參數。 U-SQL 不支援此方法。

## <a name="example-scripts"></a>範例指令碼

以下是一個簡單的初始範例：

```
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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    
   
在此案例中，我們會透過新增 `DECLARE EXTERNAL` 陳述式將 ``Region`` 參數化。

```
DECLARE EXTERNAL @TargetRegion = "en-us";

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    

此指令碼預設會篩選至僅限來自 `en-us` 區域的資料列。 到目前為止，我們已在指令碼中定義一個參數。 `EXTERNAL` 關鍵字表示使用者可以透過在前面加上定義 `@TargetRegion` 參數的 DECLARE 陳述式來重新定義 `@TargetRegion` 值。

下列指令碼示範當使用者為參數提供值時，參數化指令碼看起來的樣子。 此指令碼現在會篩選至區域為 `en-gb` 的資料列。

```
DECLARE @TargetRegion = "en-gb";

DECLARE EXTERNAL @TargetRegion = "en-us";

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    


## <a name="next-steps"></a>後續步驟
* [Microsoft Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
* [使用 Azure 入口網站監視和疑難排解 Azure Data Lake Analytics 作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

