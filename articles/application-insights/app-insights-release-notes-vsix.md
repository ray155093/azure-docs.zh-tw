<properties
	pageTitle="適用於 Developer Analytics 之 Visual Studio 延伸模組的版本資訊"
	description="適用於 Developer Analytics 之 Visual Studio 工具的最新更新。"
	services="application-insights"
    documentationCenter=""
	authors="acearun"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2016"
	ms.author="acearun"/>

# Developer Analytics 工具的版本資訊
新功能：Visual Studio 中的 Application Insights 和 HockeyApp 分析
## 7\.0 版
### Visual Studio Application Insights 趨勢
Visual Studio Application Insights 是 Visual Studio 中的新工具，您可用來分析應用程式在一段時間內的運作方式。若要開始使用，請在 **Application Insights** 工具列按鈕或在 [Application Insights 搜尋] 視窗中選擇 [探索遙測趨勢]。或者，在 [檢視] 功能表上，按一下 [其他視窗]，然後按一下 [Application Insights 趨勢]。選擇五種常見查詢的其中一個，以便開始使用。您可以根據遙測類型、時間範圍和其他屬性，分析不同的資料集。若要尋找資料中的異常狀況，請選擇 [檢視類型] 下拉式清單中的其中一個異常選項。視窗底部的篩選選項可讓您輕鬆地全神貫注於特定的遙測子集。

![Application Insights 趨勢](./media/app-insights-release-notes-vsix/Trends.PNG)

### CodeLens 中的例外狀況
CodeLens 現在會顯示例外狀況遙測。如果您已將專案連線到 Application Insights 服務，您會看到過去 24 小時內生產環境中的每個方法所發生的例外狀況數量。您可以從 CodeLens 跳到 [搜尋] 或 [趨勢] 以深入調查例外狀況。

![CodeLens 中的例外狀況](./media/app-insights-release-notes-vsix/ExceptionsCodeLens.png)

### ASP.NET Core 支援
Application Insights 現在支援 Visual Studio 中的 ASP.NET Core RC2 專案。您可以從 [新增專案] 對話方塊將 Application Insights 加入至新的 ASP.NET Core RC2 專案，如下列螢幕擷取畫面所示。或者，您可以將它加入至現有專案，在 [方案總管] 中以滑鼠右鍵按一下專案，然後按一下 [新增 Application Insights 遙測]。

![ASP.NET Core 支援](./media/app-insights-release-notes-vsix/NetCoreSupport.PNG)

ASP.NET 5 RC1 和 ASP.NET Core RC2 專案也會在 [診斷工具] 視窗中提供新的支援。當您在電腦上進行本機偵錯時，將可看見來自您 ASP.NET 應用程式的 Application Insights 事件 (例如要求和例外狀況)。從每個事件，按一下 [搜尋] 來向下切入，以取得更多詳細資訊。

![診斷工具支援](./media/app-insights-release-notes-vsix/DiagnosticTools.PNG)

### 通用 Windows 應用程式的 HockeyApp
除了 Beta 版散發套件和使用者意見反應，HockeyApp 還提供通用 Windows 應用程式的符號式當機報告。我們已經將 HockeyApp SDK 的新增作業變得更簡單︰以滑鼠右鍵按一下您的通用 Windows 專案，然後按一下 [Hockey App - 啟用當機分析]。這會安裝 SDK、設定當機資訊收集，並在雲端佈建 HockeyApp 資源，全都不需要將您的應用程式上傳至 HockeyApp 服務。

其他的新功能：

* 我們已讓 Application Insights 搜尋體驗變得更快速且更直覺化現在，會在您選取時自動套用時間範圍和詳細資料篩選條件。
* 而且在 [Application Insights 搜尋] 中，現在有選項可直接從要求遙測跳至程式碼。
* 我們已經改善的 HockeyApp 登入體驗。
* [診斷工具] 會顯示例外狀況的生產遙測資訊。

## 5\.2 版
很高興為您宣布，我們已推出 Visual Studio 中的 HockeyApp 案例。第一個整合是 Visual Studio 內通用 Windows 應用程式和 Windows Forms 應用程式的 Beta 版散發套件。

使用 Beta 版散發套件，您可將舊版應用程式上傳至 HockeyApp，以散發給一小組選定的客戶或測試人員。Beta 版散發套件在結合了 HockeyApp 當機資訊收集和使用者意見反應功能之後，可以在您廣泛發行應用程式前提供您有關應用程式的寶貴資訊。您可以使用這項資訊來解決應用程式的問題，以避免或減少日後的問題，例如應用程式評分很低、負面意見反應等。

請看看從 Visual Studio 內上傳 Beta 版散發套件的組件有多麼簡單。
### 通用 Windows 應用程式
通用 Windows 應用程式專案節點的內容功能表現在包含將組建上傳至 HockeyApp 的選項。

