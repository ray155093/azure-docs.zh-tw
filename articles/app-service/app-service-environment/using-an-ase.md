---
title: "使用 Azure App Service 環境"
description: "如何在 Azure App Service 環境中建立、發佈及調整應用程式"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: c24f716d58f534d1439377234fa1263269961db7
ms.contentlocale: zh-tw
ms.lasthandoff: 06/26/2017

---
# <a name="using-an-app-service-environment"></a>使用 App Service 環境 #

## <a name="overview"></a>概觀 ##

App Service Environment (ASE) 是 Azure App Service 到客戶之 Azure 虛擬網路 (VNet) 中子網路的部署。 其中包括：

- 前端 – 這是 HTTP/HTTPS 在 ASE 中終止的位置
- 背景工作 – 這些是裝載應用程式的資源
- 資料庫 – 這會保留定義環境的資訊
- 儲存體 – 儲存體用來裝載客戶發佈的應用程式

> [!NOTE]
> App Service Environment 有兩個版本：ASEv1 和 ASEv2。 在 ASEv1，在中，您必須先管理資源，才可加以使用。 若要了解如何設定及管理 ASEv1，請參閱[設定 App Service 環境 v1][ConfigureASEv1]。 本文件的其餘部分著重於 ASEv2。
>
>

您可以使用適用於應用程式存取的外部 VIP 或適用於應用程式存取的內部 VIP，來部署 ASE (ASEv1 和 ASEv2)。 使用外部 VIP 的部署通常稱為外部 ASE，而內部版本稱為 ILB ASE，因為它會使用內部負載平衡器 (ILB)。 若要深入了解 ILB ASE，請參閱[建立和使用 ILB ASE][MakeILBASE]。

## <a name="create-a-web-app-in-an-ase"></a>在 ASE 中建立 Web 應用程式 ##

在 ASE 中建立 Web 應用程式與其正常建立程序相同，但只有一些小差異。 建立新的 App Service 方案時：

- 您可以挑選 ASE 作為位置，而不用挑選部署應用程式的地理位置
- 在 ASE 中建立的所有 App Service 方案必須在「隔離」定價層中。

如果您沒有 ASE，您可以遵循[建立 App Service 環境][MakeExternalASE]中的指示來建立一個。

若要在 ASE 中建立 Web 應用程式：

1. 按一下 [新增] &gt; [Web + 行動]，選取 [Web 應用程式]。
2. 提供 Web 應用程式的名稱。 如果您已在 ASE 中選取 App Service 方案，則應用程式的網域名稱會反映 ASE 的網域名稱。

    ![][1]

1. 選取一個訂用帳戶。
2. 提供新資源群組的名稱，或選取 [使用現有] 並從下拉式清單中挑選一個。
3. 選取 ASE 中現有的 App Service 方案，或透過下列步驟建立一個新的方案：
    1. 選取 [新建]。
    2. 提供 App Service 方案的名稱。
    3. 選取 [位置] 下拉式清單中您的 ASE。
    4. 選取 [隔離] 定價層。 按一下 [選取] 。
    5. 按一下 [確定]。
    
    ![][2]
1. 按一下 [建立] 。

## <a name="how-scale-works"></a>調整方式 ##

每個 App Service 應用程式都會在 App Service 方案中執行。 容器模型是：保存 App Service 方案的環境，和保存應用程式的 App Service 方案。 當您調整應用程式時，您可調整 App Service 方案，因而調整相同方案中的所有應用程式。

在 ASEv2 中，當您調整 App Service 方案時，系統會自動新增所需的基礎結構。 這不同於 ASEv1，您必須先在其中新增所需的基礎結構，才能建立或相應放大 App Service 方案。 在 ASEv2 中，這表示新增基礎結構後，調整作業會有時間延遲。

在多租用戶的 App Service 中，調整通常立即，因為沒有立即可用的資源集區可用來支援相應放大。 ASE 中沒有這類緩衝區，並在需要時配置資源。

在 ASE 中，您可以相應增加至 100 個執行個體。 這 100 個執行個體可以全都位於單一 App Service 方案中，或分散於多個 App Service 方案。

## <a name="ip-addresses"></a>IP 位址 ##

App Service 能夠將專用的 IP 位址配置給應用程式。 如下所述設定以 IP 為基礎的 SSL，就可以使用這項功能：[將現有的自訂 SSL 憑證繫結至 Azure Web Apps][ConfigureSSL]。 不過，ASE 中有一個值得注意的例外狀況：您無法在 ILB ASE 中新增其他要用於以 IP 為基礎之 SSL 的 IP 位址。

在 ASEv1 中，您必須先配置 IP 位址作為資源，才可以使用它們。 在 ASEv2 中，您只要如同在多租用戶的 App Service 中一樣，從您的應用程式使用它。 ASEv2 中一律會有一個備用位址 (最多 30 個 IP 位址)。 每次您使用一個位址時，就會新增另一個位址，如此一律會有立即可供使用的位址。 配置另一個 IP 位址時，需有時間延遲，以避免快速連續新增 IP 位址。

## <a name="front-end-scaling"></a>前端調整 ##

