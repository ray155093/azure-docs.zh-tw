<properties
   pageTitle="何謂 Microsoft Power BI Embedded？"
   description="Power BI Embedded 可讓您將 Power BI 報表整合到 Web 或行動應用程式中，您就不需要建立自訂解決方案來為您的使用者將資料視覺化"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="mblythe"/>

# 何謂 Microsoft Power BI Embedded？

運用 **Power BI Embedded**，您可以將 Power BI 報告整合至您的 Web 應用程式或行動應用程式。

![](media\powerbi-embedded-whats-is\what-is.png)

Power BI Embedded 是一個 **Azure 服務**，可讓 ISV 和應用程式開發人員在其應用程式中呈現 Power BI 資料體驗。身為開發人員，您已建置應用程式，這些應用程式有它們自己的使用者和個別的功能集。這些應用程式也可能已經內建一些資料元素，例如現在可以由 Microsoft Power BI Embedded 提供的圖表和報表。使用者不需要 Power BI 帳戶就可以使用您的應用程式。他們可以像以前一樣繼續登入您的應用程式，以及在不需要任何額外授權的情況下，檢視 Power BI 記錄體驗並與它們互動。

## Microsoft Power BI Embedded 授權

在 **Microsoft Power BI Embedded** 使用模型中，Power BI 的授權並不是使用者的責任。而是由使用視覺效果之應用程式的開發人員購買**轉譯器**，並向擁有那些資源的訂用帳戶收費。

## Microsoft Power BI Embedded 概念模型

![](media\powerbi-embedded-whats-is\model.png)

就像 Azure 中的任何其他服務一樣，Power BI Embedded 也是透過 [Azure ARM API](https://msdn.microsoft.com/library/mt712306.aspx) 佈建。在此情況下，您佈建的資源是 **Power BI 工作區集合**。

## 工作區集合

**工作區集合**是包含 0 個或更多**工作區**之資源的最上層 Azure 容器。**工作區****集合**擁有所有標準 Azure 屬性，以及下列項目：

-	**存取金鑰** – 安全呼叫 Power BI API 時使用的金鑰 (會在稍後的小節中說明)。
-	**使用者** – 具有管理權現，可透過 Azure 入口網站或 ARM API 管理 Power BI 工作區集合的 Azure Active Directory (AAD) 使用者。
-	**區域** – 佈建**工作區集合**時，您可以選取要佈建的區域。如需詳細資訊，請參閱 [Azure 地區](https://azure.microsoft.com/regions/)。

## 工作區

**工作區**是 Power BI 內容的容器，可包括資料集、報告及儀表板。**工作區**在第一次建立時是空白的。在使用預覽版期間，您將會使用 Power BI Desktop 授權所有內容，且您會使用 [Power BI REST API](http://docs.powerbi.apiary.io/reference) 將它上傳至您其中一個工作區。

## 使用工作區集合和工作區
**工作區集合**與**工作區**是內容的容器，您可以針對您正在建立之應用程式的設計，採用任何最佳方式來使用及組織內容。將會有許多不同的方式可供您管理它們內部的內容。您可以選擇將所有內容放在某一個工作區內，然後之後再使用應用程式權杖進一步在您的客戶之間細分內容。您也可以選擇將所有客戶放在個別工作區中，這樣一來他們之間就會有一些區隔。或者，您可選擇依據地區 (而非依據客戶) 來組織使用者。這個彈性設計可讓您選擇最佳的內容組織方式。

## 快取的資料集

預覽版中可以使用快取的資料集但是，在資料已載入 **Microsoft Power BI Embedded** 之後，您無法重新整理快取的資料。

## 應用程式權杖中的驗證與授權

**Microsoft Power BI Embedded** 會延遲到您的應用程式執行所有必要的使用者驗證和授權。並沒有明確要求您的使用者必須是 Azure Active Directory (Azure AD) 的客戶。您的應用程式會透過使用「應用程式驗證權杖」(應用程式權杖)，將轉譯 Power BI 報表的授權出示給 **Microsoft Power BI Embedded**。這些**應用程式權杖**會在您的應用程式想要轉譯報表時視需要建立。請參閱[應用程式權杖](power-bi-embedded-get-started-sample.md#key-flow)。

![](media\powerbi-embedded-whats-is\app-tokens.png)

應用程式驗證權杖 (應用程式權杖) 是用來針對 **Microsoft Power BI Embedded** 進行驗證。應用程式權杖有三種類型：

1.	佈建權杖 - 將新的「工作區」佈建到「工作區集合」時使用
2.	開發權杖 - 在直接呼叫 **Power BI REST API** 時使用
3.	內嵌權杖 - 在進行呼叫以在內嵌的 iframe 中轉譯報表時使用

這些權杖會在您和 **Microsoft Power BI Embedded** 互動時的各種不同階段使用。設計權杖的目的是為了讓您從您的應用程式委派權限給 Power BI。如需詳細資訊，請參閱[應用程式權杖流程](power-bi-embedded-app-token-flow.md)。

## 另請參閱
- [Microsoft Power BI Embedded 常見案例](power-bi-embedded-scenarios.md)
- [開始使用 Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!----HONumber=AcomDC_0907_2016-->