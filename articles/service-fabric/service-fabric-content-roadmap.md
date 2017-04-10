---
title: "了解 Azure Service Fabric | Microsoft Docs"
description: "Service Fabric 的概觀和入門指南，其中 Service Fabric 的應用程式是由許多微服務所組成以提供調整和恢復功能。"
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
ms.date: 04/07/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 47b3fffb2d5c24b7473884e490be19ff17b61b61
ms.openlocfilehash: 9742523c0a1743ff5982e746aa3c99aed8934499
ms.lasthandoff: 02/21/2017


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>您想要了解 Service Fabric 嗎？
本學習藍圖將協助您在 Service Fabric 上開始開發可調整、可靠且易於管理的應用程式。

## <a name="the-five-minute-overview"></a>5 分鐘概觀
Azure Service Fabric 是一個分散式系統平台，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務，並解決開發與管理雲端應用程式時遭遇的重大挑戰。 透過使用 Service Fabric，開發人員與管理員能夠避免解決複雜的基礎結構問題，改為專注於實作關鍵且嚴格要求的工作負載，並了解其為可調整、可信賴及可管理的。 Service Fabric 代表新一代的中介軟體平台，可建置與管理這些企業級的 Tier-1 雲端規模應用程式。  

此 Channel9 短片簡介 Service Fabric 和微服務：<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>詳細概觀
Service Fabric 可讓您建置並管理由微服務組成的應用程式，這類應用程式可調整且可靠，以非常高的密度在共用的機器集區 (稱為叢集) 上執行。 它提供精密的執行階段，可建置分散式、可擴充的無狀態與可設定狀態的微服務。 還提供完整的應用程式管理功能，以佈建、部署、監視、升級/修補及刪除已部署的應用程式。  若要深入了解，請參閱 [Service Fabric 概觀](service-fabric-overview.md)。

為何採用微服務設計方法？ 所有應用程式會隨著時間而演化。 成功的應用程式因為有實用性而演化。 您對現今的需求了解多少，以及未來需求會有何變化？ 有時，發佈一個簡單的應用程式作為概念證明即是驅動因素，因為知道可以在稍後重新設計該應用程式。 另一方面，當公司談到針對雲端進行建置時，都會對成長和使用量有所預期。 問題在於無法預期成長和範圍。 我們想要既能夠迅速建立原型，同時又知道可以調整應用程式來因應無法預測的成長和使用量。  [什麼是微服務？](service-fabric-overview-microservices.md)說明微服務設計方法如何面對這些挑戰，以及您如何建立可相應增加或減少規模、測試、部署及獨立管理的微服務。

