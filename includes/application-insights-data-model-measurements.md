自訂度量的集合。 使用此集合以報告與遙測項目相關聯的具名度量。 一般使用案例如下：
- 相依性遙測承載大小
- 要求遙測所處理的佇列項目數
- 客戶為完成「精靈步驟完成事件遙測」中的步驟所使用的時間。

您可以在應用程式分析中查詢[自訂度量](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H)︰

```
customEvents 
| where customMeasurements != "" 
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > 與其所屬遙測項目相關聯的自訂度量。 而且系統會使用包含這些自訂度量的遙測項目來對自訂度量進行取樣。 使用[度量遙測](#metric-telemetry)以追蹤其值與其他遙測類型無關的度量。 

最大金鑰長度︰150
