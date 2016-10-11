<properties
    pageTitle="在離峰期間啟動/停止 VM [預覽] 方案 | Microsoft Azure"
    description="VM 管理方案會依照排程啟動和停止 Azure Resource Manager 虛擬機器，並從 Log Analytics 主動監視。"
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
	/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/04/2016"
    ms.author="magoedte"/>

# 自動化中的在離峰期間啟動/停止 VM [預覽] 方案

在離峰期間啟動/停止 VM (預覽) 方案會依照使用者定義的排程啟動和停止 Azure Resource Manager 虛擬機器，並透過 OMS Log Analytics 提供成功啟動和停止虛擬機器的自動化作業深入解析。

## 必要條件

- Runbook 會使用 [Azure 執行身分帳戶](automation-sec-configure-azure-runas-account.md)。執行身分帳戶是慣用的驗證方法，因為它會使用憑證驗證，而不是過期或經常變更的密碼。

- 這個方案只能管理與自動化帳戶位於相同訂用帳戶和資源群組中的 VM。

- 這個方案只會部署到下列 Azure 區域 - 澳大利亞東南部、美國東部、東南亞和西歐。管理 VM 排程的 Runbook 可以任何區域中的 VM 為目標。

- 若要在啟動和停止 VM Runbook 完成時傳送電子郵件通知，則需要有 Office 365 企業級訂用帳戶。

## 方案元件

這個方案是由下列將匯入和新增至自動化帳戶的資源所組成。

### Runbook

Runbook | 說明|
--------|------------|
CleanSolution-MS-Mgmt-VM | 當您刪除訂用帳戶中的方案時，此 Runbook 將會移除所有內含的資源和排程。|  
SendMailO365-MS-Mgmt | 此 Runbook 會透過 Office 365 Exchange 傳送電子郵件。|
StartByResourceGroup-MS-Mgmt-VM | 此 Runbook 旨在啟動位於指定 Azure 資源群組清單中的 VM (傳統與 ARM 型 VM)。
StopByResourceGroup-MS-Mgmt-VM | 此 Runbook 旨在停止位於指定 Azure 資源群組清單中的 VM (傳統與 ARM 型 VM)。|
<br>

### 變數

變數 | 說明|
---------|------------|
**SendMailO365-MS-Mgmt** Runbook ||
SendMailO365-IsSendEmail-MS-Mgmt | 指定 StartByResourceGroup-MS-Mgmt-VM 和 StopByResourceGroup-MS-Mgmt-VM Runbook 是否可以在完成時傳送電子郵件通知。選取 [True] 可啟用電子郵件警示，而選取 [False] 則為停用。預設值為 [False]。| 
**StartByResourceGroup-MS-Mgmt-VM** Runbook ||
StartByResourceGroup-ExcludeList-MS-Mgmt-VM | 輸入要從管理作業中排除的 VM 名稱；使用分號 (;) 分隔名稱。值會區分大小寫並支援萬用字元 (星號)。|
StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | 可以附加至電子郵件訊息本文開頭的文字。|
StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | 指定包含電子郵件 Runbook 的自動化帳戶名稱。**請勿修改此變數。**|
StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt | 指定電子郵件 Runbook 的名稱。此變數由 StartByResourceGroup-MS-Mgmt-VM 和 StopByResourceGroup-MS-Mgmt-VM Runbook 用來傳送電子郵件。**請勿修改此變數。**|
StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | 指定包含電子郵件 Runbook 的資源群組名稱。**請勿修改此變數。**|
StartByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | 指定電子郵件的主旨列文字。|  
StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | 指定電子郵件的收件者。使用分號 (;) 輸入不同的名稱。|
StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | 輸入要從管理作業中排除的 VM 名稱；使用分號 (;) 分隔名稱。值會區分大小寫並支援萬用字元 (星號)。預設值 (星號) 將包含訂用帳戶中的所有資源群組。|
StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | 指定包含此方案所要管理之 VM 的訂用帳戶。這必須是此方案的自動化帳戶所在的相同訂用帳戶。|
**StopByResourceGroup-MS-Mgmt-VM** Runbook ||
StopByResourceGroup-ExcludeList-MS-Mgmt-VM | 輸入要從管理作業中排除的 VM 名稱；使用分號 (;) 分隔名稱。值會區分大小寫並支援萬用字元 (星號)。|
StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | 可以附加至電子郵件訊息本文開頭的文字。|
StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | 指定包含電子郵件 Runbook 的自動化帳戶名稱。**請勿修改此變數。**|
StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | 指定包含電子郵件 Runbook 的資源群組名稱。**請勿修改此變數。**|
StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | 指定電子郵件的主旨列文字。|  
StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | 指定電子郵件的收件者。使用分號 (;) 輸入不同的名稱。|
StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | 輸入要從管理作業中排除的 VM 名稱；使用分號 (;) 分隔名稱。值會區分大小寫並支援萬用字元 (星號)。預設值 (星號) 將包含訂用帳戶中的所有資源群組。|
StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | 指定包含此方案所要管理之 VM 的訂用帳戶。這必須是此方案的自動化帳戶所在的相同訂用帳戶。|  
<br>

