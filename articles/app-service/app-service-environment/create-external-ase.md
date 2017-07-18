---
title: "建立外部 Azure App Service Environment"
description: "說明如何在建立應用程式或獨立時，建立 Azure App Service Environment"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 3f1418b71a7327264e29e3f08ef97b254ee9930d
ms.contentlocale: zh-tw
ms.lasthandoff: 06/26/2017

---
# <a name="create-an-external-app-service-environment"></a>建立外部 App Service Environment #

App Service Environment (ASE) 是 Azure App Service 到 Azure 虛擬網路 (VNet) 中之子網路的部署。 部署 ASE 的做法有二種：

- 使用外部 IP 位址上的 VIP，通常稱為_外部 ASE_。
- 使用內部 IP 位址上的 VIP，通常稱為 _ILB ASE_，因為內部端點是內部負載平衡器 (ILB)。

本文說明如何建立外部 ASE。 如需 ASE 的概觀，您可以從 [App Service Environment 簡介][Intro]開始。如需建立 ILB ASE 的詳細資訊，請參閱[建立及使用 ILB ASE][MakeILBASE]。

## <a name="before-you-create-your-ase"></a>建立 ASE 之前 ##

請務必注意在建立 ASE 之後無法變更的項目，這些項目為：

- 位置
- 訂閱
- 資源群組
- 使用的虛擬網路
- 使用的子網路
- 子網路大小

> [!NOTE]
> 挑選虛擬網路然後指定子網路時，請確定它足夠大以容納未來的成長。 建議的大小是 `/25`，具有 128 個位址。
>

## <a name="three-ways-to-create-an-ase"></a>建立 ASE 有三種方式 ##

建立 ASE 有 3 種方式。 您可以建立 ASE：

- 在建立 App Service 方案時，它會在同一個步驟中建立 ASE 和 App Service 方案。
- 從獨立 ASE 建立 UI，它會建立其中沒有任何項目的 ASE。 這是更進階的 ASE 建立 UI 體驗，也是您使用內部負載平衡器 (ILB) 建立 ASE 的方向。
- 從 Resource Manager 範本。 這是針對進階使用者，並涵蓋在[從範本建立 ASE][MakeASEfromTemplate]。

沒有 ILB 的已建立 ASE 具有公用 VIP。 這表示到 ASE 中應用程式的所有 HTTP 流量會叫用網際網路可存取 IP 位址。 具有 ILB 的 ASE 在虛擬網路 IP 位置上有端點。 這些應用程式不會直接向網際網路公開。

## <a name="create-an-ase-and-an-app-service-plan-together"></a>一起建立 ASE 和 App Service 方案 ##

App Service 方案是應用程式的容器。 當您在 App Service 中建立應用程式時，您一律需要挑選或建立 App Service 方案。 容器模型是：保存 App Service 方案的環境，和保存應用程式的 App Service 方案。

若要在 App Service 方案建立期間建立 ASE：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [新增] &gt; [Web + 行動] &gt; [Web 應用程式]

    ![][1]
1. 選取您的訂用帳戶。 如果您有多個訂用帳戶，請注意，若要在您的 ASE 中建立應用程式，必須使用您在建立 ASE 時所使用訂用帳戶來建立。
1. 選取或建立資源群組。 *資源群組*可讓您以單位的形式管理相關的 Azure 資源，並可在您為應用程式建立*角色型存取控制* (RBAC) 規則時發揮效用。 如需詳細資訊，請參閱 [Azure Resource Manager概觀][ARMOverview]。
1. 按一下 App Service 方案，然後選取 [新建]。

    ![][2]
1. 在 [位置] 下拉式清單中，挑選您想要建立 ASE 的區域。 如果您挑選現有 ASE，將不會建立新的 ASE，而是只會在您選取的 ASE 中建立 App Service 方案。
1. 按一下 [定價方案] UI，並且挑選其中一項**隔離**定價 SKU。 挑選**隔離** SKU 卡以及不是 ASE 的位置，表示您想要在該位置建立新的 ASE。 在您於定價卡分頁上按一下 [選取] 之後，執行此動作會顯示 ASE 建立 UI。 **隔離** SKU 僅供與 ASE 搭配使用。 您也無法在 ASE 中使用**隔離**以外的其他任何定價 SKU。

    ![][3]
1. 輸入 ASE 的名稱。 ASE 的名稱是用於應用程式的可定址名稱。 如果 ASE 的名稱是 _appsvcenvdemo_，則子網域名稱會是 .appsvcenvdemo.p.azurewebsites.net。 如果您因此建立名為 mytestapp 的應用程式，則可定址於 mytestapp.appsvcenvdemo.p.azurewebsites.net。 您無法在 ASE 的名稱中使用空白字元。 如果您在名稱中使用大寫字元，則網域名稱會是該名稱的全小寫版本。

    ![][4]
