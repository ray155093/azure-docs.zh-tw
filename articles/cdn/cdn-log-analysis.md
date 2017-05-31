---
title: "Azure CDN 的記錄分析 | Microsoft Docs"
description: "客戶可以啟用 Azure CDN 的記錄分析功能。"
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: rli
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e3be8aced50a2ce330523d8633f524803af8c8a7
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---

# <a name="diagnostics-logs-for-azure-cdn"></a>Azure CDN 的診斷記錄

啟用應用程式的 CDN 後，您可能會想要監視 CDN 使用情況、檢查傳遞的健康情況，並為可能的問題疑難排解。 Azure CDN 透過[核心分析](cdn-analyze-usage-patterns.md)提供這些功能。

若您身為採用 Verizon 標準或進階設定檔的目前 Azure CDN 使用者，則已能夠在補充入口網站中檢視核心分析；透過 Azure 入口網站的 [管理] 選項可存取補充入口網站。 透過這項新的[診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)功能，現在您可以檢視核心分析，並將分析儲存至一或多個目的地，包括 Azure 儲存體帳戶、Azure 事件中樞，和/或 [Log Analytics (OMS) 工作區](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)。 這項功能適用於所有屬於 Verizon (標準與進階) 及 Akamai (標準) CDN 設定檔的 CDN 端點。

診斷記錄可讓您將 CDN 端點的基本使用情況計量匯出到各種來源，讓您可以使用自訂的方式取用。 例如，您可以執行下列工作：

- 匯出資料至 Blob 儲存體、匯出至 CSV，以及在 Excel 中產生圖表。
- 匯出資料至事件中樞，並將資料與其他 Azure 服務相互關聯。
- 匯出資料至 Log Analytics，並在您自己的 OMS 工作區中檢視資料


![入口網站 - 診斷記錄](./media/cdn-diagnostics-log/OMS-workspace.png)

以下逐步解說將說明核心資料的結構描述，以及啟用功能、將功能傳遞至各種目的地、從這些目的地取用功能的步驟。

## <a name="enable-logging-with-azure-portal"></a>使用 Azure 入口網站啟用記錄

診斷記錄預設為 [關閉]。 請依照下方步驟啟用診斷記錄：


