<properties
	pageTitle="將 Operations Manager 連接到 Log Analytics | Microsoft Azure"
	description="若要維護 System Center Operations Manager 中的現有投資，並使用 Log Analytics 的延伸功能，您可以整合 Operations Manager 與 OMS 工作區。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# 將 Operations Manager 連接到 Log Analytics

若要維護 System Center Operations Manager 中的現有投資，並使用 Log Analytics 的延伸功能，您可以整合 Operations Manager 與 OMS 工作區。這可讓您利用 OMS 的機會，同時繼續使用 Operations Manager：

- 使用 Operations Manager 繼續監視 IT 服務的健全狀況
- 維護與支援事件和問題管理之 ITSM 解決方案的整合
- 管理代理程式的生命週期，這些代理程式部署到內部部署以及您使用 Operations Manager 所監視的公用雲端 IaaS 虛擬機器

與 System Center Operations Manager 整合，可透過利用從 Operations Manager 收集、儲存和分析資料時的 OMS 速度和效率，來增加您服務作業策略的價值。OMS 有助於關聯並努力找出用來識別問題錯誤，以及呈現重複支援現有問題管理程序。檢查效能、事件和警示資料的搜尋引擎的彈性 (具有豐富的儀表板和報告功能可透過有意義的方式公開此資料) 示範 OMS 對值得讚揚的 Operations Manager 的強度。

向 Operations Manager 管理群組回報的代理程式，會根據已在 OMS 訂用帳戶中啟用的 Log Analytics 資料來源和解決方案以從您的伺服器收集資料。根據已啟用的解決方案，這些解決方案中的資料會從 Operations Manager 管理伺服器直接傳送給 OMS Web 服務，或者，因為代理程式所管理系統上收集的資料量，所以這些解決方案中的資料會從代理程式直接傳送給 OMS Web 服務。管理伺服器會將 OMS 資料直接轉送到 OMS Web 服務，而且永遠不會將它寫入 OperationsManager 或 OperationsManagerDW 資料庫。如果管理伺服器中斷與 OMS Web 服務的連接，則會在重新建立與 OMS 的通訊之前，將資料快取在本機。如果管理伺服器因發生規劃的維護或未規劃的中斷而離線，管理群組中的另一部管理伺服器將繼續與 OMS 的連接。

下圖顯示 System Center Operations Manager 與 OMS 的關聯性。

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## 系統需求
開始之前，請檢閱下列詳細資料，確認您符合所需的必要條件。

- OMS 僅支援 Operations Manager 2012 SP1 UR6 和更新版本，以及 Operations Manager 2012 R2 UR2 和更新版本。Operations Manager 2012 SP1 UR7 和 Operations Manager 2012 R2 UR3 中已加入 Proxy 支援。
- OMS 訂用帳戶。如需進一步資訊，請檢閱[開始使用 Log Analytics](log-analytics-get-started.md)。

## 將 Operations Manager 連接到 OMS
執行下列一系列的步驟，設定 Operations Manager 管理群組來連接到其中一個 OMS 工作區。

1. 在 Operations Manager 主控台中，選取 [管理] 工作區。
2. 展開 Operations Management Suite 節點，然後按一下 [連接]。
3. 按一下 [註冊到 Operations Management Suite] 連結。
4. 在 [Operations Management Suite 登入精靈: 驗證] 頁面上，輸入與 OMS 訂用帳戶相關聯之系統管理員帳戶的電子郵件地址或電話號碼和密碼，然後按一下 [登入]。
5. 成功通過驗證之後，在 [Operations Management Suite 登入精靈: 選取工作區] 頁面上，系統將提示您選取 OMS 工作區。如果您有多個工作區，請從下拉式清單中選取您想要向 Operations Manager 管理群組註冊的工作區，然後按 [下一步]。
    >[AZURE.NOTE] Operations Manager 一次只支援一個 OMS 工作區。會從 OMS 移除連接以及使用前一個工作區向 OMS 註冊的電腦。
6. 在 [Operations Manager Suite 登入精靈: 摘要] 頁面上，確認您的設定，如果正確無誤，請按一下 [建立]。
7. 在 [Operations Management Suite 登入精靈: 完成] 頁面上，按一下 [關閉]。

### 加入代理程式所管理的電腦
設定與 OMS 工作區的整合之後，這只會建立與 OMS 的連接，並不會從向管理群組回報的代理程式收集任何資料。除非您設定哪些特定代理程式所管理的電腦將會收集 Log Analytics 的資料，否則不會發生這種情況。您可以個別選取電腦物件，也可以選取包含 Windows 電腦物件的群組。您無法選取包含另一個類別執行個體 (例如邏輯磁碟或 SQL 資料庫) 的群組。

1. 開啟 Operations Manager 主控台，然後選取 [**管理**] 工作區。
2. 展開 Operations Management Suite 節點，然後按一下 [連接]。
3. 按一下窗格右側之 [執行] 標題下方的 [加入電腦/群組] 連結。
4. 在 [電腦搜尋] 對話方塊中，您可以搜尋 Operations Manager 監視的電腦或群組。選取電腦或群組以登入 OMS，並按一下 [加入]，然後按一下 [確定]。

在 Operations 主控台的 [管理] 工作區中，您可以檢視電腦和群組，這些電腦和群組設定成收集來自 Operations Management Suite 下方之 [受管理的電腦] 節點的資料。您可以視需要在這裡加入或移除電腦和群組。

### 在 Operations 主控台中設定 OMS Proxy 設定
如果內部 Proxy 伺服器位在管理群組與 OMS Web 服務之間，請執行下列步驟。這些設定是從管理群組進行集中管理，並且散發至範圍中所含的代理程式所管理系統，來收集 OMS 的資料。特定解決方案略過管理伺服器並將資料直接傳送給 OMS Web 服務時，這十分有幫助。

