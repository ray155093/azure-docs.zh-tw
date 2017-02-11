---
title: "Azure App Service 方案深入概觀 | Microsoft Docs"
description: "了解 Azure App Service 之應用程式服務方案的運作方式，以及在管理經驗上帶來的效益。"
keywords: "App Service, Azure App Service, 級別, 可調整, App Service方案, App Service 成本"
services: app-service
documentationcenter: 
author: btardif
manager: wpickett
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5f4e2775a71c743c313831ce0cd567527c8ae5e2


---
# <a name="azure-app-service-plans-in-depth-overview"></a>Azure App Service 方案深入概觀
App Service 方案代表可跨多個應用程式共用的一組特性和功能。 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 中的 Web Apps、Mobile Apps、Function Apps 或 API Apps 全部都在 App Service 方案中執行。 這些方案支援 5 個定價層︰免費、共用、基本、標準和進階。 每一層都有自己的功能和容量。 相同訂閱和地理位置中的應用程式都能共用方案。 共用一個方案的所有應用程式，都可以使用方案層所定義的所有功能和特性。 與方案相關聯的所有應用程式，可在方案所定義的資源上執行。

例如，如果您的方案設定為使用標準服務層中的兩個「小型」執行個體，與該方案相關聯的所有應用程式會在兩個執行個體上執行，而且可以存取標準服務層功能。 應用程式在其上執行的方案執行個體完全受管理且高度可用。

本文探討重要的特性，例如 App Service 方案的層級與規模，以及這些特性如何在管理您的應用程式時發揮效用。

## <a name="apps-and-app-service-plans"></a>應用程式和 App Service 方案
在任何指定時間，應用程式服務方案中的一個應用程式只能與一個應用程式服務方案產生關聯。

應用程式和方案都包含在資源群組中。 資源群組可做為其內各項資源的生命週期界限。 您可以使用資源群組集中管理應用程式的一切層面。

單一資源群組可擁有多個 App Service 方案，因此您可以將不同的應用程式配置到不同的實際資源。 例如，您可以將資源分隔為開發、測試和生產環境。 將生產和開發/測試的環境分開可讓您隔離資源。 如此一來，對您應用程式的新版本進行負載測試就不會與您的生產應用程式競爭相同的資源，生產應用程式是為真實的客戶提供服務。

當您在單一資源群組中有多個方案時，您也可以定義一個跨越地理區域的應用程式。 例如，在兩個區域中執行的高度可用應用程式包括至少兩個方案，每個區域一個方案，而一個應用程式則與每個方案相關聯。 在這種情況下，應用程式的所有複本都在單一資源群組內。 將多個方案和多個應用程式放在一個資源群組中，可輕鬆管理、控制和檢視應用程式的健康情況。

## <a name="create-an-app-service-plan-or-use-existing-one"></a>建立 App Service 方案或使用現有的方案
在建立應用程式時，請考慮建立資源群組。 另一方面，如果您即將建立的應用程式是較大應用程式的元件，就應該在配置給該較大應用程式的資源群組內建立此應用程式。

不論新的應用程式是全新的應用程式還是較大應用程式的一部分，您都可以選擇使用現有 App Service 方案來裝載它，或建立一個新的 App Service 方案。 這項決定其實是容量和預期負載方面的問題。

如果這個新的應用程式會使用大量資源，而且縮放係數與現有方案中所裝載的其他應用程式不同，則建議將它隔離在其專屬方案。

在建立方案時，可以為應用程式配置一組新的資源，進而更有效地控制資源配置，因為每個方案都有其專屬的一組執行個體。

您可以跨方案移動應用程式，因此可以變更跨更大型應用程式配置資源的方式。

最後，如果您想要在不同區域中建立應用程式，而該區域目前沒有方案，請在該區域中建立方案，才能在該處裝載應用程式。

## <a name="create-an-app-service-plan"></a>建立應用程式服務方案
> [!TIP]
> 如果您有 App Service 環境，您可以檢閱這裡的 App Service 環境相關文件︰[在 App Service 環境中建立 App Service 方案](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)
> 
> 

您可以從 App Service 方案瀏覽體驗或在應用程式建立期間，建立空白的 App Service 方案。

