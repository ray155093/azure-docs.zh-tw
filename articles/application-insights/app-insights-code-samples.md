---
title: "Application Insights：程式碼範例和逐步解說"
description: "您可以針對自己的應用程式調整的範例。"
services: application-insights
documentationcenter: windows
author: alancameronwills
manager: douge
ms.assetid: 586d0fb2-5286-449b-b327-5c9493632fc1
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 2a9cb8062e1dc505706c285269bbbd1eeab72064


---
# <a name="application-insights-code-samples-and-walkthroughs"></a>Application Insights：程式碼範例和逐步解說


這是程式碼範例的編譯，這些範例說明如何使用 [Azure Application Insights](app-insights-overview.md)。

## <a name="sdk-labs"></a>SDK 實驗室
[SDK 實驗室](https://www.myget.org/gallery/applicationinsights-sdk-labs) 是實驗性 NuGet 封裝的集合，您可以將其安裝及解除安裝，做為 Application Insights SDK 的新增項目。 親身體驗並提供意見反應！

## <a name="web-services"></a>Web 服務
* [將遙測功能加入 Azure Web 和背景工作角色](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)(英文)。
* [Windows 8.1 通用應用程式](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal/)

## <a name="continuous-export"></a>連續匯出
若要使用自己的工具分析遙測資料，請 [將遙測資料匯出](app-insights-export-telemetry.md) 至儲存體，並在其中進行剖析和處理。

* [匯出至 Power BI](app-insights-export-power-bi.md) 
* [使用背景工作角色剖析匯出的資料](app-insights-code-sample-export-telemetry-sql-database.md)
* [使用串流分析匯出至 SQL](app-insights-code-sample-export-sql-stream-analytics.md)
* [針對 Microsoft Dynamics CRM 啟用遙測](app-insights-sample-mscrm.md)

## <a name="automate-tasks"></a>自動化工作
* [使用 PowerShell 將 Azure 診斷傳送至 Application Insights](app-insights-powershell.md)
* [建立新 Application Insights 資源的指令碼](app-insights-powershell-script-create-resource.md)
* [使用 PowerShell 將 Azure 診斷傳送至 Application Insights](app-insights-powershell-azure-diagnostics.md)




<!--HONumber=Nov16_HO3-->


