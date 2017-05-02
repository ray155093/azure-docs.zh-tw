---
title: "了解 Azure Service Fabric | Microsoft Docs"
description: "Azure Service Fabric 概觀與入門指南。 了解關於 Service Fabric 的資訊，並開始開發可調整、可靠、易於管理，且是由各種微服務組成的應用程式。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/18/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 94b697d01d31463d1744d7e8d4f1169a2f366242
ms.lasthandoff: 04/18/2017


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>您想要了解 Service Fabric 嗎？
此入門提供關於 Service Fabric 的簡要概觀、核心概念與術語簡介、入門指南，以及關於 Service Fabric 每個領域的概觀。  此入門不包含完整的內容清單，但會連結至 Service Fabric 每個領域的概觀與入門文章。 

## <a name="the-five-minute-overview"></a>5 分鐘概觀
Azure Service Fabric 是分散式系統平台，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務。  Service Fabric 可解決開發與管理雲端應用程式時遭遇的重大挑戰。 開發人員與系統管理員可藉由使用 Service Fabric，避免解決複雜的基礎結構問題。  相反地，您可以專注於實作高需求的任務關鍵性工作負載，了解其具備可調整、可靠和可管理等優異特性。 Service Fabric 代表新一代的中介軟體平台，可建置與管理這些企業級的 Tier-1 雲端規模應用程式。  

此 Channel9 短片簡介 Service Fabric 和微服務：<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>詳細概觀
Service Fabric 可讓您建置和管理由各種微服務所組成、可調整且可靠的應用程式。  這些微服務會在電腦共用集區上以極高密度的方式執行，此稱為叢集。 它提供精密的執行階段，可建置分散式、可擴充的無狀態與可設定狀態的微服務。 還提供完整的應用程式管理功能，以佈建、部署、監視、升級/修補及刪除已部署的應用程式。  若要深入了解，請參閱 [Service Fabric 概觀](service-fabric-overview.md)。

為何採用微服務設計方法？ 所有應用程式會隨著時間而演化。 成功的應用程式因為有實用性而演化。 您對現今的需求了解多少，以及未來需求會有何變化？ 有時，將發佈一個簡單的應用程式作為概念證明是一項推動因素，因為我們知道可以在之後重新設計該應用程式。 另一方面，當公司談到針對雲端進行建置時，都會對成長和使用量有所預期。 問題在於無法預期成長和範圍。 開發人員希望既能夠迅速建立原型，同時又知道可以調整應用程式來因應無法預測的成長和使用量。  [什麼是微服務？](service-fabric-overview-microservices.md)說明微服務設計方法如何面對這些挑戰，以及您如何建立可相應增加或減少規模、測試、部署及獨立管理的微服務。

Service Fabric 提供可靠且彈性的平台，可讓您撰寫及執行許多類型的商務應用程式和服務。  您也可以執行任何現有的應用程式 (以任何語言所撰寫)。  這些應用程式和微服務可以是無狀態或具狀態，而且它們會跨虛擬機器進行資源平衡，以達到最佳效率。 Service Fabric 的獨特架構可讓您在應用程式中執行接近即時的資料分析、記憶體中計算、平行交易和事件處理。 您可以輕鬆地根據變更的資源需求上下[調整您的應用程式](service-fabric-concepts-scalability.md) (實際上是向內或向外擴充)。 若要了解您可建立之應用程式和服務的類別，以及客戶案例研究，請參閱[應用程式案例](service-fabric-application-scenarios.md)以及[模式和案例](service-fabric-patterns-and-scenarios.md)。

