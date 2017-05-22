---
title: "Microsoft Azure 中的監視 | Microsoft Docs"
description: "您想要監視 Microsoft Azure 中的任何項目時的選項。 Azure 監視器, Application Insights Log Analytics"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d4a94a92585420cf92018084437422fd0c66fa2d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Microsoft Azure 中的監視概觀
本文提供可用來監視 Microsoft Azure 的工具概觀。 適用範圍： 
- 監視在 Microsoft Azure 中執行的應用程式 
- 在 Azure 外部執行且可監視 Azure 中物件的工具/服務。 

其會討論各種可用的產品與服務，以及它們如何一起運作。 本文可協助您判斷哪些工具在哪些情況下最適合您。  

## <a name="why-use-monitoring-and-diagnostics"></a>為何要使用監視和診斷？

您雲端應用程式中的效能問題可能會對企業產生影響。 透過多個互連的元件和頻繁的發行，隨時都可能導致效能降低。 此外，如果您正在開發應用程式，您的使用者通常會探索到您在測試時未發現的問題。 您應該立即知道這些問題，並具備可用以診斷和修復問題的工具。 Microsoft Azure 提供多種工具來識別這些問題。

## <a name="how-do-i-monitor-my-azure-cloud-apps"></a>如何監視我的 Azure 雲端應用程式？

有多種工具可用來監視 Azure 應用程式和服務。 其中有些功能會重疊。 這其中有部分是基於歷程記錄因素，而有部分是因為應用程式開發與作業之間的界限模糊不清而導致的。 

以下是主要工具：

-    **Azure 監視器**是用以監視 Azure 上執行之服務的基本工具。 它會提供關於服務輸送量及周遭環境之基礎結構等級的資料。 如果您正在 Azure 中管理所有的應用程式，決定是否要相應增加或減少資源，Azure 監視器接著就能提供可讓您用來做為起點的項目。

-    **Application Insights** 可用來開發，並作為監視生產環境的解決方案。 它的運作方式是將封裝安裝到您的應用程式，因此可讓您更深入檢視內部情形。 它的資料包括相依性的回應時間、例外狀況追蹤、偵錯快照集、執行設定檔。 它提供功能強大的智慧型工具來完整分析此遙測，以協助您偵錯應用程式，並協助您了解使用者正使用它來做什麼。 您可以判斷回應時間突然增加是否因為應用程式中的某些項目所引起，還是由一些外部資源問題所引起的。 如果您使用 Visual Studio 且應用程式發生問題，則可直接將您帶至發生問題的程式碼行，讓您能夠修正問題。  

-    **Log Analytics** 適用於需要微調效能並計劃維護在生產環境中執行之應用程式的使用者。 它是 Azure 中的基本工具。 它會從許多來源收集並彙總資料，但會有 10 到 15 分鐘的延遲。 它會針對 Azure、內部部署及協力廠商的雲端式基礎結構 (例如 Amazon Web 服務) 提供整體 IT 管理解決方案。 它提供更豐富的工具，可跨多個來源分析資料、允許跨所有記錄進行複雜的查詢，而且可以指定的條件主動提供警示。  您甚至可以將自訂資料收集到其中央存放庫，如此便能查詢並將其視覺化。 

-    **System Center Operations Manager (SCOM)** 可用來管理及監視大型雲端安裝。 您可能已經熟悉它做為適用於內部部署 Windows Sever 和 Hyper-V 型雲端的管理工具，但它也可以與 Azure 應用程式整合並加以管理。 在其他方面，它可以在現有的即時應用程式上安裝 Application Insights。  如果應用程式當機，它會在短時間內通知您。 請注意，Log Analytics 不會取代 SCOM。 它也可與其搭配使用。  


## <a name="accessing-monitoring-in-the-azure-portal"></a>在 Azure 入口網站中存取監視
所有的 Azure 監視服務現在都可在單一 UI 窗格中取得。 如需如何存取此區域的詳細資訊，請參閱[開始使用 Azure 監視器](monitoring-get-started.md)。 

您也可以存取特定資源的監視功能，方法是將那些資源反白顯示，然後向下切入至它們的監視選項。 

## <a name="examples-of-when-to-use-which-tool"></a>何時應使用哪個工具的範例 

以下各節將說明一些基本案例，以及應該一起使用哪些工具。 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>案例 1：在開發期間，修正 Azure 應用程式中的錯誤   

