---
title: "Azure 串流分析診斷記錄 |Microsoft Docs"
description: "了解如何在 Microsoft Azure 中分析串流分析工作的診斷記錄。"
keywords: 
documentationcenter: 
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
ms.sourcegitcommit: f0292efd50721ef58028df778052eb0ed6fcda84
ms.openlocfilehash: 724eba50b7428b0012e8f062e264ce057e2a5287


---
# <a name="job-diagnostic-logs"></a>作業診斷記錄

## <a name="introduction"></a>簡介
串流分析公開兩種類型的記錄： 
* [活動記錄 (英文)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)，一律會啟用，並提供作業上所執行之作業的深入資訊。
* [診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)，可供使用者設定，並提供豐富的作業深入資訊，包含從建立、更新、執行，直到刪除作業之間所發生任何事件。

## <a name="how-to-enable-diagnostic-logs"></a>如何啟用診斷記錄
診斷記錄預設為 [關閉]。 若要啟用，請依照下列步驟執行：

登入 Azure 入口網站並瀏覽到 [串流作業] 刀鋒視窗，然後使用 [監視] 底下的 [診斷記錄]。

![瀏覽到診斷記錄的刀鋒視窗](./media/stream-analytics-job-diagnostic-logs/image1.png)  

然後按一下 [開啟診斷] 連結

![開啟診斷記錄](./media/stream-analytics-job-diagnostic-logs/image2.png)

在開啟的診斷中，將狀態變更為 [開啟]。

![變更診斷記錄狀態](./media/stream-analytics-job-diagnostic-logs/image3.png)

設定所需的封存目標 (儲存體帳戶、事件中樞、Log Analytics)，並選取要收集的記錄類別 (執行、製作)。 然後儲存新的診斷組態。

儲存組態之後，需要約 10 分鐘才會生效，然後記錄會開始顯示在設定的封存目標中。您可以在 [診斷記錄] 刀鋒視窗中查看：

![瀏覽到診斷記錄的刀鋒視窗](./media/stream-analytics-job-diagnostic-logs/image4.png)

設定診斷的詳細資訊可在[診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)頁面取得。

## <a name="diagnostic-logs-categories"></a>診斷記錄類別
目前可擷取的診斷記錄類別有兩種：

* **製作：**擷取作業製作作業相關的記錄：建立、新增及刪除輸入與輸出、新增及更新查詢、開始及停止作業。
* **執行：**擷取作業執行期間所發生的事件。
    * 連線錯誤；
    * 資料處理錯誤；
        * 不符合查詢定義的事件 (不相符的欄位類型與值、遺漏欄位等)；
        * 運算式評估錯誤；
    * 等等

## <a name="diagnostic-logs-schema"></a>診斷記錄結構描述
所有記錄檔都以 JSON 格式儲存，且每個都有以下共通字串欄位：

名稱 | 說明
------- | -------
分析 | 記錄的時間戳記 (UTC 時間)
resourceId | 作業發生之資源的識別碼 (大寫)。 其中包含訂用帳戶識別碼、資源群組，以及作業名稱。 例如， `/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB`
category | 記錄檔類別，`Execution` 或 `Authoring`
operationName | 記錄的作業名稱。 例如， `Send Events: SQL Output write failure to mysqloutput`
status | 作業狀態。 例如， `Failed, Succeeded`。
層級 | 記錄層級。 例如， `Error, Warning, Informational`
屬性 | 記錄檔項目特定詳細資料，序列化為 JSON 字串，詳細資訊請參閱下面段落

### <a name="execution-logs-properties-schema"></a>執行記錄檔屬性結構描述
執行記錄檔包含在執行串流分析工作期間所發生之事件的資訊。
屬性會隨事件類型的不同而有不同的結構描述。 目前有下列幾種類型︰

### <a name="data-errors"></a>資料錯誤
任何資料處理的錯誤都屬於此記錄類別。 主要在讀取、序列化及寫入資料作業時產生。 不包含視為一般事件的連線錯誤。

名稱 | 說明
------- | -------
來源 | 發生錯誤的作業輸入或輸出的名稱。
訊息 | 與錯誤相關的訊息。
類型 | 錯誤類型。 例如，`DataConversionError, CsvParserError, ServiceBusPropertyColumnMissingError` 等等。
資料 | 包含有助於正確找到錯誤來源的資料。 視其大小，可能會做出截斷。

資料錯誤會隨 **operationName** 值的不同而有下列結構描述：
* **序列化事件** - 發生於事件讀取作業，其中輸入的資料不符合查詢結構描述：
    * 事件 (還原) 序列化期間類型不相符：欄位造成錯誤。
    * 無法讀取事件、無效的序列化︰輸入資料中發生錯誤之位置的相關資訊：Blob 輸入的 Blob 名稱、位移，以及資料樣本。
* **傳送事件** - 寫入作業：造成錯誤的串流事件。

### <a name="generic-events"></a>一般事件
其餘

名稱 | 說明
-------- | --------
錯誤 | (選則性) 錯誤資訊，若可取得則通常為例外狀況資訊。
訊息| 記錄訊息。
類型 | 訊息的類型，對應到錯誤的內部分類，如：JobValidationError、BlobOutputAdapterInitializationFailure 等。
相互關連識別碼 | 唯一識別作業執行的 [GUID (英文)](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 從作業開始到停止的期間所產生的所有執行記錄項目都會有相同的「關聯識別碼」。



## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


