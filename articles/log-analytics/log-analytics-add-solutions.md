<properties
    pageTitle="從方案庫加入 Log Analytics 方案 | Microsoft Azure"
    description="Log Analytics 方案是邏輯、視覺效果和資料擷取規則的集合，可提供針對特定問題領域進行計量的樞紐分析。"
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
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>從方案庫加入 Log Analytics 方案

Log Analytics 方案是**邏輯**、**視覺效果**和**資料擷取規則**的集合，可提供針對特定問題領域進行計量的樞紐分析。 本文列出支援 Log Analytics 的方案，並告訴您如何使用方案庫加入和移除這些方案。

方案可允許更深入的探討，以：

- 協助調查並更快解決操作問題、
- 收集並相互關聯各種類型的機器資料、
- 協助您主動出擊，例如容量規劃、修補狀態報告和安全性稽核。


>[AZURE.NOTE] Log Analytics 包含記錄檔搜尋功能，因此您不需要安裝方案即可啟用。 不過，您可以從 [方案庫] 加入方案，以取得資料視覺效果、建議的搜尋和深入資訊。

加入方案後，系統會從基礎結構中的伺服器收集資料，然後再傳送到 OMS 服務。 OMS 服務的處理時間通常需要幾分鐘到一個小時。 待服務處理完資料後，您可以在 OMS 中予以檢視。

當您不再需要解決方案時，您可以輕易地將它移除。 移除方案後，系統不會將其資料傳送到 OMS，因此可減少每日配額 (如果有的話) 所使用的資料量。


## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>Microsoft Monitoring Agent 支援的解決方案

如果伺服器是透過 Microsoft Monitoring Agent 連接至 OMS，則可使用目前提供的大多數方案，包括：

- Active Directory 評估
- 警示管理 (不含 SCOM 警示)
- 反惡意程式碼
- 變更追蹤
- 安全性
- SQL 評估
- 系統更新

不過，Microsoft Monitoring Agent 並「不支援」  下列方案，且需要 System Center Operations Manager (SCOM) 代理程式。

- 警示管理 (包括 SCOM 警示)
- 產能管理
- 組態評估

請參閱 [將 Operations Manager 連接至 Log Analytics](log-analytics-om-agents.md) ，以取得將 SCOM 代理程式連接到 Log Analytics 的資訊。

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>使用 [解決方案資源庫] 新增解決方案

1. 在 OMS 的 [概觀] 頁面中，按一下 [方案庫]  圖格。    
    ![方案庫](./media/log-analytics-add-solutions/sol-gallery.png)
2. 在 [OMS 方案庫] 頁面中，您可以了解每個可用的方案。 按一下要加入 OMS 之方案的名稱。
3. 在所選解決方案的頁面中，該解決方案的詳細資料會顯示於此。 按一下 [新增] 。
4. 加入方案之後，新建立的圖格會出現在 OMS 的 [概觀] 頁面中；待 OMS 服務處理資料後，您便可以開始使用該方案。

## <a name="to-configure-solutions"></a>若要設定方案
1. 有些方案需要進行設定。 例如，您必須先設定自動化、Azure Site Recovery 以及備份，才能使用它們。
2. 若是這類方案，請按一下 [概觀] 頁面上該方案的圖格。  
    ![設定方案](./media/log-analytics-add-solutions/configure-additional.png)
3. 然後，使用必要資訊設定方案，再按一下 [儲存] 。  
    ![設定方案](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>使用 [解決方案資源庫] 移除解決方案

1. 在 OMS 的 [概觀] 頁面中，按一下 [設定]  圖格。
2. 在 [設定] 頁面中，於要移除之方案的 [方案] 索引標籤下方按一下 [移除]  。
3. 在確認對話方塊中，按一下 [是]  以移除方案。

## <a name="data-collection-details-for-oms-features-and-solutions"></a>OMS 功能和方案的資料收集詳細資料

下表顯示 OMS 功能和方案的資料收集方法和其他資料收集方式的詳細資料。 直接代理程式和 SCOM 代理程式基本上相同，不過，直接代理程式還包含其他功能，可讓它連接至 OMS 工作區，並透過 Proxy 路由傳送。 如果您使用 SCOM 代理程式，則必須作為與 OMS 通訊的目標 OMS 代理程式。 此資料表中的 SCOM 代理程式是連線到 SCOM 的 OMS 代理程式。 如需將現有 SCOM 環境連接到 OMS 的相關資訊，請參閱[將 Operations Manager 連接到 Log Analytics](log-analytics-om-agents.md)。

>[AZURE.NOTE] 您使用的代理程式類型決定資料如何傳送至 OMS，情況如下︰

- 您使用直接代理程式或已連接 SCOM 的 OMS 代理程式。
- 需要 SCOM 時，一律會使用 SCOM 管理群組，將解決方案的 SCOM 代理程式資料傳送至 OMS。 此外，需要 SCOM 時，解決方案只會使用 SCOM 代理程式。
- 當不需要 SCOM 且資料表顯示使用管理群組將 SCOM 代理程式資料傳送至 OMS 時，一律會使用管理群組將 SCOM 代理程式資料傳送至 OMS。 直接代理程式會略過管理群組，直接將其資料傳送至 OMS。
- 當不使用管理群組來傳送 SCOM 代理程式資料時，資料會直接傳送至 OMS — 略過管理群組。


|資料類型| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|---|
|AD 評估|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|  7 天|
|AD 複寫狀態|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 天|
|警示 (Nagios)|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|與抵達同時|
|警示 (Zabbix)|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 分鐘|
|警示 (Operations Manager)|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 分鐘|
|反惡意程式碼|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 每小時|
|產能管理|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 每小時|
|變更追蹤|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 每小時|
|變更追蹤|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|每小時|
|組態評估 (舊版建議程式)|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 一天兩次|
|ETW|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分鐘|
|IIS 記錄檔|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分鐘|
|金鑰保存庫|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分鐘|
|網路應用程式閘道|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分鐘|
|網路安全性群組|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分鐘|
|Office 365|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|與通知同時|
|效能計數器|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|依排程，最少 10 秒|
|效能計數器|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|依排程，最少 10 秒|
|Service Fabric|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分鐘|
|SQL 評估|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 7 天|
|Surface Hub|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|與抵達同時|
|Syslog|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|從 Azure 儲存體 ：10 分鐘；從代理程式：與抵達同時|
|系統更新|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 安裝更新之後 15 分鐘，至少一天 2 次|
|Windows 安全性事件記錄檔|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)| 若為 Azure 儲存體：10 分鐘；代理程式：與抵達同時|
|Windows 防火牆記錄檔|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)| 與抵達同時|
|Windows 事件記錄檔|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 若為 Azure 儲存體：1 分鐘；代理程式：與抵達同時|
|連線資料|Windows (2012 R2/8.1 或更新版本)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)| 每隔 1 分鐘|

