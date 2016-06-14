<properties
	pageTitle="RBAC：內建角色 | Microsoft Azure"
	description="本主題說明角色型存取控制 (RBAC) 的內建角色。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/20/2016"
	ms.author="kgremban"/>

#RBAC：內建角色

Azure 角色型存取控制 (RBAC) 會隨附三個內建的角色，供您指派給使用者、群組與服務。您無法修改內建角色定義。不過，您可以建立 [Azure RBAC 中的自訂角色](role-based-access-control-custom-roles.md)以符合您組織的特定需求。

## Azure 中的角色

下表提供內建角色的簡短描述。按一下角色名稱，即可查看該角色的詳細 **actions** 和 **notactions** 清單。**actions** 屬性指定了 Azure 資源上允許的動作。動作字串可以使用萬用字元。**notactions** 屬性指定了從允許的動作中排除的動作。

>[AZURE.NOTE] Azure 角色定義不斷地演變。本文盡可能保持最新內容，但您永遠可以在 Azure PowerShell 中找到最新的角色定義。適用時，請使用 Cmdlet `(get-azurermroledefinition "<role name>").actions` 或 `(get-azurermroledefinition "<role name>").notactions`。

| 角色名稱 | 說明 |
| --------- | ----------- |
| [API 管理服務參與者](#api-management-service-contributor) | 可以管理 API 管理服務 |
| [Application Insights 元件參與者](#application-insights-component-contributor) | 可以管理 Application Insights 元件 |
| [自動化運算子](#automation-operator) | 能夠啟動、停止、暫停和繼續工作 |
| [BizTalk 參與者](#biztalk-contributor) | 可以管理 BizTalk 服務 |
| [ClearDB MySQL DB 參與者](#cleardb-mysql-db-contributor) | 可以管理 ClearDB MySQL 資料庫 |
| [參與者](#contributor) | 可以管理存取以外的所有內容。 |
| [Data Factory 參與者](#data-factory-contributor) | 可以管理資料處理站 |
| [DevTest Labs 使用者](#devtest-labs-user) | 可以檢視所有項目，並連接、啟動、重新啟動和關閉虛擬機器 |
| [DocumentDB 帳戶參與者](#documentdb-account-contributor) | 可以管理 DocumentDB 帳戶 |
| [Intelligent Systems 帳戶參與者](#intelligent-systems-account-contributor) | 可以管理 Intelligent Systems 帳戶 |
| [網路參與者](#network-contributor) | 可以管理網路資源 |
| [New Relic APM 帳戶參與者](#new-relic-apm-account-contributor) | 可以管理 New Relic 應用程式效能管理帳戶和應用程式 |
| [擁有者](#owner) | 可以管理所有項目，包括存取 |
| [讀取者](#reader) | 可以檢視所有項目，但是無法進行變更 |
| [Redis 快取參與者](#redis-cache-contributor]) | 可以管理 Redis 快取 |
| [排程器工作集合參與者](#scheduler-job-collections-contributor) | 可以管理排程器工作集合 |
| [搜尋服務參與者](#search-service-contributor) | 可以管理搜尋服務 |
| [安全性管理員](#security-manager) | 可以管理安全性元件、安全性原則及虛擬機器 |
| [SQL DB 參與者](#sql-db-contributor) | 可以管理 SQL 資料庫，但是無法管理它們的安全性相關原則 |
| [SQL 安全性管理員](#sql-security-manager) | 可以管理 SQL Server 和資料庫的安全性相關原則 |
| [SQL Server 參與者](#sql-server-contributor) | 可以管理 SQL Server 和資料庫，但是無法管理它們的安全性相關原則 |
| [傳統儲存體帳戶參與者](#classic-storage-account-contributor) | 可以管理傳統儲存體帳戶 |
| [儲存體帳戶參與者](#storage-account-contributor) | 可以管理儲存體帳戶 |
| [使用者存取系統管理員](#user-access-administrator) | 可以管理 Azure 資源的使用者存取 |
| [傳統虛擬機器參與者](#classic-virtual-machine-contributor) | 可以管理傳統虛擬機器，但是無法管理它們連接的虛擬網路或儲存體帳戶 |
| [虛擬機器參與者](#virtual-machine-contributor) | 可以管理虛擬機器，但是無法管理它們連接的虛擬網路或儲存體帳戶 |
| [傳統網路參與者](#classic-network-contributor) | 可以管理傳統虛擬網路和保留 IP |
| [Web 方案參與者](#web-plan-contributor) | 可以管理 Web 方案 |
| [網站參與者](#website-contributor) | 可以管理網站，但是不能管理它們連接的 Web 方案 |

## 角色權限
下表描述賦予每個角色的特定權限。這可以包括授與權限的 **Actions** 和限制權限的 **NotActions**。

### API 管理服務參與者
可以管理 API 管理服務

| **動作** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | 建立和管理 API 管理服務 |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取角色和角色指派 |
| Microsoft.Support/* | 建立和管理支援票證 |

### Application Insights 元件參與者
可以管理 Application Insights 元件

| **動作** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Insights/components/* | 建立和管理 Insights 元件 |
| Microsoft.Insights/webtests/* | 建立和管理 Web 測試 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 自動化運算子
能夠啟動、停止、暫停和繼續工作

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Automation/automationAccounts/jobs/read | 讀取自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/resume/action | 繼續自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/stop/action | 停止自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/streams/read | 讀取自動化帳戶作業串流 |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | 暫停自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/write | 撰寫自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobSchedules/read | 讀取自動化帳戶工作排程 |
| Microsoft.Automation/automationAccounts/jobSchedules/write | 讀取自動化帳戶工作排程 |
| Microsoft.Automation/automationAccounts/read | 讀取自動化帳戶 |
| Microsoft.Automation/automationAccounts/runbooks/read | 讀取自動化 Runbook |
| Microsoft.Automation/automationAccounts/schedules/read | 讀取自動化帳戶排程 |
| Microsoft.Automation/automationAccounts/schedules/write | 撰寫自動化帳戶排程 |
| Microsoft.Insights/components/* | 建立和管理 Insights 元件 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |

### BizTalk 參與者
可以管理 BizTalk 服務

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.BizTalkServices/BizTalk/* | 建立和管理 BizTalk 服務 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |

### ClearDB MySQL DB 參與者
可以管理 ClearDB MySQL 資料庫

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |
| successbricks.cleardb/databases/* | 建立和管理 ClearDB MySQL 資料庫 |

### 參與者
可以管理存取以外的所有內容

| **動作** ||
| ------- | ------ |
| * | 建立和管理所有類型的資源 |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Write | 無法建立角色和角色指派 |
| Microsoft.Authorization/*/Delete | 無法刪除角色和角色指派 |

### Data Factory 參與者
可以管理資料處理站

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.DataFactory/dataFactories/* | 管理 Data Factory |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |

### DevTest Labs 使用者
可以檢視所有項目，並連接、啟動、重新啟動和關閉虛擬機器

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Compute/availabilitySets/read | 讀取可用性設定組的屬性 |
| Microsoft.Compute/virtualMachines/*/read | 讀取虛擬機器的屬性 (VM 大小、執行階段狀態、VM 擴充功能等) |
| Microsoft.Compute/virtualMachines/deallocate/action | 取消配置虛擬機器 |
| Microsoft.Compute/virtualMachines/read | 設定虛擬機器的屬性 |
| Microsoft.Compute/virtualMachines/restart/action | 重新啟動虛擬機器 |
| Microsoft.Compute/virtualMachines/start/action | 啟動虛擬機器 |
| Microsoft.DevTestLab/*/read | 讀取實驗室的屬性 |
| Microsoft.DevTestLab/labs/createEnvironment/action | 建立實驗室環境 |
| Microsoft.DevTestLab/labs/formulas/delete | 刪除公式 |
| Microsoft.DevTestLab/labs/formulas/read | 讀取公式 |
| Microsoft.DevTestLab/labs/formulas/write | 新增或修改公式 |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | 評估實驗室原則 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入負載平衡器輸入 NAT 規則 |
| Microsoft.Network/networkInterfaces/*/read | 讀取網路介面的屬性 (例如網路介面所屬的所有負載平衡器) |
| Microsoft.Network/networkInterfaces/join/action | 將虛擬機器加入網路介面 |
| Microsoft.Network/networkInterfaces/read | 讀取網路介面 |
| Microsoft.Network/networkInterfaces/write | 撰寫網路介面 |
| Microsoft.Network/publicIPAddresses/*/read | 讀取公用 IP 位址的屬性 |
| Microsoft.Network/publicIPAddresses/join/action | 加入公用 IP 位址 |
| Microsoft.Network/publicIPAddresses/read | 讀取網路公用 IP 位址 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 加入虛擬網路 |
| Microsoft.Resources/deployments/operations/read | 讀取部署作業 |
| Microsoft.Resources/deployments/read | 讀取部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Storage/storageAccounts/listKeys/action | 列出儲存體帳戶金鑰 |

### DocumentDB 帳戶參與者
可以管理 DocumentDB 帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.DocumentDb/databaseAccounts/* | 建立及管理 DocumentDB 帳戶 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |

### Intelligent Systems 帳戶參與者
可以管理 Intelligent Systems 帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.IntelligentSystems/accounts/* | 建立及管理 Intelligent Systems 帳戶 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 網路參與者
可以管理網路資源

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Network/* | 建立和管理網路 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |

### New Relic APM 帳戶參與者
可以管理 New Relic 應用程式效能管理帳戶和應用程式

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |
| NewRelic.APM/accounts/* | 建立和管理 New Relic 應用程式效能管理帳戶 |

### 擁有者
可以管理所有項目，包括存取

| **動作** ||
| ------- | ------ |
| * | 建立和管理所有類型的資源 |

### 讀取者
可以檢視所有項目，但是無法進行變更

| **動作** ||
| ------- | ------ |
| **/read | 讀取密碼以外的所有類型的資源。|

### Redis 快取參與者
可以管理 Redis 快取

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Cache/redis/* | 建立和管理 Redis 快取 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 排程器工作集合參與者
可以管理排程器工作集合

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Scheduler/jobcollections/* | 建立和管理工作集合 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 搜尋服務參與者
可以管理搜尋服務

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Search/searchServices/* | 建立和管理搜尋服務 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 安全性管理員
可以管理安全性元件、安全性原則及虛擬機器

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.ClassicCompute/*/read | 讀取傳統運算虛擬機器的組態資訊 |
| Microsoft.ClassicCompute/virtualMachines/*/write | 撰寫虛擬機器的組態 |
| Microsoft.ClassicNetwork/*/read | 讀取傳統網路的組態資訊 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Security/* | 建立和管理安全性元件和原則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### SQL DB 參與者
可以管理 SQL 資料庫，但是無法管理它們的安全性相關原則

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Sql/servers/databases/* | 建立和管理 SQL 資料庫 |
| Microsoft.Sql/servers/read | 讀取 SQL Server |
| Microsoft.Support/* | 建立和管理支援票證 |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 無法編輯稽核原則 |
| Microsoft.Sql/servers/databases/auditingSettings/* | 無法編輯稽核設定 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 無法編輯連接原則 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 無法編輯資料遮罩原則 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | 無法編輯安全性警示原則 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 無法編輯安全性度量 |

### SQL 安全性管理員
可以管理 SQL Server 和資料庫的安全性相關原則

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取 Microsoft 授權 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Sql/servers/auditingPolicies/* | 建立和管理 SQL Server 稽核原則 |
| Microsoft.Sql/servers/auditingSettings/* | 建立和管理 SQL Server 稽核設定 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 建立和管理 SQL Server 資料庫稽核原則 |
| Microsoft.Sql/servers/databases/auditingSettings/* | 建立和管理 SQL Server 資料庫稽核設定 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 建立和管理 SQL Server 資料庫連接原則 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 建立和管理 SQL Server 資料庫資料遮罩原則 |
| Microsoft.Sql/servers/databases/read | 讀取 SQL 資料庫 |
| Microsoft.Sql/servers/databases/schemas/read | 讀取 SQL Server 資料庫結構描述 |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | 讀取 SQL Server 資料庫資料表資料行 |
| Microsoft.Sql/servers/databases/schemas/tables/read | 讀取 SQL Server 資料庫資料表 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | 建立和管理 SQL Server 資料庫安全性警示原則 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 建立和管理 SQL Server 資料庫安全性度量 |
| Microsoft.Sql/servers/read | 讀取 SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* | 建立和管理 SQL Server 安全性警示原則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### SQL Server 參與者
可以管理 SQL Server 和資料庫，但是無法管理它們的安全性相關原則

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取授權|
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Sql/servers/* | 建立和管理 SQL Server |
| Microsoft.Support/* | 建立和管理支援票證 |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | 無法編輯 SQL Server 稽核原則 |
| Microsoft.Sql/servers/auditingSettings/* | 無法編輯 SQL Server 稽核設定 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 無法編輯 SQL Server 資料庫稽核原則 |
| Microsoft.Sql/servers/databases/auditingSettings/* | 無法編輯 SQL Server 資料庫稽核設定 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 無法編輯 SQL Server 資料庫連接原則 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 無法編輯 SQL Server 資料庫資料遮罩原則 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | 無法編輯 SQL Server 資料庫安全性警示原則 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 無法編輯 SQL Server 資料庫安全性度量 |
| Microsoft.Sql/servers/securityAlertPolicies/* | 無法編輯 SQL Server 安全性警示原則 |

### 傳統儲存體帳戶參與者
可以管理傳統儲存體帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.ClassicStorage/storageAccounts/* | 建立及管理儲存體帳戶 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Support/* | 建立和管理支援票證 |

### 儲存體帳戶參與者
可管理儲存體帳戶，但無法存取它們。

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取所有授權 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Insights/diagnosticSettings/* | 管理診斷設定 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Storage/storageAccounts/* | 建立及管理儲存體帳戶 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 使用者存取系統管理員
可以管理 Azure 資源的使用者存取

| **動作** ||
| ------- | ------ |
| */read | 讀取密碼以外的所有類型資源。 |
| Microsoft.Authorization/* | 管理授權 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 傳統虛擬機器參與者
可以管理傳統虛擬機器，但是無法管理它們連接的虛擬網路或儲存體帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.ClassicCompute/domainNames/* | 建立和管理傳統運算網域名稱 |
| Microsoft.ClassicCompute/virtualMachines/* | 建立和管理虛擬機器 |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | 加入網路安全性群組 |
| Microsoft.ClassicNetwork/reservedIps/link/action | 連結保留 IP |
| Microsoft.ClassicNetwork/reservedIps/read | 讀取保留的 IP 位址 |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | 加入虛擬網路 |
| Microsoft.ClassicNetwork/virtualNetworks/read | 讀取虛擬網路 |
| Microsoft.ClassicStorage/storageAccounts/disks/read | 讀取儲存體帳戶磁碟 |
| Microsoft.ClassicStorage/storageAccounts/images/read | 讀取儲存體帳戶映像 |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出儲存體帳戶金鑰 |
| Microsoft.ClassicStorage/storageAccounts/read | 讀取傳統儲存體帳戶 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Support/* | 建立和管理支援票證 |

### 虛擬機器參與者
可以管理虛擬機器，但是無法管理它們連接的虛擬網路或儲存體帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Compute/availabilitySets/* | 建立和管理運算可用性集合 |
| Microsoft.Compute/locations/* | 建立和管理運算位置 |
| Microsoft.Compute/virtualMachines/* | 建立和管理虛擬機器 |
| Microsoft.Compute/virtualMachineScaleSets/* | 建立和管理虛擬機器擴展集 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入網路應用程式閘道器後端位址集區 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入負載平衡器輸入 NAT 集區 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入負載平衡器輸入 NAT 規則 |
| Microsoft.Network/loadBalancers/read | 讀取負載平衡器 |
| Microsoft.Network/locations/* | 建立和管理網路位置 |
| Microsoft.Network/networkInterfaces/* | 建立和管理網路介面 |
| Microsoft.Network/networkSecurityGroups/join/action | 加入網路安全性群組 |
| Microsoft.Network/networkSecurityGroups/read | 讀取網路安全性群組 |
| Microsoft.Network/publicIPAddresses/join/action | 加入網路公用 IP 位址 |
| Microsoft.Network/publicIPAddresses/read | 讀取網路公用 IP 位址 |
| Microsoft.Network/virtualNetworks/read | 讀取虛擬網路 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 加入虛擬網路子網路 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Storage/storageAccounts/listKeys/action | 列出儲存體帳戶金鑰 |
| Microsoft.Storage/storageAccounts/read | 讀取儲存體帳戶 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 傳統網路參與者
可以管理傳統虛擬網路和保留 IP

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.ClassicNetwork/* | 建立和管理傳統網路 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Support/* | 建立和管理支援票證 |

### Web 方案參與者
可以管理 Web 方案

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Support/* | 建立和管理支援票證 |
| Microsoft.Web/serverFarms/* | 建立和管理伺服器陣列 |

### 網站參與者
可以管理網站，但是不能管理它們連接的 Web 方案

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Insights/components/* | 建立和管理 Insights 元件 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 讀取資源的健康狀態 |
| Microsoft.Resources/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 | Microsoft.Support/* | 建立和管理支援票證 |
| Microsoft.Web/certificates/* | 建立和管理網站憑證 |
| Microsoft.Web/listSitesAssignedToHostName/read | 讀取指派給主機名稱的網站 |
| Microsoft.Web/serverFarms/join/action | 加入伺服器陣列 |
| Microsoft.Web/serverFarms/read | 讀取伺服器陣列 |
| Microsoft.Web/sites/* | 建立和管理網站 |

## 另請參閱
- [角色型存取控制](role-based-access-control-configure.md)：開始在 Azure 入口網站中使用 RBAC。
- [Azure RBAC 中的自訂角色](role-based-access-control-custom-roles.md)︰了解如何建立自訂角色以符合您的存取需求。
- [建立存取權變更歷程記錄報告](role-based-access-control-access-change-history-report.md)︰記錄 RBAC 中的角色指派變更。
- [角色型存取控制疑難排解](role-based-access-control-troubleshooting.md)︰取得修正常見問題的建議。

<!---HONumber=AcomDC_0601_2016-->