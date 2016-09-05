<properties
	pageTitle="Microsoft Azure 中的監視概觀 | Microsoft Azure"
	description="Microsoft Azure 中監視與診斷的最上層概觀，包括警示、webhook、自動調整等等。"
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/03/2016"
	ms.author="robb"/>

# Microsoft Azure 中的監視概觀

這篇文章提供 Azure 中監視資源的概念性概觀，以及特定資源類型詳細資訊的指標。如需從非 Azure 角度監視應用程式的高層級資訊，請參閱[監視和診斷指引](../best-practices-monitoring.md)。

雲端應用程式相當複雜，且具有許多移動組件。監視功能可以讓您確保應用程式持續運作並以健全的狀態執行，同時追蹤重要計量以預防潛在問題，或是針對過去所發生的問題進行疑難排解。除此之外，您可以使用監視資料將某些原本需要手動介入的動作自動化，並取得應用程式的深入解析以協助您提升它的效能和維護性。

下圖顯示 Azure 監視的邏輯檢視。其基礎實作將會更為複雜。下圖顯示您可以收集的記錄檔類型，以及使用該資料的方式。

![針對非計算資源的監視與診斷邏輯模型](./media/monitoring-overview/monitoring-azure-resources-non-compute.png)

圖 1：針對非計算資源的監視與診斷邏輯模型

<br/>

![針對計算資源的監視與診斷邏輯模型](./media/monitoring-overview/monitoring-azure-resources-compute.png)

圖 2：針對計算資源的監視與診斷邏輯模型


## 監視來源
### 基礎結構稽核 (活動) 記錄檔
您可以取得由 Azure 基礎結構所見、關於您資源的稽核記錄檔 (有時被稱為作業或活動記錄檔)。這些記錄檔包含如資源的建立或終結時間等資訊。

### 主機 VM
**僅限計算**

您目前無法在主機 VM 上收集計量，但基於完整性我們也將它列在這裡。

某些計算資源 (例如雲端服務、虛擬機器及 Service Fabric) 具有它們會與之互動的專用主機 VM。主機 VM 等同於 Hyper-V Hypervisor 模型中的根 VM。

針對其他 Azure 服務，您的資源和特定主機 VM 之間並不一定具有 1:1 的對應。


### 資源 - 計量和診斷記錄檔
可收集的計量視資源類型而異。例如，虛擬機器會提供磁碟 IO 和百分比 CPU 的統計資料，但服務匯流排佇列提供這些資料並沒什麼意義，而是改為提供如佇列大小和訊息輸送量等計量。

針對計算資源，您可以在客體 OS 及診斷模組 (如 Azure 診斷) 上取得計量。Azure 診斷可以協助收集診斷資料，並將該資料路由到其他的位置 (包括 Azure 儲存體)。

### 應用程式 - 診斷記錄檔、應用程式記錄檔及計量
**僅限計算**

應用程式能以計算模型在客體 OS 上執行。它們會發出自己的記錄檔和計量集合。

計量類型包括

- 效能計數器
- 應用程式記錄檔
- Windows 事件記錄檔
- .NET 事件來源
- IIS 記錄檔
- 以資訊清單為基礎的 ETW
- 損毀傾印
- 客戶錯誤記錄檔


## 監視資料的用途

### 視覺化
以圖形和圖表視覺化您的監視資料，協助您透過比查看資料本身更快的速度找出趨勢。執行這項作業的幾個方式包括：

- 使用 Azure 入口網站
- 將資料路由至 Azure Application Insights
- 將資料路由至 PowerBI
- 將資料路由至協力廠商視覺化工具，無論是透過即時串流，或是讓該工具讀取 Azure 儲存體中的封存

### 封存
監視資料通常會寫入 Azure 儲存體，並持續保存在那裡，直到您將它刪除為止。

以下為使用此資料的幾個方式：

- 資料寫入後，您可以讓其他位於 Azure 之內或之外的工具讀取該資料並加以處理。
- 您可以於本機將該資料下載到本機封存，或是在雲端變更您的保留原則，以延長資料的保留時間。
- 您可以將該資料無限期地保留在 Azure 儲存體中。(請注意，您必須根據您所保留的資料量支付 Azure 儲存體費用)。

### 查詢
您可以使用 Insights REST API、通用語言介面 (CLI) 命令、PowerShell Cmdlet，或是 .NET SDK 來存取系統或是 Azure 儲存體中的資料，範例包括：

-  針對您所撰寫的自訂監視應用程式取得資料
-  建立自訂查詢並將該資料傳送到協力廠商應用程式。

### 路由
您可以將監視資料即時串流到其他位置。範例包括：

- 傳送到 Application Insights，讓您可以使用那裡的視覺化工具。
- 傳送到事件中樞，讓您可以路由到協力廠商工具以執行即時分析。

### 自動化
您可以使用監視資料來觸發事件或甚至觸發整個程序，範例包括：

- 使用資料來根據應用程式負載，自動向上或向下調整計算執行個體。
- 在某個計量超過預先定義的臨界值時傳送電子郵件。
- 呼叫 Web URL (webhook) 以在 Azure 之外的系統中執行動作
- 在 Azure 自動化中啟動 Runbook 以執行各種工作



## 使用方法
一般來說，您可以使用下列其中一個方法來管理要追蹤的項目，以及應該去哪裡擷取資料。並非所有方法都適用所有動作。

- [Azure 入口網站](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)
- [通用程式庫介面 (CLI)](insights-cli-samples.md)
- [REST API](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## Azure 的監視供應項目
Azure 有提供監視您服務 (從裸機基礎結構到應用程式遙測) 的供應項目。最好的監視策略，便是結合全部三種供應項目，以獲得服務健全狀況的完整、詳細深入解析。

- [Azure Insights (平台) 監視](http://aka.ms/azmondocs) - 針對來自 Azure 基礎結構 (稽核記錄檔) 及每個個別的 Azure 資源 (診斷記錄檔) 的資料，提供視覺化、查詢、路由、警示、自動調整及自動化功能。此文章為 Azure Insights 文件的一部分。
- **[Application Insights](https://azure.microsoft.com/documentation/services/application-insights/)** - 針對您服務的應用程式層問題提供豐富的偵測和診斷，並完美整合在來自 Azure 監視的資料之上。
- **[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)** 中的 **[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/)** - 為內部部署和雲端式基礎結構提供全面性的 IT 管理解決方案，包括來自您 Azure 資源的記錄檔搜尋和分析。


## 後續步驟
深入了解

- [Azure Insights](http://aka.ms/azmondocs)，請探索連結頁面左側目錄中的連結，以及頁面中的影片。
- [Azure 診斷](../azure-diagnostics.md)，如果您正在嘗試診斷您的雲端服務、虛擬機器或 Service Fabric 應用程式中的問題。
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/)，如果您正在嘗試診斷 App Service Web 應用程式中的問題。
- [針對 Azure 儲存體進行疑難排解](../storage/storage-e2e-troubleshooting.md)，如果您正在使用儲存體 Blob、資料表或佇列
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 及 [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)

<!---HONumber=AcomDC_0824_2016-->