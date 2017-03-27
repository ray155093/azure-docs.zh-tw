---
title: "Azure 監視器概觀 | Microsoft Docs"
description: "Azure 監視器會收集用於警示、webhook、自動調整，以及自動化的統計資料。 文章也會列出其他 Microsoft 監視選項。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ca55f1c295d99ff92abaef1a919d4c90d3c33b8a
ms.lasthandoff: 03/09/2017


---

# <a name="overview-of-azure-monitor"></a>Azure 監視器的概觀
本文提供監視 Azure 資源的概念性概觀， 以及特定資源類型詳細資訊的指標。  如需從非 Azure 角度監視應用程式的高層級資訊，請參閱 [監視和診斷指引](../best-practices-monitoring.md)。

Azure 監視器的影片逐步解說位於  
[開始使用 Azure 監視器](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor)。 在[探索 Microsoft Azure 監視和診斷](https://channel9.msdn.com/events/Ignite/2016/BRK2234)可取得額外影片，該影片說明您可以使用 Azure 監視器的案例。  

雲端應用程式相當複雜，且具有許多移動組件。 監視會提供資料，以確保應用程式持續運作並以健全的狀態執行。 它也可協助您預防潛在問題，或是針對過去所發生的問題進行疑難排解。 除此之外，您還可以使用監視資料來取得應用程式的深入解析。 這些知識可協助您提升應用程式效能或維護性，或是將原本需要手動介入的動作自動化。

下圖顯示 Azure 監視的概念式檢視，包括您可以收集的記錄檔類型，以及可以使用該資料進行的作業。   

![非計算資源的監視與診斷模型](./media/monitoring-overview/Monitoring_Azure_Resources-compute_v4.png)

圖 1：非計算資源的監視與診斷概念模型

<br/>

![計算資源的監視與診斷模型](./media/monitoring-overview/Monitoring_Azure_Resources-non-compute_v4.png)

圖 2：計算資源的監視與診斷概念模型

## <a name="monitoring-sources"></a>監視來源
### <a name="activity-logs"></a>活動記錄檔
您可以搜尋活動記錄檔 (之前稱為「作業記錄」或「稽核記錄」)，以取得 Azure 基礎結構所見之資源的相關資訊。 此記錄檔包含資源的建立或終結時間等資訊。  

### <a name="host-vm"></a>主機 VM
**僅限計算**

某些計算資源 (例如雲端服務、虛擬機器及 Service Fabric) 具有它們會與之互動的專用主機 VM。 主機 VM 等同於 Hyper-V Hypervisor 模型中的根 VM。 在此情況下，在客體 OS 之外，您可以只收集主機 VM 上的度量。  

針對其他 Azure 服務，您的資源和特定主機 VM 之間並不一定具有 1:1 的對應，因此沒有主機 VM 度量可用。

### <a name="resource---metrics-and-diagnostics-logs"></a>資源 - 計量和診斷記錄檔
可收集的計量視資源類型而異。 例如，虛擬機器會提供磁碟 IO 和百分比 CPU 的統計資料。 但服務匯流排佇列就沒有這些統計資料，而是改為提供佇列大小和訊息輸送量等計量。

針對計算資源，您可以在客體 OS 及診斷模組 (如 Azure 診斷) 上取得計量。 Azure 診斷可以協助收集診斷資料，並將該資料路由到其他的位置 (包括 Azure 儲存體)。

[支援的度量](monitoring-supported-metrics.md)中有目前可收集的度量清單。

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>應用程式 - 診斷記錄檔、應用程式記錄檔及計量
**僅限計算**

應用程式能以計算模型在客體 OS 上執行。 它們會發出自己的記錄檔和計量集合。

計量類型包括

* 效能計數器
* 應用程式記錄檔
* Windows 事件記錄檔
* .NET 事件來源
* IIS 記錄檔
* 以資訊清單為基礎的 ETW
* 損毀傾印
* 客戶錯誤記錄檔

## <a name="uses-for-monitoring-data"></a>監視資料的用途
### <a name="visualize"></a>視覺化
以圖形和圖表視覺化您的監視資料，協助您透過比查看資料本身更快的速度找出趨勢。  

幾個視覺化方法包括︰

* 使用 Azure 入口網站
* 將資料路由至 Azure Application Insights
* 將資料路由至 Microsoft PowerBI
* 將資料路由至協力廠商視覺化工具，無論是透過即時串流，或是讓該工具讀取 Azure 儲存體中的封存

### <a name="archive"></a>封存
監視資料通常會寫入 Azure 儲存體，並持續保存在那裡，直到您將它刪除為止。

以下為使用此資料的幾個方式：

* 資料寫入後，您可以讓其他位於 Azure 之內或之外的工具讀取該資料並加以處理。
* 您可以於本機將該資料下載到本機封存，或是在雲端變更您的保留原則，以延長資料的保留時間。  
* <a name="you-leave-the-data-in-azure-storage-indefinitely-though-you-have-to-pay-for-azure-storage-based-on-the-amount-of-data-you-keep"></a>您將資料無限期的放在 Azure 儲存體中，則必須根據您所保留的資料量支付 Azure 儲存體費用。
  -

### <a name="query"></a>查詢
您可以使用 Azure 監視器 REST API、跨平台命令列介面 (CLI) 命令、PowerShell Cmdlet 或 .NET SDK 來存取系統或是 Azure 儲存體中的資料

範例包括：

* 針對您所撰寫的自訂監視應用程式取得資料
* 建立自訂查詢並將該資料傳送到協力廠商應用程式。

### <a name="route"></a>路由
您可以將監視資料即時串流到其他位置。

範例包括：

* 傳送到 Application Insights，讓您可以使用那裡的視覺化工具。
* 傳送到事件中樞，讓您可以路由到協力廠商工具以執行即時分析。

### <a name="automate"></a>自動化
您可以使用監視資料來觸發警示或甚至觸發整個程序。
範例包括：

* 使用資料來根據應用程式負載，自動向上或向下調整計算執行個體。
* 在某個計量超過預先定義的臨界值時傳送電子郵件。
* 呼叫 Web URL (webhook) 以在 Azure 之外的系統中執行動作
* 在 Azure 自動化中啟動 Runbook 以執行各種工作

## <a name="methods-of-use"></a>使用方法
一般來說，您可以使用下列其中一個方法操作資料追蹤、路由及擷取。 並非所有方法都適用所有動作或資料類型。

* [Azure 入口網站](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [跨平台命令列介面 (CLI)](insights-cli-samples.md)
* [REST API](https://msdn.microsoft.com/library/dn931943.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Azure 的監視供應項目
Azure 有提供監視您服務 (從裸機基礎結構到應用程式遙測) 的供應項目。 最好的監視策略，便是結合全部三種供應項目，以獲得服務健全狀況的完整、詳細深入解析。

* [Azure 監視器](http://aka.ms/azmondocs) - 針對來自 Azure 基礎結構 (活動記錄檔) 及每個個別的 Azure 資源 (診斷記錄檔) 的資料，提供視覺化、查詢、路由、警示、自動調整及自動化功能。 此文章為 Azure 監視器文件的一部分。 「Azure 監視器」這個名稱於 2016 年 9 月 25 日在 Ignite 發表。  先前的名稱是「Azure Insights」。  
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) - 針對您服務的應用程式層問題提供豐富的偵測和診斷，並完美整合在來自 Azure 監視的資料之上。 它是 App Service Web Apps 的預設診斷平台。  您可以將其他服務資料路由至此。  
* [Operations Management Suite](https://www.microsoft.com/oms/) 中的 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) - 為內部部署和協力廠商雲端式基礎結構 (如 AWS) 提供 Azure 資源之外的全面性 IT 管理解決方案。  Azure 監視器中的資料可以直接路由至 Log Analytics，以便您可以在同一個地方看到整個環境的度量與記錄。     

## <a name="next-steps"></a>後續步驟
深入了解

* [Ignite 2016 上的 Azure 監視器影片](https://myignite.microsoft.com/videos/4977)
* [開始使用 Azure 監視器](monitoring-get-started.md)
* [Azure 診斷](../azure-diagnostics.md) ，如果您正在嘗試診斷您的雲端服務、虛擬機器或 Service Fabric 應用程式中的問題。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) ，如果您正在嘗試診斷 App Service Web 應用程式中的問題。
* [疑難排解 Azure 儲存體](../storage/storage-e2e-troubleshooting.md) 
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 及 [Operations Management Suite](https://www.microsoft.com/oms/)

