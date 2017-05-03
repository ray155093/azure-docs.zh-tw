---
title: "Azure Application Insights 遙測資料模型 | Microsoft Docs"
description: "Application Insights 資料模型概觀"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 52f700bd18da87a9a38e0a791d0ec3496f201e0a
ms.lasthandoff: 04/22/2017


---
# <a name="application-insights-telemetry-data-model"></a>Application Insights 遙測資料模型

Application Insights 會定義 Application Performance Management (APM) 的遙測資料模型。 此模型會將資料收集標準化，並啟用建立平台以及和語言無關的監視案例。 Application Insights 所收集的資料會建立一般應用程式執行模式的模型︰

![Application Insights 應用程式模型](./media/application-insights-data-model/application-insights-data-model.png)

應用程式的類型有兩種 — 具有端點可接收外部***要求***的應用程式 - Web 應用程式，以及會定期「喚醒」來處理儲存在某處之資料的應用程式 - WebJobs 或函式。 在這兩種情況下，我們將唯一執行稱為***作業***。 作業透過***例外狀況***會成功或失敗，或可能依賴其他服務/儲存體來執行其商務邏輯。 為了反映這些概念，Application Insights 資料模型會定義三種遙測類型︰[要求](./application-insights-data-model-request-telemetry.md)、[例外狀況](/application-insights-data-model-exception-telemetry.md)和[相依性](/application-insights-data-model-dependency-telemetry.md)。

一般而言，這些類型會由應用程式架構定義，而且會由 SDK 自動收集。 `ASP.NET MVC` 會在其模型檢視控制器配管中定義要求執行的概念 - 標記要求的開始和停止。 SQL 的相依性呼叫是由 `System.Data` 所定義。 HTTP 端點的呼叫是由 `System.Net` 所定義。 您可以使用自訂屬性和度量，將特定平台和架構所收集的遙測類型進行擴充。 不過，一些情況下您會報告自訂遙測。 您可能想要使用熟悉的檢測架構 (例如 `Log4Net` 或 `System.Diagnostics`) 來實作診斷記錄。 或者，您可能需要擷取使用者與您服務的互動，來分析使用模式。 Application Insights 會辨識三種其他的資料類型︰[追蹤](/application-insights-data-model-trace-telemetry.md)、[事件](/application-insights-data-model-event-telemetry.md)和[計量](/application-insights-data-model-metric-telemetry.md)來建立這些案例的模型。

Application Insights 遙測模型會定義將遙測與其所屬作業[相互關聯](/correlation.md)的方式。 例如，要求會發出 SQL Database 呼叫，並記錄診斷資訊。 您可以設定這些會將其固定回要求遙測之遙測項目的相互關聯內容。

## <a name="schema-improvements"></a>結構描述的增強功能

Application Insights 資料模型是簡單且基本但功能強大的方式，可建立您應用程式遙測的模型。 我們盡量保持模型可簡單且輕鬆地支援重要的案例，並擴充結構描述以供進階使用。

若要報告資料模型或結構描述的問題及建議，請使用 GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) 存放庫。

## <a name="next-steps"></a>後續步驟

- 查看 Application Insights 支援的[平台](/app-insights-platforms.md)。
- 了解如何[擴充和篩選遙測](/app-insights-api-filtering-sampling.md)。
- 使用[取樣](/app-insights-sampling.md)，根據資料模型將遙測量降到最低。