## <a name="log-analytics-preview-solutions-and-features"></a>Log Analytics 預覽方案和功能

我們執行服務並遵循 devops 做法，能夠與客戶合作開發功能和方案。

在私人預覽期間，我們讓一小群客戶存取功能或方案的初期實作，以收集意見反應來進行改善。 此初期實作只有最少的功能和運作能力。

我們的目標是快速試驗，以了解何者可行或不可行。 我們反覆進行此程序，直到私人預覽客戶的意見反應可讓我們決定推出公開預覽為止。

在公開預覽期間，我們將功能或方案開放給所有使用者，以收集更多意見反應來驗證我們的調整彈性和效率。 在這個階段中：

- 預覽功能會出現在 [設定] 索引標籤中，可以由任何使用者啟用
- 使用者可以透過資源庫或利用已發行的指令碼，加入預覽方案

### <a name="what-should-i-know-about-preview-features-and-solutions?"></a>我對預覽功能和方案應該有何了解？

新的功能和方案讓我們感到很興奮，期待與您一起開發。

預覽功能和方案並不適合每個人，因此，在請求加入私人預覽或啟用公開預覽之前，請確定您使用開發中的功能是沒有問題的。

透過入口網站啟用預覽功能時，您將會看到警告，提醒您此功能還在預覽階段。

#### <a name="for-both-*private*-and-*public*-preview"></a>私人和公開預覽

公開和私人預覽都有下列情況︰

- 不見得永遠正常運作。
  - 從造成小麻煩到完全無法運作都有可能
- 預覽版可能對您的系統/環境造成負面影響
  - 我們努力不讓負面狀況發生在您使用 OMS 的系統上，但有時難免發生非預期的情形
- 可能發生資料遺失/損毀
- 我們可能要求您收集診斷記錄或其他資料，以協助進行問題疑難排解
- 可能會移除功能或方案 (暫時或永久)
  - 我們根據在預覽期間所得的經驗，可能決定不推出功能或方案
- 預覽可能無法運作，或尚未經過所有組態的測試，我們可能會限制︰
  - 可用的作業系統 (例如，預覽階段的某項功能可能只適用於 Linux)
  - 可用的代理程式類型 (MMA、SCOM) (例如，預覽階段的某項功能可能只適用於 SCOM)  
- 服務等級協定未涵蓋預覽方案和功能
- 使用預覽功能需要支付使用費用
- 讓功能或方案發揮效果的功能可能遺失或不完整
- 並非所有區域都可使用功能/方案
- 功能/方案可能未完成當地語系化
- 可使用功能/方案的客戶數或裝置數可能有所限制
- 您可能需要使用指令碼來執行組態和啟用方案/功能
- 使用者介面 (UI) 不完整，每天都可能變更
- 公開預覽可能不是適用於生產/重要系統

#### <a name="for-*private*-preview"></a>私人  預覽

除了上述幾項，私人預覽還有下列特性︰

- 希望您從體驗中提供意見反應，協助我們改善功能/方案
- 我們可能利用意見調查、電話或電子郵件，請您提供意見反應
- 情況不見得一帆風順
- 我們可能在您加入之前要求簽署保密合約 (NDA)，也可能含有機密內容
  - 在發表於部落格、推特或與協力廠商接觸之前，請洽詢負責預覽版的方案經理，以了解任何保密規定
- 請勿在生產/重要系統上執行


### <a name="how-do-i-get-access-to-private-preview-features-and-solutions?"></a>如何取得私人預覽功能和方案？

根據預覽而定，我們會透過幾種不同的方式邀請客戶體驗私人預覽。

- 請回答每月的客戶問卷調查，並授權我們與您保持連繫，就會更有機會受邀體驗私人預覽。
- 您的 Microsoft 帳戶小組可以提名您。
- 您可以根據 twitter [msopsmgmt](https://twitter.com/msopsmgmt)
- 您可以根據社群活動分享的詳細資料來註冊 – 請在會面場合、會議和線上社群與我們接觸。


## <a name="next-steps"></a>後續步驟

- [搜尋記錄](log-analytics-log-searches.md) 以檢視方案所蒐集的詳細資訊。



<!--HONumber=Oct16_HO2-->


