---
title: "Azure API 管理原則參考文件"
description: "了解可用來設定 API 管理的原則。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 97a6f253eda39d0573a2384691a6b294c3894c25


---
# <a name="azure-api-management-policy-reference"></a>Azure API 管理原則參考文件
本節提供 [API 管理原則參考文件][API 管理原則參考文件]中原則的索引。 如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則][API 管理中的原則]。

如果原則不另行指定，則可以在任何 API 管理原則中，使用原則運算式做為屬性值或文字值。 某些原則是以原則運算式為基礎，例如[控制流程][控制流程]和[設定變數][設定變數]原則。 如需詳細資訊，請參閱[進階原則][進階原則]和[原則運算式][原則運算式]

## <a name="policy-reference-index"></a>原則參考索引
* [存取限制原則][存取限制原則]
  * [檢查 HTTP 標頭][檢查 HTTP 標頭] - 強制 HTTP 標頭必須存在和/或強制採用 HTTP 標頭的值。
  * [依訂閱限制呼叫率][依訂閱限制呼叫率] - 依據訂用帳戶來限制呼叫率，以避免 API 使用量暴增。
  * [依金鑰限制呼叫率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - 以金鑰為單位，限制呼叫率以避免 API 使用量暴增。
  * [限制呼叫端 IP][限制呼叫端 IP] - 篩選 (允許/拒絕) 來自特定 IP 位址和/或位址範圍的呼叫。
  * [依訂閱設定使用量配額][依訂閱設定使用量配額] - 可讓您依據訂用帳戶，強制採用可續訂或有存留期的呼叫量和/或頻寬配額。
  * [依金鑰設定使用量配額](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - 以金鑰為單位，讓您可以強制採用可續訂或有存留期呼叫量與 (或) 頻寬配額。
  * [驗證 JWT][驗證 JWT] - 強制擷取自指定 HTTP 標頭或指定查詢參數的 JWT 必須存在且有效。
* [進階原則][進階原則]
  * [控制流程][控制流程] - 根據布林值[運算式][運算式]的評估結果，有條件地套用原則陳述式。
  * [轉寄要求][轉寄要求] - 將要求轉送給後端服務。
  * [記錄到事件中樞][記錄到事件中樞] - 將訊息以指定的格式傳送至[記錄器](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger)實體所定義的訊息目標。
  * [重試](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - 重試已括住的原則陳述式執行，直到符合條件為止。 系統會在指定的時間間隔重複執行，直到指定的重試計數為止。
  * [傳回回應](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - 中止管線執行，並將指定的回應直接傳回呼叫者。
  * [傳送單向要求](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - 將要求傳送到指定的 URL，無須等待回應。
  * [傳送要求](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - 將要求傳送到指定的 URL。
  * [設定要求方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - 允許您變更要求的 HTTP 方法。
  * [設定狀態碼](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - 將 HTTP 狀態碼變更為指定的值。
  * [設定變數][設定變數] - 將值保存在具名 [context][context] 變數中以供日後存取。
  * [追蹤](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - 將字串新增至 [API 檢查器](api-management-howto-api-inspector.md)輸出。
  * [等候](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - 等候括住的 Send 要求、取得快取的值或控制流程原則於完成後再繼續。
* [驗證原則][驗證原則]
  * [使用基本驗證進行驗證][使用基本驗證進行驗證] - 使用基本驗證來向後端服務進行驗證。
  * [使用用戶端憑證進行驗證][使用用戶端憑證進行驗證] - 使用用戶端憑證來向後端服務進行驗證。
* [快取原則][快取原則] 
  * [從快取中取得][從快取中取得] - 執行快取查閱並傳回有效的快取回應 (如果有的話)。
  * [儲存至快取][儲存至快取] - 根據指定的快取控制組態來快取回應。
  * [從快取取得值](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - 依金鑰擷取快取的項目。
  * [儲存快取中的值](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) -依金鑰儲存快取中的項目。
  * [移除快取中的值](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - 依金鑰移除快取中的項目。
* [跨網域原則][跨網域原則] 
  * [允許跨網域呼叫][允許跨網域呼叫] - 將 API 設為可供 Adobe Flash 和 Microsoft Silverlight 瀏覽器型用戶端存取。
  * [CORS][CORS] - 將跨原始來源資源共用 (CORS) 支援新增至作業或 API，以允許來自瀏覽器型用戶端的跨網域呼叫。
  * [JSONP][JSONP] - 將 JSON 與補充的 (JSONP) 支援新增至作業或 API，以允許來自 JavaScript 瀏覽器型用戶端的跨網域呼叫。
* [轉換原則][轉換原則] 
  * [將 JSON 轉換成 XML][將 JSON 轉換成 XML] - 將要求或回應本文從 JSON 轉換成 XML。
  * [將 XML 轉換成 JSON][將 XML 轉換成 JSON] - 將要求或回應本文從 XML 轉換成 JSON。
  * [在內文中尋找並取代字串][在內文中尋找並取代字串] - 尋找要求或回應子字串並取代成其他子字串。
  * [遮罩內容中的 URL][遮罩內容中的 URL] - 重寫 (遮罩) 回應本文的連結，使其經由閘道指向同等的連結。
  * [設定後端服務][設定後端服務] - 變更連入要求的後端服務。
  * [設定本文][設定本文] - 設定連入和連出要求的訊息本文。
  * [設定 HTTP 標頭][設定 HTTP 標頭] - 指派值給現有的回應和/或要求標頭，或新增回應和/或要求標頭。
  * [設定查詢字串參數][設定查詢字串參數] - 新增要求查詢字串參數、取代其值，或將此參數刪除。
  * [重寫 URL][重寫 URL] - 將要求 URL 從公用格式轉換成 Web 服務所預期的格式。

## <a name="next-steps"></a>後續步驟
如需原則運算式的詳細資訊，請觀看以下影片。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[存取限制原則]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[檢查 HTTP 標頭]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[依訂閱限制呼叫率]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[限制呼叫端 IP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[依訂閱設定使用量配額]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[驗證 JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[進階原則]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[控制流程]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[設定變數]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[運算式]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[轉寄要求]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[記錄到事件中樞]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[驗證原則]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[使用基本驗證進行驗證]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[使用用戶端憑證進行驗證]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[快取原則]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[從快取中取得]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[儲存至快取]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[跨網域原則]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[允許跨網域呼叫]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[轉換原則]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[將 JSON 轉換成 XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[將 XML 轉換成 JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[在內文中尋找並取代字串]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[遮罩內容中的 URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[設定後端服務]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[設定本文]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[設定 HTTP 標頭]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[設定查詢字串參數]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[重寫 URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[API 管理中的原則]: api-management-howto-policies.md
[API 管理原則參考文件]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[原則運算式]: https://msdn.microsoft.com/library/azure/dn910913.aspx





<!--HONumber=Nov16_HO3-->


