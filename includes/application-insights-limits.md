每個應用程式 (亦即每個檢測金鑰) 都有一些度量和事件的數目限制。 

限制取決於您選擇的[定價方案](https://azure.microsoft.com/pricing/details/application-insights/)。

| **Resource** | **預設限制** | **注意**
| --- | --- | --- |
| 每日資料總量 | 100 GB | 您可以設定上限來減量。 如果您需要更多資訊，請寄送郵件至 AIDataCap@microsoft.com 
| 每月免費的資料量<br/> (基本定價方案) | 1 GB | 每 GB 收費的額外資料量
| 資料保留 | 90 天 | 適用於[搜尋](../articles/application-insights/app-insights-diagnostic-search.md)、[分析](../articles/application-insights/app-insights-analytics.md)和[計量瀏覽器](../articles/application-insights/app-insights-metrics-explorer.md)
| [可用性多步驟測試](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests)詳述的結果保留期 | 90 天 | 每個步驟的詳細結果
| [屬性](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)和[度量](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)<sup>2</sup> 名稱計數 | 200 | 
| 屬性和度量名稱長度 | 150 |
| 屬性值字串長度 | 8192 |
| 屬性<sup>3、4</sup> 的相異值 | 100 | > 100 => 不能使用屬性做為計量瀏覽器中的篩選條件
| 追蹤和例外狀況的訊息長度 | 10000 |
| 每個應用程式的[可用性測試](../articles/application-insights/app-insights-monitor-web-app-availability.md)計數  | 10 |

1. 所有的這些數字是屬於每個檢測金鑰。
2. 度量名稱會定義於 TrackMetric 和其他 Track*() 呼叫的測量參數中。 每個檢測金鑰的度量名稱都是全域的。
3. 只有在每個屬性具有少於 100 個唯一值時，屬性才能用於篩選和分組依據。 唯一值的數目超過 100 個之後，您仍可以搜尋屬性，但無法再用於篩選或分組依據。
4. 標準屬性，例如要求名稱和網頁 URL 會限制為每週 1000 個唯一值。 超過 1000 個唯一值之後，額外值都會標示為「其他值」。 原始值仍然可以用於全文檢索搜尋和篩選。


[關於 Application Insights 中的定價和配額](../articles/application-insights/app-insights-pricing.md)

<!--HONumber=Dec16_HO3-->


