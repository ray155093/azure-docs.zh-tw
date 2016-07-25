<properties
   pageTitle="使用 JSON 格式化標籤來建立 Azure VM 啟動和關閉的排程 | Microsoft Azure"
   description="本文示範如何在標籤上使用 JSON 字串，自動排定 VM 的啟動和關閉。"
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="magoedte;paulomarquesc" />

# Azure 自動化案例 - 使用 JSON 格式化標籤來建立 Azure VM 啟動和關閉的排程

客戶通常會想要排程虛擬機器的啟動與關閉，以協助減少訂用帳戶成本或支援業務和技術需求。

下列案例可讓您在 Azure 中的資源群組層級或虛擬機器層級，使用稱為「排程」的標籤，設定自動啟動和關閉您的 VM。您可以設定從星期日至星期六具有啟動時間和關閉時間的排程。雖然我們有一些現成的選項，像是使用[虛擬機器擴充集合](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)搭配允許放大或縮小的 [自動調整設定]，以及有內建排程啟動和關閉作業功能的 [DevTest Labs](../devtest-lab/devtest-lab-overview.md) 服務，但這些選項只支援特定的案例並不適用於 IaaS VM。

當 [排程] 標籤套用至資源群組時，也會套用到該資源群組內的所有虛擬機器。如果排程也會直接套用至 VM，則最後一個排程將會優先，順序如下︰

1.  套用至資源群組的排程
2.  套用至資源群組和資源群組中虛擬機器的排程
3.  套用至虛擬機器的排程

此案例基本上會採用具有指定格式的 JSON 字串，並將它加入為「排程」標籤的值。然後 Runbook 會列出所有資源群組和虛擬機器，並根據上面所列的案例識別每個 VM 的排程，然後它會依序顯示這些已附加排程的 VM 並評估應該採取的動作 (可能是停止、關閉或忽略)。


## 取得案例

此案例包含四個 PowerShell 工作流程 Runbook，您可以從 [TechNet 資源庫](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7)或此專案的 [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) 儲存機器下載這些 Runbook。

Runbook | 說明 
----------|----------
Test-ResourceSchedule | 檢查每部虛擬機器的排程 (根據排程，執行虛擬機器的關機或啟動)
Add-ResourceSchedule | 將排程標籤加入至 VM 或資源群組
Update-ResourceSchedule | 以新的排程標籤取代現有的排程標籤以進行修改
Remove-ResourceSchedule | 從 VM 或資源群組移除排程標籤 


## 安裝和設定此案例

### 安裝並發佈 Runbook

下載 Runbook 之後，您可以使用[匯入 Runbook 程序](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation)中的程序匯入它們。在每個 Runbook 成功匯入您的自動化帳戶之後予以發佈。


### 將排程加入至 Test-ResourceSchedule

請遵循以下步驟以對 Test-ResourceSchedule Runbook 啟用排程。這個 Runbook 可測試將會啟動、關閉或保持原狀的虛擬機器。

1. 從 Azure 入口網站中，開啟您的自動化帳戶，然後按一下 [Runbook] 圖格 。
2. 在 [Test-ResourceSchedule] 刀鋒視窗中，按一下 [排程] 圖格。
3. 在 [排程] 刀鋒視窗上，按一下 [加入排程]。
4. 在 [排程] 刀鋒視窗中選取 [將排程連結至您的 Runbook]，然後在 [排程] 刀鋒視窗中選取 [建立新的排程]。
5.  在 [新增排程] 刀鋒視窗中，輸入此排程的名稱。例如，HourlyExecution。
6. 若為排程 [啟動] 排程，請將開始時間設為已四捨五入的小時遞增值。
7. 選取 [週期] 並針對 [重複出現間隔] 選取 [1 小時]。
8. 確認 [設定到期] 已設為 [否]，然後按一下 [建立] 以儲存新排程。
9. 在 [排程 Runbook] 選項刀鋒視窗中，選取 [參數和回合設定] 選項，然後在 Test-ResourceSchedule [參數] 刀鋒視窗的 [SubscriptionName] 欄位中，輸入您的訂用帳戶名稱。這是 Runbook 所需的唯一參數。在完成時按一下 [確定]。
   

