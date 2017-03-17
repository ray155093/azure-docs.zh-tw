---
title: "Azure Government 的可用服務 | Microsoft Docs"
description: "提供 Azure Government 中可用服務的概觀"
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: liki
ms.assetid: a453a23c-bc0f-4203-9075-0f579dea7e23
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 21c2a0faad87b84058093f02c831b374a644b4b6
ms.lasthandoff: 03/06/2017


---
# <a name="available-services-on-azure-government"></a>Azure Government 中的可用服務
Azure Government 持續擴充可用的服務。  這些服務是使用 Azure 公用中所使用之相同程式碼進行部署。  本節說明 Azure Government 上目前可用的服務，包括兩種主要類型的資訊：

* **變化︰**因尚未部署之功能或政府環境特有之屬性 (例如，URL) 的變化。  
* **考量︰**政府特定實作詳細資料，以確保資料能夠符合合規性的要求。

在這些服務的一般文件中，可以找到您對這些服務的所有其他必要知識。

如需最新的服務清單，請參閱[依區域的產品](https://azure.microsoft.com/regions/services/)。 

在下表中，啟用 Resource Manager 時所指定的服務會具備資源提供者，而且可以使用 PowerShell 來管理。 如需 Resource Manager 提供者、API 版本和結構描述的詳細資訊，請參閱[這裡](../azure-resource-manager/resource-manager-supported-services.md)。 在入口網站中指定為可用的服務可在 [Azure Government 入口網站](https://portal.azure.us/)中加以管理。 


## <a name="computedocumentation-government-computemd"></a>[計算](documentation-government-compute.md)

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| [虛擬機器](documentation-government-compute.md#virtual-machines) | 是 | 是 |
| 批次 | 是 | 是 |
| 雲端服務 | 是 | 是 |
| Service Fabric | 是 | 是 |
| VM 擴展集 | 是 | 是 |


## <a name="networkingdocumentation-government-networkingmd"></a>[網路功能](documentation-government-networking.md)

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| [ExpressRoute](documentation-government-networking.md#expressroute-private-connectivity) | 是 | 是 |
| 虛擬網路 | 是 | 是 |
| [負載平衡器](documentation-government-networking.md#support-for-load-balancer) | 是 | 是 |
| [流量管理員](documentation-government-networking.md#support-for-traffic-manger) | 是 | 是 |
| [VPN 閘道](documentation-government-networking.md#support-for-vpn-gateway) | 是 | 是 |
| 應用程式閘道 | 是 | 是 |
| ExpressRoute | 是 | 是 |



## <a name="storagedocumentation-government-services-storagemd"></a>[儲存體](documentation-government-services-storage.md)

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| [儲存體 - Blob](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [儲存體 - 資料表](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [儲存體 - 佇列](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [儲存體 - 檔案](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [儲存體 - 磁碟](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [StorSimple](documentation-government-services-storage.md) | 是 | 是 |
| [備份](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [站台復原](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [匯入/匯出](documentation-government-services-storage.md#azure-storage) | 是 | 否 |



## <a name="web--mobiledocumentation-government-services-webandmobilemd"></a>[Web + 行動](documentation-government-services-webandmobile.md)

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| [App Service - Web Apps](documentation-government-services-webandmobile.md#app-services) | 是 | 是 |
| [App Service - API Apps](documentation-government-services-webandmobile.md#app-services) | 是 | 是 |
| [App Service - Mobile Apps](documentation-government-services-webandmobile.md#app-services) | 是 | 是 |
| 媒體服務 | 是 | 是 |


## <a name="databasesdocumentation-government-services-databasemd"></a>[資料庫](documentation-government-services-database.md)

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| [SQL Database](documentation-government-services-database.md#sql-database) | 是 | 是 |
| SQL 資料倉儲 | 是 | 是 |
| SQL Server Stretch Database | 是 | 是 |
| [Redis 快取](documentation-government-services-database.md#azure-redis-cache) | 是 | 是 |


## <a name="intelligence--analyticsdocumentation-government-services-intelligenceandanalyticsmd"></a>[智慧 + 分析](documentation-government-services-intelligenceandanalytics.md)

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| [HDInsights](documentation-government-services-intelligenceandanalytics.md#hdinsight) | 是 | 是 |
| [Power BI Pro](documentation-government-services-intelligenceandanalytics.md#power-bi) | 否 | 否 (Office 365 管理入口網站) |


## <a name="internet-of-things-iot"></a>物聯網 (IoT)

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| 事件中樞 | 是 | 是 |
| 通知中樞 | 否 | 否 (請移至[舊版入口網站](https://manage.windowsazure.us/)) |


## <a name="enterprise-integration"></a>企業整合

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| 服務匯流排 | 是 | 是 |
| [StorSimple](documentation-government-services-storage.md) | 是 | 是 |
| SQL Server Stretch Database | 是 | 是 |



## <a name="security--identitydocumentation-government-services-securityandidentitymd"></a>[安全性 + 身分識別](documentation-government-services-securityandidentity.md)

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| Azure Active Directory | 是 | 是 |
| [金鑰保存庫](documentation-government-services-securityandidentity.md#key-vault) | 是 | 否 (敬請期待) |
| Multi-Factor Authentication | 是 | 是 |


## <a name="intelligence--analytics"></a>智慧 + 分析

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| Power BI | 是 | 否 |
| HDInsight | 是 | 是 |



## <a name="monitoring--managementdocumentation-government-services-monitoringandmanagementmd"></a>[監視 + 管理](documentation-government-services-monitoringandmanagement.md)

| 服務 | 已啟用資源管理員 | 入口網站 |
| --- | --- | --- |
| [自動化](documentation-government-services-monitoringandmanagement.md#automation) | 是 | 是 |
| [備份](documentation-government-services-backup.md) | 是 | 是 |
| [Log Analytics](documentation-government-services-monitoringandmanagement.md#log-analytics) | 是 | 是 |
| [站台復原](documentation-government-services-monitoringandmanagement.md#site-recovery) | 是 | 是 |
| 排程器 | 是 | 否 |
| 監視和診斷 | 是 | 是 |




## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 [Microsoft Azure Government 部落格](https://blogs.msdn.microsoft.com/azuregov/)。


