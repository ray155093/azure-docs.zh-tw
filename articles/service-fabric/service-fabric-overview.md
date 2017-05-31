---
title: "Azure 上的 Service Fabric 概觀 | Microsoft Docs"
description: "組成多個微服務以提供調整和恢復功能的 Service Fabric 概觀。 Service Fabric 是一種分散式系統平台，用來建置可調整、可靠且輕鬆管理的雲端應用程式。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: mfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 81dae4c35fead96af8a8a2823952a0bd7f2e537e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="overview-of-azure-service-fabric"></a>Azure Service Fabric 概觀
Azure Service Fabric 是分散式系統平台，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務。 Service Fabric 也可解決開發與管理雲端應用程式時遭遇的重大挑戰。 開發人員與管理員能夠避免複雜的基礎結構問題，專注於實作關鍵且嚴格要求之可調整、可信賴且可管理的工作負載。 Service Fabric 代表新一代的中介軟體平台，可建置與管理這些企業級的 Tier-1 雲端規模應用程式。

此 Channel9 短片簡介 Service Fabric 和微服務：<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

此 Microsoft Virtual Academy 長片說明 Service Fabric 核心概念：<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-overview/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="applications-composed-of-microservices"></a>由微服務組成的應用程式
Service Fabric 可讓您建置並管理由微服務組成的應用程式，這類應用程式可調整且可靠，以非常高的密度在共用的機器集區 (稱為叢集) 上執行。 它提供精密的執行階段，可建置分散式、可擴充的無狀態與可設定狀態的微服務。 還提供完整的應用程式管理功能，以佈建、部署、監視、升級/修補及刪除已部署的應用程式。

為什麼微服務方式如此重要？ 兩個主要原因如下：

* 您可根據應用程式的需求，調整應用程式的不同部分。
* 開發團隊能夠更敏捷地推出更動，進而更快速且更頻繁地為您的客戶提供新功能。

Service Fabric 提供技術支援給現今許多 Microsoft 服務，包括 Azure SQL Database、Azure Cosmos DB、Cortana、Microsoft Power BI、Microsoft Intune、「Azure 事件中樞」、「Azure IoT 中樞」、「商務用 Skype」以及許多核心 Azure 服務。

Service Fabric 是針對建立雲端原生服務量身打造而成，此類服務可視需要以小規模開始，接著擴充為包含成千上萬個機器的大規模服務。

現今的網際網路級別服務是使用微服務建立。 微服務範例包括通訊協定閘道器、使用者設定檔、購物車、清查處理、佇列和快取。 Service Fabric 是微服務平台，會給予每一個可以是無狀態或可設定狀態的微服務唯一的名稱。

Service Fabric 為由微服務組成的應用程式，提供完整的執行階段與生命週期管理功能。 其將微服務裝載在容器中，這些容器部署在整個 Service Fabric 叢集之中並已啟動。 從虛擬機器改成用容器，可讓密度極為大幅地增加。 同樣地，從容器改用微服務也可讓密度再提升一個層次。 例如，單一 Azure SQL Database 叢集是由數百個機器所組成，這些機器執行數萬個容器，而容器總共裝載了數十萬個資料庫。 每個資料庫都是 Service Fabric 的可設定狀態微服務。 前面提到過的其他服務也是如此，這也是為什麼會使用「超大規模」一詞來描述 Service Fabric 的功能。 如果容器的密度高，微服務就有超大的規模。

如需有關微服務方法的詳細資訊，請閱讀[為何要用微服務方式建置應用程式？](service-fabric-overview-microservices.md)。

## <a name="container-deployment-and-orchestration"></a>容器部署和協調流程
Service Fabric 是跨電腦叢集的微服務[協調者](service-fabric-cluster-resource-manager-introduction.md)。 微服務有許多開發方式，從 [Service Fabric 程式設計模型](service-fabric-choose-framework.md)，到部署[來賓可執行檔](service-fabric-deploy-existing-app.md)。 Service Fabric 可以在容器映像中部署服務。 重要的是，您可以在相同應用程式中同時混合容器中的服務和處理序中的服務。 如果您只想要跨電腦叢集[部署和管理容器映像](service-fabric-containers-overview.md)，Service Fabric 是最佳選擇。

## <a name="create-clusters-for-service-fabric-anywhere"></a>隨處建立 Service Fabric 的叢集
您可以在許多環境 (包括 Azure 或內部部署、Windows Server 上或 Linux 上) 建立 Service Fabric 的叢集。 此外，SDK 中的開發環境等同於生產環境，沒有涉及模擬器。 換句話說，在本機開發叢集上執行的東西，會部署到其他環境中的相同叢集。

如需建立叢集內部部署的詳細資訊，請參閱[在 Windows Server 或 Linux 上建立叢集](service-fabric-deploy-anywhere.md)，或是[透過 Azure 入口網站](service-fabric-cluster-creation-via-portal.md)針對 Azure 建立叢集。

![Service Fabric 平台][Image1]

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Service Fabric 的無狀態與具狀態微服務
Service Fabric 可讓您建置由微服務組成的應用程式。 無狀態微服務 (如通訊協定閘道器、Web Proxy) 不會維護要求之外的可變動狀態及來自服務的回應。 Azure 雲端服務背景工作角色即為無狀態服務的範例。 可設定狀態的微服務 (如使用者帳戶、資料庫、裝置、購物車、佇列) 會維護要求及其回應外的可變動授權狀態。 現今的網際網路級別應用程式包含無狀態與可設定狀態微服務的組合。

