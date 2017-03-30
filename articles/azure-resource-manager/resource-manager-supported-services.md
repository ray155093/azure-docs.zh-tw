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
ms.date: 03/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 46eb4a20c85140a7893d77984f375d6abf3e7634
ms.lasthandoff: 03/22/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>資源管理員提供者、區域、API 版本及結構描述
本主題提供支援 Azure Resource Manager 的資源提供者清單。

部署資源時，您也需要知道哪些區域支援這些資源，以及哪些 API 版本適用於資源。 [支援區域](#supported-regions)一節說明如何找出哪些區域適用於您的訂用帳戶和資源。 [支援的 API 版本](#supported-api-versions) 一節說明如何判斷您可以使用哪些 API 版本。

如要 Azure 入口網站和傳統入口網站支援哪些服務，請參閱 [Azure 入口網站的可用性圖表](https://azure.microsoft.com/features/azure-portal/availability/)。 若要查看哪些服務可支援移動資源，請參閱 [將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

下表列出哪些 Microsoft 服務可透過資源管理員支援部署和管理，哪些則否。 另外也有許多支援 Resource Manager 的第三方資源提供者。 您可以在[資源提供者和類型](#resource-providers-and-types)一節了解如何查看所有資源提供者。

## <a name="compute"></a>計算
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- |
| Batch |是 |[Batch REST](/rest/api/batchservice) |[Batch 資源](/azure/templates/microsoft.batch/batchaccounts) |
| Container Registry |是 |[容器登錄 REST](/rest/api/containerregistry) |[容器登錄資源](/azure/templates/microsoft.containerregistry/registries) |
| 容器服務 |是 |[Container Service REST](/rest/api/compute/containerservices) |[Container Service 資源](/azure/templates/microsoft.containerservice/containerservices) |
| Dynamics 週期服務 |是 | | |
| 擴展集 |是 |[擴展集 REST](/rest/api/compute/virtualmachinescalesets) |[擴展集資源](/azure/templates/microsoft.compute/virtualmachinescalesets) |
| Service Fabric |是 |[Service Fabric Rest](/rest/api/servicefabric) | [Service Fabric 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-09-01/Microsoft.ServiceFabric.json) |
| 虛擬機器 |是 |[VM REST](/rest/api/compute/virtualmachines) |[VM 資源](/azure/templates/microsoft.compute/virtualmachines) |
| 虛擬機器 (傳統) |限制 |- |- |
| 遠端應用程式 |否 |- |- |
| 雲端服務 (傳統) |限制 (請參閱下文) |- |- |

虛擬機器 (傳統) 是指已透過傳統部署模型部署的資源，而不是透過資源管理員部署模型部署的資源。 一般而言，這些資源不支援資源管理員作業，但已啟用某些作業。 如需這些部署模型的詳細資訊，請參閱 [了解資源管理員部署和傳統部署](resource-manager-deployment-model.md)。 

雲端服務 (傳統) 可搭配其他傳統資源使用。 不過，傳統資源不會利用所有的資源管理員功能，也不是未來解決方案的好選項。 請改為考慮變更您的應用程式基礎結構，以從 Microsoft.Compute、Microsoft.Storage，和 Microsoft.Network 命名空間使用資源。

## <a name="networking"></a>網路
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- |
| 應用程式閘道 |是 |[應用程式閘道 REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | [應用程式閘道資源](/azure/templates/microsoft.network/applicationgateways) |
| DNS |是 |[DNS REST](/rest/api/dns) |[DNS 資源](/azure/templates/microsoft.network/dnszones) |
| ExpressRoute |是 |[ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | [ExpressRoute 資源](/azure/templates/microsoft.network/expressroutecircuits) |
| 負載平衡器 |是 |[負載平衡器 REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[負載平衡器資源](/azure/templates/microsoft.network/loadbalancers) |
| 流量管理員 |是 |[流量管理員 REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[流量管理員資源](/azure/templates/microsoft.network/trafficmanagerprofiles) |
| 虛擬網路 |是 |[虛擬網路 REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) |[虛擬網路資源](/azure/templates/microsoft.network/virtualnetworks) |
| 網路閘道 |是 |[網路閘道 REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | [連線資源](/azure/templates/microsoft.network/connections) <br /> [區域網路閘道資源](/azure/templates/microsoft.network/localnetworkgateways) <br /> [虛擬網路閘道資源](/azure/templates/microsoft.network/virtualnetworkgateways) |

## <a name="storage"></a>儲存體
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- | --- |
| 匯入匯出 | 是 | [匯入匯出 REST](/rest/api/storageimportexport/) | [匯入匯出資源](/azure/templates/microsoft.importexport/jobs) |
| 儲存體 |是 |[儲存體 REST](/rest/api/storagerp) |[儲存體資源](/azure/templates/microsoft.storage/storageaccounts) |
| StorSimple |是 | | |

## <a name="databases"></a>資料庫
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- | --- |
| DocumentDB |是 |[DocumentDB REST](/rest/api/documentdbresourceprovider) |[DocumentDB 資源](/azure/templates/microsoft.documentdb/databaseaccounts) |
| Redis 快取 |是 | [Redis 快取 REST API](/rest/api/redis) |[Redis 資源](/azure/templates/microsoft.cache/redis) |
| SQL Database |是 |[SQL Database REST](/rest/api/sql) |[SQL Database 資源](/azure/templates/microsoft.sql/servers) |
| SQL 資料倉儲 |是 | | |

## <a name="web--mobile"></a>Web 與行動
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- |
| API Apps |是 | [App Service REST](/rest/api/appservice) |[Web 資源](/azure/templates/microsoft.web/sites) |
| API 管理 |是 |[API 管理 REST](/rest/api/apimanagement) |[API 管理資源](/azure/templates/microsoft.apimanagement/service) |
| 憑證註冊 | 是 | [憑證註冊 REST](/rest/api/appservice/appservicecertificateorders) | [憑證註冊資源](/azure/templates/microsoft.certificateregistration/certificateorders)  |
| 內容仲裁 |是 | | | |
| 網域註冊 | 是 | [網域註冊](/rest/api/appservice/domains) | [網域註冊資源](/azure/templates/microsoft.domainregistration/domains)  |
| 函式應用程式 |是 | [函式應用程式 REST](/rest/api/appservice) | [Web 資源](/azure/templates/microsoft.web/sites) |
| Logic Apps |是 |[Logic Apps REST](/rest/api/logic) |[Logic App 資源](/azure/templates/microsoft.logic/workflows) |
| Mobile Apps |是 | [App Service REST](/rest/api/appservice) | [Web 資源](/azure/templates/microsoft.web/sites) |
| Mobile Engagement |是 |[Mobile Engagement REST](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |
| 搜尋 |是 |[搜尋 REST](/rest/api/searchservice) | [搜尋資源](/azure/templates/microsoft.search/searchservices) |
| Web Apps |是 | [Web Apps REST](/rest/api/appservice/webapps) | [Web 資源](/azure/templates/microsoft.web/sites) |

## <a name="intelligence--analytics"></a>智慧 + 分析
| 服務 | 已啟用資源管理員 | REST API | 範本格式 | 
| --- | --- | --- | --- |
| Analysis Services | 是 | [分析服務 REST](/rest/api/analysisservices) | [Analysis Services 資源](/azure/templates/microsoft.analysisservices/servers) |
| 辨識服務 |是 | [辨識服務 REST](/rest/api/cognitiveservices) |[辨識服務資源](/azure/templates/microsoft.cognitiveservices/accounts) |
| 資料目錄 |是 |[資料目錄 REST](/rest/api/datacatalog) |[資料目錄結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |
| Data Factory |是 |[Data Factory REST](/rest/api/datafactory) | |
| Data Lake Analytics |是 | [Data Lake REST](/rest/api/datalakeanalytics) |[Data Lake Analytics 資源](/azure/templates/microsoft.datalakeanalytics/accounts) |
| Data Lake Store |是 |[Data Lake Store REST](/rest/api/datalakestore) |[Data Lake Store 資源](/azure/templates/microsoft.datalakestore/accounts) |
| HDInsights |是 |[HDInsights REST](/rest/api/hdinsight) | |
| Machine Learning |是 |[Machine Learning REST](/rest/api/machinelearning) |[Machine Learning 資源](/azure/templates/microsoft.machinelearning/commitmentplans) |
| 串流分析 |是 |[串流分析 REST](/rest/api/streamanalytics) | |
| Power BI |是 |[Power BI Embedded REST](/rest/api/powerbiembedded) |[Power BI 資源](/azure/templates/microsoft.powerbi/workspacecollections) |


## <a name="internet-of-things"></a>物聯網
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- |
| 事件中心 |是 |[事件中樞 REST](/rest/api/eventhub) |[事件中樞資源](/azure/templates/microsoft.eventhub/namespaces) |
| IoTHubs |是 |[IoT 中樞 REST (英文)](/rest/api/iothub) |[IoT 中樞資源](/azure/templates/microsoft.devices/iothubs) |
| 通知中樞 |是 |[通知中樞 REST](/rest/api/notificationhubs) |[通知中樞資源](/azure/templates/microsoft.notificationhubs/namespaces) |

## <a name="media--cdn"></a>媒體與 CDN
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- |
| CDN |是 |[CDN REST](/rest/api/cdn) |[CDN 資源](/azure/templates/microsoft.cdn/profiles) |
| 媒體服務 |是 |[媒體服務 REST](/rest/api/media) |[媒體資源](/azure/templates/microsoft.media/mediaservices) |

## <a name="hybrid-integration"></a>混合式整合
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- |
| BizTalk 服務 |是 | |[BizTalk 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| 復原服務 |是 |[復原服務 REST](/rest/api/recoveryservices) |[復原服務資源](/azure/templates/microsoft.recoveryservices/vaults) |
| 服務匯流排 |是 |[服務匯流排 REST](/rest/api/servicebus) |[服務匯流排資源](/azure/templates/microsoft.servicebus/namespaces) |

## <a name="identity--access-management"></a>身分識別與存取管理
Azure Active Directory 可搭配資源管理員使用，以針對您的訂用帳戶啟用角色型存取控制。 若要了解如何使用角色型存取控制和 Active Directory，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

## <a name="developer-services"></a>開發人員服務
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- |
| 監視 |是 |[監視 REST](/rest/api/monitor) |[Insights 資源](/azure/templates/microsoft.insights/alertrules) |
| Bing 地圖 |是 | | |
| DevTest Labs |是 | [DevTest REST](/rest/api/dtl) |[DevTest Lab 資源](/azure/templates/microsoft.devtestlab/labs) |
| Visual Studio 帳戶 |是 | |[Visual Studio 結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## <a name="management-and-security"></a>管理和安全性
| 服務 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- |
| 建議程式 | 是 | [Advisor REST](/rest/api/advisor/) | - |
| 自動化 |是 |[自動化 REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[自動化結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |
| 計費 | 是 | [計費 REST](/rest/api/billing/) | - |
| 金鑰保存庫 |是 |[金鑰保存庫 REST](/rest/api/keyvault) |[Key Vault 資源](/azure/templates/microsoft.keyvault/vaults) |
| Operational Insights |是 | | |
| 排程器 |是 |[排程器 REST](/rest/api/scheduler) |[排程器資源](/azure/templates/microsoft.scheduler/jobcollections) |
| 安全性 |是 |[安全性 REST](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |
| 伺服器管理 | 是 | [伺服器管理 REST](/rest/api/servermanagement/) | [伺服器管理資源](/azure/templates/microsoft.servermanagement/gateways) |

## <a name="resource-manager"></a>資源管理員
| 功能 | 已啟用資源管理員 | REST API | 範本格式 |
| --- | --- | --- | --- | --- |
| Authorization |是 |[授權 REST](/rest/api/authorization) |[授權資源](/azure/templates/microsoft.authorization/locks) |
| 資源 |是 |[資源 REST](/rest/api/resources) |[部署資源](/azure/templates/microsoft.resources/deployments) |

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

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


下一個範例示範如何取得特定資源提供者的資源類型。

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

若要註冊資源提供者，請提供命名空間：

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>Azure CLI
下列範例示範如何取得所有可用的資源提供者。

```azurecli
az provider list
```

您可以使用下列命令，檢視特定資源提供者的資訊：

```azurecli
az provider show --namespace Microsoft.Web
```

若要註冊資源提供者，請提供命名空間：

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

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

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>Azure CLI
下列範例說明如何取得網站的支援區域。

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>支援的 API 版本
當您部署範本時，您必須指定要用來建立每個資源的 API 版本。 API 版本會對應至資源提供者所發行的 REST API 作業版本。 當資源提供者啟用新功能時，它會發行新版本的 REST API。 因此，您在範本中指定的 API 版本會影響您可以在範本中指定的屬性。 一般而言，您會想要在建立範本時選取最新的 API 版本。 對於現有的範本，您可以決定是否繼續使用舊的 API 版本，或更新您的範本以便最新版本利用新功能。

### <a name="portal"></a>入口網站
您可以利用和先前判斷支援區域 (先前所示) 時相同的方式，判斷支援的 API 版本。

### <a name="rest-api"></a>REST API
若要探索哪些 API 版本可供資源類型使用，請使用 [列出所有資源提供者](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 作業。 

### <a name="powershell"></a>PowerShell
下列範例示範如何取得特定資源類型可用的 API 版本。

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

輸出如下：

```powershell
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
```

### <a name="azure-cli"></a>Azure CLI
您可以透過下列命令，取得資源提供者可用的 API 版本：

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>後續步驟
* 若要了解如何建立資源管理員範本，請參閱 [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
* 若要了解如何部署資源，請參閱 [使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。