### 排程

排程 | 說明|
---------|------------|
StartByResourceGroup-Schedule-MS-Mgmt | StartByResourceGroup Runbook 的排程。|
StopByResourceGroup-Schedule-MS-Mgmt | StopByResourceGroup Runbook 的排程。|

### 認證

認證 | 說明|
-----------|------------|
O365Credential | 指定有效的 Office 365 使用者帳戶以傳送電子郵件。只有在 SendMailO365-IsSendEmail-MS-Mgmt 變數設定為 [True] 時才需要。

## 組態

執行下列步驟，將在離峰期間啟動/停止 VM [預覽] 方案新增至您的自動化帳戶，然後設定變數以自訂方案。

1. 從 Azure 入口網站的主畫面中選取 [Marketplace] 圖格。如果圖格已不再釘選到您的主畫面，請從左導覽窗格中選取 [新增]。
2. 在 [Marketplace] 刀鋒視窗的搜尋方塊中輸入 [啟動 VM]，然後從搜尋結果中選取 [在離峰期間啟動/停止 VM [預覽]] 方案。
3. 在所選方案的 [在離峰期間啟動/停止 VM [預覽]] 刀鋒視窗中，檢閱摘要資訊，然後按一下 [建立]。
4. [新增方案] 刀鋒視窗隨即出現，系統會提示您先設定方案，才可以將它匯入自動化訂用帳戶。<br><br> ![VM 管理的新增方案刀鋒視窗](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5.  在 [新增方案] 刀鋒視窗中，選取 [工作區] 並在此選取連結到自動化帳戶所在相同 Azure 訂用帳戶的 OMS 工作區，或建立新的 OMS 工作區。如果您沒有 OMS 工作區，您可以選取 [建立新工作區]，然後在 [OMS 工作區] 刀鋒視窗上執行下列動作︰
   - 指定新 [OMS 工作區] 的名稱。
   - 如果選取的預設值不合適，請從下拉式清單中選取要連結的 [訂用帳戶]。
   - 對於 [資源群組]，您可以建立新的資源群組，或選取現有的資源群組。
   - 選取 [位置]。目前可供選取的位置只有 [澳大利亞東南部]、[美國東部]、[東南亞] 和 [西歐]。
   - 選取 [定價層]。此方案以兩種定價層提供︰免費和 OMS 付費層。免費層會限制每日可收集的資料量、保留期和 Runbook 作業執行階段分鐘數。OMS 付費層不會限制每日可收集的資料量。

        > [AZURE.NOTE]
        > While the Stadalone paid tier is displayed as an option, it is not applicable.  If you select it and proceed with the creation of this solution in your subscription, it will fail.  This will be addressed when this solution is officially released.<br>If you use this solution, it will only use automation job minutes and log ingestion.  The solution does not add additional OMS nodes to your environment.  

6. 在 [OMS 工作區] 刀鋒視窗上提供必要資訊之後，按一下 [建立]。在驗證資訊及建立工作區時，您可以在功能表的 [通知] 底下追蹤其進度。您會回到 [新增方案] 刀鋒視窗。
7. 在 [新增方案] 刀鋒視窗中，選取 [自動化帳戶]。如果您要建立新的 OMS 工作區，您也必須建立將與先前指定的新 OMS 工作區產生關聯的新自動化帳戶，包括您的 Azure 訂用帳戶、資源群組和區域。您可以選取 [建立自動化帳戶]，然後在 [新增自動化帳戶] 刀鋒視窗上提供下列資訊︰
  - 在 [名稱] 欄位中輸入自動化帳戶的名稱。

    系統會根據所選的 OMS 工作區自動填入所有其他選項，您無法修改這些選項。Azure 執行身分帳戶是此方案內含 Runbook 的預設驗證方法。一旦您按一下 [確定]，就會驗證組態選項並建立自動化帳戶。您可以在功能表的 [通知] 底下追蹤其進度。

    不然，您可以選取現有的自動化執行身分帳戶。請注意，您選取的帳戶不能已經連結到另一個 OMS 工作區，否則刀鋒視窗中會顯示一則訊息來通知您。如果帳戶已經連結，您必須選取不同的自動化執行身分帳戶或建立一個新帳戶。<br><br> ![自動化帳戶已經連結至 OMS 工作區](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 最後在 [新增方案] 刀鋒視窗上選取 [組態]，[參數] 刀鋒視窗隨即出現。在 [參數] 刀鋒視窗上，系統會提示您︰
   - 指定 [目標資源群組名稱]，這個資源群組名稱內含將由此方案管理的 VM。您可以輸入多個名稱，然後使用分號加以分隔 (這些值需區分大小寫)。如果您想要以訂用帳戶的所有資源群組中的 VM 為目標，則可使用萬用字元。
   - 選取 [排程]，這是一個週期性日期和時間，可用於啟動及停止目標資源群組中的 VM。

10. 設定好方案所需的初始設定後，選取 [建立]。系統會驗證所有的設定，然後嘗試在您的訂用帳戶中部署此方案。此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。

## 收集頻率

自動化作業記錄檔和工作串流資料會每隔五分鐘擷取到 OMS 儲存機制中。

## 使用方案

當您在 OMS 工作區中新增 VM 管理方案時，[StartStopVM 檢視] 圖格會新增至 OMS 儀表板。此圖格會顯示方案中已啟動和順利完成的 Runbook 作業計數和圖形表示。<br><br> ![VM 管理 StartStopVM 檢視圖格](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)

在自動化帳戶中，您可以存取和管理方案，方法是選取 [方案] 圖格，然後從 [方案] 刀鋒視窗的清單中選取 [Start-Stop-VM[工作區]] 方案。<br><br> ![自動化方案清單](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)

選取此方案將會顯示 [Start-Stop-VM[工作區]] 方案刀鋒視窗，您可以在其中檢閱重要的詳細資料，例如 **StartStopVM** 圖格，就像在您的 OMS 工作區中，該圖格會顯示方案中已啟動和順利完成的 Runbook 作業計數和圖形表示。<br><br> ![自動化 VM 方案刀鋒視窗](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)

### 設定電子郵件通知

若要在啟動和停止 VM Runbook 完成時啟用電子郵件通知，您必須修改 **O365Credential** 認證以及至少下列的變數︰

 - SendMailO365-IsSendEmail-MS-Mgmt
 - StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
 - StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

若要設定 **O365Credential** 認證，請執行下列步驟︰

1. 從您的自動化帳戶，按一下視窗頂端的 [所有設定]。
2. 在 [設定] 刀鋒視窗的 [自動化資源] 區段之下，選取 [資產]。
3. 在 [資產] 刀鋒視窗上選取 [認證] 圖格，然後從 [認證] 刀鋒視窗選取 [O365Credential]。
4. 輸入有效的 Office 365 使用者名稱和密碼，然後按一下 [儲存] 以儲存變更。

若要設定先前醒目提示的變數，請執行下列步驟︰

1. 從您的自動化帳戶，按一下視窗頂端的 [所有設定]。
2. 在 [設定] 刀鋒視窗的 [自動化資源] 區段之下，選取 [資產]。
3. 在 [資產] 刀鋒視窗上選取 [變數] 圖格，接著從 [變數] 刀鋒視窗中選取上面所列的變數，然後遵循先前在[變數](##variables)一節中為其指定的描述來修改其值。
4. 按一下 [儲存] 以儲存對變數所做的變更。

## Log Analytics 記錄

自動化會在 OMS 儲存機制中建立兩種類型的記錄。

### 作業記錄檔

屬性 | 說明|
----------|----------|
呼叫者 | 起始作業的人員。可能的值為電子郵件地址或排程作業的系統。|
類別 | 資料類型的分類。對自動化來說，該值是 JobLogs。|
CorrelationId | Runbook 作業之相互關聯識別碼的 GUID。|
JobId | Runbook 作業之識別碼的 GUID。|
operationName | 指定在 Azure 中執行的作業類型。對自動化來說，該值是 Job。|
resourceId | 指定 Azure 中的資源類型。對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。對自動化來說，此值是 Azure 自動化。|
ResourceType | 指定 Azure 中的資源類型。對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
resultType | Runbook 作業的狀態。可能的值為︰<br>- Started<br>- Stopped<br>- Suspended<br>- Failed<br>- Succeeded|
resultDescription | 說明 Runbook 作業的結果狀態。可能的值為︰<br>- Job is started<br>- Job Failed<br>- Job Completed|
RunbookName | 指定 Runbook 的名稱。|
SourceSystem | 指定所提交資料的來源系統。對自動化來說，此值會是 :OpsManager|
StreamType | 指定事件的類型。可能的值為︰<br>- 詳細資訊<br>- 輸出<br>- 錯誤<br>- 警告|
SubscriptionId | 指定作業的訂用帳戶 ID。
時間 | Runbook 作業的執行日期和時間。|


### 作業串流

屬性 | 說明|
----------|----------|
呼叫者 | 起始作業的人員。可能的值為電子郵件地址或排程作業的系統。|
類別 | 資料類型的分類。對自動化來說，該值是 JobStreams。|
JobId | Runbook 作業之識別碼的 GUID。|
operationName | 指定在 Azure 中執行的作業類型。對自動化來說，該值是 Job。|
ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
resourceId | 指定 Azure 中的資源識別碼。對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。對自動化來說，此值是 Azure 自動化。|
ResourceType | 指定 Azure 中的資源類型。對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
resultType | Runbook 作業在產生事件時的結果。可能的值為：<br>- InProgres|
resultDescription | 包含來自 Runbook 的輸出串流。|
RunbookName | Runbook 的名稱。|
SourceSystem | 指定所提交資料的來源系統。對自動化來說，此值會是 OpsManager|
StreamType | 作業串流的類型。可能的值為︰<br>-Progress<br>- Output<br>- Warning<br>- Error<br>- Debug<br>- Verbose|
時間 | Runbook 作業的執行日期和時間。|

當您執行的記錄檔搜尋傳回記錄 **JobLogs** 或 **JobStreams** 類別的目錄時，您可以選取 **JobLogs** 或 **JobStreams** 檢視，其中顯示一組彙總搜尋所傳回更新的圖格。

## 記錄檔搜尋範例

下表提供此方案所收集之作業記錄的記錄檔搜尋範例。

查詢 | 說明|
----------|----------|
尋找已順利完成的 Runbook StartVM 作業 | Category=JobLogs RunbookName\_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded | measure count() by JobId\_g|
尋找已順利完成的 Runbook StopVM 作業 | Category=JobLogs RunbookName\_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed | measure count() by JobId\_g
顯示 StartVM 和 StopVM Runbook 不同時間的作業狀態 | Category=JobLogs RunbookName\_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") | measure Count() by ResultType interval 1day|

## 後續步驟

- 若要深入了解如何建構不同的搜尋查詢，以及使用 Log Analytics 檢閱自動化工作記錄檔，請參閱 [Log Analytics 中的記錄檔搜尋](../log-analytics/log-analytics-log-searches.md)
- 若要深入了解 Runbook 執行方式、如何監視 Runbook 工作，以及其他技術性詳細資料，請參閱[追蹤 Runbook 工作](automation-runbook-execution.md)
- 若要深入了解 OMS Log Analytics 和資料收集來源，請參閱[在 Log Analytics 中收集 Azure 儲存體資料概觀](../log-analytics/log-analytics-azure-storage.md)






   

<!---HONumber=AcomDC_1005_2016-->