1. 選擇 [新建] 或 [選取現有]。 只有當您在選取的區域中有虛擬網路時，才能使用選取現有虛擬網路的選項。 如果您選取 [新建]，提供虛擬網路的名稱，則會建立該名稱的新資源管理員虛擬網路，具有已選取區域中的位址空間 `192.168.250.0/23`。 如果您選取 [選取現有]，您需要：
    1. 如果您有多個位址區塊，請選取虛擬網路位址區塊。
    2. 提供新的子網路名稱。
    3. 選取子網路的大小。 **提醒：大小應該足以容納 ASE 的未來成長。** 建議的大小是 `/25`，其中具有 128 個位址，而且可以處理最大大小的 ASE。 舉例來說，不建議 `/28`，因為只有 16 個位址可供使用。 基礎結構需求會用盡至少 5 個地址，讓您在 `/28` 子網路中只有最多 11 個執行個體的規模。
    4. 選取子網路 IP 範圍。

在您選取 [建立] 之後，ASE 建立流程隨即開始。 這也會建立 App Service 方案和應用程式。 ASE、App Service 方案和應用程式會在相同的訂用帳戶底下，同時在相同的資源群組中。 如果您需要 ASE 是在與 App Service 方案和應用程式不同的資源群組中，或者如果您需要 ILB ASE，則使用獨立 ASE 建立體驗。

## <a name="create-an-ase-by-itself"></a>由 ASE 本身建立 ##

透過獨立 ASE 建立流程，將會建立其中沒有任何項目的 ASE。 空白 ASE 仍會產生基礎結構的每月費用。 透過此工作流程的主要原因是建立具有 ILB 的 ASE，或是在它自己的資源群組中建立 ASE。 建立 ASE 之後，您可以在 ASE 中建立應用程式，方法是使用一般應用程式建立體驗，以及選取新的 ASE 作為位置。

在 Azure Marketplace 中搜尋 App Service Environment，或經由 [新增] -&gt; [Web + 行動] -&gt; [App Service 環境]，即可存取 ASE 建立 UI。 若要使用獨立建立體驗來建立 ASE：

1. 提供 ASE 的名稱。 針對 ASE 指定的名稱將用於在 ASE 中建立的應用程式。 如果 ASE 的名稱是 mynewdemoase，則子網域名稱會是 .mynewdemoase.p.azurewebsites.net。 如果您因此建立名為 mytestapp 的應用程式，則可定址於 mytestapp.mynewdemoase.p.azurewebsites.net。 您無法在 ASE 的名稱中使用空白字元。 如果您在名稱中使用大寫字元，則網域名稱會是該名稱的全小寫版本。 如果您使用 ILB，則 ASE 名稱不會用於您的子網域中，但是會在 ASE 建立期間明確指定。

    ![][5]
1.  選取您的訂用帳戶。 用於 ASE 的訂用帳戶也是將用來在該 ASE 中建立所有應用程式的帳戶。 您無法將 ASE 放在另一個訂用帳戶中的虛擬網路。
1.  選取或指定新的資源群組。 用於 ASE 的資源群組必須是與用於您的虛擬網路的相同。 如果您選取現有 VNet，則您的 ASE 資源群組選取項目將會更新，以反映虛擬網路的資源群組。

    ![][6]
1. 請選取您的虛擬網路及位置選項。 您可以選擇建立新的虛擬網路，或選取現有的虛擬網路。 如果您選取新的虛擬網路，則可以指定名稱和位置。 新的虛擬網路會有位址範圍 192.168.250.0/23，和定義為 192.168.250.0/24 名為 **default** 的子網路。 您只能選取資源管理員虛擬網路。 [VIP 類型] 選項會決定您的 ASE 是否可以從網際網路 (外部) 直接存取，或者它使用內部負載平衡器 (ILB)。 若要深入了解，請參閱[在 App Service Environment 中建立及使用內部負載平衡器][MakeILBASE]。 如果您選取 [外部] VIP 類型，則可以選取系統針對以 IP 為主的 SSL 用途會建立幾個外部 IP 位址。 如果您選取 [內部]，則需要指定 ASE 將使用的子網域。 ASE 可以部署到使用公用位址範圍「或」RFC1918 位址空間 (也就是 私人位址) 的虛擬網路。 若要搭配使用虛擬網路與公用位址範圍，您必須事先建立虛擬網路。 選取現有的虛擬網路時，您必須在 ASE 建立期間建立新的子網路。 **您無法在入口網站中使用預先建立的子網路。如果您使用 Resource Manager 範本建立 ASE，則可以使用現有的子網路建立 ASE。** 若要從範本建立 ASE，請參閱[從範本建立 App Service Environment][MakeASEfromTemplate]

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

您仍然可以建立 ASE 功能 (ASEv1) 的第一個版本執行個體。 若要達到該體驗，請在 Marketplace 中搜尋 **App Service Environment v1** 建立體驗與獨立 ASE 建立體驗相同。 完成時，您的 ASEv1 隨即建立，具有兩個前端和兩個背景工作角色。 使用 ASEv1 時，您必須管理「前端」和「背景工作角色」。 當您建立您的 App Service 方案時，它們不會自動新增。 前端可作為 HTTP/HTTPS 端點，將流量傳送到背景工作角色 (也就是裝載您的應用程式的角色)。 建立 ASE 之後，您可以調整數量。 若要深入了解 ASEv1，請參閱 [App Service Environment v1 簡介][ASEv1Intro]。 如需更多關於調整、管理及監視 ASEv1 的詳細資料，請參閱[如何設定 App Service Environment][ConfigureASEv1]。

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png



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