完成時的 Runbook 排程應如下所示︰<br> ![已設定的 Test-ResourceSchedule Runbook](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## 排程 JSON 格式

此解決方案基本上會採用具有指定格式的 JSON 字串，並將它加入為「排程」標籤的值。然後 Runbook 會列出所有資源群組和虛擬機器，並識別每部虛擬機器的排程，然後它會依序顯示這些已附加排程的虛擬機器並檢查應該採取的動作。以下是其格式化方式的範例。

    {
       "TzId": "Eastern Standard Time", 
        "0": {  
           "S": "11",
           "E": "17" 
        },
        "1": {
           "S": "9",
           "E": "19"
        },
        "2": {
           "S": "9",
           "E": "19"
        },
    }

此結構的詳細資訊︰

1. 此 JSON 結構的格式已經過最佳化，可解決 Azure 中單一標籤值 256 個字元的限制

2. *TzId* 代表虛擬機器的時區。此識別碼可以在 PowerShell 工作階段中使用 TimeZoneInfo .NET 類別來取得 - **[System.TimeZoneInfo]::GetSystemTimeZones()**。<br>

    ![PowerShell 中的 GetSystemTimeZones](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

    - 一週日期是以 0 到 6 的數字值來表示。值 0 等於星期日。
    - 開始時間以 **S** 屬性表示，其值採用 24 小時制的格式。
    - 結束或關閉時間以 **E** 屬性表示，其值採用 24 小時制的格式。

    如果 S 和 E 屬性的值為零 (0)，則虛擬機器會在評估時留在其目前的狀態。

3. 如果您想要跳過一週特定一天的評估，請勿加入相關一週日期的區段。在下列範例中，只會評估星期一，而會忽略一周的其他天數。
   
        {
          "TzId": "Eastern Standard Time",
           "1": {
             "S": "11",
             "E": "17"
           }
        }

## 標記資源群組或 VM

為了關閉 VM，您需要標記它們或其所在的資源群組。不會評估沒有 [排程] 標籤的虛擬機器，因此不會啟動或關閉這些虛擬機器。有兩種方式可透過此解決方案標記資源群組或 VM，直接從入口網站或使用 **Add-ResourceSchedule**、**Update-ResourceSchedule** 和 **Remove-ResourceSchedule** Runbook。

### 透過入口網站進行標記

請依照下列步驟，在入口網站中標記虛擬機器或資源群組。

1. 壓平合併 JSON 字串，並確認沒有任何空格。您的 JSON 字串應該如下所示︰

        {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
   

2. 選取 [標籤] 圖示，以選取要套用此排程的 VM 或資源群組。<br>![VM 標籤選項](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)
3. 標籤會定義於金鑰/值組之後。在 [金鑰] 欄位中輸入 [排程] 並將 JSON 字串貼到 [值] 欄位中，然後按一下 [儲存]。新標籤現在應出現在您的資源的標籤清單中。<br>![VM 排程標籤](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)


### 從 PowerShell 進行標記

所有匯入的 Runbook 都在指令碼的開頭包含說明資訊，以描述如何直接從 PowerShell 執行 Runbook。傳遞可讓您在入口網站外部的 VM 或資源群組上建立或更新 [排程] 標籤的必要參數，即可從 PowerShell 呼叫 Add-ScheduleResource 和 Update-ScheduleResource Runbook。

若要透過 PowerShell 建立、新增和刪除標籤，您必須先[設定 Azure 的 PowerShell 環境](../powershell-install-configure.md)。完成安裝後，您可以繼續進行下列步驟。

### 使用 PowerShell 建立排程標籤

1. 開啟 PowerShell 工作階段並執行下列命令，以使用您的「執行身分」帳戶進行驗證並指定訂用帳戶︰
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
   
2. 定義排程雜湊表。以下是其建構方式的範例：
    
        $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}

3. 定義 Runbook 所需的參數。在下列範例中，我們會以 VM 為目標。

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "VmName"="VM01";"Schedule"=$schedule}

    如果您要標記資源群組，只需從 $params 雜湊表移除 VMName 參數，如下所示︰

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "Schedule"=$schedule}

4. 使用下列參數執行 **Add-ResourceSchedule** Runbook，以建立 [排程] 標籤︰

        Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

5. 如果您想要更新資源群組或虛擬機器標籤，請使用下列參數執行 **Update-ResourceSchedule** Runbook︰

        Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

### 使用 PowerShell 移除排程標籤

1. 開啟 PowerShell 工作階段並執行下列命令，以使用您的「執行身分」帳戶進行驗證並選取和指定訂用帳戶︰
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"

2. 定義 Runbook 所需的參數。在下列範例中，我們會以 VM 為目標。

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01" ` 
        ;"VmName"="VM01"}

    如果您要從資源群組移除標籤，只需從 $params 雜湊表移除 VMName 參數，如下所示︰

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}

3. 執行 **Remove-ResourceSchedule** Runbook 以移除 [排程] 標籤︰

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

4. 如果您想要更新資源群組或虛擬機器標籤，請使用下列參數執行 **Remove-ResourceSchedule** Runbook︰

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"


>[AZURE.NOTE] 建議您主動監視這些 Runbook (和虛擬機器狀態)，以確認您的虛擬機器相應地進行關機並啟動。

選取 Runbook 的 [作業] 圖格，即可在 Azure 入口網站中檢視 **Test-ResourceSchedule** Runbook 作業的詳細資料。除了作業和任何例外狀況 (如果發生) 的一般資訊以外，作業的 [摘要] 也會顯示輸入參數和輸出串流。[歷程記錄] 會包含 [輸出串流]、[警告] 和 [錯誤串流] 的訊息。選取 [輸出] 圖格以檢視 Runbook 執行的詳細結果。<br> ![Test-ResourceSchedule 輸出](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## 後續步驟

-  若要開始使用 PowerShell 工作流程 Runbook，請參閱[我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)
-  若要深入了解 Runbook 類型、其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)
-  如需 PowerShell 指令碼支援功能的詳細資訊，請參閱 [Azure 自動化中的原生 PowerShell 指令碼支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
-  若要深入了解 Runbook 記錄和輸出，請參閱 [Azure 自動化中的 Runbook 輸出和訊息](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0713_2016-->