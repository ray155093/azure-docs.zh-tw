---
title: "Azure 計費企業版 API - 使用量詳細資料| Microsoft Docs"
description: "了解 Azure 計費使用情況和 RateCard API，其可用來提供 Azure 資源耗用量和趨勢的見解。"
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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 0f751063510707d53ac76a287aa420818a31b04b
ms.contentlocale: zh-tw
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---usage-details"></a>適用於企業客戶的報告 API - 使用量詳細資料

使用量詳細資料 API 可提供註冊之使用量和預估費用的每日明細。 結果也包含執行個體、計量和部門的資訊。 API 可依計費週期或指定開始和結束日期來查詢。 
## <a name="consumption-apis"></a>使用情況 API


##<a name="request"></a>要求 
需要新增的常見標頭屬性在[這裡](billing-enterprise-api.md)詳述。 如果未指定計費週期，則會傳回目前計費週期的資料。 可以使用格式為 yyyy-MM-dd 的開始和結束日期參數來指定自訂的時間範圍。 支援的最大時間範圍是 36 個月。  

|方法 | 要求 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> 若要使用 API 的預覽版本，請將上述 URL 中的 v2 取代為 v1。
>

## <a name="response"></a>Response

> 由於結果的資料可能會相當龐大，因此結果集會以多個頁面顯示。 nextLink 屬性 (若存在) 會指定下一個資料分頁的連結。 如果連結是空的，則表示該分頁是最後一個分頁。 
<br/>

    {
        "id": "string",
        "data": [
            {                       
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "Cost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ],
        "nextLink": "string"
    }

<br/>
**回應屬性定義**

|屬性名稱| 類型| 說明
|-|-|-|
|id| 字串| API 呼叫的唯一識別碼。 |
|data| JSON 陣列| 每個執行個體\計量的每日使用量詳細資料陣列。|
|nextLink| 字串| 當有更多的資料分頁時，nextLink 會指向能傳回下一個資料分頁的 URL。 |
|accountId| int| 已淘汰的欄位。 之所以顯示是為了提供回溯相容性。 |
|productId| int| 已淘汰的欄位。 之所以顯示是為了提供回溯相容性。 |
|resourceLocationId| int| 已淘汰的欄位。 之所以顯示是為了提供回溯相容性。 |
|consumedServiceID| int| 已淘汰的欄位。 之所以顯示是為了提供回溯相容性。 |
|departmentId| int| 已淘汰的欄位。 之所以顯示是為了提供回溯相容性。 |
|accountOwnerEmail| 字串| 帳戶擁有者的電子郵件帳戶。 |
|accountName| 字串| 客戶輸入的帳戶名稱。 |
|serviceAdministratorId| 字串| 服務系統管理員的電子郵件地址。 |
|subscriptionId| int| 已淘汰的欄位。 之所以顯示是為了提供回溯相容性。 |
|subscriptionGuid| 字串| 訂用帳戶的全域唯一識別碼。 |
|subscriptionName| 字串| 訂用帳戶的名稱。 |
|日期| 字串| 取用的發生日期。 |
|product| 字串| 計量的其他詳細資料。 範例：A1(VM)Windows - 亞太地區東部|
|meterId| 字串| 發出使用量之計量的識別碼。 |
|meterCategory| 字串| 所使用的 Azure 平台服務。 |
|meterSubCategory| 字串| 定義可能會影響費率的 Azure 服務類型。 範例：A1 VM (非 Windows)|
|meterRegion| 字串| 針對根據資料中心位置定價的某些服務，識別資料中心的位置。 |
|meterName| 字串| 計量的名稱。 |
|consumedQuantity| double| 已耗用的計量數量。 |
|resourceRate| double| 每個可計費單位的適用費率。 |
|cost| double| 計量已產生的費用。 |
|resourceLocation| 字串| 識別正在執行計量的資料中心。 |
|consumedService| 字串| 所使用的 Azure 平台服務。 |
|instanceId| 字串| 此識別碼是資源的名稱或完整的資源識別碼。 如需詳細資訊，請參閱 [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources) |
|serviceInfo1| 字串| 內部的 Azure 服務中繼資料。 |
|serviceInfo2| 字串| 例如，虛擬機器的映像類型和 ExpressRoute 的 ISP 名稱。 |
|additionalInfo| 字串| 服務專屬的中繼資料。 例如，虛擬機器的影像類型。 |
|tags| 字串| 客戶已新增的標記。 如需詳細資訊，請參閱[使用標記組織您的 Azure 資源](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags)。 |
|storeServiceIdentifier| 字串| 不會使用這個資料行。 之所以顯示是為了提供回溯相容性。 |
|departmentName| 字串| 部門名稱。 |
|costCenter| 字串| 使用量的相關聯成本中心。 |
|unitOfMeasure| 字串| 識別服務的計費單位。 範例：GB、小時、10,000 秒。 |
|resourceGroup| 字串| 部署的資源正在其中執行的計量群組。 如需詳細資訊，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 |
<br/>
## <a name="see-also"></a>另請參閱

* [計費週期 API](billing-enterprise-api-billing-periods.md)

* [餘額與摘要 API](billing-enterprise-api-balance-summary.md)

* [Marketplace 市集費用 API](billing-enterprise-api-marketplace-storecharge.md) 

* [價位表 API](billing-enterprise-api-pricesheet.md)

