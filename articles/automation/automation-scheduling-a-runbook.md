---
title: 在 Azure 自動化中排程 Runbook | Microsoft Docs
description: 描述如何在 Azure 自動化中建立排程，以便以特定時間或循環排程來自動啟動 Runbook。
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/05/2016
ms.author: bwren

---
# 在 Azure 自動化中排程 Runbook
若要排程在指定時間啟動 Azure 自動化中的 Runbook，您會將它連結到一個或多個排程。在 Azure 傳統入口網站和 Azure 入口網站中，Runbook 的排程可以設定為執行一次，或以每小時或每日重複發生的排程來執行，另外，您也可以將 Runbook 排程為每週、每月、一週或一個月當中的特定幾天，或每月的特定一天來執行。Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。

## 建立排程
您可以在 Azure 入口網站、傳統入口網站或使用 Windows PowerShell 為 Runbook 建立新排程。當您使用 Azure 傳統入口網站或 Azure 入口網站將 Runbook 連結至排程時，也可以選擇建立新的排程。

> [!NOTE]
> 當您將排程與 Runbook 產生關聯時，自動化會在您的帳戶中儲存目前的模組版本，並將它們連結到該排程。這表示如果在建立排程時，您的帳戶中有 1.0 版的模組，而後將模組更新為 2.0 版，則此排程會繼續使用 1.0。若要使用更新後的模組版本，您必須建立新的排程。
> 
> 

### 在 Azure 傳統入口網站中建立新排程
1. 在 Azure 傳統入口網站中選取 [自動化]，然後選取自動化帳戶的名稱。
2. 選取 [**資產**] 索引標籤。
3. 在視窗的底端按一下 [**加入設定**]。
4. 按一下 [**加入排程**]。
5. 為新排程輸入 [名稱] 並選擇性地輸入 [描述]。您的排程將會以 [一次]、[每小時]、[每日]、[每週]，或 [每月] 的方式執行。
6. 視您選取的排程類型而定，指定 [**開始時間**] 和其他選項。

### 在 Azure 入口網站中建立新排程
1. 在 Azure 入口網站中，從您的自動化帳戶按一下 [資產] 磚，以開啟 [資產] 刀鋒視窗。
2. 按一下 [排程] 磚，以開啟 [排程] 刀鋒視窗。
3. 在刀鋒視窗的頂端按一下 [加入排程]。
4. 在 [新增排程] 刀鋒視窗中，為新排程輸入 [名稱] 並選擇性地輸入 [描述]。
5. 選取 [一次] 或 [週期]，以選取排程將會執行一次或以週期性的排程執行。如果您選取 [一次]，請指定 [開始時間]，然後按一下 [建立]。如果您選取 [週期]，則請指定 [開始時間] 和所需的 Runbook 重複頻率：依 [小時]、[天]、[週] 還是 [月] 執行。如果您在下拉式清單中選取 [週] 或 [月]，刀鋒視窗將會出現 [週期選項]，一經選取，就會顯示 [週期選項] 刀鋒視窗，如果您選取了 [週]，將可以進一步選取星期幾。如果您選取了 [月]，則可以在行事曆上選擇要依 [工作日] 或當月的特定幾天，最後則是您是否要在當月最後一天執行，然後按一下 [確定]。

### 使用 Windows PowerShell 建立新排程
您可以使用 [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) Cmdlet 在 Azure 自動化中為傳統 Runbook 建立新的排程，或使用 [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) Cmdlet 為 Azure 入口網站中的 Runbook 建立新的排程。您必須指定排程的開始時間，以及其應該執行的頻率。

下列範例命令顯示如何使用 Azure 服務管理 Cmdlet 建立新的排程，從 2015 年 1 月 20 日起於每日下午 3:30 執行。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

下列範例命令顯示如何使用 Azure Resource Manager Cmdlet，建立會在每月 15 號到 30 號執行的排程。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"


