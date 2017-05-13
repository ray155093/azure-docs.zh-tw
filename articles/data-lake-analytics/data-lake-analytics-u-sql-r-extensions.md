---
title: "在 Azure Data Lake Analytics 中使用 R 擴充 U-SQL 指令碼 | Microsoft Docs"
description: "了解如何在 U-SQL 指令碼中執行 R 程式碼"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ea837a735404d11b087cd552d1fce64184e88e00
ms.contentlocale: zh-tw
ms.lasthandoff: 05/02/2017


---

# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>教學課程︰開始使用 R 擴充 U-SQL

以下範例說明部署 R 程式碼的基本步驟：
* 使用 REFERENCE ASSEMBLY 陳述式啟用 U-SQL 指令碼的 R 擴充。
* 使用 REDUCE 作業分割索引鍵上的輸入資料。
* U-SQL 的 R 擴充有內建的歸納器 (Extension.R.Reducer)，會在指派給歸納器的每一個頂點上執行 R 程式碼。 
* 專用具名資料框架的使用情況會相對應地呼叫 inputFromUSQL 和 outputToUSQL 以在 USQL 和 R 之間傳遞資料。輸入和輸出資料框架識別碼名稱是固定的 (也就是使用者無法變更輸入和輸出資料框架識別碼的這些預先定義名稱)。


--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-r-integrates-with-u-sql"></a>R 如何與 U-SQL 整合

### <a name="datatypes"></a>資料類型
* 來自 U-SQL 的字串和數值資料行會在 R 資料框架和 U-SQL 之間以現狀進行轉換 [支援的類型：Double, String, Bool, Integer, Byte]。
* U-SQL 不支援 Factor 資料類型。
* byte[] 必須序列化為 base64 編碼的字串。
* 當 U-SQL 建立 R 輸入資料框架，或是設定歸納器參數 stringsAsFactors: true 之後，U-SQL 字串便可以轉換為 R 程式碼的因素。

### <a name="schemas"></a>結構描述
* U-SQL 資料集不能有重複的資料行名稱。
* U-SQL 資料集的資料行名稱必須為字串。
* 資料行名稱在 U-SQL 和 R 指令碼中皆必須為相同。
* 唯讀資料行不能是輸出資料框架的一部分。 因為如果唯讀資料行是 UDO 輸出結構描述的一部分，便會自動插入回 U-SQL 資料表。

## <a name="next-steps"></a>後續步驟
* [Microsoft Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* [使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
* [針對 Azure 資料湖分析工作使用 U-SQL 視窗函式](data-lake-analytics-use-window-functions.md)