為什麼有具狀態的微服務以及無狀態的微服務？ 兩個主要原因如下：

* 藉由將程式碼和資料放在相同電腦上且盡量靠近，您可以建立高輸送量、低延遲、容錯的線上交易處理 (OLTP) 服務。 其中的一些範例包括互動式店面、搜尋、物聯網 (IoT) 系統、交易系統、信用卡處理和詐欺偵測系統，以及個人記錄管理。
* 您可以簡化應用程式的設計。 可設定狀態的微服務不需要其他佇列與快取，但傳統上為滿足純粹無狀態應用程式的可用性與延遲需求則需要這些項目。 可設定狀態服務有高可用性、低延遲性的特性，整體來說可減少需要在應用程式中管理的移動組件。

如需有關使用 Service Fabric 的應用程式模式的詳細資訊，請參閱您的服務適用的[應用程式案例](service-fabric-application-scenarios.md)和[選擇程式設計模型架構](service-fabric-choose-framework.md)。

您也可以觀看此 Microsoft Virtual Academy 影片，以取得無狀態和具狀態服務的概觀︰<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">  
<img src="./media/service-fabric-overview/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="application-lifecycle-management"></a>應用程式生命週期管理
Service Fabric 可支援雲端應用程式的完整應用程式生命週期管理。 此生命週期包括開發到部署、每日管理、維護，到最終的解除委任。

Service Fabric 應用程式生命週期管理讓應用程式管理員和 IT 操作員能夠使用簡單、低接觸的工作流程來佈建、部署、修補與監視應用程式。 這些內建的工作流程能大幅降低 IT 操作員的負擔，讓應用程式持續可用。

大多數的應用程式都結合了無狀態與可設定狀態的微服務，以及其他一起部署的可執行檔/執行階段。 Service Fabric 擁有應用程式與封裝微服務的強式型別，因此能部署多個應用程式執行個體。 每個執行個體都能獨立管理與升級。 重要的是，Service Fabric 能夠部署「任何」的執行檔或執行階段，並使其可靠。 例如，Service Fabric 會部署 .NET、ASP.NET Core、Node.js、Java 虛擬機器、指令碼、Angular 或任何其他組成您應用程式的項目。

如需應用程式生命週期管理的詳細資訊，請參閱[應用程式生命週期](service-fabric-application-lifecycle.md)。 如需有關如何部署任何程式碼的詳細資訊，請參閱[部署來賓執行檔](service-fabric-deploy-existing-app.md)。

您也可以觀看此 Microsoft Virtual Academy 影片，以取得應用程式生命週期管理概觀︰<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">  
<img src="./media/service-fabric-overview/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="key-capabilities"></a>主要功能
藉由使用 Service Fabric，您可以：

* 開發可大幅調整且自我修復的應用程式。
* 使用 Service Fabric 程式設計模型，可開發由微服務所組成的應用程式。 或者，您可以只裝載客體執行檔及你選擇的其他應用程式架構，例如 ASP.NET Core 或 Node.js。
* 開發高度可靠的無狀態與可設定狀態微服務。
* 部署並協調「包含整個叢集中 Windows 容器和 Docker 容器」的容器。 這些容器可以是容器來賓執行檔或可靠的無狀態與具狀態微服務。 不論是哪一種情況，您都會得到容器連接埠至主機連接埠的對應、容器可搜尋性和自動容錯移轉。
* 使用可設定狀態的微服務取代快取和佇列，以簡化應用程式的設計。
* 部署至 Azure，或部署至執行 Windows 或 Linux 的內部部署資料中心，且程式碼零變更。 只要撰寫一次，就能夠在任何地方部署至任何 Service Fabric 叢集。
* 採用「機器的資料中心」法開發。 本機開發環境的程式碼與在 Azure 資料中心執行的程式碼相同。
* 部署應用程式 (單位為秒)。
* 部署密度比虛擬機器高的應用程式，亦即為每部機器部署數百或數千個應用程式。
* 並列部署相同應用程式的不同版本，並獨立升級每個應用程式。
* 無須停機即可管理可設定狀態應用程式的生命週期，包括即時升級與非即時升級。
* 使用 .NET API、Java (Linux)、PowerShell、Azure CLI (Linux) 或 REST 介面來管理應用程式。
* 獨立地升級與修補應用程式內的微服務。
* 監視和診斷應用程式的健全狀況，並設定用來執行自動修復的原則。
* 相應放大或相應縮小叢集中的節點數目，以及相應增加或相應減少每個節點的大小。 當您調整節點時，您的應用程式會自動調整，並根據可用資源分佈。
* 監看自我修復資源平衡器會協調整個叢集的應用程式重新發佈。 Service Fabric 會從失敗中復原，並會根據可用資源將負載分佈最佳化。
* 先使用錯誤分析服務，在您的服務上執行混亂測試以找出問題和失敗，然後才在生產環境中執行。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
* 其他資訊：
  * [為何要用微服務方式建置應用程式？](service-fabric-overview-microservices.md)
  * [術語概觀](service-fabric-technical-overview.md)
* 設定 Service Fabric [開發環境](service-fabric-get-started.md)  
* [選擇程式設計模型架構](service-fabric-choose-framework.md) 做為您的服務
* 了解 [Service Fabric 支援選項](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

