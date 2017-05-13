---
title: "Azure 計費企業版 API - PriceSheet| Microsoft Docs"
description: "了解可讓企業 Azure 客戶以程式設計方式提取使用情況資料的報告 API。"
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: e5deb660ae5cfed5ac64000d070f4dd2e42ec9c0
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---pricesheet-preview"></a>適用於企業客戶的報告 API - PriceSheet (預覽)

價位表 API 可針對指定註冊和計費週期的每個計量提供適用的費率。

##<a name="request"></a>要求
需要新增的常見標頭屬性在[這裡](billing-enterprise-api.md)詳述。 如果未指定計費週期，則會傳回目前計費週期的資料。

|方法 | 要求 URI|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|


## <a name="response"></a>回應

    
          [
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                  "billingPeriodId": "201704",
                  "meterName": "A1 VM",
                  "unitOfMeasure": "100 Hours",
                  "includedQuantity": 0,
                  "partNumber": "N7H-00015",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                  "billingPeriodId": "201404",
                  "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                  "unitOfMeasure": "100 GB",
                  "includedQuantity": 0,
                  "partNumber": "N9H-00402",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            ...
        ]
    

**回應屬性定義**

|屬性名稱| 類型| 描述
|-|-|-|
|id| string| 表示特定 PriceSheet 項目的唯一識別碼 (依計費週期的計量)|
|billingPeriodId| 字串| 表示特定計費週期的唯一識別碼|
|meterName| 字串| 計量名稱|
|unitOfMeasure| 字串| 測量服務的度量單位|
|includedQuantity| decimal| 包含的數量 |
|partNumber| string| 與計量相關聯的組件編號|
|unitPrice| decimal| 計量的單價|
|currencyCode| 字串| unitPrice 的貨幣代碼|
<br/>
## <a name="see-also"></a>另請參閱

* [計費週期 API](billing-enterprise-api-billing-periods.md)

* [使用量詳細資料 API](billing-enterprise-api-usage-detail.md)

* [餘額與摘要 API](billing-enterprise-api-balance-summary.md)

* [Marketplace 市集費用 API](billing-enterprise-api-marketplace-storecharge.md)

