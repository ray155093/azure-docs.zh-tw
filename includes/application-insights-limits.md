每個應用程式 (亦即每個檢測金鑰) 都有一些度量和事件的數目限制。 

限制取決於您選擇的[定價方案](https://azure.microsoft.com/pricing/details/application-insights/)。

| **Resource** | **預設限制** | **注意**
| --- | --- | --- |
| 每日資料總量 | 500 GB | 您可以設定上限來減量。 如果您需要更多資訊，請寄送郵件至 AIDataCap@microsoft.com 
| 每月免費的資料量<br/> (基本定價方案) | 1 GB | 每 GB 收費的額外資料量
| 節流 | 16 k 事件數/秒 | 測量超過一分鐘。 
| 資料保留 | 90 天 | 適用於[搜尋](../articles/application-insights/app-insights-diagnostic-search.md)、[分析](../articles/application-insights/app-insights-analytics.md)和[計量瀏覽器](../articles/application-insights/app-insights-metrics-explorer.md)
| [可用性多步驟測試](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests)詳述的結果保留期 | 90 天 | 每個步驟的詳細結果
| 屬性和度量名稱長度 | 150 |
| 屬性值字串長度 | 8192 |
| 追蹤和例外狀況的訊息長度 | 10000 |
| 每個應用程式的[可用性測試](../articles/application-insights/app-insights-monitor-web-app-availability.md)計數  | 10 |

1. 所有的這些數字是屬於每個檢測金鑰。

[關於 Application Insights 中的定價和配額](../articles/application-insights/app-insights-pricing.md)


<!--HONumber=Jan17_HO4-->