在 [Azure 入口網站](https://portal.azure.com)中，按一下 [新增] > [Web + 行動]，然後選取 [Web 應用程式] 或其他 App Service 應用程式種類。
![在 Azure 入口網站中建立應用程式。][createWebApp]

接著，您可以選取或建立新應用程式的應用程式服務方案。

 ![建立 App Service 方案。][createASP]

若要建立新的 App Service 方案，請按一下 [[+] 建立新的]，輸入 **App Service 方案**名稱，然後選取適當 [位置]。 按一下 [定價層] ，然後為服務選取適當的定價層。 選取 [檢視全部] 以檢視其他價格選項，例如 [免費] 和 [共用]。 選取定價層後，請按一下 [選取]  按鈕。

## <a name="move-an-app-to-a-different-app-service-plan"></a>將應用程式移到不同的應用程式服務方案
您可以在 [Azure 入口網站](https://portal.azure.com)中將應用程式移到不同的 App Service 方案。 只要方案都位於相同的資源群組與地理區域中，您就可以在這些方案之間移動應用程式。

若要將應用程式移到其他方案，請前往您想要移動的應用程式。 在 [設定] 功能表上尋找 [變更 App Service 方案]。

[變更 App Service 方案] 會開啟 [App Service 方案] 選取器。 此時，您可以挑選現有的方案，或建立新的方案。 只會顯示有效的方案 (位於相同的資源群組和地理位置)。

![App Service 方案選取器。][change]

每個方案都有其專屬定價層。 例如，當您將網站從免費層移到 [標準] 層時，您的應用程式現在就可以使用 [標準] 層的所有功能和資源。

## <a name="clone-an-app-to-a-different-app-service-plan"></a>將應用程式移到不同的 App Service 方案
如果您想要將應用程式移至不同的區域，有一個替代方法是複製應用程式。 複製會將您的應用程式複製到任何區域中新的或現有的 App Service 方案或 App Service 環境。

 ![複製應用程式。][appclone]

您可以在 [工具] 功能表上找到 [複製應用程式]。

複製具有某些限制，若要深入了解，請閱讀 [使用 Azure 入口網站的 Azure App Service 應用程式複製](../app-service-web/app-service-web-app-cloning-portal.md)。

## <a name="scale-an-app-service-plan"></a>調整 App Service 方案
有三種方式可以調整方案：

* **變更方案的定價層**。 例如，[基本] 層中的方案可以轉換成 [標準] 層或 [進階] 層，而且與該方案相關聯的所有應用程式現在都可以使用新服務層所提供的功能。
* **變更方案的執行個體大小**。 例如，基本層中使用小型執行個體的方案可以變更為使用大型執行個體。 與該方案相關聯的所有應用程式現在都可以使用較大執行個體大小所提供的額外記憶體和 CPU 資源。
* **變更方案的執行個體計數**。 例如，已相應放大為 3 個執行個體的標準方案可調整為 10 個執行個體。 進階方案可以相應放大為 20 個執行個體 (視實際情況而定)。 與該方案相關聯的所有應用程式現在都可以使用較大執行個體計數所提供的額外記憶體和 CPU 資源。

您可以按一下應用程式或 App Service 方案設定下方的 [相應增加]  項目，來變更定價層和執行個體大小。 變更會套用到 App Service 方案，並影響它裝載的所有應用程式。

 ![設定值以相應增加應用程式。][pricingtier]

## <a name="app-service-plan-cleanup"></a>App Service 方案清除
沒有相關聯應用程式的**App Service 方案**仍會產生費用，因為它們會繼續保留 App Service 方案調整屬性中設定的計算容量。
為了避免非預期的費用，刪除 App Service 方案中裝載的最後一個應用程式時，也會刪除產生的空白 App Service 方案。

## <a name="summary"></a>摘要
應用程式服務方案代表可跨應用程式共用的一組特性和功能。 App Service 方案可讓您將特定應用程式彈性地配置給一組資源，並進一步最佳化 Azure 資源使用率。 如此一來，如果您想要節省測試環境的成本，則可以跨多個應用程式共用方案。 透過延展到多個區域和方案，也可以最大化生產環境的輸送量。

## <a name="whats-changed"></a>變更的項目
* 如需從網站變更為 App Service 的指南，請參閱： [Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png



<!--HONumber=Nov16_HO3-->


