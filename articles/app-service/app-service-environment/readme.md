---
title: "Azure App Service Environment 讀我檔案"
description: "列出描述 Azure App Service Environment 的文件"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 77452413-5193-4762-8b3d-5fa8e4edf1ca
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8b401036d4fa8e6c4e6430433b1641f5cb4ae010
ms.contentlocale: zh-tw
ms.lasthandoff: 06/26/2017

---

# <a name="app-service-environment-documentation"></a>App Service 環境文件
App Service Environment 是 Azure App Service 功能，可提供完全隔離和專用的環境，以便安全地在大規模作業執行 Azure App Service 應用程式。 此功能可裝載 [Web Apps][webapps]、[Mobile Apps][mobileapps]、[API Apps][APIApps] 及 [Functions][Functions]。

適合應用程式工作負載的 App Service Environment 需要：

* 非常高的延展性
* 隔離和安全的網路存取

客戶可以在單一 Azure 區域，以及跨多個 Azure 區域中建立多個 App Service 環境。 這使得 App Service 環境很適合用來水平調整無狀態應用程式層的規模，以支援高 RPS 工作負載。

App Service 環境已經過隔離，可執行只有單一客戶的應用程式，且一律會部署到虛擬網路。 客戶可以使用[網路安全性群組][NSGs]，精確控制輸入和輸出應用程式網路流量。 應用程式也可以透過虛擬網路建立與內部部署公司資源的高速安全連線。

應用程式經常需要存取公司資源，例如內部資料庫和 Web 服務。 只要是可透過[站對站][SiteToSite] VPN 和 [Azure ExpressRoute][ExpressRoute] 連接來取得的資源，App Service 環境上執行的應用程式都可以存取。

* [何謂 App Service Environment？][Intro]
* [建立 App Service Environment][MakeExternalASE]
* [建立內部負載平衡器 App Service Environment][MakeILBASE]
* [使用 App Service Environment][UsingASE]
* [網路考量和 App Service Environment][ASENetwork]
* [從範本建立 App Service Environment][MakeASEfromTemplate]


## <a name="videos"></a>影片
利用 Azure App Service 的企業主要現代化 PaaS
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

部署高度可擴充且安全的應用程式
>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

在 Azure App Service 上執行企業 Web 和行動裝置應用程式
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]

## <a name="asev1"></a>ASEv1 ##
App Service Environment 有兩個版本：ASEv1 和 ASEv2。 如需 ASEv1 的資訊，請參閱 [App Service Environment v1 文件][ASEv1README]。


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[ASEv1README]: ../app-service-app-service-environments-readme.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-site-to-site-create.md
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

