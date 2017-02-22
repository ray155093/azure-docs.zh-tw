---
title: "Resource Manager 支援的服務 | Microsoft Docs"
description: "說明支援資源管理員、其結構描述及可用 API 版本的資源提供者，以及可裝載資源的區域。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2016
ms.author: magoedte;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 53e57807e97671bd279c03ada4c147fc1e7f1e45
ms.openlocfilehash: c7bfc5584c11a7e69aedeb93f143a78d97c9369a


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>資源管理員提供者、區域、API 版本及結構描述
Azure 資源管理員提供一種新方式來部署和管理組成應用程式的服務。 大部分但並非所有的服務都支援資源管理員，有些服務僅部分支援資源管理員。 本主題提供支援 Azure 資源管理員的資源提供者清單。

部署資源時，您也需要知道哪些區域支援這些資源，以及哪些 API 版本適用於資源。 [支援區域](#supported-regions)一節說明如何找出哪些區域適用於您的訂用帳戶和資源。 [支援的 API 版本](#supported-api-versions) 一節說明如何判斷您可以使用哪些 API 版本。

如要 Azure 入口網站和傳統入口網站支援哪些服務，請參閱 [Azure 入口網站的可用性圖表](https://azure.microsoft.com/features/azure-portal/availability/)。 若要查看哪些服務可支援移動資源，請參閱 [將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

下表列出哪些 Microsoft 服務可透過資源管理員支援部署和管理，哪些則否。 標題為「快速入門範本」  的資料行中的連結，會為特定資源提供者傳送查詢給 Azure 快速入門範本存放庫。 快速入門範本會頻繁地新增及更新。 即使特定服務有連結，也不一定代表查詢會從存放庫傳回範本。 另外也有許多支援 Resource Manager 的第三方資源提供者。 您可以在[資源提供者和類型](#resource-providers-and-types)一節了解如何查看所有資源提供者。

## <a name="compute"></a>計算
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- |
| 批次 |是 |[Batch REST](/rest/api/batchservice) |[Batch 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json) | |
| Container Registry |是 |[容器登錄 REST](/rest/api/containerregistry) |[容器登錄結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-27-preview/Microsoft.ContainerRegistry.json) |[Microsoft.ContainerRegistry](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerRegistry%22&type=Code) |
| 容器服務 |是 |[Container Service REST](/rest/api/compute/containerservices) |[容器結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) |[Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Dynamics 週期服務 |是 | | | |
| 擴展集 |是 |[擴展集 REST](/rest/api/compute/virtualmachinescalesets) |[擴展集結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) |
| Service Fabric |是 |[Service Fabric Rest](/rest/api/servicefabric) | [Service Fabric 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-09-01/Microsoft.ServiceFabric.json) |[Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| 虛擬機器 |是 |[VM REST](/rest/api/compute/virtualmachines) |[VM 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| 虛擬機器 (傳統) |限制 |- |- |- |
| 遠端應用程式 |否 |- |- |- |
| 雲端服務 (傳統) |限制 (請參閱下文) |- |- |- |

虛擬機器 (傳統) 是指已透過傳統部署模型部署的資源，而不是透過資源管理員部署模型部署的資源。 一般而言，這些資源不支援資源管理員作業，但已啟用某些作業。 如需這些部署模型的詳細資訊，請參閱 [了解資源管理員部署和傳統部署](resource-manager-deployment-model.md)。 

雲端服務 (傳統) 可搭配其他傳統資源使用。 不過，傳統資源不會利用所有的資源管理員功能，也不是未來解決方案的好選項。 請改為考慮變更您的應用程式基礎結構，以從 Microsoft.Compute、Microsoft.Storage，和 Microsoft.Network 命名空間使用資源。

## <a name="networking"></a>網路
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- |
| 應用程式閘道 |是 |[應用程式閘道 REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | |[applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS |是 |[DNS REST](/rest/api/dns) |[DNS 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) |[dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute |是 |[ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |[expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| 負載平衡器 |是 |[負載平衡器 REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[負載平衡器結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| 流量管理員 |是 |[流量管理員 REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[流量管理員結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) |[trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| 虛擬網路 |是 |[虛擬網路 REST](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) |[虛擬網路結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| VPN 閘道 |是 |[網路閘道 REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | |[virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[連線](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |

## <a name="storage"></a>儲存體
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- | --- |
| 儲存體 |是 |[儲存體 REST](/rest/api/storagerp) |[儲存體帳戶](resource-manager-template-storage.md) |[Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple |是 | | | |

## <a name="databases"></a>資料庫
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- | --- |
| DocumentDB |是 |[DocumentDB REST](/rest/api/documentdbresourceprovider) |[DocumentDB 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) |[Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis 快取 |是 | [Redis 快取 REST API](/rest/api/redis) |[Redis 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) |[Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| SQL Database |是 |[SQL Database REST](/rest/api/sql) |[SQL Database 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |[Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL 資料倉儲 |是 | | | |

## <a name="web--mobile"></a>Web 與行動
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- |
| API 應用程式 |是 | [App Service REST](/rest/api/appservice) |[API Apps 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[API 應用程式](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| API 管理 |是 |[API 管理 REST](/rest/api/apimanagement) |[API 管理結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json) |[Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) |
| 內容仲裁 |是 | | | |
| 函式應用程式 |是 | [函式應用程式 REST](/rest/api/appservice) | |[functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| 邏輯應用程式 |是 |[Logic Apps REST](/rest/api/logic) |[Logic Apps 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) |[Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| 行動應用程式 |是 | [App Service REST](/rest/api/appservice) |[Mobile Apps 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code) |
| Mobile Engagement |是 |[Mobile Engagement REST](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |[Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| 搜尋 |是 |[搜尋 REST](/rest/api/searchservice) | [搜尋結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-19/Microsoft.Search.json) |[Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Web Apps |是 | [Web Apps REST](/rest/api/appservice/webapps) |[Web Apps 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="intelligence--analytics"></a>智慧 + 分析
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- |
| Analysis Services | 是 | [分析服務 REST](/rest/api/analysisservices) | [分析服務結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-16/Microsoft.AnalysisServices.json) | |
| 辨識服務 |是 | [辨識服務 REST](/rest/api/cognitiveservices) |[辨識服務結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) | |
| 資料目錄 |是 |[資料目錄 REST](/rest/api/datacatalog) |[資料目錄結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) | |
| Data Factory |是 |[Data Factory REST](/rest/api/datafactory) | |[Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| 資料湖分析 |是 | [Data Lake REST](/rest/api/datalakeanalytics) |[Data Lake Analytics 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| 資料湖存放區 |是 |[Data Lake Store REST](/rest/api/datalakestore) |[Data Lake Store 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeStore.json) |[Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights |是 |[HDInsights REST](/rest/api/hdinsight) | |[Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| 機器學習服務 |是 |[Machine Learning REST](/rest/api/machinelearning) |[機器學習服務結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) | |
| 串流分析 |是 |[串流分析 REST](/rest/api/streamanalytics) | | |
| Power BI |是 |[Power BI Embedded REST](/rest/api/powerbiembedded) |[Power BI 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) | |


## <a name="internet-of-things"></a>物聯網
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- |
| 事件中心 |是 |[事件中樞 REST](/rest/api/eventhub) |[事件中樞結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) |[Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| IoTHubs |是 |[IoT 中樞 REST (英文)](/rest/api/iothub) |[IoT 中樞結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) |[Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| 通知中樞 |是 |[通知中樞 REST](/rest/api/notificationhubs) |[通知中樞結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |[Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>媒體與 CDN
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- |
| CDN |是 |[CDN REST](/rest/api/cdn) |[CDN 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) |[Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| 媒體服務 |是 |[媒體服務 REST](/rest/api/media) |[媒體結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |[Microsoft.Media](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Media%22&type=Code)  |

## <a name="hybrid-integration"></a>混合式整合
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- |
| BizTalk 服務 |是 | |[BizTalk 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | |
| 復原服務 |是 |[復原服務 REST](/rest/api/recoveryservices) |[復原服務結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) |[Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| 服務匯流排 |是 |[服務匯流排 REST](/rest/api/servicebus) |[服務匯流排結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json) |[Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>身分識別與存取管理
Azure Active Directory 可搭配資源管理員使用，以針對您的訂用帳戶啟用角色型存取控制。 若要了解如何使用角色型存取控制和 Active Directory，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

## <a name="developer-services"></a>開發人員服務
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- |
| 監視 |是 |[監視 REST](/rest/api/monitor) |[了解結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |[Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing 地圖 |是 | | | |
| DevTest Labs |是 | [DevTest REST](/rest/api/dtl) |[DevTest Lab 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) |[Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Visual Studio 帳戶 |是 | |[Visual Studio 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | |

## <a name="management-and-security"></a>管理和安全性
| 服務 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- |
| 自動化 |是 |[自動化 REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[自動化結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |[Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| 金鑰保存庫 |是 |[金鑰保存庫 REST](/rest/api/keyvault) |[Key vault](resource-manager-template-keyvault.md)<br />[金鑰保存庫密碼](resource-manager-template-keyvault-secret.md) |[Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Operational Insights |是 | | | |
| 排程器 |是 |[排程器 REST](/rest/api/scheduler) |[排程器結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) |[Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| 安全性 (Preview) |是 |[安全性 REST](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |[Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## <a name="resource-manager"></a>資源管理員
| 功能 | 已啟用資源管理員 | REST API | 結構描述 | 快速入門範本 |
| --- | --- | --- | --- | --- | --- |
| Authorization |是 |[授權 REST](/rest/api/authorization) |[資源鎖定](resource-manager-template-lock.md)<br />[角色指派](resource-manager-template-role.md) |[Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| 資源 |是 |[資源 REST](/rest/api/resources) |[資源連結](resource-manager-template-links.md) |[Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |

## <a name="resource-providers-and-types"></a>資源提供者和類型
部署資源時，您經常需要擷取有關資源提供者和類型的資訊。 您可以透過 REST API、Azure PowerShell 或 Azure CLI 擷取此資訊。

若要使用資源提供者，必須在您的帳戶中註冊該資源提供者。 根據預設，許多資源提供者會自動註冊。不過，您可能需要手動註冊某些資源提供者。 下列範例示範如何取得資源提供者註冊狀態，以及必要時註冊資源提供者。

### <a name="portal"></a>入口網站
您可以輕易地查看支援的資源提供者清單，方法是從訂用帳戶刀鋒視窗中選取**資源提供者**。 若要使用資源提供者註冊您的訂用帳戶，請選取 [註冊] 連結。
   
![列出資源提供者](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>REST API
若要取得所有可用的資源提供者 (包括其類型、位置、API 版本及註冊狀態)，請使用[列出所有資源提供者](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)作業。 如果您需要註冊資源提供者，請參閱 [向資源提供者註冊訂用帳戶](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register)。

### <a name="powershell"></a>PowerShell
下列範例示範如何取得所有可用的資源提供者。

    Get-AzureRmResourceProvider -ListAvailable


下一個範例示範如何取得特定資源提供者的資源類型。

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes

輸出如下：

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...

若要註冊資源提供者，請提供命名空間：

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>Azure CLI
下列範例示範如何取得所有可用的資源提供者。

    azure provider list

輸出如下：

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

您可以使用下列命令，將特定資源提供者的資訊儲存至檔案：

    azure provider show Microsoft.Web -vv --json > c:\temp.json

若要註冊資源提供者，請提供命名空間：

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>支援區域
部署資源時，通常需要指定資源的區域。 所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。 此外，您的訂用帳戶上可能會有一些限制，以防止您使用某些支援該資源的區域。 這些限制可能與您所在國家/地區的稅務問題有關，或者與由您的訂用帳戶管理員所放置，只能使用特定區域的原則結果有關。 

如需所有 Azure 服務的所有支援區域完整清單，請參閱[依據區域的服務](https://azure.microsoft.com/regions/#services)。 不過，這份清單可能包含您的訂用帳戶不支援的區域。 您可以透過入口網站、REST API、PowerShell 或 Azure CLI，判斷您的訂用帳戶所支援之特定資源類型的區域。

### <a name="portal"></a>入口網站
您可以透過下列步驟，查看某個資源類型支援的區域︰

1. 選取 [更多服務] > [資源總管]。
   
    ![資源總管](./media/resource-manager-supported-services/select-resource-explorer.png)
2. 開啟 [提供者] 節點。
   
    ![選取提供者](./media/resource-manager-supported-services/select-providers.png)
3. 選取資源提供者，並檢視支援的區域和 API 版本。
   
    ![檢視提供者](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>REST API
若要探索哪些區域可供您訂用帳戶中特定資源類型使用，請使用 [列出所有資源提供者](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 作業。 

### <a name="powershell"></a>PowerShell
下列範例示範如何取得支援網站的區域。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

輸出如下：

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>Azure CLI
下列範例會針對每個資源類型傳回所有支援的位置。

    azure location list

您也可以使用 [jq](https://stedolan.github.io/jq/)這類 JSON 公用程式來篩選位置結果。

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

它會傳回：

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>支援的 API 版本
當您部署範本時，您必須指定要用來建立每個資源的 API 版本。 API 版本會對應至資源提供者所發行的 REST API 作業版本。 當資源提供者啟用新功能時，它會發行新版本的 REST API。 因此，您在範本中指定的 API 版本會影響您可以在範本中指定的屬性。 一般而言，您會想要在建立範本時選取最新的 API 版本。 對於現有的範本，您可以決定是否繼續使用舊的 API 版本，或更新您的範本以便最新版本利用新功能。

### <a name="portal"></a>入口網站
您可以利用和先前判斷支援區域 (先前所示) 時相同的方式，判斷支援的 API 版本。

### <a name="rest-api"></a>REST API
若要探索哪些 API 版本可供資源類型使用，請使用 [列出所有資源提供者](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 作業。 

### <a name="powershell"></a>PowerShell
下列範例示範如何取得特定資源類型可用的 API 版本。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

輸出如下：

    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>Azure CLI
您可以使用下列命令，將資源提供者的資訊 (包括可用的 API 版本) 儲存至檔案：

    azure provider show Microsoft.Web -vv --json > c:\temp.json

您可以開啟該檔案，然後尋找 **apiVersions** 元素

## <a name="next-steps"></a>後續步驟
* 若要了解如何建立資源管理員範本，請參閱 [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
* 若要了解如何部署資源，請參閱 [使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。




<!--HONumber=Feb17_HO2-->