**最佳選項是一起使用 Application Insights、Azure 監視器和 Visual Studio**

Azure 目前在雲端中提供 Visual Studio Debugger 的完整功能。 設定 Azure 監視器，以將遙測傳送至 Application Insights。 讓 Visual Studio 可將 Application Insights SDK 納入您的應用程式。 一旦位於 Application Insights 之後，您就能使用應用程式對應，以視覺化方式探索您執行中應用程式的哪些部分是否狀況不良。 針對那些狀況不良的部分，已經有錯誤和例外狀況可供探索。 您可以使用 Application Insights 中的各種分析來深入了解。 如果您不確定錯誤，可以使用 Visual Studio 偵錯工具來追蹤程式碼，並進一步準確地確認問題。 

如需詳細資訊，請參閱[監視 Web Apps](../application-insights/app-insights-azure-web-apps.md)，並參考左邊的目錄以取得各種類型之應用程式和語言的相關指示。  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>案例 2：對 Azure.NET Web 應用程式進行偵錯，以取得只會在生產環境中顯示的錯誤 

> [!NOTE]
> 這些功能目前為預覽狀態。 

**最佳選項是使用 Application Insights，而且，如果可能，請使用 Visual Studio 以取得完整的偵錯體驗。**

使用 Application Insights 快照集偵錯工具來偵錯您的應用程式。 當生產元件發生特定的錯誤臨界值時，系統會在稱為「快照集」的時段中自動擷取遙測。 擷取的數量對生產雲端而言是安全的，因為夠小而不足以影響效能，但顯然足以允許追蹤。  系統可以擷取多個快照集。 您可以查看 Azure 入口網站中的時間點，或使用 Visual Studio 取得完整體驗。 使用 Visual Studio，開發人員就能逐步執行該快照集，如同他們正在進行即時偵錯。 本機變數、參數、記憶體及框架全都可供使用。 開發人員必須透過 RBAC 角色，被授與存取此生產資料的權限。  

如需詳細資訊，請參閱[快照集偵錯](../application-insights/app-insights-snapshot-debugger.md)。 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>案例 3：對使用容器或微服務的 Azure 應用程式進行偵錯 

**與案例 1 相同。一起使用 Application Insights、Azure 監視器和 Visual Studio** Application Insights 也支援從在容器內部執行的程序以及從微服務 (Kubernetes、Docker、Azure Service Fabric) 蒐集遙測。 如需詳細資訊，[請參閱這段對容器和微服務進行偵錯的相關影片](https://go.microsoft.com/fwlink/?linkid=848184)。 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>案例 4：修正 Azure 應用程式中的效能問題

[Application Insights Profiler](../application-insights/app-insights-profiler.md) 的設計目的是協助對這些類型的問題進行疑難排解。 您可以針對在應用程式服務 (Web Apps、Logic Apps、Mobile Apps、API Apps) 中執行的應用程式及其他元件 (例如，虛擬機器、虛擬機器擴展集 (VMSS)、雲端服務及 Service Fabric)，識別效能問題並進行疑難排解。 

> [!NOTE]
> 分析虛擬機器、虛擬機器擴展集 (VMSS)、雲端服務及 Service Fabric 的功能目前為預覽狀態。   

此外，智慧型偵測工具會透過電子郵件主動通知您特定錯誤類型的相關資訊，例如頁面載入時間變慢。  您不需要在此工具上進行任何設定。 如需詳細資訊，請參閱[智慧型偵測 - 效能異常](../application-insights/app-insights-proactive-performance-diagnostics.md)和[智慧型偵測 - 效能異常](https://azure.microsoft.com/blog/Enhancments-ApplicationInsights-SmartDetection/preview)。



## <a name="next-steps"></a>後續步驟
深入了解

* [Ignite 2016 上的 Azure 監視器影片](https://myignite.microsoft.com/videos/4977)
* [開始使用 Azure 監視器](monitoring-get-started.md)
* [Azure 診斷](../azure-diagnostics.md)，如果您正在嘗試診斷雲端服務、虛擬機器、虛擬機器擴展集或 Service Fabric 應用程式中的問題。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) ，如果您正在嘗試診斷 App Service Web 應用程式中的問題。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 及 [Operations Management Suite](https://www.microsoft.com/oms/)，監視生產環境的解決方案