在 ASEv2 中，當您相應放大 App Service 方案時，系統會自動新增背景工作來支援它們。 除了隨著每個 ASE 一起建立的兩個前端，系統也會以每 15 個執行個體一個前端的速率，在您的 App Service 方案中自動相應放大前端。 這表示如果您有 15 個執行個體，則您會有三個前端。 如果調整至 30 個執行個體，則您會有四個前端 ，依此類推。

這應該遠超過大部分案例所需，但如果有需要以更快的速率相應放大，您可以在 App Service 方案中將比率變低為每 5 個執行個體一個前端。 如 [Azure App Service 價格][Pricing]中所述，變更比率費用需酌收費用。

前端資源是 ASE 的 HTTP/HTTPS 端點。 依照預設前端組態，每個前端的記憶體使用量始終大約為 60%。 客戶工作負載不會在前端上執行。 前端調整的關鍵因素是 CPU，其主要是由 HTTPS 流量所驅動。

## <a name="app-access"></a>應用程式存取 ##

在外部 ASE 中，建立應用程式時使用的網域不同於多租用戶的 App Service 並且包含 ASE 的名稱。 如需有關建立外部 ASE 的詳細資訊，請參閱[建立 App Service 環境][MakeExternalASE]。 外部 ASE 中的網域名稱看起來像 *.&lt;asename&gt;.p.azurewebsites.net*。 也就是說，如果您的 ASE 名為 _external-ase_，而且您在該 ASE 中裝載名為 _contoso_ 的應用程式，則您會在下列 URL 連到它：

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

URL *contoso.scm.external-ase.p.azurewebsites.net* 用來存取 Kudu 主控台，或可利用 Web 部署發佈您的應用程式。 如需 Kudu 主控台的詳細資訊，請參閱 [Azure App Service 的 Kudu 主控台][Kudu]。 Kudu 主控台提供給您的 Web UI 可供偵錯、上傳檔案、編輯檔案及更多功能。

在 ILB ASE 中，您會在部署時決定網域。 如需有關建立 ILB ASE 的詳細資訊，請參閱[建立並使用 ILB ASE][MakeILBASE]。 如果您指定網域名稱 _ilb-ase.info_，則該 ASE 中的應用程式會在應用程式建立期間使用該網域。 對於名為 _contoso_ 的應用程式，則 URL 會是：

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>發佈 ##

就如同多租用戶的 App Service，在 ASE 中，您可以透過下列各項發佈：

- Web 部署
- FTP
- 持續整合
- 在 Kudu 主控台中拖放
- IDE，例如 Visual Studio、Eclipse 或 Intellij IDEA

在外部 ASE 中，這一切的行為方式都相同。 如需詳細資訊，請參閱[在 Azure App Service 中部署][AppDeploy]。 

就 ILB ASE 而言，發佈的差異很大。 使用 ILB ASE，所有發佈端點都只能透過 ILB 取得。 ILB 位於虛擬網路的 ASE 子網路中的私人 IP。 如果您沒有 ILB 的網路存取權，便無法在該 ASE 上發佈任何應用程式。 如[建立和使用 ILB ASE][MakeILBASE] 中所述，您需要在系統中設定應用程式的 DNS。 其中包含 SCM 端點。 如果未正確加以定義，您將無法發佈。 您的 IDE 也需要有 ILB 的網路存取權，以便直接發佈到它。

以網際網路為基礎的 CI 系統，例如 Github 和 VSTS，不會與 ILB ASE 搭配運作，因為發佈端點不是網際網路可存取。 相反地，您需要使用會使用提取模型的 CI 系統，例如 Dropbox。

ILB ASE 中應用程式的發佈端點會使用用來建立 ILB ASE 的網域。 這可以在 app 的發行設定檔，以及 app 的入口網站刀鋒視窗中看到 (在 [概觀]  >  [基本資訊]，以及 [屬性])。 

## <a name="pricing"></a>價格 ##

ASEv2 有新的定價 SKU，其只能用於名為 [隔離] 的 ASEv2 使用。 ASEv2 中裝載的所有 App Service 方案都位於「隔離」定價 SKU 中。 除了 App Service 方案的價格，ASE 本身有固定費用。 這個價格不會隨著 ASE 的大小變更。 

其他可能的費用適用於調整前端級別比率或前端大小。 如果您調整級別比率，好讓前端更快速地新增，您將要支付任何尚未自動新增至系統的額外核心費用。 同樣地，如果您針對前端選取較大的大小，您將要支付任何未自動配置的核心費用。 例如，如果您將級別比率調整為 10，這表示系統會針對 App Service 方案中每 10 個執行個體新增一個前端。 固定費用涵蓋每隔 15 個執行個體 1 個前端的級別費率。 如果級別比率為 10，您將要支付針對 10 個 ASP 執行個體新增的第三個後端費用，但是達到 15 個執行個體時，您就不需要支付其費用，因為它已自動新增。

如需詳細資訊，請參閱 [Azure App Service 價格][Pricing]。

## <a name="deleting-an-ase"></a>刪除 ASE ##

如果您要刪除 App Service 環境，只需使用 App Service 環境刀鋒視窗頂端的 [刪除] 動作。 當您這麼做時，系統將提示您輸入 App Service 環境的名稱，以確認您真正想要執行這項操作。 請注意，當您刪除 App Service 環境時，將同時刪除其中包含的所有內容。 

![][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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