此 Microsoft Virtual Academy 長片說明 Service Fabric 核心概念：<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>開始建立您的第一個應用程式 
藉由使用 Service Fabric SDK 和工具，您不僅可以在 Windows、Linux 或 MacOS 環境中開發應用程式，還可以將這些應用程式部署到在 Windows 或 Linux 上執行的叢集。  下列指南將可讓您在幾分鐘內就部署完應用程式。  在執行您的第一個應用程式之後，請下載並執行我們的一些[範例應用程式](http://aka.ms/servicefabricsamples)。 特別是從[開始使用範例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)著手

### <a name="on-windows"></a>在 Windows 上
Service Fabric SDK 包含的 Visual Studio 增益集提供用來建立、部署和偵錯 Service Fabric 應用程式的範本和工具。 這些主題將逐步引導您完成在 Visual Studio 中建立第一個應用程式並在開發電腦上執行它的程序。

[設定開發環境](service-fabric-get-started.md)
[建立第一個應用程式 (C#)](service-fabric-create-your-first-application-in-visual-studio.md)

#### <a name="practical-hands-on-labs"></a>實用的線上實習實驗室
請嘗試這個廣泛的[實習實驗室第 1 部分](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx)，以熟悉 Service Fabric 的端對端開發流程。  了解如何建立無狀態服務、設定監視和健康情況報告，以及執行應用程式升級。 完成第 1 部分後，執行[實習實驗室第 2 部分](http://aka.ms/sflab2)，此部分會引領您完成具狀態服務。

下列 Channel9 影片將逐步引導您完成在 Visual Studio 中建立 C# 應用程式的程序：  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>在 Linux 上
Service Fabric 提供了在 Linux 上建置服務的 .NET Core 和 Java SDK。 這些主題將逐步引導您完成在 Linux 上建立第一個 Java 或 C# 應用程式，並在開發電腦上執行該應用程式的程序：[設定開發環境](service-fabric-get-started-linux.md)、[建立您的第一個應用程式 (Java)](service-fabric-create-your-first-linux-application-with-java.md)，以及[建立您的第一個應用程式 (C#)](service-fabric-create-your-first-linux-application-with-csharp.md)。

下列 Microsoft Virtual Academy 影片會引導您完成在 Linux 上建立 Java 應用程式的程序︰  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>在 MacOS 上
您可以在 MacOS X 上建置要在 Linux 叢集上執行的 Service Fabric 應用程式。 這些文章將說明如何設定您的 Mac 以供開發用途，逐步引導您在 MacOS 上建立 Java 應用程式，並在 Ubuntu 虛擬機器上執行該應用程式：[設定開發環境](service-fabric-get-started-mac.md)以及[建立您的第一個應用程式 (Java)](service-fabric-create-your-first-linux-application-with-java.md)。

## <a name="core-concepts"></a>核心概念
[Service Fabric 術語](service-fabric-technical-overview.md)、[應用程式模型](service-fabric-application-model.md)及[支援的程式設計模型](service-fabric-choose-framework.md)提供更多概念和描述，但這裡提供基本概念。

<table><tr><th>核心概念</th><th>設計階段</th><th>執行階段</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-app-type-service-type-app-package-and-manifest-service-package-and-manifest"></a>設計階段：應用程式類型、服務類型、應用程式封裝和資訊清單、服務封裝和資訊清單
應用程式類型是指派給服務類型集合的名稱/版本。 定義在 ApplicationManifest.xml 檔案中，內嵌在應用程式封裝目錄，然後會複製到 Service Fabric 叢集的映像存放區中。 您可以接著從這個應用程式類型建立一個具名應用程式，然後在叢集內執行該應用程式。 

服務類型是指派給服務的程式碼封裝、資料封裝及組態封裝的名稱/版本。 定義在 ServiceManifest.xml 檔案中，內嵌在服務封裝目錄中，然後應用程式封裝的 ApplicationManifest.xml 檔案會參考此服務封裝目錄。 在叢集內，建立具名應用程式之後，可以從應用程式類型的其中一個服務類型建立具名服務。 服務類型會由其 ServiceManifest.xml 檔案來描述。  其包含在執行階段載入的可執行程式碼服務組態設定，以及服務所取用的靜態資料。

![Service Fabric 應用程式類型和服務類型][cluster-imagestore-apptypes]

應用程式封裝是一個磁碟目錄，其中包含應用程式類型的 ApplicationManifest.xml 檔案，此檔案會參考組成應用程式類型之每個服務類型的服務封裝。 例如，電子郵件應用程式類型的應用程式封裝可能包含指向佇列服務封裝、前端服務封裝、資料庫服務封裝的參考。 應用程式封裝目錄中的檔案會複製到 Service Fabric 叢集的映像存放區。 

服務封裝是一個磁碟目錄，其中包含服務類型的 ServiceManifest.xml 檔案，此檔案會參考服務類型的程式碼、靜態資料及組態封裝。 應用程式類型的 ApplicationManifest.xml 檔案會參考服務封裝目錄中的檔案。 例如，服務套件可能會參考構成資料庫服務的程式碼、靜態資料和組態封裝。

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>執行階段︰叢集和節點，具名應用程式、具名服務、資料分割及複本
[Service Fabric 叢集](service-fabric-deploy-anywhere.md)是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 叢集可擴充至數千部機器。

隸屬於叢集的機器或 VM 即稱為節點。 需為每個節點指派節點名稱 (字串)。 節點具有各種特性，如 placement 屬性。 每部機器或 VM 皆有自動啟動的 Windows 服務 FabricHost.exe，該服務會在開機時開始執行，然後啟動兩個可執行檔：Fabric.exe 和 FabricGateway.exe。 這兩個執行檔構成節點。 針對開發或測試案例，您可以藉由執行多個 Fabric.exe 和 FabricGateway.exe 執行個體，在單一機器或 VM 上裝載多個節點。

具名應用程式是執行一或多個特定功能之具名服務的集合。 服務會執行完整且獨立的函數 (其可獨立於其他服務啟動和執行)，並且是由程式碼、組態和資料組成。 在應用程式封裝被複製到映像存放區之後，您可藉由指定應用程式封裝的應用程式類型 (使用其名稱/版本)，在叢集內建立應用程式的執行個體。 系統會為每個應用程式類型執行個體指派一個看起來如下的 URI 名稱：*fabric:/MyNamedApp*。 在叢集中，您可以從單一應用程式類型建立多個具名應用程式。 也可以從不同的應用程式類型建立具名應用程式。 每個具名應用程式都是獨立管理和控制版本。

在建立具名應用程式之後，您可藉由指定服務類型 (使用其名稱/版本)，在叢集內建立它其中一種服務類型的執行個體。 需為每個服務類型執行個體指派一個 URI (名稱範圍需在其具名應用程式的 URI 之下)。 例如，如果您在 "MyNamedApp" 具名應用程式內建立 "MyDatabase" 具名服務，URI 看起來會像這樣：*fabric:/MyNamedApp/MyDatabase*。 您可以在具名應用程式內建立一或多個具名服務。 每個具名服務可以有自己的分割配置和執行個體/複本計數。 

服務類型有兩種：無狀態和具狀態。  無狀態服務的持續狀態會儲存在 Azure 儲存體、Azure SQL Database 或 Azure DocumentDB 等外部儲存服務中。 當服務完全沒有持續性儲存體時，請使用無狀態服務。 具狀態服務會使用 Service Fabric 透過其 Reliable Collections 或 Reliable Actors 程式設計模型來管理您的服務狀態。  

建立具名服務時，您需指定資料分割配置。 含有大量狀態的服務會跨資料分割切割其資料。  每個資料分割會負責服務完整狀態的一部分，其會分散至全體叢集的節點。  在分割內，無狀態的具名服務會有執行個體，而具狀態的具名服務則有複本。 通常，無狀態具名服務只會有 1 個分割，因為它們有沒有內部狀態。 具狀態的具名服務會在複本中維持其狀態，且每個資料分割都有自己的複本集。  讀取與寫入作業會在單一複本上執行 (稱為「主要複本」)。 從寫入作業對狀態進行的變更，會複寫至多個其他複本 (稱為「作用中次要複本」)。  

下圖顯示應用程式和服務執行個體、分割和複本之間的關聯性。

![服務內的分割和複本][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>資料分割、調整大小和可用性
[資料分割](service-fabric-concepts-partitioning.md)並非 Service Fabric 所獨有。 資料分割是一種知名的資料分割形式，也稱為分區化。 含有大量狀態的具狀態服務，會跨資料分割切割其資料。  每個資料分割會負責服務完整狀態的一部分。 

每個資料分割的複本會分散至各個叢集節點，以允許[調整](service-fabric-concepts-scalability.md)具名服務的狀態規模。 資料需求成長時，資料分割也會成長，而 Service Fabric 會重新平衡全體節點之間的資料分割，以期有效率地使用硬體資源。 若您新增節點至叢集，則 Service Fabric 會重新平衡全體增加節點數的資料分割複本。  整體應用程式效能會有所改善，改善，並減少爭用記憶體的存取權。  若未有效率地使用叢集中的節點，您可減少叢集中的節點數目。  Service Fabric 會再次重新平衡全體減少節點數的資料分割複本，以善加使用每個節點上的硬體。

在分割內，無狀態的具名服務會有執行個體，而具狀態的具名服務則有複本。 通常，無狀態具名服務只會有 1 個分割，因為它們有沒有內部狀態。 資料分割執行個體提供[可用性](service-fabric-availability-services.md)。  若某個執行個體失敗，其他執行個體會繼續正常運作，接著 Service Fabric 會建立新的執行個體。 具狀態的具名服務會在複本中維持其狀態，且每個資料分割都有自己的複本集。  讀取與寫入作業會在單一複本上執行 (稱為「主要複本」)。 從寫入作業對狀態進行的變更，會複寫至多個其他複本 (稱為「作用中次要複本」)。  複本失敗失敗時，Service Fabric 會從現有複本建立新的複本。

## <a name="supported-programming-models"></a>支援的程式設計模型
Service Fabric 提供多種撰寫和管理服務的方式。 服務可選擇使用 Service Fabric API 以善加運用平台的功能和應用程式架構。  或者，服務可為以任何語言撰寫，並裝載於 Service Fabric 叢集的已編譯可執行程式。 如需詳細資訊，請參閱[支援的程式設計模型](service-fabric-choose-framework.md)。

### <a name="guest-executables"></a>客體可執行檔
[客體可執行檔](service-fabric-deploy-existing-app.md)是以任何語言所撰寫，且與其他服務一同裝載於 Service Fabric 叢集的現有任意可執行檔。 不過，客體可執行檔未直接與 Service Fabric API 整合。  客體可執行檔不會受益於平台提供的一組完整功能，例如自訂的健康情況和負載報告、服務端點註冊，以及具狀態計算。

### <a name="containers"></a>容器
根據預設，Service Fabric 會以處理程序形式部署和啟用這些服務。 Service Fabric 也可以在[容器映像](service-fabric-containers-overview.md)中部署服務。 重要的是，您可以在相同應用程式中混合容器中的服務和處理序中的服務。  目前，Service Fabric 支援將 Docker 容器部署在 Linux，也支援將 Windows Server 容器部署在 Windows Server 2016。 在 Service Fabric 應用程式模型中，容器代表多個服務複本所在的應用程式主機。  您可以使用 Service Fabric 在容器中部署現有的應用程式、無狀態服務或具狀態服務。  

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) 是輕量型的服務撰寫架構，這些服務可與 Service Fabric 平台整合，而從一組完整的平台功能受益。 Reliable Services 可為無狀態 (類似大部分的服務平台，例如網頁伺服器或 Azure 雲端服務中的背景工作角色)。 狀態會保存於 Azure DB 或 Azure 表格儲存體等外部解決方案。 Reliable Services 也可以是具狀態的，其狀態是使用 Reliable Collections 直接保存在服務本身中。 狀態透過複寫變得[高度可用](service-fabric-availability-services.md)，並透過[資料分割](service-fabric-concepts-partitioning.md)散發，全由 Service Fabric 自動管理。

### <a name="reliable-actors"></a>Reliable Actors
[Reliable Actor](service-fabric-reliable-actors-introduction.md) 架構是建置在 Reliable Services 上的應用程式架構，它會根據動作項目設計模式來實作 Virtual Actor 模式。 Reliable Actor 架構使用獨立的計算單位，和以單一執行緒方式執行稱為動作項目的狀態。 Reliable Actor 架構提供動作項目的內建通訊，以及預先設定狀態持續性和相應放大組態。

## <a name="app-lifecycle"></a>應用程式生命週期
如同其他平台，Service Fabric 上的應用程式通常會經歷下列階段：設計、開發、測試、部署、升級、維護和移除。 從開發到部署、到每日管理、維護，以及最終的解除委任，Service Fabric 為雲端應用程式的完整應用程式生命週期提供第一等的支援。 服務模型可以啟用數個不同的角色，在應用程式生命週期中獨立參與。 [Service Fabric 應用程式生命週期](service-fabric-application-lifecycle.md)說明 API 的概觀，以及不同的角色如何在 Service Fabric 應用程式生命週期的各個階段使用 API。  

您可使用 [PowerShell cmdlet](/powershell/module/ServiceFabric/)、[C# API](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[Java API](/java/api/system.fabric._application_management_client) 和 [REST API](/rest/api/servicefabric/) 來管理整個應用程式生命週期。 您亦可使用 [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) 或 [Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md) 等工具，設定連續整合/連續部署管線

下列 Microsoft Virtual Academy 影片說明如何管理應用程式生命週期︰<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-apps-and-services"></a>測試應用程式和服務
為了建立真實的雲端級別服務，能夠確保應用程式 aps 和服務可以承受真實失敗實為至關重要。  錯誤分析服務的設計用意，是測試建置於 Service Fabric 的服務。 您可運用[錯誤分析服務](service-fabric-testability-overview.md)引發有意義的錯誤，針對應用程式執行完整的測試案例。 這些錯誤及案例會在受控制、安全且一致的情況下，執行及驗證服務在其生命週期會發生的各種狀態和轉換情形。

[動作](service-fabric-testability-actions.md)可鎖定服務並以個別錯誤執行測試。 服務開發人員可以使用這些動作當做建置組塊，來撰寫複雜的案例。 模擬錯誤的範例如下︰

* 重新啟動節點，以模擬任意次數的機器或 VM 重新開機情況。
* 移動您的具狀態服務複本，以模擬負載平衡、容錯移轉或應用程式升級。
* 在具狀態服務上叫用仲裁遺失，以建立無法繼續進行寫入作業的狀況，因為可接受新資料的「備份」或「次要」複本不足。
* 在具狀態服務上叫用資料遺失，以建立所有記憶體內狀態為完全抹除的狀況。

[案例](service-fabric-testability-scenarios.md)是由一或多個動作組成的複雜作業。 錯誤分析服務提供兩個內建的完整案例︰

* [混亂案例](service-fabric-controlled-chaos.md)會以很長的時間在整個叢集上模擬連續的交錯錯誤 (包括非失誤性和失誤性錯誤)。
* [容錯移轉測試案例](service-fabric-testability-scenarios.md#failover-test)是以特定服務資料分割為目標的混亂測試案例版本，且其他服務不會受到任何影響。

## <a name="clusters"></a>叢集
[Service Fabric 叢集](service-fabric-deploy-anywhere.md)是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 叢集可擴充至數千部機器。 隸屬於叢集的機器或 VM 稱為叢集模式。 需為每個節點指派節點名稱 (字串)。 節點具有各種特性，如 placement 屬性。 每部機器或 VM 皆有自動啟動的服務 FabricHost.exe，該服務會在開機時開始執行，然後啟動兩個可執行檔：Fabric.exe 和 FabricGateway.exe。 這兩個執行檔構成節點。 針對測試案例，您可以藉由執行多個 Fabric.exe 和 FabricGateway.exe 執行個體，在單一機器或 VM 上裝載多個節點。

您可在執行 Windows Server 或 Linux 的任何虛擬機器或電腦上，建立 Service Fabric 叢集。 只要有一組互連式 Windows Server 或 Linux 電腦，不論是在內部部署、Microsoft Azure 或透過任何雲端提供者，您皆可在任何環境中部署和執行 Service Fabric 應用程式。

下列 Microsoft Virtual Academy 影片說明 Service Fabric 叢集相關資訊：<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Azure 上的叢集
我們可藉由在 Azure 上執行 Service Fabric 叢集，與其他的 Azure 功能和服務彼此整合，因此能夠更加輕鬆可靠地操作與管理叢集。  叢集為 Azure Resource Manager 資源，因此您可如同 Azure 中的其他任何資源般建立叢集模型。 Resource Manager 亦可輕鬆管理由叢集以單一單位方式使用的所有資源。  Azure 上的叢集會與 Azure 診斷及 Log Analytics 整合。  叢集節點類型為[虛擬機器擴展集](/azure/virtual-machine-scale-sets/index)，因此內建自動調整功能。

您可透過 [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)、[範本](service-fabric-cluster-creation-via-arm.md)或 [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md)，在 Azure 上建立叢集。

Linux 上的 Service Fabric 預覽版可讓您如同在 Windows 上一樣，在 Linux 上建置、部署和管理高可用性、可高度調整的應用程式。 Service Fabric 架構 (Reliable Services 和 Reliable Actors) 除了可在 C# (.NET Core) 中使用，也能在 Java on Linux 中使用。  您也可以使用任何語言或架構來建置 [來賓可執行檔服務](service-fabric-deploy-existing-app.md) 。 此外，預覽也支援協調 Docker 容器。 Docker 容器可以執行使用 Service Fabric 架構的來賓可執行檔或原生 Service Fabric 服務。 如需詳細資訊，請參閱 [Linux 上的 Service Fabric](service-fabric-linux-overview.md)。

由於 Linux 上的 Service Fabric 是預覽，有一些 Windows 上支援的功能在 Linux 上並不支援。 若要深入了解詳細資訊，請參閱 [Linux 與 Windows 上的 Service Fabric 差異](service-fabric-linux-windows-differences.md)。

### <a name="standalone-clusters"></a>獨立叢集
Service Fabric 提供安裝套件，可讓您在內部部署或任何雲端提供者上建立獨立 Service Fabric 叢集。  獨立叢集可讓您在任何想要的位置自由裝載叢集。  若您的資料受限於合規性或法規限制，或是您想要保留本機資料，則可裝載您專屬的叢集和應用程式。  Service Fabric 應用程式可在多個主機環境中執行而無須任何變更，因此可讓您將建置應用程式的知識從某個主機環境轉移至其他主機環境。 

[建立您的第一個 Service Fabric 獨立叢集](service-fabric-get-started-standalone-cluster.md)

尚不支援 Linux 獨立叢集。

### <a name="cluster-security"></a>叢集安全性
叢集必須受到安全保護，以防止未經授權使用者連線您的叢集，特別是當叢集上有生產工作負載正在執行時。 雖然可以建立不安全的叢集，但這樣做會在叢集向公用網際網路公開管理端點時，允許匿名使用者連線叢集。  無法在稍後為不安全的叢集啟用安全性：建立叢集時即會啟用叢集安全性。

叢集安全性案例包括：
* 節點對節點安全性
* 用戶端對節點安全性
* 角色型存取控制 (RBAC)

如需詳細資訊，請參閱[保護叢集](service-fabric-cluster-security.md)。

### <a name="scaling"></a>調整大小
若您新增節點至叢集，則 Service Fabric 會重新平衡全體增加節點數的資料分割複本和執行個體。  整體應用程式效能會有所改善，改善，並減少爭用記憶體的存取權。  若未有效率地使用叢集中的節點，您可減少叢集中的節點數目。  Service Fabric 會再次重新平衡全體減少節點數的資料分割複本和執行個體，以善加使用每個節點上的硬體。  您可透過[手動](service-fabric-cluster-scale-up-down.md)或[程式設計方式](service-fabric-cluster-programmatic-scaling.md)，調整 Azure 上叢集的規模。  您可[手動](service-fabric-cluster-windows-server-add-remove-nodes.md)調整獨立叢集的規模。

### <a name="cluster-upgrades"></a>叢集升級
新版本的 Service Fabric 執行階段會定期發行。  在叢集上執行執行階段或網狀架構升級，以確保一律執行[支援的版本](service-fabric-support.md)。  除了網狀架構升級，您亦可更新憑證或應用程式連接埠等叢集組態。

Service Fabric 叢集是由您所擁有，但由 Microsoft 部分管理的資源。  Microsoft 負責修補基礎 OS，以及在叢集上執行網狀架構升級。  您可以設定您的叢集 (當 Microsoft 發行新版本時) 接收自動網狀架構升級，或選擇選取您需要的受支援網狀架構版本。  您可透過 Azure 入口網站或 Resource Manager，設定網狀架構和組態升級。  如需詳細資訊，請參閱[升級 Service Fabric 叢集](service-fabric-cluster-upgrade.md)。  

獨立叢集是由您完全擁有的資源。  由您負責修補基礎 OS 和起始網狀架構升級。  若您的叢集可連線至 [https://www.microsoft.com/download](https://www.microsoft.com/download)，則您可將叢集設為自動下載和佈建新的 Service Fabric 執行階段封裝。  接著您即會起始升級。  若您的叢集無法存取 [https://www.microsoft.com/download](https://www.microsoft.com/download)，則您可透過連線至網際網路的電腦下載新執行階段封裝，然後再起始升級。  如需詳細資訊，請參閱[升級獨立 Service Fabric 叢集](service-fabric-cluster-upgrade-windows-server.md)。

## <a name="health-monitoring"></a>健康狀況監視
Service Fabric 引入了[健康狀態模型](service-fabric-health-introduction.md)，主要用於在特定實體 (例如叢集節點和服務複本) 上標示狀況不良的叢集和應用程式條件。 健康狀態模型使用健康狀態報告程式 (系統元件及看門狗)。 目標為輕易迅速的診斷並修復問題。 服務撰寫者必須預先考量健康狀態以及如何[設計健康狀態報告](service-fabric-report-health.md#design-health-reporting)等相關層面。 任何可能會影響到健康狀態的條件都需加以回報，尤其是如果它有助標示出接近根目錄的問題。 一旦在生產階段大規模啟動並執行服務，健康狀態資訊將可有效減少偵錯和調查工作所需的時間和心力。

Service Fabric 報告程式可監控感興趣的已識別條件。 它們會依據其本機檢視回報這些條件。 [健康狀態存放區](service-fabric-health-introduction.md#health-store) 可彙總報告程式送出的所有健康狀態資料，以判斷實體的健康狀態是否為全域良好。 必須為豐富、彈性且容易使用的模型。 健康狀態報告的品質可決定叢集的健康狀態檢視準確度。 不正確顯示出健康不良的問題之誤報，會對升級或其他使用健康狀態資料的服務產生負面影響。 這類服務的範例包括修復服務和警示機制。 因此，需要針對報告加以考量，才能讓其以最佳的方式擷取感興趣的條件。

報告可從以下項目完成：
* 受監視的 Service Fabric 服務複本或執行個體。
* 內部監視程式會部署為 Service Fabric 服務 (例如，可監視條件和問題報告的 Service Fabric 無狀態服務)。 可以將監視程式部署為所有節點，或可以與受監視的服務相關。
* 在 Service Fabric 節點上執行，但未以 Service Fabric 服務實作的內部看門狗監視程式。
* 從 Service Fabric 叢集外探查資源的外部看門狗監視程式 (例如，監視 Gomez 等服務)。

Service Fabric 元件會針對叢集中的所有實體，提供現成的報告。  [系統健康狀態報告](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)可讓您全盤掌握叢集和應用程式功能，並透過健康狀態標記問題。 系統健康狀態報告會針對應用程式和服務來確認實體是否已實作，並從 Service Fabric 執行階段的角度來確認其是行為是否正確。 報告並不會監控任何服務商務邏輯的健康狀態，也不會偵測是否有無回應的處理程序。 若要將特定的健康狀態資訊新增至服務的邏輯，請在服務中[實作自訂健康狀態報告](service-fabric-report-health.md)。

Service Fabric 提供多種[健康狀態報告檢視](service-fabric-view-entities-aggregated-health.md)方式，且會將此報告彙總於健康狀態資料存放區：
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 或其他視覺效果工具/
* 健康情況查詢 (透過 [PowerShell](/powershell/module/ServiceFabric/)、[C# FabricClient API](/api/system.fabric.fabricclient.healthclient) 和 [Java FabricClient API](/java/api/system.fabric._health_client)，或是 [REST API](/rest/api/servicefabric))。
* 一般查詢會傳回一份實體清單，這些實體的其中一個屬性即為健康情況 (透過 Powershell、API 或 REST)。

下列 Microsoft Virtual Academy 影片說明 Service Fabric 健康狀態模型及其使用方式：<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="next-steps"></a>後續步驟
* 了解如何[在 Azure 中建立叢集](service-fabric-cluster-creation-via-portal.md)或[在 Windows 上建立獨立叢集](service-fabric-cluster-creation-for-windows-server.md)。
* 嘗試使用 [Reliable Services](service-fabric-reliable-services-quick-start.md) 或 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 程式設計模型來建立服務。
* 了解如何[從雲端服務移轉](service-fabric-cloud-services-migration-differences.md)。
* 了解如何[監視和診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。 
* 學習如何[測試您的應用程式和服務](service-fabric-testability-overview.md)。
* 學習如何[管理和組織叢集資源](service-fabric-cluster-resource-manager-introduction.md)。
* 查看 [Service Fabric 範例 ](http://aka.ms/servicefabricsamples)。
* 了解 [Service Fabric 支援選項](service-fabric-support.md)。
* 如需相關文章與公告資訊，請參閱[小組部落格](https://blogs.msdn.microsoft.com/azureservicefabric/)。


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

