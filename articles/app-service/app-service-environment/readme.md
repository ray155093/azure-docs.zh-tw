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
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 5b1362854dbc3b0098718bd2ea3cffb06366000c
ms.contentlocale: zh-tw
ms.lasthandoff: 08/03/2017

---

# <a name="app-service-environment-documentation"></a>App Service Environment 環境文件
 Azure App Service Environment 是 Azure App Service 的功能，可提供完全隔離和專用的環境，以便安全地大規模執行 App Service 應用程式。 此功能可裝載 [Web 應用程式][webapps]、[行動裝置應用程式][mobileapps]、[API 應用程式][APIApps]及[函式][Functions]。

適合應用程式工作負載的 App Service Environment (ASE) 需要：

* 非常高的延展性。
* 隔離和安全的網路存取。

客戶可以在單一 Azure 區域中和跨多個 Azure 區域建立多個 ASE。 這種靈活性讓 ASE 很適合用於水平調整無狀態應用程式層的規模，以支援高 RPS 的工作負載。

ASE 已經過隔離，可執行只有單一客戶的應用程式，且一律會部署到 Azure 虛擬網路。 客戶可以使用[網路安全性群組][NSGs]，同時精確控制輸入和輸出應用程式網路流量。 應用程式也可以透過虛擬網路建立與內部部署公司資源的高速安全連線。

應用程式經常需要存取公司資源，例如內部資料庫和 Web 服務。 執行於 ASE 上的應用程式可透過[站對站][SiteToSite] VPN 連線和[Azure ExpressRoute][ExpressRoute] 連線來存取資源。

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

## <a name="app-service-environment-v1"></a>App Service 環境 v1 ##
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

