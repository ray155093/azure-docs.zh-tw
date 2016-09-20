<properties
	pageTitle="Microsoft Azure 虛擬機器、雲端服務和 Web Apps 的自動調整概觀 | Microsoft Azure"
	description="Microsoft Azure 的自動調整概觀。適用於虛擬機器、雲端服務和 Web Apps。"
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
	ms.date="08/30/2016"
	ms.author="robb"/>

# Microsoft Azure 虛擬機器、雲端服務和 Web Apps 的自動調整概觀

本文說明何謂 Microsoft Azure 自動調整、其優點，並引導您開始使用它。

Azure Insights 自動調整僅適用於

* [虛擬機器擴展集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)
* [雲端服務](https://azure.microsoft.com/services/cloud-services/)
* [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)
  
   
>[AZURE.NOTE] Azure 有兩種自動調整方法。舊版自動調整適用於虛擬機器 (可用性設定組)。這項功能的支援能力有限，建議您移轉至 VM 擴展集，以獲得更快、更可靠的自動調整支援。本文包含如何使用舊版技術的連結。


## 何謂自動調整 

自動調整可讓您執行適當數量的資源來處理應用程式的負載，不會因為資源閒置而浪費金錢。它可讓您根據一組規則自動新增或移除計算資源。圖 1 顯示此概念。

![說明自動調整概念](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

**圖 1︰說明自動調整概念**

自動調整只會水平調整，亦即增加 (相應放大) 或減少 (相應縮小) VM 執行個體數目。在雲端的情況下，水平調整會更有彈性，因為它可讓您有機會執行數千台 VM 來處理負載。另一種調整類型是垂直調整。垂直調整會保持相同數量的 VM，但會讓 VM 的能力變強 (相應增加) 或變弱 (相應減少)。能力是以記憶體、CPU 速度、磁碟空間等項目來加以測量。垂直調整有更多限制。它取決於是否可以使用較大的硬體，因此會依區域而異，而且很快就會達到上限。垂直調整通常也需要 VM 停止和啟動。如需詳細資訊，請參閱[使用 Azure 自動化垂直調整 Azure 虛擬機器大小](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md)。


自動調整會要求您建立關於何時要相應放大或相應縮小資源的規則。可設定來控制調整動作的準則包括

* 要執行的執行個體數目**下限**和**上限**。下限可確保應用程式會永遠執行，上限則可控制您的成本。
* 自動調整**規則或條件**。可以依度量或排程來調整。
* **冷卻時間**，在發生自動調整事件之後等候的時間量，此時間過後才允許另一個自動調整事件發生。這段時間是為了防止稱為「Flapping」的狀態，當 VM 在幾分鐘的時間內快速新增和移除時就會發生此狀態。啟動或停止 VM 需要成本。Flapping 並不會節省成本，而且啟動和停止的 VM 無法執行任何有用的處理，因此比讓 VM 繼續執行還遭。

   
可設定值的完整清單可在[自動調整 REST API](https://msdn.microsoft.com/library/dn931928.aspx) 中取得。


## 概念圖  
圖 2 顯示自動調整的概念性概觀並接著說明圖表的各個部分。

![Add alert](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)

**圖 2︰自動調整程序概觀**

## 資源度量 
資源會發出度量，並於稍後由規則處理。度量來自不同的方法。VM 擴展集使用來自 Azure 診斷代理程式的遙測資料，而 Web 應用程式和雲端服務的遙測則直接來自 Azure 基礎結構。一些常用的統計資料包括 CPU 使用率、記憶體使用量、執行緒計數、佇列長度和磁碟使用量。如需可使用哪些遙測資料的清單，請參閱[自動調整的常用度量](insights-autoscale-common-metrics.md)。

 
## 時間
排程型規則是以 UTC 為基礎。設定規則時必須正確設定時區。

## 自動調整規則
圖中只顯示一個自動調整規則，但您可以擁有多個。您可以視情況需要建立複雜的重疊規則。規則類型包含
 
 - **度量型** - 例如，當 CPU 使用率高於 50% 時執行此動作。
 - **時間型** - 例如，在指定時區的每星期六上午 8 點觸發 Webhook。

 
## 自動調整動作與自動化

規則可以觸發一個或多個類型的動作。

- **調整** - 將 VM 相應放大或相應縮小
- **電子郵件** - 傳送電子郵件給訂用帳戶的管理員和共同管理員，以及您指定的其他電子郵件地址
- **透過 Webhook 自動執行** - 呼叫 Webhook，以觸發 Azure 內部或外部的多個複雜動作。在 Azure 內部，您可以啟動 Azure 自動化 Runbook、Azure 函數或 Azure 邏輯應用程式。Azure 外部的範例第三方 URL 包含 Slack 和 Twilio 等服務。


## Resource Manager 規則的詳細資料

在 Azure Resource Manager 範本中，自動調整規則具有下列結構。

![Azure Resource Manager 自動調整規則結構](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure2.png)

您可以列出多個設定檔。每個設定檔都可以有多個規則。通知方法和位置會包含在設定檔之後。通知範例包括具有 URI 的 Webhook，或具有電子郵件地址的電子郵件。

如需程式碼範例，請參閱

* [針對 VM 擴展集使用 Resource Manager 範本進行進階自動調整設定](insights-advanced-autoscale-virtual-machine-scale-sets.md)
* [自動調整 REST API](https://msdn.microsoft.com/library/dn931953.aspx)

## 存取方法 
您可以透過下列途徑設定自動調整規則

- Azure 入口網站
- PowerShell
- 通用程式庫介面 (CLI)
- Insights REST API

## 自動調整逐步解說

- [調整雲端服務](../cloud-services/cloud-services-how-to-scale-portal.md)
- [調整 Web Apps](insights-how-to-scale.md)
- [調整傳統的虛擬機器可用性設定組](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/)
- [在 Windows 中調整 VM 擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)
- [在 Linux 中調整 VM 擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md)
- [針對 VM 擴展集使用 Resource Manager 範本進行進階自動調整設定](insights-advanced-autoscale-virtual-machine-scale-sets.md)

## 後續步驟

若要深入了解自動調整，請使用先前所列的＜自動調整逐步解說＞或參閱下列資源︰

- [Azure Insights 自動調整的常用度量](insights-autoscale-common-metrics.md)
- [Azure Insights 自動調整的最佳做法](insights-autoscale-best-practices.md)
- [使用自動調整動作傳送電子郵件和 Webhook 警示通知](insights-autoscale-to-webhook-email.md)
- [自動調整 REST API](https://msdn.microsoft.com/library/dn931953.aspx) -　請參閱每個欄位和值的完整 API 和意義
- [排解虛擬機器擴展集自動調整的問題](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

<!----HONumber=AcomDC_0907_2016-->