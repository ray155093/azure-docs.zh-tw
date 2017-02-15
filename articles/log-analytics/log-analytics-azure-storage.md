---
title: "在 Log Analytics 中收集 Azure 儲存體資料概觀 | Microsoft Docs"
description: "Azure 資源可以將記錄檔和度量寫入 Azure 儲存體帳戶，通常是使用 Azure 診斷。 Log Analytics 可以編製此資料的索引，將它變成可搜尋的資料。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a160030ab51799199fc6df08133f811d4987feb


---
# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>在 Log Analytics 中收集 Azure 儲存體資料概觀
許多 Azure 資源能夠將記錄檔和度量寫入 Azure 儲存體帳戶。 Log Analytics 可以取用此資料，讓您輕鬆地監視您的 Azure 資源。

若要寫入 Azure 儲存體，資源可能使用 Azure 診斷，或以它自己的方式寫入資料。 此資料可能以各種不同格式寫入下列其中一個位置︰

* Azure 資料表
* Azure Blob
* EventHub

Log Analytics 支援使用 [Azure 診斷記錄](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)來寫入資料的 Azure 服務。 此外，Log Analytics 也支援以不同格式和位置來輸出記錄檔和度量的其他服務。  

> [!NOTE]
> 當您將診斷傳送到儲存體帳戶時，或是當 Log Analytics 從您的儲存體帳戶讀取資料時，您將需要支付儲存體和交易的一般 Azure 資料費用。
> 
> 

![Azure 儲存體圖表](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>支援的 Azure 資源
Log Analytics 可以收集下列 Azure 資源的資料︰

| 資源類型 | 記錄檔 (診斷類別) | Log Analytics 解決方案 |
| --- | --- | --- |
| Application Insights |Availability <br> 自訂事件 <br> 例外狀況 <br> 要求 <br> |Application Insights (預覽) |
| 自動化 <br> Microsoft.Automation/AutomationAccounts |JobLogs <br> JobStreams |AzureAutomation (預覽) |
| 金鑰保存庫 <br> Microsoft.KeyVault/vaults |AuditEvent |KeyVault (預覽) |
| 應用程式閘道 <br> Microsoft.Network/ApplicationGateways |ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog |AzureNetworking (預覽) |
| 網路安全性群組 <br> Microsoft.Network/NetworkSecurityGroups |NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter |AzureNetworking (預覽) |
| Service Fabric |ETWEvent <br> 運作事件 <br> Reliable Actor 事件 <br> Reliable Service 事件 |ServiceFabric (預覽) |
| 虛擬機器 |Linux Syslog <br> Windows 事件 <br> IIS 記錄 <br> Windows ETWEvent |無 |
| Web 角色 <br> 背景工作角色 |Linux Syslog <br> Windows 事件 <br> IIS 記錄 <br> Windows ETWEvent |無 |

> [!NOTE]
> 若要監視 Azure 虛擬機器 (Linux 和 Windows)，我們建議您安裝 [Log Analytics VM 擴充](log-analytics-azure-vm-extension.md)。 相較於使用寫入儲存體的診斷，代理程式可讓您更深入洞察虛擬機器。
> 
> 

您可以在 [意見反應頁面](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy)進行投票，幫助我們為 OMS 排定其他記錄檔的分析優先順序。

* 若要深入了解 Log Analytics 如何從支援 [Azure 診斷記錄](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)的 Azure 服務讀取記錄，請參閱[使用 Log Analytics 分析 Azure 診斷記錄](log-analytics-azure-storage-json.md)：
  * Azure 金鑰保存庫
  * Azure 自動化
  * 應用程式閘道
  * 網路安全性群組
* 請參閱[對 IIS 使用 Blob 儲存體，對事件使用表格儲存體](log-analytics-azure-storage-iis-table.md)，以深入了解 Log Analytics 如何讀取 Azure 服務 (將診斷寫入表格儲存體) 的記錄，或如何讀取寫入 Blob 儲存體的 IIS 記錄，包括︰
  * Service Fabric
  * Web 角色
  * 背景工作角色
  * 虛擬機器

## <a name="next-steps"></a>後續步驟
* [使用 Log Analytics 分析 Azure 診斷記錄](log-analytics-azure-storage-json.md)，從 Azure 服務讀取記錄，這些服務以 JSON 格式將診斷寫入 Blob 儲存體。
* [對 IIS 使用 Blob 儲存體，對事件使用表格儲存體](log-analytics-azure-storage-iis-table.md)，以讀取 Azure 服務 (將診斷寫入表格儲存體) 的記錄，或讀取寫入 Blob 儲存體的 IIS 記錄。
* [啟用解決方案](log-analytics-add-solutions.md) 以提供資料的深入見解。
* [使用搜尋查詢](log-analytics-log-searches.md) 以分析資料。




<!--HONumber=Nov16_HO3-->