登入 [Azure 入口網站](http://portal.azure.com)。 如果您尚未為您的工作流程啟用 CDN，請先[啟用 Azure CDN](cdn-create-new-endpoint.md)，再繼續進行。

1. 在入口網站中，瀏覽至 [CDN 設定檔]。
2. 選取 CDN 設定檔，然後選取您想要啟用 [診斷記錄] 的 CDN 端點。
    ![入口網站 - 診斷記錄](./media/cdn-diagnostics-log/Browse-to-Diagnostics-logs.png)
3. 移至 [監視] 區段下方的 [診斷記錄] 刀鋒視窗，然後將狀態變更為 [開啟]。
    ![入口網站 - 診斷記錄](./media/cdn-diagnostics-log/Diagnostics-logs-options.png)
4. 選取並設定所需的封存目標 (儲存體帳戶、事件中樞、Log Analytics)。 
    
    在此範例中，Azure 儲存體用於儲存記錄，選取 [封存至儲存體帳戶]，選取保留天數，然後按一下 [記錄] 下方的 [CoreAnalytics]。 我們目前僅提供 CoreAnalytics，但未來將提供更多記錄類型。 請參閱下方有關 CoreAnalytics 的結構描述、彙總及延遲資訊。 

    ![入口網站 - 診斷記錄](./media/cdn-diagnostics-log/Diagnostics-logs-storage.png)
5.  儲存新的診斷組態。
    
    Verizon 記錄資料會延遲 1 小時，端點傳播完成後需花費最多 2 小時才會開始出現。
    Akamai 記錄資料會延遲 24 小時，如果資料是在 24 小時前建立的，則需花費最多 2 小時才會開始出現。 如果是剛建立的，則記錄需花費最多 25 小時才會開始出現。

## <a name="enable-logging-with-powershell"></a>使用 PowerShell 啟用記錄

以下兩個範例說明如何透過 Azure PowerShell Cmdlet 啟用與取得診斷記錄。

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>啟用儲存體帳戶中的診斷記錄

若要啟用儲存體帳戶中的診斷記錄，請使用此命令︰

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```

## <a name="consuming-logs-from-storage"></a>從儲存體取用記錄
本節說明 CDN 核心分析的結構描述、其在 Azure 儲存體帳戶內的組織方式，並提供範例程式碼將記錄下載至 CSV 檔案。

### <a name="using-microsoft-azure-storage-explorer"></a>使用 Microsoft Azure 儲存體總管
首先您需要一個可存取儲存體帳戶中內容的工具，才能夠存取 Azure 儲存體帳戶的核心分析資料。 雖然市場中有數種工具可使用，但我們建議使用 Microsoft Azure 儲存體總管。 您可以從[這裡](http://storageexplorer.com/)下載該工具。 下載並安裝軟體後，請設定使用同一個已設定為 CDN 診斷記錄檔目的地的 Azure 儲存體帳戶。

1.    開啟 [Microsoft Azure 儲存體總管]
2.    找到儲存體帳戶
3.    移至此儲存體帳戶下方的 [Blob 容器] 節點，然後展開該節點
4.    選取名為 [insights-logs-coreanalytics] 的容器，再按兩下該容器
5.    結果會顯示在右側窗格，開頭的第一層顯示 [resourceId=]。 一直按一下 [繼續]，直到您看到 PT1H.json 檔案為止。 請見下方附註中有關路徑的說明。
6.    每個 blob PT1H.json 代表特定 CDN 端點或其自訂網域一小時的分析記錄。
7.    此 JSON 檔案內容的結構描述如＜Core Analytics 記錄結構描述＞一節所述


> [!NOTE]
> **Blob 路徑格式**
> 
> Core Analytics 記錄每小時產生一次。 系統會將一小時的資料全部收集並儲存在單一 Azure Blob 內作為 JSON 承載。 此 Azure Blob 的路徑會顯示成如同有階層式結構一般。 這是因為儲存體總管工具會將 '/' 解譯為目錄分隔符號，並顯示成階層以供方便檢視。 事實上，整個路徑只代表 blob 名稱。 此 blob 名稱遵循下列命名慣例    
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**欄位說明：**

|value|說明|
|-------|---------|
|訂用帳戶識別碼    |Azure 訂用帳戶識別碼。 採用 Guid 格式。|
|資源 |群組名稱    CDN 資源所屬資源群組的名稱。|
|設定檔名稱 |CDN 設定檔名稱|
|端點名稱 |CDN 端點名稱|
|Year|    4 位數年份表示法，例如 2017|
|月|    2 位數月份表示法。 01 =一月.. 12 =十二月|
|天|    2 位數當月日期表示法|
|PT1H.json|    儲存分析資料的實際 JSON 檔案|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>匯出 Core Analytics 資料至 CSV 檔案

為了能輕鬆存取 Core Analytics，我們提供工具範例程式碼，以供將 JSON 檔案下載至以逗號分隔的一般檔案格式，此格式可讓您輕鬆地建立圖表或其他彙總。

以下為使用此工具的方式：

1.    造訪 github 連結：[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.    下載程式碼
3.    依照指示編譯與設定
4.    執行工具
5.    產生的 CSV 檔案會以簡單的平面階層顯示分析資料。

## <a name="diagnostic-logs-types"></a>診斷記錄類型

我們目前僅提供 Core Analytics 記錄，其中包含的計量會顯示 HTTP 回應統計資料和輸出統計資料，如從 CDN POP/邊緣所見。 經過一段時間，我們將新增其他類型的記錄。

### <a name="core-analytics-metrics-details"></a>Core Analytics 計量詳細資料
以下為 Core Analytics 記錄中可用計量清單。 並非所有提供者的所有計量皆可用，雖然這樣的差異極少。 下表也會顯示提供者的指定計量是否可用。 請注意，計量僅適用於有流量的 CDN 端點。


|度量                     | 說明   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |這段期間要求命中總數| 是  |是   |
| RequestCountHttpStatus2xx |產生 2xx HTTP 代碼 (例如 200、202) 的所有要求計數              | 是  |是   |
| RequestCountHttpStatus3xx | 產生 3xx HTTP 代碼 (例如 300、302) 的所有要求計數              | 是  |是   |
| RequestCountHttpStatus4xx |產生 4xx HTTP 代碼 (例如 400、404) 的所有要求計數               | 是   |是   |
| RequestCountHttpStatus5xx | 產生 5xx HTTP 代碼 (例如 500、504) 的所有要求計數              | 是  |是   |
| RequestCountHttpStatusOthers |  所有其他 HTTP 代碼 (2xx-5xx 以外) 的計數 | 是  |是   |
| RequestCountHttpStatus200 | 產生 200 HTTP 代碼回應的所有要求計數              |否   |是   |
| RequestCountHttpStatus206 | 產生 206 HTTP 代碼回應的所有要求計數              |否   |是   |
| RequestCountHttpStatus302 | 產生 302 HTTP 代碼回應的所有要求計數              |否   |是   |
| RequestCountHttpStatus304 |  產生 304 HTTP 代碼回應的所有要求計數             |否   |是   |
| RequestCountHttpStatus404 | 產生 404 HTTP 代碼回應的所有要求計數              |否   |是   |
| RequestCountCacheHit |產生快取命中的所有要求計數。 這表示資產是從 POP 直接提供給用戶端。               | 是  |否   |
| RequestCountCacheMiss | 產生快取遺漏的所有要求計數。 這表示最靠近用戶端的 POP 上找不到資產，且因此從來源擷取。              |是   | 否  |
| RequestCountCacheNoCache | 因為邊緣上的使用者組態之故，而無法予以快取的所有資產要求計數。              |是   | 否  |
| RequestCountCacheUncacheable | 無法由資產的 Cache-Control 與 Expires 標頭快取的所有資產要求計數，這表示不應在 POP 上或由 HTTP 用戶端快取要求                |是   |否   |
| RequestCountCacheOthers | 非上述快取狀態的所有要求計數。              |是   | 否  |
| EgressTotal | 輸出資料傳輸 (單位 GB)              |是   |是   |
| EgressHttpStatus2xx | 狀態代碼為 2xx HTTP 之回應的輸出資料傳輸* (單位為 GB)            |是   |否   |
| EgressHttpStatus3xx | 狀態代碼為 3xx HTTP 之回應的輸出資料傳輸 (單位為 GB)              |是   |否   |
| EgressHttpStatus4xx | 狀態代碼為 4xx HTTP 之回應的輸出資料傳輸 (單位為 GB)               |是   | 否  |
| EgressHttpStatus5xx | 狀態代碼為 5xx HTTP 之回應的輸出資料傳輸 (單位為 GB)               |是   |  否 |
| EgressHttpStatusOthers | 狀態代碼為其他 HTTP 之回應的輸出資料傳輸 (單位為 GB)                |是   |否   |
| EgressCacheHit |  直接從 CDN POP/邊緣上 CDN 快取所傳遞回應的輸出資料傳輸    |是   |  否 |
| EgressCacheMiss | 在最靠近的 POP 伺服器上找不到和從原始伺服器擷取之回應的輸出資料傳輸              |是   |  否 |
| EgressCacheNoCache | 因為邊緣上使用者組態之故而無法予以快取的資產輸出資料傳輸。                |是   |否   |
| EgressCacheUncacheable | 無法由資產的 Cache-Control 和/或 Expires 標頭快取的資產輸出資料傳輸，這表示不應在 POP 上或由 HTTP 用戶端快取要求                    |是   | 否  |
| EgressCacheOthers |  其他快取案例的輸出資料傳輸。             |是   | 否  |

*輸出資料傳輸是指從 CDN POP 伺服器傳遞到用戶端的流量。


### <a name="schema-of-the-core-analytics-logs"></a>Core Analytics 記錄結構描述 

所有記錄都以 JSON 格式儲存，且每個項目都有遵循下列結構描述的字串欄位：

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

其中 ‘time’ 代表報告某段時間統計資料時該時間範圍的開始時間。 請注意，當 CDN 提供者不支援計量時，而非雙精確度值或整數值，則將會有 null 值。 此 null 值表示沒有計量，且這與 0 值不同。 亦請注意，各網域將會有一組這類計量設定在端點上。

範例屬性如下︰

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```
## <a name="additional-resources"></a>其他資源

* [Azure 診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [分析 Azure CDN 使用模式](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)