1. 開啟 Operations Manager 主控台，然後選取 [**管理**] 工作區。
2. 展開 Operations Management Suite，然後按一下 [連接]。
3. 在 [OMS 連接] 檢視中，按一下 [設定 Proxy 伺服器]。
4. 在 [Operations Management Suite 精靈: Proxy 伺服器] 頁面上，選取 [使用 Proxy 伺服器來存取 Operations Management Suite]，然後輸入具有連接埠號碼的 URL (例如，http://corpproxy:80)，然後按一下 [完成]。

如果需要驗證您的 Proxy 伺服器，請執行下列步驟來設定認證和設定，而設定需要傳播到將向管理群組中 OMS 回報的受管理電腦。

1. 開啟 Operations Manager 主控台，然後選取 [**管理**] 工作區。
2. 在 [**RunAs 組態**] 底下，選取 [**設定檔**]。
3. 開啟 [System Center Advisor 執行身分設定檔 Proxy] 設定檔。
4. 在 [執行身分設定檔精靈] 中，按一下 [加入] 使用執行身分帳戶。您可以建立新的[執行身分帳戶](https://technet.microsoft.com/library/hh321655.aspx)，或使用現有的帳戶。此帳戶必須有足夠的權限，才能通過 Proxy 伺服器。
5. 若要設定要管理的帳戶，請選擇 [選取的類別、群組或物件]，並按一下 [選取]，然後按一下 [群組] 來開啟 [群組搜尋] 方塊。
6. 搜尋，然後選取 [Microsoft System Center Advisor 監控伺服器群組]。選取群組之後，按一下 [確定] 關閉 [群組搜尋] 方塊。
7.	按一下 [確定] 關閉 [加入執行身分帳戶] 方塊。
8.	按一下 [儲存] 完成精靈並儲存變更。

建立連接而且您設定將收集資料並將資料回報給 OMS 的代理程式之後，會在管理群組中套用下列組態，但不一定會依照下列順序︰

- 建立執行身分帳戶 **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**。它是與執行身分設定檔 **Microsoft System Center Advisor Run As Profile Blob** 相關聯，而且將目標設為兩個類別：[收集伺服器] 和 [Operations Manager 管理群組]。
- 會建立兩個連接器。第一個命名為 **Microsoft.SystemCenter.Advisor.DataConnector**，而且會自動設定成訂用帳戶，這個訂用帳戶會將管理群組中所有類別執行個體產生的所有警示轉送給 OMS Log Analytics。第二個連接器是 **Advisor 連接器**，負責與 OMS Web 服務進行通訊以及共用資料。
- 管理群組中您已選擇要收集資料的代理程式和群組將會加入 [Microsoft System Center Advisor 監控伺服器群組]。

## 管理組件更新
組態完成之後，Operations Manager 管理群組就會建立與 OMS 服務的連接。管理伺服器將會與 Web 服務同步處理，並以下列形式接收更新的組態資訊：已啟用且與 Operations Manager 整合之解決方案的管理組件。Operations Manager 將檢查這些管理組件的更新，以在有可用更新時自動下載和匯入它們。有兩個規則特別可控制這個行為︰

- **Microsoft.SystemCenter.Advisor.MPUpdate** - 更新基底 OMS 管理組件。預設會每十二 (12) 個小時執行一次。
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - 更新工作區中所啟用的解決方案管理組件。預設會每五 (5) 分鐘執行一次。

您可以透過停用來覆寫這兩個規則以防止自動下載，或者修改管理伺服器與 OMS 同步處理之頻率的頻率，來決定新的管理組件是否可用而且應該予以下載。遵循[如何覆寫規則或監視器](https://technet.microsoft.com/library/hh212869.aspx)步驟，使用值 (秒) 修改 [頻率] 參數來變更同步處理排程，或修改 [已啟用] 參數來停用規則。將目標設為覆寫 [Operations Manager 管理群組] 類別的所有物件。

如果您想要繼續遵循現有的變更控制程序來控制生產管理群組中的管理組件發行版本，則可以停用規則，並在允許更新時於特定期間啟用它們。如果您的環境中有開發或 QA 管理群組，而且該管理群組連接到網際網路，則可以設定該管理群組與 OMS 工作區，以支援此案例。這可讓您先檢閱和評估 OMS 管理組件的反覆版本，再將其發行到生產管理群組。

## 驗證 Operations Manager 與 OMS 的整合
您有幾種不同的方式可以確認 OMS 與 Operations Manager 的整合成功。

### 從 OMS 入口網站確認整合

1.	在 OMS 主控台中，按一下 [設定] 圖格。
2.	在頂端功能表上，按一下 [連接的來源]。
3.	在 [System Center Operations Manager] 區段下方，您應該會看到 [已連接 1 個 MGMT 群組] 狀態，而在其下的表格中，是上次收到資料時列有代理程式數目和狀態的管理群組名稱。

### 從 Operations 主控台確認整合

1.	開啟 Operations Manager 主控台，然後選取 [**管理**] 工作區。
2.	按一下 [管理組件] 節點，並在 [尋找:] 文字方塊中輸入 **Advisor** 或 **Intelligence**。
3.	根據您已啟用的解決方案，您會看到搜尋結果中列出對應的管理組件。例如，如果您已啟用警示管理解決方案，則 [Microsoft System Center Advisor 警示管理] 管理組件將會在清單中。

## 後續步驟

- [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)，以加入功能和收集資料。
- 如果您的組織使用 Proxy 伺服器或防火牆，請[在 Log Analytics 中設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)，以讓代理程式可與 Log Analytics 服務通訊。

<!---HONumber=AcomDC_0504_2016-->