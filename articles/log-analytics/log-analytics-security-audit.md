<properties
	pageTitle="Log Analytics 中的安全性和稽核方案 | Microsoft Azure"
	description="Log Analytics 中的安全性和稽核方案針對值得您注意的問題，使用內建的搜尋查詢，為您組織的 IT 安全性狀況提供全面性的檢視。"
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
	ms.date="05/03/2016"
	ms.author="banders"/>

# Log Analytics 中的安全性和稽核方案

Log Analytics 中的安全性和稽核方案針對值得您注意的問題，使用內建的搜尋查詢，為您組織的 IT 安全性狀況提供全面性的檢視。

## 安裝和設定方案
請使用下列資訊來安裝和設定方案。

- 使用[從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)所述的程序，將安全性和稽核方案加入您的 OMS 工作區。不需要進一步的組態。



## 「安全性和稽核」資料收集詳細資訊

下表顯示安全性和稽核方案的資料收集方法及如何收集資料的其他詳細資料。

|資料類型| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|---|
|Windows 安全性事件記錄檔|Windows|![是](./media/log-analytics-security-audit/oms-bullet-green.png)|![是](./media/log-analytics-security-audit/oms-bullet-green.png)|![是](./media/log-analytics-security-audit/oms-bullet-green.png)| ![否](./media/log-analytics-security-audit/oms-bullet-red.png)|![否](./media/log-analytics-security-audit/oms-bullet-red.png)| 若為 Azure 儲存體：10 分鐘；代理程式：與抵達同時|
|Windows 防火牆記錄檔|Windows|![是](./media/log-analytics-security-audit/oms-bullet-green.png)|![是](./media/log-analytics-security-audit/oms-bullet-green.png)|![否](./media/log-analytics-security-audit/oms-bullet-red.png)| ![否](./media/log-analytics-security-audit/oms-bullet-red.png)|![否](./media/log-analytics-security-audit/oms-bullet-red.png)| 與抵達同時|
|Windows 事件記錄檔|Windows|![是](./media/log-analytics-security-audit/oms-bullet-green.png)|![是](./media/log-analytics-security-audit/oms-bullet-green.png)|![是](./media/log-analytics-security-audit/oms-bullet-green.png)| ![否](./media/log-analytics-security-audit/oms-bullet-red.png)|![是](./media/log-analytics-security-audit/oms-bullet-green.png)| 若為 Azure 儲存體：1 分鐘；代理程式：與抵達同時|


## 取證分析的最佳作法

**尋找證據**

當您使用「安全性和稽核」解決方案執行取證分析時，您就是在尋找潛在惡意使用者所留下的證據。無論使用者在其 IT 環境中執行什麼動作，他們所參與的許多活動都會產生安全性構件。您必須注意，日誌經常會被攻擊者清除，而且這通常是為了隱藏其蹤跡所採取的第一步。

不過，無論使用者是存取自己的電腦或存取遠端的電腦，都會在事件日誌中儲存使用證據。OMS 會在構件「發生時立即」收集這些構件，防止被任何人竄改，因此允許您將多台電腦之間的資料進行關聯，執行不同類型的分析。

**了解如何設定和收集稽核事件**

若要充分利用「安全性和稽核」解決方案，您必須使用下列 Web 資源，設定由 Windows 環境所收集、最符合您需求的事件層級。

