每個應用程式 (亦即每個檢測金鑰) 都有一些度量和事件的數目限制。

限制取決於您選擇的[定價層](https://azure.microsoft.com/pricing/details/application-insights/)。

| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| 每個月的工作階段資料點<sup>1、2</sup> |無限制 | |
| 要求、事件、相依性、追蹤、例外狀況及頁面檢視的每月資料點總數 |5 百萬 |5 千萬<sup>3</sup> |
| [追蹤與記錄](../articles/application-insights/app-insights-search-diagnostic-logs.md)資料速率 |200 dp/s |500 dp/s |
| [例外狀況](../articles/application-insights/app-insights-asp-net-exceptions.md)資料速率 |50 dp/s |50 dp/s |
| 要求、事件、相依性及頁面檢視遙測的總資料速率 |200 dp/s |500 dp/s |
| [搜尋](../articles/application-insights/app-insights-diagnostic-search.md)和[分析](../articles/application-insights/app-insights-analytics.md)的未經處理資料保留 |7 天 | |
| [計量瀏覽器](../articles/application-insights/app-insights-metrics-explorer.md)的彙總資料保留 |90 天 | |
| [屬性](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)名稱計數 |100 | |
| 屬性名稱長度 |150 | |
| 屬性值長度 |8192 | |
| 追蹤和例外狀況的訊息長度 |10000 | |
| [度量](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)名稱計數 |100 | |
| 度量名稱長度 |150 | |
| [可用性測試](../articles/application-insights/app-insights-monitor-web-app-availability.md) |10 | |

<sup>1</sup> 資料點是個別的度量值或事件，具有附加的屬性和測量值。

<sup>2</sup> 工作階段資料點會記錄工作階段的開始或結束，以及記錄使用者身分識別。

<sup>3</sup> 您可以購買超過 5 千萬的額外容量。

[關於 Application Insights 中的定價和配額](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0803_2016-->