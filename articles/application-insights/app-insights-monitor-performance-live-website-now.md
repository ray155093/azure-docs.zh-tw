---
title: 在執行中的 IIS 網站上診斷效能問題 | Microsoft Docs
description: 監視網站的效能而不重新部署網站。使用獨立或帶有 Application Insights 的 SDK，取得相依性遙測資料。
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2016
ms.author: awills

---
# 在執行階段使用 Application Insights 檢測 Web 應用程式
*Application Insights 目前僅供預覽。*

您可以使用 Visual Studio Application Insights 檢測即時 Web 應用程式，而不需修改或重新部署您的程式碼。如果您的應用程式是由內部部署 IIS 伺服器裝載，請安裝「狀態監視器」；或者，如果它們是 Azure Web 應用程式或在 Azure VM 中執行，您可以安裝 Application Insights 延伸模組。(我們孩提供有關檢測[即時 J2EE Web 應用程式](app-insights-java-live.md)和 [Azure 雲端服務](app-insights-cloudservices.md)的個別文章)。

![範例圖表](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

下列三種方法均可讓您將 Application Insights 套用至 .NET Web 應用程式：

* **建置階段：**[加入 Application Insights SDK][greenbrown] 至您的 Web 應用程式程式碼。
* **執行階段︰**如下所述，檢測伺服器上的 Web 應用程式，而不需重建並重新部署程式碼。
* **兩者︰** 將 SDK 建置到您的 Web 應用程式程式碼中，同時套用執行階段延伸模組。集合兩者之優點。

以下是您可在每種方法中取得的優勢摘要︰

|  | 建置階段 | 執行階段 |
| --- | --- | --- |
| 要求和例外狀況 |是 |是 |
| [更詳細的例外狀況](app-insights-asp-net-exceptions.md) | |是 |
| [相依性診斷](app-insights-asp-net-dependencies.md) |在 .NET 4.6+ 上 |是 |
| [系統效能計數器](app-insights-web-monitor-performance.md#system-performance-counters) | |IIS 或 Azure 雲端服務，而非 Azure Web 應用程式 |
| [自訂遙測的 API][api] |是 | |
| [追蹤記錄檔整合](app-insights-asp-net-trace-logs.md) |是 | |
| [頁面檢視和使用者資料](app-insights-javascript.md) |是 | |
| 不需要重建程式碼 |否 | |

## 在執行階段檢測 Web 應用程式
您需要 [Microsoft Azure](http://azure.com) 訂用帳戶。

### 如果您的應用程式裝載於您的 IIS 伺服器
1. 在 IIS Web 伺服器上，以系統管理員認證登入。
2. 下載並執行[狀態監視器安裝程式](http://go.microsoft.com/fwlink/?LinkId=506648)。
3. 在安裝精靈中，登入 Microsoft Azure。
   
    ![使用 Microsoft 帳戶認證登入 Azure](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)
   
    *連接錯誤？ 請參閱[疑難排解](#troubleshooting)。*
4. 挑選您想要監視的已安裝 Web 應用程式或網站，然後設定您在 Application Insights 入口網站中查看結果時想要使用的資源。
   
    ![選擇應用程式和資源。](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)
   
    一般來說，您可以選擇設定新的資源和[資源群組][roles]。
   
    否則，如果您已經為網站設定 [Web 測試][availability]，或設定 [Web 用戶端監視][client]，請使用現有的資源。
5. 重新啟動 IIS。
   
    ![選擇對話方塊頂端的 [重新啟動]。](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)
   
    您的 Web 服務將會中斷一小段時間。
6. 請注意，ApplicationInsights.config 已插入至您想要監視的 Web 應用程式。
   
    ![在 Web 應用程式的程式碼檔案旁找到 .config 檔案。](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)
   
   web.config 也有一些變動。

#### 稍後再 (重新) 設定嗎？
完成精靈之後，您隨時都可以重新設定代理程式。如果已安裝代理程式，但初始設定有一些問題，則您也可以這樣做。

![按一下工作列上的 [Application Insights] 圖示](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

### 如果您的應用程式是以 Azure Web 應用程式執行
1. 在 [Azure 入口網站](https://portal.azure.com)中，建立 ASP.NET 類型的 Application Insights 資源。這是將會儲存、分析和顯示您的應用程式遙測的位置。
   
    ![加入 Application Insights。選取 ASP.NET 類型。](./media/app-insights-monitor-performance-live-website-now/01-new.png)
2. 現在開啟您的 Azure Web 應用程式的控制項刀鋒視窗，開啟 [工具] > [效能監視] 以加入 Application Insights 延伸模組。
   
    ![在您的 Web 應用程式中，依序按一下 [工具]、[延伸模組]、[加入]、[Application Insights]](./media/app-insights-monitor-performance-live-website-now/05-extend.png)
   
    選取您剛建立的 Application Insights 資源。

### 如果是 Azure 雲端服務專案
[將指令碼加入至 Web 和背景工作角色](app-insights-cloudservices.md)

## 檢視效能遙測資料
登入 [Azure 入口網站](https://portal.azure.com)、瀏覽 Application Insights，然後開啟您建立的資源。

![依序選擇 [瀏覽]、[Application Insights]，然後選取您的應用程式](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

開啟 [效能] 刀鋒視窗，即可查看要求、回應時間、相依性和其他資料。

![效能](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

按一下任何圖表即可開啟更詳細的檢視。

您可以[編輯、重新排列、儲存](app-insights-metrics-explorer.md)圖表或整個刀鋒視窗，以及將其釘選至[儀表板](app-insights-dashboards.md)。

## 相依項目
相依性工期圖表會顯示呼叫從應用程式到外部元件 (例如資料庫、REST API 或 Azure Blob 儲存體) 所經過的時間。

若要藉由呼叫不同的相依性來分隔圖表：請編輯圖表，開啟 [群組]，然後依 [相依性]、[相依性類型] 或 [相依性效能] 分組。

![相依性](./media/app-insights-monitor-performance-live-website-now/23-dep.png)

## 效能計數器
(不適用於 Azure Web 應用程式。) 在 [概觀] 刀鋒視窗中按一下 [伺服器]，以查看伺服器效能計數器的圖表，例如 CPU 佔用和記憶體使用量。

如果您有數個伺服器執行個體，您可能想要編輯圖表以便依角色執行個體分組。

![伺服器](./media/app-insights-monitor-performance-live-website-now/22-servers.png)

您也可以[變更由 SDK 報告的效能計數器設定](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)。

## 例外狀況
![逐一點選伺服器例外狀況圖表](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

您可以鑽研過去七天的特定例外狀況，並取得堆疊追蹤和內容資料。

## 取樣
如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則調適性取樣功能可能會運作，並只傳送一部分的遙測資料。[深入了解取樣。](app-insights-sampling.md)

## 疑難排解
### 連接錯誤
您需要在伺服器防火牆開啟[某些傳出的連接埠](app-insights-ip-addresses.md#outgoing-ports)，以允許狀態監視器運作。

### 沒有遙測資料？
* 使用您的網站來產生一些資料。
* 等候幾分鐘讓資料抵達，然後按一下 [重新整理]。
* 開啟 [診斷搜尋] \([搜尋] 圖格) 以查看個別事件。彙總資料在圖表中出現之前，事件通常會顯示在 [診斷搜尋] 中。
* 開啟狀態監視器，然後選取左窗格中的應用程式。檢查 [設定通知] 區段中是否有任何關於此應用程式的診斷訊息：
  
  ![開啟 [效能] 刀鋒視窗，即可查看要求、回應時間、相依性和其他資料](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* 請確定伺服器防火牆允許上列連接埠的連出流量。
* 如果您在伺服器上看到有關「權限不足」的訊息，請嘗試下列操作：
  * 在 IIS 管理員中，選取應用程式集區，開啟 [進階設定]，並記下 [處理序模型] 下的身分識別。
  * 在電腦的管理控制台中，將此身分識別加入至效能監試器使用者群組。
* 如果您的伺服器上已安裝 MMA/SCOM，某些版本可能會發生衝突。解除安裝 SCOM 和狀態監視器，重新安裝最新版本。
* 請參閱[疑難排解][qna]。

## 系統需求
支援伺服器上 Application Insights 狀態監視器的作業系統：

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2

(含最新版 SP 和 .NET Framework 4.0 和 4.5)

在用戶端 Windows 7、8 和 8.1 上，一樣需含有 .NET Framework 4.0 和 4.5

IIS 支援：IIS 7、7.5、8、8.5 (需要有 IIS)

## 使用 PowerShell 進行自動化
您可以使用 PowerShell 啟動和停止監視。

先匯入 Application Insights 模組︰

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

找出受監視的應用程式︰

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (選擇性) Web 應用程式的名稱。
* 顯示此 IIS 伺服器中每個 Web 應用程式 (或具名應用程式) 的 Application Insights 監視狀態。
* 傳回每個應用程式的 `ApplicationInsightsApplication`︰
  
  * `SdkState==EnabledAfterDeployment`︰應用程式正受到監視，並已在執行階段透過「狀態監視器」工具或 `Start-ApplicationInsightsMonitoring` 進行檢測。
  * `SdkState==Disabled`︰不會針對 Application insights 檢測應用程式。應用程式從未接受檢測，或「狀態監視器」工具或 `Stop-ApplicationInsightsMonitoring` 已停用執行階段監視。
  * `SdkState==EnabledByCodeInstrumentation`︰已透過將 SDK 加入至原始程式碼來檢測應用程式。其 SDK 無法更新或停止。
  * `SdkVersion` 會顯示正用來監視此應用程式的版本。
  * `LatestAvailableSdkVersion` 會顯示 NuGet 資源庫上目前可用的版本。若要將應用程式升級至此版本，請使用 `Update-ApplicationInsightsMonitoring`。

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` 應用程式在 IIS 中的名稱
* `-InstrumentationKey` 您想要在其中顯示結果之 Application Insights 資源的 ikey。
* 這個 Cmdlet 只會影響尚未檢測的應用程式，也就是 SdkState==NotInstrumented。
  
    這個 Cmdlet 不會影響已檢測的應用程式，不論是在建置階段透過將 SDK 加入至程式碼，還是在執行階段透過先前使用的這個 Cmdlet。
  
    用來檢測應用程式的 SDK 版本是最常下載到此伺服器的版本。
  
    若要下載最新版本，請使用 Update-ApplicationInsightsVersion。
* 成功時會傳回 `ApplicationInsightsApplication`。如果失敗，則會在 stderr 記錄追蹤。

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` 應用程式在 IIS 中的名稱
* `-All` 停止監視此 IIS 伺服器中所有 `SdkState==EnabledAfterDeployment` 的應用程式
* 停止監視指定應用程式並移除檢測。它僅適用於已在執行階段使用「狀態監視器」工具或 Start-ApplicationInsightsApplication 進行檢測的應用程式。(`SdkState==EnabledAfterDeployment`)
* 傳回 ApplicationInsightsApplication。

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`：Web 應用程式在 IIS 中的名稱。
* `-InstrumentationKey` (選擇性)。 使用此參數可變更應用程式的遙測所要傳送至的資源。
* 此 Cmdlet：
  * 將具名應用程式升級至最近下載到這台電腦之 SDK 的版本。(僅適用於 `SdkState==EnabledAfterDeployment` 時)
  * 如果您提供檢測金鑰，具名應用程式會重新設定為將遙測傳送至具有該索引鍵的資源。(適用於 `SdkState != Disabled` 時)

`Update-ApplicationInsightsVersion`

* 將最新的 Application Insights SDK 下載至伺服器。

## Azure 範本
如果 Web 應用程式位於 Azure 內，而且您使用 Azure Resource Manager 範本建立資源，您可以透過將以下內容新增到資源節點來設定 Application Insights：

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` - Application Insights 資源的名稱
* `myWebAppName` - Web 應用程式的識別碼

## <a name="next"></a>接續步驟
* [建立 Web 測試][availability]，確定您的網站保持即時狀態。
* [搜尋事件和記錄][diagnostic]以協助診斷問題。
* [加入 Web 用戶端遙測][usage]，以查看網頁程式碼中的例外狀況，並讓您插入追蹤呼叫。
* [將 Application Insights SDK 加入至您的 Web 服務程式碼][greenbrown]，便可以將追蹤和記錄呼叫插入至伺服器程式碼中。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=AcomDC_0907_2016-->