![通用 Windows 應用程式的專案內容功能表](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

請選擇該項目，HockeyApp 上傳對話方塊隨即開啟。您必須有 HockeyApp 帳戶才能上傳組建。如果您是新使用者，別擔心。建立帳戶的程序相當簡單。

連線之後，您會在對話方塊中看到上傳表單。

![通用 Windows 應用程式的上傳對話方塊](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

選取要上傳的內容 (.appxbundle 或 .appx 檔案)，然後在精靈中選擇發行選項。在下一頁中，您可以選擇性地新增版本資訊。選擇 [完成] 以開始上傳。

當上傳完成時，HockeyApp 通知隨即出現，裡面有確認訊息和通往 HockeyApp 入口網站中應用程式的連結。

![上傳完成通知](./media/app-insights-release-notes-vsix/UploadComplete.png)

這樣就大功告成了！ 您剛才已上傳 Beta 版散發套件的組建，而整個過程只是按幾下滑鼠而已。

您可以在 HockeyApp 入口網站中以多種方式管理您的應用程式。這包括邀請使用者、檢視當機報告和意見反應、變更詳細資料等等。

![HockeyApp 入口網站](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

如需應用程式管理的詳細資訊，請參閱 [HockeyApp 知識庫](http://support.hockeyapp.net/kb/app-management-2)。

### Windows Forms 應用程式
Windows Forms 專案節點的內容功能表現在包含將組建上傳至 HockeyApp 的選項。

![Windows Forms 應用程式的專案內容功能表](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

這會開啟與通用 Windows 應用程式中類似的 HockeyApp 上傳對話方塊。

![Windows Forms 應用程式的上傳對話方塊](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

請注意，此精靈有一個新欄位可供指定應用程式的版本。在通用 Windows 應用程式中，資訊會從資訊清單填入。不幸的是，Windows Forms 應用程式沒有這項功能的對等項目。您需要手動進行指定。

流程的其餘部分與通用 Windows 應用程式類似：在 HockeyApp 入口網站中選擇組建和發行選項、新增版本資訊、上傳及管理。

整個過程就是這麼簡單。立即試試並與我們分享您的想法。
## 版本 4.3
### 從本機偵錯工作階段搜尋遙測
在此版本中，您現在可以搜尋在 Visual Studio 偵錯工作階段中產生的 Application Insights 遙測。以前，您只能在向 Application Insights 註冊應用程式後才能使用搜尋。現在，您的應用程式只需要安裝 Application Insights SDK，就能搜尋本機遙測。

如果您的 ASP.NET 應用程式具有 Application Insights SDK，請執行下列步驟來使用搜尋功能。

1. 為您的應用程式偵錯。
2. 使用下列其中一種方式來開啟 Application Insights 搜尋：
	- 在 [檢視] 功能表上，按一下 [其他視窗]，然後按一下 [Application Insights 搜尋]。
	- 按一下 [Application Insights] 工具列按鈕。
	- 在 [方案總管] 中，展開 [ApplicationInsights.config]，然後按一下 [搜尋偵錯工作階段遙測]。
3. 如果您尚未註冊 Application Insights，搜尋視窗會以偵錯工作階段遙測模式開啟。
4. 按一下 [搜尋] 圖示，以查看您的本機遙測。

![上傳完成](./media/app-insights-release-notes-vsix/LocalSearch.png)

## 4\.2 版
在此版本中，我們新增了一些功能，可讓您更容易在事件內容中搜尋資料、能夠從更多資料事件跳到程式碼，以及輕鬆將記錄資料傳送給 Application Insights。這個擴充功能會每月更新。如果您有意見反應或功能要求，請傳送至 aidevtools@microsoft.com。
### 零點選的記錄體驗
如果您已經在使用 NLog、Log4Net 或 System.Diagnostics 追蹤功能，則不必擔心要如何將所有追蹤移到 Application Insights。在此版本中，我們已將 Application Insights 記錄配接器與標準設定體驗整合。如果您已經設定了這些記錄架構其中之一，則下一節會說明取得它的方式。**如果您已經新增 Application Insights：**
1. 以滑鼠右鍵按一下專案節點，然後按一下 [Application Insights]，再按一下 [設定 Application Insights]。請確定您在設定視窗中看到可新增正確配接器的選項。
2. 或者，當您建置方案時，請注意出現在畫面右上角的快顯視窗並按一下 [設定]。

![記錄通知](./media/app-insights-release-notes-vsix/LoggingToast.png)

安裝記錄配接器後，請執行應用程式並確定是否在 [診斷工具] 索引標籤中看到資料，如下所示：

![追蹤](./media/app-insights-release-notes-vsix/Traces.png)

### 跳至或尋找發出遙測事件屬性的程式碼
使用新版本時，使用者可以按一下事件詳細資料中的任何值，這將會搜尋目前已開啟的方案中相符的字串。結果會顯示在 Visual Studio [尋找結果] 清單中，如以下所示：

![尋找相符項目](./media/app-insights-release-notes-vsix/FindMatch.png)

### 當您未登入時的新搜尋視窗
我們已改善 [Application Insights 搜尋] 視窗的外觀，以協助您在應用程式生產時搜尋資料。

![[搜尋] 視窗](./media/app-insights-release-notes-vsix/SearchWindow.png)

### 查看與事件關聯的所有遙測事件
我們在事件詳細資料索引標籤的旁邊加入了新的索引標籤，其中包含使用者所檢視之遙測事件的所有相關資料的預先定義查詢。例如，要求有一個名為 [作業識別碼] 的欄位。與此要求相關聯的每一個事件都有相同的 [作業識別碼] 值。如果作業在處理要求時發生例外狀況，例外狀況會取得與要求相同的作業識別碼，以便輕鬆尋找。如果您正在查看要求，請按一下 [此作業的所有遙測]，以開啟可顯示新的搜尋結果的新索引標籤。

![相關項目](./media/app-insights-release-notes-vsix/RelatedItems.png)

### 搜尋中的前進/後退歷程記錄
您現在可以在搜尋結果之間前後移動。

![返回](./media/app-insights-release-notes-vsix/GoBAck.png)

## 4\.1 版
此版本隨附許多新功能和更新。您必須先安裝 Update 1，才能安裝此版本。

### 從例外狀況跳至原始程式碼中的方法
現在，如果您在 [Application Insights 搜尋] 視窗中檢視生產應用程式的例外狀況，您可以跳到程式碼中發生例外狀況的方法。您只需載入正確的專案，Application Insights 會負責處理其他部分！ (若要深入瞭解 [Application Insights 搜尋] 視窗，請參閱下列各節中適用於 4.0 版的版本資訊)。

運作方式 即使未開啟方案，您仍可使用 pplications Insights 搜尋。堆疊追蹤區域會顯示資訊訊息，而堆疊追蹤中有許多項目無法使用。

如果檔案資訊可用，某些項目可能是連結，但仍會顯示方案的資訊項目。

如果您按一下超連結，您會跳到程式碼中所選方法的位置。版本號碼可能有所不同，但未來版本中將會引進跳至正確的程式碼版本的功能。

![按一下例外狀況詳細資料](./media/app-insights-release-notes-vsix/jumptocode.png)

### 方案總管中搜尋體驗的新進入點
您現在可以透過 [方案總管] 存取搜尋。

![在方案總管中搜尋](./media/app-insights-release-notes-vsix/searchentry.png)

### 在發佈完成時顯示通知
在線上發佈專案後，即會顯示快顯對話方塊，以便您在生產環境中檢視 Application Insights 資料。

![發佈完成通知](./media/app-insights-release-notes-vsix/publishtoast.png)

## 4\.0 版

### 在 Visual Studio 內搜尋 Application Insights 資料
就像 Application Insights 入口網站中的搜尋功能，您可以依事件類型、屬性值和文字進行篩選和搜尋，然後檢查個別事件。

![[搜尋] 視窗](./media/app-insights-release-notes-vsix/search.png)

### 在診斷工具中查看來自本機電腦的資料

除了其他偵錯資料，您可以在 [Visual Studio 診斷工具] 頁面上檢視您的遙測。僅支援 ASP.NET 4.5。

![診斷工具頁面](./media/app-insights-release-notes-vsix/diagtools.png)

### 將 SDK 加入您的專案，而不需登入 Azure

透過 [新增專案] 對話方塊或經由專案內容功能表，您不再需要登入 Azure 即可將 Application Insights 套件加入至專案。如果您登入，就會如同過去一般安裝 SDK 並設定為將遙測傳送至入口網站。如果您不登入，則會將 SDK 加入至專案，且會產生診斷中樞的遙測。您可以稍後視需要加以設定。

![[新增專案] 對話方塊](./media/app-insights-release-notes-vsix/newproject.png)

### 裝置支援

我們在 Connect(); 2015 中[宣布了](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/)裝置的行動開發人員體驗為 HockeyApp。HockeyApp 可協助您發佈 Beta 組建發佈給測試人員、從您的應用程式收集和分析所有的損毀，並直接收集客戶的意見反應。HockeyApp 可讓您在任何所選的平台上建置應用程式，無論是 iOS、Android 或 Windows，或是 Xamarin、Cordova 或 Unity 之類的跨平台解決方案上。

在未來版本的 Application Insights 延伸模組中，我們會在 HockeyApp 與 Visual Studio 之間提供更為整合的體驗。現在您只要新增 NuGet 參考，即可開始使用 HockeyApp。如需詳細資訊，請參閱[文件](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)。

<!---HONumber=AcomDC_0727_2016-->