---
title: "Azure App Service Environment 簡介"
description: "Azure App Service Environment 簡要概觀"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0d078aefbf73a45298f397d02ab24b2c8232ecef
ms.contentlocale: zh-tw
ms.lasthandoff: 06/26/2017

---
# <a name="introduction-to-the-app-service-environment"></a>App Service 環境簡介 #
 
## <a name="overview"></a>概觀 ##

App Service Environment 是 Azure App Service 功能，可提供完全隔離和專用的環境，以便安全地在大規模作業執行 Azure App Service 應用程式。 此功能可裝載 [Web Apps][webapps]、[Mobile Apps][mobileapps]、[API Apps][APIapps] 及 [Functions][Functions]。

適合應用程式工作負載的 App Service Environment 需要：

- 非常高的延展性
- 隔離和安全的網路存取
- 高記憶體使用量

客戶可以在單一 Azure 區域，或跨多個 Azure 區域中建立多個 App Service Environment。 這使得 App Service 環境很適合用來水平調整無狀態應用程式層的規模，以支援高 RPS 工作負載。

App Service 環境已經過隔離，可執行只有單一客戶的應用程式，且一律會部署到虛擬網路。 客戶對於輸入和輸出的應用程式網路流量都有更細微的控制，且應用程式可以透過 VPN 建立與內部部署公司資源的高速安全連線。

您可以在[應用程式服務環境的讀我檔案][ASEReadme]中，取得 App Service Environment 的所有相關文章與做法。

如需 App Service Environment 如何提供高延展性和安全的網路存取的概觀，請參閱關於 App Service Environment 的 [AzureCon 深入探討](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/)。

如需使用多個 App Service Environment 水平延展的深入探討，請參閱關於如何設定[地理位置發佈的應用程式使用量](https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/)一文。

若要查看 AzureCon Deep Dive 中顯示之安全性架構的設定方式，請參閱有關使用 App Service Environment 實作 [分層安全性架構](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-layered-security) 的文章。

在 App Service 環境中執行之應用程式的存取權可能會受到 Web 應用程式防火牆 (WAF) 等上游裝置的管制。 [設定 App Service Environment 的 WAF](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall) 上的文章將說明這種情況。

## <a name="dedicated-environment"></a>專用的環境 ##

App Service Environment 以獨佔方式專屬於單一訂用帳戶，並可以裝載 100 個執行個體。 可以是單一 App Service 方案到 100 個單一執行個體 App Service 方案中的 100 個執行個體，以及兩者之間的所有項目。

App Service Environment 是由前端和背景工作角色所組成。 前端負責處理 HTTP/HTTPS 終止以及 App Service Environment 中應用程式要求的自動負載平衡。 前端會隨 App Service Environment 中的 App Service 方案相應放大而自動新增。

背景工作角色是裝載客戶應用程式的角色。 背景工作角色可以 3 個固定的大小提供：
* 單核心/3.5 GB RAM
* 2 核心/7GB RAM
* 4 核心/14GB RAM。

客戶不需要管理「前端」和「背景工作角色」。 所有的基礎結構會隨客戶的 App Service 方案相應放大而自動新增。 隨著 App Service 方案建立或相應縮小 App Service Environment，會視需要將基礎結構新增或移除。

App Service Environment 會有一般每月費率來支付基礎結構，且不會依 App Service Environment 的大小而變更。 接著，每個 App Service 方案核心會有其成本。 App Service Environment 中裝載的所有應用程式會位於隔離定價 SKU 中。 如需 App Service Environment 的定價詳細資訊，請參閱 [App Service 定價][Pricing]頁面，並檢閱 App Service Environment 可用的選項。

## <a name="virtual-network-support"></a>虛擬網路支援 ##

App Service Environment 只可在 Azure Resource Manager 虛擬網路中加以建立。 您可以從下列資源深入了解 Azure 虛擬網路：[Azure 虛擬網路常見問題集](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/)。 因為 App Service Environment 一律存在於虛擬網路中，而且更精確來說是在虛擬網路的子網路內，所以您可以運用虛擬網路的安全性功能來控制應用程式傳入和傳出網路通訊。

App Service Environment 可以是具有公用 IP 位址的網際網路對應，或只具有 Azure 內部負載平衡器 (ILB) 位址的內部對應。

您可以使用[網路安全性群組][NSGs]將傳入網路通訊限制為 App Service Environment 所在的子網路。 這可讓您在上游裝置和服務 (例如 Ｗeb 應用程式防火牆和網路 SaaS 提供者) 背後執行應用程式。

應用程式也經常需要存取公司資源，例如內部資料庫和 Web 服務。 如果在 Azure 虛擬網路中部署的 App Service Environment 具有內部部署網路的 VPN 連線，則 App Service Environment 中的應用程式將能夠存取內部部署資源。 無論 VPN 是[站對站](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/)或 [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/) VPN，這都可適用。

如需有關 App Service Environment 與虛擬網路和內部部署網路運作方式的詳細資訊，請參閱 [App Service Environment 的網路考量][ASENetwork]。

## <a name="asev1"></a>ASEv1 ##

App Service Environment 有兩個版本：ASEv1 和 ASEv2。 上述資訊著重在 ASEv2。 本節說明 ASEv1 與 ASEv2 之間的差異。 

在 ASEv1 中，您必須手動管理所有資源。 其中包含前端、背景工作角色和用於 IP 型 SSL 的 IP 位址。 首先，您必須將想要在其中裝載的背景工作角色集區相應放大，才能相應放大 App Service 方案。

ASEv1 使用與 ASEv2 不同的定價模式。 在 ASEv1 中，您需要支付每個配置的核心。 其中包含用於前端或未裝載任何工作負載之背景工作角色的核心。 在 ASEv1 中，App Service Environment 的預設最大調整大小總計是 55 個主機。 其中包含背景工作角色與前端。 ASEv1 的其中一個優點，是可以部署在傳統虛擬網路，以及資源管理員虛擬網路。 您可以從下列資源深入了解 ASEv1：[App Service Environment v1 簡介][ASEv1Intro]

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

