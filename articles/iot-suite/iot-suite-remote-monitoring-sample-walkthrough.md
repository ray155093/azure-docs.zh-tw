---
title: 遠端監視預先設定解決方案逐步解說 | Microsoft Docs
description: 說明 Azure IoT 預先設定解決方案遠端監視及其架構。
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: dobett

---
# 遠端監視預先設定解決方案逐步解說
## 簡介
IoT 套件遠端監視 [預先設定解決方案][lnk-preconfigured-solutions] 是適用於在遠端位置執行的多部機器之端對端監視解決方案實作。解決方案結合了主要的 Azure 服務以提供一般商務案例的實作，您可以將其做為自己實作的起點。您可以 [自訂][lnk-customize] 解決方案以滿足特定的業務需求。

本文將逐步介紹遠端監視解決方案的一些重要元素，讓您瞭解它的運作方式。這項知識能協助您︰

* 在解決方案中進行疑難排解。
* 規劃如何自訂解決方案以滿足您的特定需求。
* 設計使用 Azure 服務的 IoT 解決方案。

## 邏輯架構
下圖概述預先設定解決方案的邏輯元件：

![邏輯架構](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## 模擬的裝置
在預先設定解決方案中，模擬的裝置會表示冷卻裝置 (例如建築物空調或設備空氣處理單位)。當您部署預先設定解決方案時，也會自動佈建四個在 [Azure WebJob][lnk-webjobs] 中執行的模擬裝置。模擬裝置讓您可以輕鬆探索解決方案的行為，而不需要部署任何實體裝置。若要部署真實的實體裝置，請參閱[將裝置連接至遠端監視預先設定解決方案][lnk-connect-rm]教學課程。

每個模擬裝置可以傳送下列訊息類型至 IoT 中樞︰

| 訊息 | 說明 |
| --- | --- |
| 啟動 |裝置啟動時會將含有本身相關資訊的**裝置資訊**訊息傳送至後端。這項資料包括裝置識別碼、裝置中繼資料、裝置支援的命令清單，以及裝置目前的設定。 |
| 目前狀態 |裝置會定期傳送**目前狀態**訊息，以報告該裝置是否可以感應到感應器的目前狀態。 |
| 遙測 |裝置會定期傳送**遙測**訊息，以報告從裝置的模擬感應器收集到的溫度和溼度模擬值。 |

模擬裝置會在**裝置資訊**訊息中傳送下列裝置屬性：

| 屬性 | 目的 |
| --- | --- |
| 裝置識別碼 |在解決方案中建立裝置時所提供或指派的識別碼。 |
| 製造商 |裝置製造商 |
| 型號 |裝置的型號 |
| 序號 |裝置的序號 |
| 韌體 |裝置的目前韌體版本 |
| 平台 |裝置的平台架構 |
| 處理器 |執行裝置的處理器 |
| 已安裝的 RAM |在裝置上安裝的 RAM 數量 |
| 中樞已啟用狀態 |裝置的 IoT 中樞狀態屬性 |
| 建立時間 |在解決方案中建立裝置的時間 |
| 更新時間 |裝置上次更新屬性的時間 |
| 緯度 |裝置的緯度位置 |
| 經度 |裝置的經度位置 |

模擬器會以範例值在模擬裝置中植入這些屬性。模擬器每次初始化模擬的裝置時，裝置會將預先定義的中繼資料發佈至 IoT 中樞。請注意，這會覆寫在裝置入口網站中所做的任何中繼資料更新。

模擬的裝置可以處理透過 IoT 中樞從解決方案儀表板傳送的下列命令：

| 命令 | 說明 |
| --- | --- |
| PingDevice |傳送 *ping* 給裝置以檢查是否運作 |
| StartTelemetry |傳送遙測以啟動裝置 |
| StopTelemetry |傳送遙測以停止裝置 |
| ChangeSetPointTemp |變更設定點值，在其周圍會產生隨機資料 |
| DiagnosticTelemetry |觸發裝置模擬器以傳送其他遙測值 (externalTemp) |
| ChangeDeviceState |變更裝置的擴充狀態屬性，並從裝置傳送裝置資訊訊息 |

解決方案後端的裝置命令通知是透過 IoT 中樞提供。

## IoT 中樞
[IoT 中樞][lnk-iothub]內嵌從裝置傳送至雲端的資料，並提供給 Azure 串流分析 (ASA) 作業。IoT 中樞也會代表裝置入口網站傳送命令到您的裝置。每個串流 ASA 作業使用不同的 IoT 中樞取用者群組，以從您的裝置讀取訊息串流。

## Azure 串流分析
在遠端監視解決方案中，[Azure 串流分析][lnk-asa] \(ASA) 會將透過 IoT 中樞所收到的裝置訊息分派至其他後端元件進行處理或儲存。不同的 ASA 作業會根據訊息內容執行特定的功能。

**作業 1：裝置資訊**會篩選來自傳入訊息串流的裝置資訊訊息，並將它們傳送到事件中樞端點。裝置會在啟動時傳送裝置資訊訊息，並且回應 **SendDeviceInfo** 命令。此作業使用下列查詢定義來識別**裝置資訊**訊息︰

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

此作業會將其輸出傳送到事件中樞進行進一步的處理。

**作業 2：規則**會針對每一裝置臨界值評估傳入氣溫和溼度遙測值。臨界值是在解決方案儀表板中提供的規則編輯器中設定。每個裝置/值組是依據時間戳記儲存在 blob 中，串流分析會讀取做為**參考資料**。工作會針對裝置的設定臨界值比較任何非空白值。如果超過 '>' 條件，作業會輸出**警示**事件，表示已超過臨界值，並且提供裝置、值和時間戳記值。此作業會使用下列查詢定義來識別應觸發警示的遙測訊息︰

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

作業會將其輸出傳送至事件中樞供進一步處理，並將每項警示的詳細資料儲存至 blob 儲存體，解決方案儀表板可以從該位置讀取警示資訊。

**作業 3：遙測**會以兩種方法來操作傳入裝置遙測串流。第一種方法會將裝置的所有遙測訊息都傳送至持續性 blob 儲存體以供長期儲存。第二種方法會透過五分鐘滑動視窗計算平均、最小和最大溼度值，並將此資料傳送至 Blob 儲存體。解決方案儀表板會從 blob 儲存體讀取遙測資料來填入圖表。此作業會使用下列查詢定義：

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## 事件中樞
**裝置資訊**和**規則** ASA 作業將資料輸出至事件中樞，以便可靠地轉送給在 WebJob 中執行的**事件處理器**。

## Azure 儲存體
此解決方案使用 Azure blob 儲存體來保存解決方案裝置中的所有原始和摘要遙測資料。儀表板會從 blob 儲存體讀取遙測資料來填入圖表。若要顯示警示，儀表板會從 blob 儲存體讀取當遙測值超過設定的臨界值時所記錄的資料。解決方案也使用 Blob 儲存體記錄您在儀表板中設定的臨界值。

## WebJobs
除了裝載裝置模擬器，在解決方案中的 WebJob 也託管在 Azure WebJob 中執行的**事件處理器**，它會處理裝置資訊訊息和命令回應。它會使用：

* 具有目前裝置資訊的裝置資訊訊息以更新裝置登錄 (儲存於 DocumentDB 資料庫)。
* 命令回應訊息以更新裝置命令歷程記錄 (儲存於 DocumentDB 資料庫)。

## DocumentDB
解決方案使用 DocumentDB 資料庫來儲存連接至解決方案的裝置相關資訊。這項資訊包括裝置的中繼資料和從儀表板傳送至裝置的命令記錄。

## Web 應用程式
### 遠端監視儀表板
Web 應用程式中的此頁面會使用 PowerBI javascript 控制項 (請參閱 [PowerBI 視覺效果儲存機制](https://www.github.com/Microsoft/PowerBI-visuals)) 以視覺化方式呈現裝置的遙測資料。解決方案會使用 ASA 遙測作業，將遙測資料寫入至 blob 儲存體。

### 裝置系統管理入口網站
此 Web 應用程式可讓您：

* 佈建新裝置。這個動作會設定唯一裝置識別碼並產生驗證金鑰。會將裝置相關資訊寫入至 IoT 中樞身分識別登錄以及解決方案特定的 DocumentDB 資料庫。
* 管理裝置屬性。這個動作包括檢視現有屬性和使用新的屬性更新。
* 將命令傳送至裝置。
* 檢視裝置的命令歷程記錄。
* 啟用和停用裝置。

## 後續步驟
下列 TechNet 部落格文章提供有關遠端監視預先設定之解決方案更多的詳細資料︰

* [IoT 套件 - 幕後 - 遠端監視](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT 套件 - 遠端監視 - 新增即時與模擬裝置](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

您可以繼續閱讀下列文章，了解如何開始使用 IoT 套件︰

* [將裝置連接至遠端監視預先設定方案][lnk-connect-rm]
* [azureiotsuite.com 網站的權限][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md

<!---HONumber=AcomDC_0817_2016-->