- [如何設定安全性原則設定](https://technet.microsoft.com/library/dn135243&#40;v=ws.10&#41;.aspx)
- [進階稽核原則組態](https://technet.microsoft.com/library/jj852202&#40;v=ws.10&#41;.aspx)
- [稽核原則建議](https://technet.microsoft.com/library/dn487457.aspx)

**啟用 AppLocker**

您也應該啟用 AppLocker 事件，取得在 IT 環境中所發生之處理序可執行檔的相關豐富資訊。如需詳細資訊，請參閱[設定僅稽核的 AppLocker 原則](https://technet.microsoft.com/library/hh994622.aspx)。

**設定 Windows 事件的稽核層級**

Windows 運算環境可讓您設定安全性相關記錄的擷取層級。例如，您可以設定環境，任何時候只要有人存取檔案、讀取檔案或開啟檔案時，就會產生事件。您想要收集的詳細資料層級根據您的需求而定。不過，您啟用的每個選項都會需要某種程度的成本，因為您必須儲存您收集的所有資訊。因此，在許多組織的 IT 環境中，人們會決定不要啟用物件讀取或物件寫入的資料收集。每次一有人存取任何檔案，就會產生成千上萬的事件，其中一些只是無用的訊息。您決定的收集層級取決於您的最佳判斷。

>[AZURE.NOTE] 如果您使用直接代理程式，而且組織中有 Proxy 伺服器，您就應該設定 Proxy 伺服器允許代理程式存取 OMS。如需詳細資訊，請參閱[在 Log Analytics 中設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)。

## 安全性缺口模式調查的最佳作法

**調查不正常的活動模式**

安全性缺口通常從合法憑證開始，然後惡意使用者嘗試透過攻擊來取得提升的權限。「安全性和稽核」解決方案不會著重在入侵偵測，而是透過「值得注意的問題」確實協助您調查和探索不正常活動的模式。例如，您應該調查下列不正常的活動，以及在 [值得注意的問題] 下方出現的任何其他活動。

- 某位使用者在不常使用的電腦上有不尋常的登入
- 來自異常使用者或電腦有不尋常的網路列舉
- 使用管理權限建立新的使用者帳戶
- 日誌記錄或安全性原則有變更
- 可疑的可執行檔

## 稽核案例的最佳作法

您的組織可能會有一些必須遵守的電腦和網路法規原則和規章，需要大量稽核記錄。例如，如果您的組織是一家財務公司，您可能必須保留在任何特定時間點，能證明使用者在網路上執行特定操作的記錄。您可能也必須在需要時以及回溯幾個月前 (有時甚至多年前)，產生特定使用者或選定伺服器之詳細活動的報表。

此時，無論您的電腦是內部部署或位於雲端，您都可以使用「安全性和稽核」解決方案來收集您整個 IT 環境的稽核資料。所有稽核資料都會根據每個訂用帳戶加以儲存、編製索引，以及保留。如果對 OMS 使用「進階」訂用帳戶，無限數量的資料都能儲存一年。然後您可以檢視稽核資料、執行搜尋，以及在不同的資料類型和電腦之間進行關聯，取得自收集資料以來任何時間區間的綜合性結果。

**使用群組原則收集稽核資料**

您想要收集並傳送至 OMS 的任何稽核資料，都會使用群組原則完整進行管理。使用群組原則將安全性組態定義為「群組原則物件」(GPO) 的一部分，這些組態會連結到 Active Directory 容器 (例如站台、網域，以及組織單位)，而這些容器會協助您管理安全性設定。原則資料會進行記錄，然後傳送至 OMS 服務。

**使用 AppLocker 收集稽核資料**

除了本機原則設定之外，如果您使用 AppLocker 收集稽核資料，OMS 將會收集這些資料，然後您就可以加以檢視。

## 對可疑的可執行檔執行簡單的調查

1. 登入 OMS。
2. 在 [概觀] 頁面上，複查 [安全性和稽核] 磚中所顯示的資訊，然後按一下資訊。![[概觀] 頁面的影像](./media/log-analytics-security-audit/oms-security-audit-dash02.png)
3. 在 [安全性和稽核] 頁面上，複查 [值得注意的問題] 刀鋒視窗中的資訊。在範例影像中，您會看到今天有 6 個，而昨天有 2 個值得注意的問題。在此範例中，有 1 個可疑的可執行檔。在 [值得注意的問題] 刀鋒視窗中按一下 [可疑的可執行檔]。![[安全性和稽核] 頁面的影像](./media/log-analytics-security-audit/oms-security-audit-dash03.png)
4. 搜尋會針對您按一下的可疑可執行檔顯示查詢和結果。在範例中，有 1 個結果，並顯示其檔案雜湊。按一下 **FILEHASH** 識別碼。![搜尋結果 filehash 的影像](./media/log-analytics-security-audit/oms-security-audit-search01.png)
5. 搜尋會顯示有關可執行檔的額外資訊，包括其檔案路徑和處理序名稱。按一下**處理序 &lt;檔案名稱&gt;**。在範例中，這是 HEXEDIT.EXE。![搜尋結果處理序的影像](./media/log-analytics-security-audit/oms-security-audit-search02.png)
6. 搜尋會將加上引號的處理序名稱附加到查詢中。在此範例中為 "**HEXEDIT.EXE"**。![搜尋查詢的影像](./media/log-analytics-security-audit/oms-security-audit-search03.png)
7. 在 [搜尋] 查詢方塊中，刪除處理序名稱和引號以外的所有內容，然後按一下搜尋圖示。![詳細搜尋資訊的影像](./media/log-analytics-security-audit/oms-security-audit-search04.png)
8. 搜尋會顯示有關處理序的詳細資訊，包括處理序執行所在的電腦、執行處理序的使用者帳戶，以及針對處理序所建立之事件的日期和時間。
9. 利用您找到的資訊，可以視需要採取修正的動作。例如，如果您判斷某個可執行檔是惡意程式碼，您可能會想要從所有它影響的電腦系統中採取動作將它移除。移除該可執行檔且 OMS 收到電腦系統的更新日誌和稽核事件之後，[值得注意的問題] 刀鋒視窗中的值就會在隔天變更。

## 調查惡意 IP 位址

保護伺服器的一種方法是確保伺服器不與任何已知惡意或可疑 IP 位址進行通訊。OMS 中有一個集中式檢視，顯示受管理伺服器和用戶端可能通訊的所有已知惡意 IP。配合 Microsoft 威脅情報中心 (MSTIC)，OMS 每小時會取得最新已知惡意 IP 的更新資料，並於可能危及您的任何伺服器時通知您。MSTIC 小組與各個威脅情報協力電腦合作，收集並提供此彙總清單給 OMS 服務。

在 OMS 入口網站的安全性和稽核方案內，可以找到惡意 IP 檢視，顯示為 [存取的明顯可疑 IP 位址]。

您可以開啟圖格，檢視您的裝置可能通訊的明顯可疑 IP 位址的完整清單。OMS 會掃描您傳送至服務的所有資料來源，包括︰

- Windows 防火牆記錄檔
- IIS 記錄檔
- WireData


OMS 會在記錄檔搜尋結果中顯示可疑 IP 位址的許多相關欄位。最重要的一些欄位包括：

- **IndicatorThreatType**︰例如殭屍網路、Proxy、暗網、惡意程式碼命令和控制節點。
- **Description**︰威脅類型和我們認定是惡意 IP 位址的信賴等級。這與 Microsoft 用以自我保護的資料相同，開放給 OMS 使用者。

如需可疑 IP 位址欄位的完整清單，請參閱以下的[惡意 IP 記錄檔搜尋欄位](#Malicious-IP-log-search-fields)。

### 執行惡意 IP 位址調查

在此範例中，我們將使用 Windows 防火牆記錄檔資料，查看可能的惡意 IP 位址。

1. 登入 OMS。
2. 在 [概觀] 頁面上，複查 [安全性和稽核] 磚中所顯示的資訊，然後按一下資訊。![[概觀] 頁面的影像](./media/log-analytics-security-audit/oms-security-audit-dash02.png)  
3. 在 [安全性和稽核] 頁面上，檢閱 [存取的明顯可疑 IP 位址] 圖格中的摘要資訊。![Security and Audit page showing suspicious IP](./media/log-analytics-security-audit/oms-maliciousip-01.png)  
4. 在此範例中，有 6 個可疑 IP 位址。按一下 [存取的明顯可疑 IP 位址] 圖格。
5. 搜尋會顯示查詢和找到的可疑 IP 位址結果。在範例中，有 6 的結果，而且顯示 IP 位址。![search results showing suspicious IP addresses](./media/log-analytics-security-audit/oms-maliciousip-02-revised.png)  
6. 在上圖中，請注意最後一個 IP 位址。在此範例中為 **94.102.56.130**。按一下該位址。
7. 搜尋會顯示詳細的結果，其中顯示各種威脅指標資料。按一下 [顯示更多] 可查看完整結果。![search results showing detailed threat indicator data](./media/log-analytics-security-audit/oms-maliciousip-03-revised.png)  
8. 如果想要檢視環境中可能與惡意 IP 通訊的所有伺服器的清單，您可以使用下列記錄檔搜尋查詢。

    ```
    IsActive=True | measure count() by Computer
    ```

### 對惡意 IP 通訊採取更正動作

如果您確定網路中有元件或程序與已知惡意 IP 位址通訊來傳送您的資料，則您應該採取的動作就很明確︰

- 確認您的資源將資料傳送到惡意 IP 位址。
- 可能的話，從電腦中移除或封鎖傳送資料的軟體，並防止與特定軟體的特定 IP 位址通訊。
- 調查用來執行可疑處理序的任何任何帳戶，以判斷用來執行可執行檔或處理序的使用者認證是否可能受到危害。判斷可能已存取其他哪些資源。
- 判斷此軟體如何安裝在電腦上。
- 暫時讓可疑軟體維持已安裝，以密切監視它的活動。完整評估之後，必要的話，採取更正動作。


不過，如果「不確定」資料是否傳送至已知惡意 IP 位址，或如果「信賴等級仍為可疑」，則更正動作就較不明確。一般而言，您可能使用上列某些步驟來調查。在其他情況下，您也可能完全不想採取任何動作。畢竟，您是組織中最熟悉 IT 基礎結構的人，最有立場決定如何處理潛在的威脅。


防火牆記錄檔會顯示資料移動的方向。當防火牆設定為封鎖對特定 IP 的連出 (在記錄檔中顯示為 send) 通訊時，在記錄檔搜尋結果中，可能受危害軟體對可疑 IP 嘗試的通訊會顯示為 [blocked]。在此情況下，您應該採取其他動作 - 雖然可以封鎖受危害軟體存取特定 IP 位址，但此軟體未來仍可能嘗試與其他未知和潛在的惡意 IP 位址通訊。

防火牆記錄檔也會顯示傳入資料遭到封鎖，您還可能看到這種情況下有相當多的活動。您應該擔心這種情況嗎？ 通常不必擔心。當防火牆記錄檔顯示已封鎖傳入資料時，即可確定防火牆已善盡職責 - 保護您的基礎結構。


### 惡意 IP 威脅情報

Microsoft 與各種組織分享資料，建立可能危及資料的所有已知指標的摘要檢視。摘要的用意是識別資料集裡的命中點，並以連結顯示更多的威脅詳細資料，以輕鬆檢視進一步的詳細資料。您可以在惡意 IP 的記錄檔搜尋結果中檢視此威脅情報。

Microsoft 以 3 種方式接收威脅資料，彙總形成威脅情報資料集。這些提供者基本上分為下列類別︰

- 付費訂閱 - Microsoft 已簽約購買資料的公司。
- 協力廠商資料 - 安全性情報公司。您可以將此資料視為「通訊情報」，因為不含冗長的報告，但資料仍然有效。
- Microsoft 內部程序 – 主要是 Microsoft 威脅情報中心 (MSTIC)。

#### 惡意 IP 記錄檔搜尋欄位

記錄檔搜尋結果中會顯示下列惡意 IP 位址欄位。

|搜尋欄位| 說明 |
|---|---|
| **IsActive**| 指定指標在使用中，如果符合找到的 IP 位址，則應該視為有效命中。MSTIC 維護此指標。|
|**Confidence**| 顯示 1-100 的值。MSTIC 維護這個值及其定義演算法。這是用來篩選搜尋結果的理想值。|
|**TLPLevel**| 號誌燈通訊協定層級。值︰綠色、琥珀色和紅色。這些值分別對應到低業務衝擊、中業務衝擊，以及高業務衝擊。|
| **IndicatorThreatType** | 指標代表的威脅類型的簡短描述。其中一些包括︰殭屍網路、DDoS、惡意程式碼、惡意 URL、惡意 IP、網路釣魚和垃圾郵件。|
| **嚴重性** | 顯示 0-5 的值。這指出威脅的嚴重性。|
| **MaliciousIP** | 惡意主機的 IP 位址。|
| **CommunicationDirection** | 顯示 IP 流量到惡意 IP 位址的方向。|

## 後續步驟

- [搜尋記錄](log-analytics-log-searches.md)以檢視詳細的安全性和稽核資料。

<!---HONumber=AcomDC_0518_2016-->