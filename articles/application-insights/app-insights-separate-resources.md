---
title: "在 Azure Application Insights 中監視開發、測試和實際執行 | Microsoft Docs"
description: "監視應用程式在不同開發階段的效能和使用量"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 96614dd3c0bf9c55ffd81d0912ecb62b71c32b22
ms.openlocfilehash: 2eeb22d310a3aa67bacf821d319fb2919d0b536f


---
# <a name="separating-application-insights-resources"></a>分隔 Application Insights 資源
來自應用程式不同元件和版本的遙測，應該傳送到不同的 Application Insights 資源，還是結合為一呢？ 本文將探討最佳作法和所需的技術。

首先，讓我們了解這個問題。 從您的應用程式收到的資料會由 Microsoft Azure 儲存及處理在 Application Insights *資源*中。 每項資源均會以「檢測金鑰」  (iKey) 來加以識別。 在應用程式中，該金鑰會提供給 Application Insights SDK，使它可以將所收集的資料傳送到正確的資源。 可以在程式碼或在 ApplicationInsights.config 中提供金鑰。 藉由變更 SDK 中的金鑰，您可以將資料導向不同資源。 

在簡單的案例中，當您為新的應用程式建立程式碼時，您也會在 Application Insights 中建立新的資源。 在 Visual Studio 中，[新增專案]  對話方塊會為您執行此作業。

如果是高容量網站，它可能會部署在多個伺服器執行個體上。

在更複雜的案例中，您的系統是由多個元件組成，例如網站和後端處理器。 

## <a name="when-to-use-separate-ikeys"></a>使用個別 iKey 的時機
以下有幾個一般方針︰

* 當您擁有可獨立部署的應用程式單位，而其執行於一組可以不管其他元件，單獨擴充/縮編的伺服器執行個體上時，您通常可以將其對應至單一資源，也就是它會擁有單一檢測金鑰 (iKey)。
* 相較之下，使用個別 iKey 的原因包括︰
  * 可輕鬆地從個別元件讀取個別度量。
  * 區隔低容量遙測與高容量遙測，讓一個串流上的節流、配額和取樣不會影響另一個。
  * 區隔警示、匯出和工作項目組態。
  * 分散 [限制](app-insights-pricing.md#limits-summary) ，例如遙測配額、節流和 Web 測試計數。
  * 開發和測試中的程式碼應該傳送至與生產戳記不同的 iKey。  

許多 Application Insights 入口網站體驗在設計時，都會將這些指導方針納入考量。 例如，伺服器會檢視伺服器執行個體上的區段，並假設一個邏輯元件的相關遙測可能來自數個伺服器執行個體。

## <a name="single-ikey"></a>單一 iKey
若您將來自多個元件的遙測傳送給單一 iKey：

* 對所有遙測新增屬性可讓您區隔和篩選元件的身份識別。 角色識別碼會自動從伺服器角色執行個體新增至遙測，但在其他案例中，您可以使用 [遙測初始設定式](app-insights-api-filtering-sampling.md#add-properties) 來新增屬性。
* 同時更新不同元件中的 Application Insights SDK。 一個 iKey 的遙測應該源自相同版本的 SDK。

## <a name="separate-ikeys"></a>個別 iKey
若您有不同應用程式元件的多個 iKey：

* 建立 [儀表板](app-insights-dashboards.md) ，以檢視由不同應用程式元件結合而成的邏輯應用程式中的重要遙測。 儀表板可以共用，因此一個邏輯系統檢視可供不同團隊使用。
* 在團隊層級組織 [資源群組](app-insights-resources-roles-access-control.md) 。 存取權限是由資源群組指派，且這些權限包含用來設定警示的權限。 
* 使用 [Azure Resource Manager 範本和 Powershell](app-insights-powershell.md) 來協助管理構件，例如警示規則和 Web 測試。

## <a name="separate-ikeys-for-devtest-and-production"></a>適用於開發/測試和生產環境的個別 iKey
若要能更輕鬆地在應用程式發行時自動變更金鑰，請在程式碼設定 iKey，而非在 ApplicationInsights.config 中設定。

(如果您的系統是「Azure 雲端服務」，有[另一個設定個別 ikey 的方法](app-insights-cloudservices.md))。

### <a name="a-namedynamic-ikeya-dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> 動態檢測金鑰
在初始化方法中設定金鑰，例如 ASP.NET 服務中的 global.aspx.cs：

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

在此範例中，不同資源的 ikeys 會放置在不同版本的 Web 組態檔中。 交換 Web 組態檔 (您可以在發行指令碼中進行) 將會交換目標資源。

### <a name="web-pages"></a>網頁
iKey 也會用在您的應用程式網頁中，在 [您從快速啟動刀鋒視窗取得的指令碼](app-insights-javascript.md)中。 不要按其原義編寫至指令碼，請從伺服器狀態產生。 例如，在 ASP.NET 應用程式中：

*Razor 中的 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="creating-an-additional-application-insights-resource"></a>建立其他 Application Insights 資源
如果您決定區隔不同應用程式元件的遙測，或區隔相同元件的不同戳記 (開發/測試/生產) 的遙測，則必須建立新的 Application Insights 資源。

在 [portal.azure.com](https://portal.azure.com)中新增 Application Insights 資源：

![按一下 [新增]，然後按一下 [Application Insights]](./media/app-insights-separate-resources/01-new.png)

* **應用程式類型** 會影響您在 [概觀] 刀鋒視窗中看到的內容，以及 [計量瀏覽器](app-insights-metrics-explorer.md)中提供的屬性。 如果沒有看到您的應用程式類型，請針對網頁選擇其中一個 Web 類型。
* **資源群組** 可讓您輕鬆管理屬性，例如 [存取控制](app-insights-resources-roles-access-control.md)。 您可以對開發、測試和生產環境使用不同的資源群組。
* **訂用帳戶** 是您在 Azure 中的付款帳戶。
* **位置** 是我們保留您資料的地方。 目前無法變更位置。 
* **新增至儀表板** 可在 Azure 首頁上放置資源的快速存取圖格。 

建立資源需要幾秒鐘。 完成時，您會看到警示。

(您可以撰寫 [PowerShell 指令碼](app-insights-powershell-script-create-resource.md)來自動建立資源)。

## <a name="getting-the-instrumentation-key"></a>取得檢測金鑰
檢測金鑰會識別您所建立的資源。 

![按一下 [基本功能]，按一下 [檢測金鑰]，CTRL+C](./media/app-insights-separate-resources/02-props.png)

您將需要您的應用程式會將資料傳送至其中的所有資源的檢測金鑰。




<!--HONumber=Jan17_HO4-->