Service Fabric 提供可靠且彈性的平台，可讓您撰寫及執行許多類型的商務應用程式和服務。  您也可以執行任何現有的應用程式 (以任何語言所撰寫)。  這些應用程式和微服務可以是無狀態或具狀態，而且它們會跨虛擬機器進行資源平衡，以達到最佳效率。 Service Fabric 的獨特架構可讓您在應用程式中執行接近即時的資料分析、記憶體中計算、平行交易和事件處理。 您可以輕鬆地根據變更的資源需求上下調整 (其實是收發) 您的應用程式。 若要了解您可建立之應用程式和服務的類別，以及客戶案例研究，請參閱[應用程式案例](service-fabric-application-scenarios.md)。

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
請嘗試這個廣泛的[實習實驗室第 1 部分](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx)，以熟悉 Service Fabric 的端對端開發流程。  了解如何建立無狀態服務、設定監視和健康情況報告，以及執行應用程式升級。 在這之後，執行[實習實驗室第 2 部分](http://aka.ms/sflab2)，這個部分會引領您完成具狀態服務。


下列 Channel9 影片將逐步引導您完成在 Visual Studio 中建立 C# 應用程式的程序：  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>在 Linux 上
Service Fabric 提供了在 Linux 上建置服務的 .NET Core 和 Java SDK。 這些主題將逐步引導您完成在 Linux 上建立第一個 Java 或 C# 應用程式並在開發電腦上執行它的程序。
[設定開發環境](service-fabric-get-started-linux.md)
[建立第一個應用程式 (Java)](service-fabric-create-your-first-linux-application-with-java.md)
[建立第一個應用程式 (C#)](service-fabric-create-your-first-linux-application-with-csharp.md)

下列 Microsoft Virtual Academy 影片會引導您完成在 Linux 上建立 Java 應用程式的程序︰  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>在 MacOS 上
您可以在 MacOS X 上建置要在 Linux 叢集上執行的 Service Fabric 應用程式。 這些文章將說明如何設定您的 Mac 以供開發使用、逐步引導您完成在 MacOS 上建立第一個 Java 應用程式並在 Ubuntu 虛擬機器上執行它的程序。
[設定開發環境](service-fabric-get-started-mac.md)
[建立第一個應用程式 (Java)](service-fabric-create-your-first-linux-application-with-java.md)

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
應用程式類型是指派給服務類型集合的名稱/版本。 定義在 ApplicationManifest.xml 檔案中，內嵌在應用程式封裝目錄，然後會複製到 Service Fabric 叢集的映像存放區中。 您可以接著從這個應用程式類型建立一個具名應用程式，然後在叢集內執行。 

服務類型是指派給服務的程式碼封裝、資料封裝及組態封裝的名稱/版本。 定義在 ServiceManifest.xml 檔案中，內嵌在服務封裝目錄中，然後應用程式封裝的 ApplicationManifest.xml 檔案會參考此服務封裝目錄。 在叢集內，建立具名應用程式之後，可以從應用程式類型的其中一個服務類型建立具名服務。 服務類型會由其 ServiceManifest.xml 檔案描述，且組成成分包括在執行階段載入的可執行程式碼服務組態設定，以及服務所取用的靜態資料。

![Service Fabric 應用程式類型和服務類型][cluster-imagestore-apptypes]

應用程式封裝是一個磁碟目錄，其中包含應用程式類型的 ApplicationManifest.xml 檔案，此檔案會參考組成應用程式類型之每個服務類型的服務封裝。 例如，電子郵件應用程式類型的應用程式封裝可能包含指向佇列服務封裝、前端服務封裝、資料庫服務封裝的參考。 應用程式封裝目錄中的檔案會複製到 Service Fabric 叢集的映像存放區。 

服務封裝是一個磁碟目錄，其中包含服務類型的 ServiceManifest.xml 檔案，此檔案會參考服務類型的程式碼、靜態資料及組態封裝。 應用程式類型的 ApplicationManifest.xml 檔案會參考服務封裝目錄中的檔案。 例如，服務套件可能會參考構成資料庫服務的程式碼、靜態資料和組態封裝。

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>執行階段︰叢集和節點，具名應用程式、具名服務、資料分割及複本
[Service Fabric 叢集](service-fabric-deploy-anywhere.md)是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 叢集可擴充至數千部機器。

隸屬於叢集的機器或 VM 即稱為節點。 需為每個節點指派節點名稱 (字串)。 節點具有各種特性，如 placement 屬性。 每部機器或 VM 皆有自動啟動的 Windows 服務 FabricHost.exe，該服務會在開機時開始執行，然後啟動兩個可執行檔：Fabric.exe 和 FabricGateway.exe。 這兩個執行檔構成節點。 針對開發或測試案例，您可以藉由執行多個 Fabric.exe 和 FabricGateway.exe 執行個體，在單一機器或 VM 上裝載多個節點。

具名應用程式是執行一或多個特定功能之具名服務的集合。 服務會執行完整且獨立的函數 (其可獨立於其他服務啟動和執行)，並且是由程式碼、組態和資料組成。 在應用程式封裝被複製到映像存放區之後，您可藉由指定應用程式封裝的應用程式類型 (使用其名稱/版本)，在叢集內建立應用程式的執行個體。 每個應用程式類型執行個體會被指派一個看起來像這樣的 URI 名稱：*fabric:/MyNamedApp*。 在叢集中，您可以從單一應用程式類型建立多個具名應用程式。 也可以從不同的應用程式類型建立具名應用程式。 每個具名應用程式都是獨立管理和控制版本。

在建立具名應用程式之後，您可藉由指定服務類型 (使用其名稱/版本)，在叢集內建立它其中一種服務類型的執行個體。 需為每個服務類型執行個體指派一個 URI (名稱範圍需在其具名應用程式的 URI 之下)。 例如，如果您在 "MyNamedApp" 具名應用程式內建立 "MyDatabase" 具名服務，URI 看起來會像這樣：*fabric:/MyNamedApp/MyDatabase*。 您可以在具名應用程式內建立一或多個具名服務。 每個具名服務可以有自己的分割配置和執行個體/複本計數。 

服務類型有兩種：無狀態和具狀態。  無狀態服務的持續狀態會儲存在 Azure 儲存體、Azure SQL Database、Azure DocumentDB 等外部儲存服務中。 當服務完全沒有持續性儲存體時，請使用無狀態服務。 具狀態服務會使用 Service Fabric 透過其 Reliable Collections 或 Reliable Actors 程式設計模型來管理您的服務狀態。  

建立具名服務時，您需指定資料分割配置。 含有大量狀態的服務會跨分割切割其資料，所以服務是分散在叢集的節點上。 這使得具名服務的狀態可以擴充。 在分割內，無狀態的具名服務會有執行個體，而具狀態的具名服務則有複本。 通常，無狀態具名服務只會有&1; 個分割，因為它們有沒有內部狀態。 分割執行個體提供可用性；如果一個執行個體失敗，其他執行個體會繼續正常運作，接著 Service Fabric 會建立新的執行個體。 具狀態的具名服務會在複本中維持其狀態，且每個分割都有自己的複本集，其中包含保持同步的所有狀態。 複本失敗失敗時，Service Fabric 會從現有複本建立新的複本。

下圖顯示應用程式和服務執行個體、分割和複本之間的關聯性。

![服務內的分割和複本][cluster-application-instances]

## <a name="supported-programming-models"></a>支援的程式設計模型
Service Fabric 提供多種撰寫和管理服務的方式。 服務可以選擇使用 Service Fabric API，充分利用平台功能和應用程式架構，或者服務可以僅僅是以任何語言撰寫、只裝載在 Service Fabric 叢集上的任何已編譯的可執行程式。 如需詳細資訊，請參閱[支援的程式設計模型](service-fabric-choose-framework.md)。

### <a name="guest-executables"></a>客體可執行檔
[客體可執行檔](service-fabric-deploy-existing-app.md)是以任何語言撰寫的任意可執行檔，因此您可以將現有的應用程式與其他服務一起裝載在 Service Fabric 叢集上。 不過，客體可執行檔不會與 Service Fabric API 直接整合，因此它們不會受益於平台提供的一組完整功能，例如自訂的健康情況和負載報告、服務端點註冊，以及具狀態計算。

### <a name="containers"></a>容器
根據預設，Service Fabric 會以處理程序形式部署和啟用這些服務。 Service Fabric 也可以在[容器映像](service-fabric-containers-overview.md)中部署服務。 重要的是，您可以在相同應用程式中混合容器中的服務和處理序中的服務。  目前，Service Fabric 支援將 Docker 容器部署在 Linux，也支援將 Windows Server 容器部署在 Windows Server 2016。 在 Service Fabric 應用程式模型中，容器代表多個服務複本所在的應用程式主機。  您可以使用 Service Fabric 在容器中部署現有的應用程式、無狀態服務或具狀態服務。  

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) 是輕量型的服務撰寫架構，這些服務可與 Service Fabric 平台整合，而從一組完整的平台功能受益。 Reliable Services 可以是無狀態的 (類似大多數服務平台，例如「Azure 雲端服務」中的 Web 伺服器或「背景工作角色」)，其中狀態會保存在外部解決方案中，例如 Azure DB 或「Azure 資料表儲存體」。 Reliable Services 也可以是具狀態的，其狀態是使用 Reliable Collections 直接保存在服務本身中。 狀態透過複寫變得高度可用，並透過資料分割散發，全由 Service Fabric 自動管理。

### <a name="reliable-actors"></a>Reliable Actors
[Reliable Actor](service-fabric-reliable-actors-introduction.md) 架構是建置在 Reliable Services 上的應用程式架構，它會根據動作項目設計模式來實作 Virtual Actor 模式。 Reliable Actor 架構使用獨立的計算單位，和以單一執行緒方式執行稱為動作項目的狀態。 Reliable Actor 架構提供動作項目的內建通訊和預先設定狀態持續性和相應放大組態。

## <a name="next-steps"></a>後續步驟
* 了解如何[在 Azure 中建立叢集](service-fabric-cluster-creation-via-portal.md)或[在 Windows 上建立獨立叢集](service-fabric-cluster-creation-for-windows-server.md)。
* 嘗試使用 [Reliable Services](service-fabric-reliable-services-quick-start.md) 或 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 程式設計模型來建立服務。
* 了解[應用程式生命週期](service-fabric-application-lifecycle.md)。
* 了解如何[檢查應用程式與叢集健康情況](service-fabric-health-introduction.md)。
* 了解如何[監視和診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。 
* 了解 [Service Fabric 支援選項](service-fabric-support.md)。


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