## 將排程連結至 Runbook
Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。如果 Runbook 有參數，您可以為它們提供值。您必須為任何必要參數提供值，並可以提供任何選擇性參數的值。每次此排程啟動 Runbook 時將會使用這些值。您可以將相同的 Runbook 附加到另一個排程，並指定不同的參數值。

### 使用 Azure 傳統入口網站將排程連結至 Runbook
1. 在 Azure 傳統入口網站中，選取 [自動化]，然後按一下自動化帳戶的名稱。
2. 選取 [**Runbook**] 索引標籤。
3. 按一下要排程的 Runbook 名稱。
4. 按一下 [**排程**] 索引標籤。
5. 如果 Runbook 目前未連結至排程，則將提供您 [**連結至新排程**] 或 [**連結至現有排程**] 選項。如果 Runbook 目前連結至排程，請按一下視窗底部的**連結**來存取這些選項。
6. 如果 Runbook 有參數，系統會提示您輸入它們的值。

### 使用 Azure 入口網站將排程連結至 Runbook
1. 在 Azure 入口網站中，從您的自動化帳戶按一下 [Runbook] 磚，以開啟 [Runbook] 刀鋒視窗。
2. 按一下要排程的 Runbook 名稱。
3. 如果 Runbook 目前未連結至排程，則將提供您建立新排程或連結至現有排程的選項。
4. 如果 Runbook 有參數，您可以選取 [修改執行設定 (預設值: Azure)] 選項，隨即便會顯示 [參數] 刀鋒視窗供您據以輸入資訊。

### 使用 Windows PowerShell 將排程連結至 Runbook
您可以使用 [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) 將排程連結至傳統 Runbook，或使用 [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) Cmdlet 將排程連結至 Azure 入口網站中的 Runbook。您可以使用 Parameters 參數來指定 Runbook 參數的值。請參閱[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)，以取得指定參數值的詳細資訊。

下列範例命令顯示如何使用 Azure 服務管理 Cmdlet 與參數來連結排程。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

下列範例命令顯示如何使用 Azure Resource Manager Cmdlet 與參數，將排程連結至 Runbook。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"

## 停用排程
停用排程時，與其連結的任何 Runbook 將無法再依該排程執行。您可以手動停用排程，或在建立排程時為具有頻率的排程設定到期時間。當到達到期時間時，就會停用排程。

### 從 Azure 傳統入口網站停用排程
您可以在 Azure 傳統入口網站中從排程的 [排程詳細資料] 頁面停用排程。

1. 在 Azure 傳統入口網站中，選取 [自動化]，然後按一下自動化帳戶的名稱。
2. 選取 [資產] 索引標籤。
3. 按一下排程的名稱以開啟其詳細資料頁面。
4. 將 [**已啟用**] 變更為 [**否**]。

### 從 Azure 入口網站停用排程
1. 在 Azure 入口網站中，從您的自動化帳戶按一下 [資產] 磚，以開啟 [資產] 刀鋒視窗。
2. 按一下 [排程] 磚，以開啟 [排程] 刀鋒視窗。
3. 按一下排程的名稱以開啟詳細資料刀鋒視窗。
4. 將 [**已啟用**] 變更為 [**否**]。

### 使用 Windows PowerShell 停用排程
您可以使用 [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) Cmdlet 為傳統 Runbook 變更現有排程的屬性，或使用 [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) Cmdlet 為 Azure 入口網站中的 Runbook 變更現有排程的屬性。若要停用排程，請對 **IsEnabled** 參數指定 **false**。

下列範例命令顯示如何使用 Azure 服務管理 Cmdlet 來停用排程。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

下列範例命令顯示如何使用 Azure Resource Manager Cmdlet 來停用 Runbook 的排程。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"


## 後續步驟
* 若要深入了解如何使用排程，請參閱 [Azure 自動化中的排程資產](http://msdn.microsoft.com/library/azure/dn940016.aspx)
* 若要在 Azure 自動化中開始使用 Runbook，請參閱[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)

<!---HONumber=AcomDC_0810_2016------>