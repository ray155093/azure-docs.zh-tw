---
title: "什麼是 Log Analytics？ | Microsoft Docs"
description: "Log Analytics 是 Operations Management Suite (OMS) 中的一項服務，可協助您收集和分析雲端和內部部署環境中的資源所產生的操作資料。  本文提供 Log Analytics 之不同元件的簡短概觀，以及詳細內容的連結。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 09a2b3ccd2048ab2638dd91d1905483f10d6d339


---
# <a name="what-is-log-analytics"></a>什麼是 Log Analytics？
Log Analytics 是 [Operations Management Suite \(OMS\)](../operations-management-suite/operations-management-suite-overview.md) 中的一項服務，可協助您收集和分析雲端和內部部署環境中的資源所產生的資料。 它可讓您在所有工作負載和伺服器之間，無論其實體位置為何，使用整合式搜尋和自訂儀表板輕易地分析數百萬筆記錄，提供您即時的深入資訊。

## <a name="log-analytics-components"></a>Log Analytics 元件
Log Analytics 的中心是裝載在 Azure 雲端的 OMS 儲存機制。  資料會藉由設定資料來源以及將方案加入訂用帳戶中，而從連接的來源收集到儲存機制。  資料來源和方案都會建立不同的記錄類型，它們具有自己的屬性集，但仍可以一起分析，以便查詢儲存機制。  這可讓您使用相同的工具和方法，來處理由不同來源收集的不同類型的資料。

![OMS 儲存機制](media/log-analytics-overview/overview.png)

連接的來源是指產生 Log Analytics 所收集資料的電腦和其他資源。  這可以包括安裝在直接連接的 [Windows](log-analytics-windows-agents.md) 和 [Linux](log-analytics-linux-agents.md) 電腦的代理程式，或[已連線的 System Center Operations Manager 管理群組](log-analytics-om-agents.md)中的代理程式。  Log Analytics 也可以從 [Azure 儲存體](log-analytics-azure-storage.md)收集資料。

[資料來源](log-analytics-data-sources.md) 是從每一個已連接來源收集的不同種類的資料。  這包括來自 [Windows](log-analytics-data-sources-windows-events.md) 和 Linux 代理程式的事件和[效能資料](log-analytics-data-sources-performance-counters.md)，以及例如 [IIS 記錄檔](log-analytics-data-sources-iis-logs.md)和[自訂文字記錄檔](log-analytics-data-sources-custom-logs.md)等來源。  您設定想要收集的每個資料來源，組態會自動傳遞到每一個已連接的來源。

## <a name="analyzing-log-analytics-data"></a>分析 Log Analytics 資料
與 Log Analytics 的大部分互動會透過 OMS 入口網站，它可在任何瀏覽器中執行，並讓您存取組態設定和多項工具，來分析及處理收集到的資料。  從入口網站，您可以利用[記錄搜尋](log-analytics-log-searches.md)建構查詢以分析收集的資料、您可以使用由最有價值之搜尋的圖形檢視所自訂的[儀表板](log-analytics-dashboards.md)，和提供額外功能和分析工具的[方案](log-analytics-add-solutions.md)。

![OMS 入口網站](media/log-analytics-overview/portal.png)

Log Analytics 提供快速擷取及彙總儲存機制中資料的查詢語法。  您可以建立並儲存 [記錄搜尋](log-analytics-log-searches.md) ，以直接在 OMS 入口網站中分析資料，或讓記錄搜尋自動執行，以在查詢的結果表示重要狀況時建立警示。

![記錄搜尋](media/log-analytics-overview/log-search.png)

為了快速取得整體環境健全狀況的圖形化檢視，您可以將儲存記錄搜尋的視覺效果加入到您的 [儀表板](log-analytics-dashboards.md)。   

![儀表板](media/log-analytics-overview/dashboard.png)

為了在 Log Analytics 外分析資料，您可以將資料從 OMS 儲存機制工具匯出到例如 [Power BI](log-analytics-powerbi.md) 或 Excel 的工具。  您也可以利用 [記錄搜尋 API](log-analytics-log-search-api.md) 建置運用 Log Analytics 資料的自訂方案，或與其他系統整合。

## <a name="solutions"></a>方案
方案會將功能加入 Log Analytics。  它們主要是在雲端執行，並提供 OMS 儲存機制中所收集資料的分析。 它們也可以定義要收集的新記錄類型，可以使用記錄搜尋進行分析，或是藉由 OMS 儀表板方案所提供的其他使用者介面進行分析。  

![資料追蹤方案](media/log-analytics-overview/change-tracking.png)

方案適用於各種功能，您可以輕鬆地瀏覽可用方案，並 [將它們從方案庫加入 OMS 工作區](log-analytics-add-solutions.md) 。  許多將會自動部署並立即開始運作，而有些則可能需要一些組態。

![方案庫](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-architecture"></a>Log Analytics 架構
Log Analytics 的部署需求非常少，因為中心元件託管於 Azure 雲端。  這包括儲存機制，以及可讓您關聯與分析所收集資料的服務。  可以從任何瀏覽器存取入口網站，因此不需要用戶端軟體。

您必須在 [Windows](log-analytics-windows-agents.md) 和 [Linux](log-analytics-linux-agents.md) 電腦上安裝代理程式，但已經是[連線的 SCOM 管理群組](log-analytics-om-agents.md)成員的電腦則不需要其他代理程式。  SCOM 代理程式將會繼續與管理伺服器通訊，管理伺服器會將其資料轉送至 Log Analytics。  不過，某些方案需要代理程式以便直接與 Log Analytics 通訊。  每個方案的文件將詳述其通訊需求。

當您 [註冊 Log Analytics](log-analytics-get-started.md)時，會建立一個 OMS 工作區。  您可以將工作區視為唯一的 OMS 環境，有其自己的資料儲存機制、資料來源和方案。 您可以在訂用帳戶中建立多個工作區以支援多個環境，例如生產環境和測試環境。

![Log Analytics 架構](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>後續步驟
* [申請免費的 Log Analytics 帳戶](log-analytics-get-started.md) ，在自己的環境中測試。
* 檢視可用來將資料收集到 OMS 儲存機制的不同 [資料來源](log-analytics-data-sources.md) 。
* [瀏覽方案庫中可用的方案](log-analytics-add-solutions.md) ，將功能加入 Log Analytics。




<!--HONumber=Nov16_HO2-->


