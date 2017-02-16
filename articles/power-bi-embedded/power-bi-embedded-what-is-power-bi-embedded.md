---
title: "何謂 Microsoft Power BI Embedded？"
description: "Power BI Embedded 可讓您將 Power BI 報告整合到 Web 或行動應用程式中，您就不需要建置自訂解決方案。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 81215430b5731a53d4f4700499c1d9af963e712c


---
# <a name="what-is-microsoft-power-bi-embedded"></a>何謂 Microsoft Power BI Embedded？
運用 **Power BI Embedded**，您可以將 Power BI 報告整合至您的 Web 應用程式或行動應用程式。

![](media/powerbi-embedded-whats-is/what-is.png)

Power BI Embedded 是一個 **Azure 服務** ，可讓 ISV 和應用程式開發人員在其應用程式中呈現 Power BI 資料體驗。 身為開發人員，您已建置應用程式，這些應用程式有它們自己的使用者和個別的功能集。 這些應用程式也可能已經內建一些資料元素，例如現在可以由 Microsoft Power BI Embedded 提供的圖表和報表。 您不需要 Power BI 帳戶就可以使用您的應用程式。 您可以像以前一樣繼續登入您的應用程式，以及在不需要任何額外授權的情況下，檢視 Power BI 記錄體驗並與它們互動。

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Microsoft Power BI Embedded 授權
在 **Microsoft Power BI Embedded** 使用模型中，Power BI 的授權並不是使用者的責任。  而是由使用視覺效果之應用程式的開發人員購買**工作階段**，並向擁有那些資源的訂用帳戶收費。 其他資訊請見定價頁面 (https://azure.microsoft.com/en-us/pricing/details/power-bi-embedded/)。

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Microsoft Power BI Embedded 概念模型
![](media/powerbi-embedded-whats-is/model.png)

就像 Azure 中的任何其他服務一樣，Power BI Embedded 的資源也是透過 [Azure Resource Manager API](https://msdn.microsoft.com/library/mt712306.aspx) 佈建。 在此情況下，您佈建的資源是 **Power BI 工作區集合**。

## <a name="workspace-collection"></a>工作區集合
**工作區集合**是包含 0 個或更多**工作區**之資源的最上層 Azure 容器。  **工作區** **集合** has all of the standard zure properties, as well as the following:

* **存取金鑰** – 安全呼叫 Power BI API 時使用的金鑰 (會在稍後的小節中說明)。
* **使用者** – 具有管理權現，可透過 Azure 入口網站或 Azure Resource Manager API 管理 Power BI 工作區集合的 Azure Active Directory (AAD) 使用者。
* **區域** – 佈建**工作區集合**時，您可以選取要佈建的區域。 如需詳細資訊，請參閱 [Azure 地區](https://azure.microsoft.com/regions/)。

## <a name="workspace"></a>工作區
**工作區** 是 Power BI 內容的容器，可包括資料集、報告及儀表板。 **工作區** 在第一次建立時是空白的。 在使用預覽版期間，您將會使用 Power BI Desktop 編寫所有內容，且您會使用 [Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx) 將 PBIX 自動部署至您的工作區中。

## <a name="using-workspace-collections-and-workspaces"></a>使用工作區集合和工作區
**工作區集合**與**工作區**是內容的容器，您可以針對您正在建立之應用程式的設計，採用任何最佳方式來使用及組織內容。 將會有許多不同的方式可供您管理它們內部的內容。 您可以選擇將所有內容放在某一個工作區內，然後之後再使用應用程式權杖進一步在您的客戶之間細分內容。 您也可以選擇將所有客戶放在個別工作區中，這樣一來他們之間就會有一些區隔。 或者，您可選擇依據地區 (而非依據客戶) 來組織使用者。 這個彈性設計可讓您選擇最佳的內容組織方式。

## <a name="cached-datasets"></a>快取的資料集
預覽版中可以使用快取的資料集  但是，在資料已載入 **Microsoft Power BI Embedded** 之後，您無法重新整理快取的資料。

## <a name="authentication-and-authorization-with-app-tokens"></a>應用程式權杖中的驗證與授權
**Microsoft Power BI Embedded** 會延遲到您的應用程式執行所有必要的使用者驗證和授權。 並沒有明確要求您的使用者必須是 Azure Active Directory (Azure AD) 的客戶。  您的應用程式會透過使用「應用程式驗證權杖」(應用程式權杖)，將轉譯 Power BI 報表的授權出示給 **Microsoft Power BI Embedded**。  這些 **應用程式權杖** 會在您的應用程式想要轉譯報表時視需要建立。

![](media/powerbi-embedded-whats-is/app-tokens.png)

應用程式驗證權杖 (應用程式權杖) 是用來針對 **Microsoft Power BI Embedded** 進行驗證。  應用程式權杖 有三種類型：

1. 佈建權杖 - 將新的「工作區」佈建到「工作區集合」時使用
2. 開發權杖 - 在直接呼叫 **Power BI REST API**
3. 內嵌權杖 - 在進行呼叫以在內嵌的 iframe 中轉譯報表時使用

這些權杖會在您和 **Microsoft Power BI Embedded**互動時的各種不同階段使用。  設計權杖的目的是為了讓您從您的應用程式委派權限給 Power BI。 如需詳細資訊，請參閱 [應用程式權杖流程](power-bi-embedded-app-token-flow.md)。

## <a name="see-also"></a>另請參閱
* [Microsoft Power BI Embedded 常見案例](power-bi-embedded-scenarios.md)
* [開始使用 Microsoft Power BI Embedded](power-bi-embedded-get-started.md)



<!--HONumber=Dec16_HO2-->


