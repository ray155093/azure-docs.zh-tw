---
title: "Application Insights 使用的 IP 位址 | Microsoft Docs"
description: "Application Insights 所需的伺服器防火牆例外狀況"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 032d32be1719d4b459c88b13616bbef8d2d7be09
ms.openlocfilehash: d5d6065aa2958ebb858d421ea7c3eb2a198b2a80


---
# <a name="ip-addresses-used-by-application-insights"></a>Application Insights 使用的 IP 位址
[Azure Application Insights](app-insights-overview.md) 服務會使用一些 IP 位址。 如果您所監視的應用程式裝載於防火牆後面，您可能需要知道這些位址。

> [!NOTE]
> 雖然這些位址是靜態的，但可能隨時需要變更。
> 
> 

## <a name="outgoing-ports"></a>連出連接埠
您需要在伺服器防火牆開啟某些連出連接埠，以允許 Application Insights SDK 和/或狀態監視器將資料傳送至入口網站：

| 目的 | URL | IP | 連接埠 |
| --- | --- | --- | --- |
| 遙測 |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221 |443 |
| 即時計量串流 |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |

## <a name="status-monitor"></a>狀態監視器
狀態監視器組態 - 只有在進行變更時才需要。

| 目的 | URL | IP | 連接埠 |
| --- | --- | --- | --- |
| 組態 |`management.core.windows.net` | |`443` |
| 組態 |`management.azure.com` | |`443` |
| 組態 |`login.windows.net` | |`443` |
| 組態 |`login.microsoftonline.com` | |`443` |
| 組態 |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| 組態 |`auth.gfx.ms` | |`443` |
| 組態 |`login.live.com` | |`443` |
| 安裝 |`packages.nuget.org` | |`443` |

## <a name="hockeyapp"></a>HockeyApp
| 目的 | URL | IP | 連接埠 |
| --- | --- | --- | --- |
| 當機資料 |gate.hockeyapp.net |104.45.136.42 |80、443 |

## <a name="availability-tests"></a>可用性集合
這是用來執行 [可用性 Web 測試](app-insights-monitor-web-app-availability.md) 的位址清單。 如果您想要在您的應用程式上執行 Web 測試，但您的 Web 伺服器限於為特定用戶端提供服務，則您必須允許來自我們的可用性測試伺服器的連入流量。

為來自這些位址的連入流量開啟連接埠 80 (http) 和 443 (https)︰

```
13.106.106.20
13.106.106.21
13.106.106.22
13.106.106.23
13.106.106.24
13.106.106.25
13.106.106.26
13.106.106.27
13.106.106.28
13.106.106.29
157.55.14.43
157.55.14.44
157.55.14.47
157.55.14.49
157.55.14.50
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.64
157.55.14.65
202.89.228.57
202.89.228.67
202.89.228.68
202.89.228.69
207.46.14.51
207.46.14.52
207.46.14.55
207.46.14.56
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.63
207.46.14.64
207.46.14.65
207.46.14.67
207.46.14.68
207.46.56.57
207.46.56.58
207.46.56.59
207.46.56.61
207.46.56.62
207.46.56.63
207.46.56.64
207.46.56.67
207.46.71.37
207.46.71.38
207.46.71.51
207.46.71.52
207.46.71.54
207.46.71.55
207.46.71.57
207.46.71.58
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.157
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.162
207.46.98.169
207.46.98.170
207.46.98.171
207.46.98.172
213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.61
213.199.178.63
213.199.178.64
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
65.54.78.57
65.54.78.58
65.54.78.59
65.54.78.60
65.55.82.77
65.55.82.78
65.55.82.81
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
94.245.72.52
94.245.72.53
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38


```  

## <a name="data-access-api"></a>資料存取 API
| 目的 | URI | IP | 連接埠 |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |13.82.26.252<br/>40.76.213.73 |80,443 |
| API 文件 |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |13.82.24.149<br/>40.114.82.10 |80,443 |




<!--HONumber=Jan17_HO1-->


