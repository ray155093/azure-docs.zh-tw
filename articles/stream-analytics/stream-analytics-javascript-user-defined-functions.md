---
title: "Azure 串流分析 JavaScript 使用者定義函數 | Microsoft Docs"
description: "IoT 感應器標記和具有串流分析的資料串流與即時資料處理"
keywords: "iot solution, get started with iot, tools, iot 解決方案, iot 入門, 工具"
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 822f0870396e1fa5fe2dfee3bae410aad3da1122
ms.openlocfilehash: 959fd48cb7d7f21385f6eea21f79ad26e8164bc0

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Azure 串流分析 JavaScript 使用者定義函數
Azure 串流分析支援以 JavaScript 撰寫的使用者定義函數 (UDF)。 JavaScript 提供豐富的 String、RegExp、Math、Array 和 Date 方法，可讓使用串流分析工作建立複雜的資料轉換變得更容易。

## <a name="overview"></a>概觀
JavaScript UDF 支援無狀態且只做為計算用途的純量函數，而且不需要外部連線能力。 函數的傳回值只能是純量 (單一) 值。 一旦加入作業，就能在查詢的任何位置使用該函數，如同內建的純量函數。

從以下範例案例可以看出使用 JavaScript UDF 的好處：
* 使用規則運算式函數剖析及操作字串，例如：Regexp_Replace() 和 Regexp_Extract()
* 針對編碼資料進行解碼，例如：從二進位轉換成十六進位
* 使用 JavaScript Math 函數做數學運算
* 陣列作業，例如，排序、連結、尋找及填入

以下是在串流分析中無法使用 JavaScript UDF 達成的事項：
* 呼叫外部 REST 端點，例如，反向 IP 查詢或從外部來源提取參考資料
* 輸入/輸出上的自訂事件格式序列化或還原序列化
* 自訂彙總

雖然沒有在函數定義中封鎖 Date.GetDate() 或 Math.random() 等函數，您仍應避免使用它們。 這些函數「不會」在每次呼叫時都傳回同樣的結果，且「串流分析」服務不會記錄函數叫用和傳回值的日誌。 因此，若函數針對同樣事件傳回不同的值，當您或串流分析重新啟動某作業之後，將不保證它具有可重覆性。

## <a name="adding-a-javascript-udf-from-azure-portal"></a>從 Azure 入口網站新增 JavaScript UDF
若要在現有串流分析工作下建立簡單的 JavaScript 使用者定義函數，請依照下列步驟執行。

1.  開啟 Azure 入口網站。

2.  找出您的串流分析工作，然後按一下 [作業拓撲] 下方的函數連結。
 
3.  您會看到空白的現有函數清單，按一下 [新增] 圖示來新增 UDF。

4.  在 [新增函式] 刀鋒視窗上，選取 JavaScript 作為 [函式類型]，您將會在編輯器中看見預設的函數範本。
 
5.  將 UDF 別名設為 _hex2Int_，並變更函數實作，如下所示

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  按一下 [儲存] 按鈕，您的函數將會顯示在函數清單上。 

7.  按一下新的函數 **hex2Int** 就能查看函數定義。 請注意，所有函數的別名開頭都會加上前置詞 "UDF"。 您需要在串流分析的查詢中呼叫「具有此前置詞」的函數，在此範例為 **UDF.hex2Int**。
 
## <a name="calling-javascript-udf-in-a-query"></a>在查詢中呼叫 JavaScript UDF

1. 按一下 [作業拓撲] 下的 [查詢]，以開啟查詢編輯器。 

2.  編輯您的查詢並呼叫剛才新增的 UDF，方法如下：

    ```
    SELECT 
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  按一下作業輸入來上傳範例資料檔案 
 
4.  按一下 [測試] 來測試您的查詢。


## <a name="supported-javascript-objects"></a>支援的 JavaScript 物件
Azure 串流分析 JavaScript UDF 支援 JavaScript 語言的標準內建物件。 如需物件清單，請參閱[標準內建物件](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)。

### <a name="stream-analytics-and-javascript-type-conversion"></a>串流分析與 JavaScript 類型轉換

串流分析查詢語言與 JavaScript 支援的類型有所差異。 下表列出兩者之間的轉換對應。


---
串流分析 | JavaScript
--- | ---
bigint | Number (請注意，JavaScript 只能準確地表示最高到 2^53 的整數)
DateTime | Date (請注意，JavaScript 只支援毫秒) 
double | Number
nvarchar(MAX) | String
Record | Object
陣列 | 陣列
NULL | Null


這裡也列出 JavaScript 到 ASA 的轉換對應。

---
JavaScript | 串流分析
--- | ---
Number | 若數字為整數且介於 long.MinValue 和 long.MaxValue 之間，則為 bigint，否則為 double
日期 | DateTime
String | nvarchar(MAX)
Object | Record
陣列 | 陣列
Null、Undefined | NULL
任何其他類型，如 Function、Error 等。 | 不支援 – 執行階段錯誤

## <a name="troubleshooting"></a>疑難排解
JavaScript 執行階段錯誤會被視為嚴重問題，並顯示在活動記錄。 若要從 Azure 入口網站擷取記錄檔，請移至您的作業並按一下 [活動記錄]。
 

## <a name="other-javascript-udf-usage-patterns"></a>其他 JavaScript UDF 使用量模式

### <a name="writing-nested-json-to-output"></a>將巢狀 JSON 寫入至輸出
當您有以串流分析工作輸出做為輸入值，且需要 JSON 格式的後續處理步驟時，將 JSON 字串寫入至輸出會是一項常見的工作。 以下範例呼叫 JSON.stringify() 函數以包裝所有輸入的名稱/值對，並將它們以單一字串值於輸出中寫入。 

### <a name="javascript-udf-definition"></a>JavaScript UDF 定義︰

```
function main(x) {
return JSON.stringify(x);
}
```

**範例查詢︰**
```
SELECT 
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--HONumber=Feb17_HO1-->


