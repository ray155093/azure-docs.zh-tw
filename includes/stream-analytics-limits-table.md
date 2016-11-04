---
title: 串流分析限制資料表
description: 描述串流分析元件和連線的系統限制及建議大小。
services: stream-analytics
documentationcenter: NA
author: jeffstokes72
manager: paulettm
editor: cgronlun

ms.service: stream-analytics
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-data
ms.date: 07/25/2016
ms.author: jeffstok

---
| 限制識別碼 | 限制 | 註解 |
| --- | --- | --- |
| 每個區域的每個訂用帳戶串流單位數目上限 |50 |如需要求將訂用帳戶的串流單位數目增加至 50 個以上，請連絡 [Microsoft 支援](https://support.microsoft.com/zh-TW)。 |
| 串流單位的最大輸送量 |1 MB/秒* |每個 SU 的最大輸送量取決於視案例。實際的輸送量可能較低，而且取決於查詢複雜性和資料分割。可在[調整 Azure 串流分析工作以增加輸送量](../articles/stream-analytics/stream-analytics-scale-jobs.md)文章中找到進一步的詳細資料。 |
| 每個工作的輸入數目上限 |60 |每個串流分析工作固定限制 60 個輸入。 |
| 每個工作的輸出數目上限 |60 |每個串流分析工作固定限制 60 個輸出。 |
| 每個工作的函式數目上限 |60 |每個串流分析工作固定限制 60 個函式。 |
| 每個地區的工作數目上限 |1500 |每個訂用帳戶在每個地理區域最多可以有 1500 個工作。 |

<!---HONumber=AcomDC_0727_2016-->