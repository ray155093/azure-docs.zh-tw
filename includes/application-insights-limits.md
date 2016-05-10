每個應用程式 (亦即每個檢測金鑰) 都有一些度量和事件的數目限制。

限制取決於您選擇的[定價層](https://azure.microsoft.com/pricing/details/application-insights/)。

**Resource** | **預設限制** | **上限**
-------- | ------------- | -------------
每個月的工作階段資料點<sup>1</sup> | 無限制 | 
每個月的其他資料點 | 5 百萬 | 5 千萬<sup>2</sup>
[追蹤或記錄](../articles/application-insights/app-insights-search-diagnostic-logs.md)資料速率 | 200 dp/s | 500 dp/s
[例外狀況](../articles/application-insights/app-insights-asp-net-exceptions.md)資料速率 | 50 dp/s | 50 dp/s
其他遙測資料速率 | 200 dp/s | 500 dp/s
[原始資料](../articles/application-insights/app-insights-diagnostic-search.md)保留期間 | 7 天
[彙總資料](../articles/application-insights/app-insights-metrics-explorer.md)保留期間 | 90 天
[屬性](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)名稱計數 | 100 |
屬性名稱長度 | 100 | 
屬性值長度 | 1000 | 
追蹤和例外狀況的訊息長度 | 10000 |
[計量](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)名稱計數 | 100 |
度量名稱長度 | 100 | 
[可用性測試](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> 資料點是個別的度量值或事件，具有附加的屬性和測量值。

<sup>2</sup> 您可購買超過 5 千萬的額外容量。
 
[關於 Application Insights 中的定價和配額](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0427_2016-->