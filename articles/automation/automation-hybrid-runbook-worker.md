---
title: "Azure 自動化混合式 Runbook 背景工作 | Microsoft Docs"
description: "本文提供有關安裝和使用混合式 Runbook 背景工作的相關資訊，它是 Azure 自動化的一項功能，可讓您在本機資料中心內的機器上執行 Runbook。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 3db5c8a80f72c7af20c501daf035337f23d3d2c9
ms.openlocfilehash: 29825a205e88809bcfc197e9dc82140db8fd885d


---
# <a name="azure-automation-hybrid-runbook-workers"></a>Azure 自動化混合式 Runbook 背景工作
Azure 自動化中的 Runbook 無法存取您的本機資料中心中的資源，因為其在 Azure 雲端中執行。  Azure 自動化的混合式 Runbook 背景工作功能可讓您在位於資料中心的機器上執行 Runbook，以管理本機資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個內部部署機器。  

下圖說明這項功能。<br>  

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/automation.png)

您可以指定您的資料中心中的一或多部電腦，以做為混合式 Runbook 背景工作，然後從 Azure 自動化執行 Runbook。  每一個背景工作角色都需要 Microsoft Management Agent 能夠連接 Microsoft Operations Management Suite 和 Azure 自動化 Runbook 環境。  Operations Management Suite 僅用來安裝及維護管理代理程式，以及監視背景工作角色的功能。  Runbook 的傳遞和執行的指示都是透過 Azure 自動化執行。

沒有輸入防火牆需求可支援混合式 Runbook 背景工作。 本機電腦上的代理程式會起始與雲端中 Azure 自動化的所有通訊。 啟動 Runbook 時，Azure 自動化會建立代理程式會擷取的指示。 代理程式接著會在執行之前取得 Runbook 和任何參數。  它也會從 Azure 自動化擷取 Runbook 使用的任何 [資產](http://msdn.microsoft.com/library/dn939988.aspx) 。

> [!NOTE]
> 混合式 Runbook 背景工作目前不支援將 [DSC 組態](automation-dsc-overview.md)套用至主控此角色的電腦。
>

## <a name="hybrid-runbook-worker-groups"></a>混合式 Runbook 背景工作群組
每一個混合式 Runbook 背景工作是您安裝代理程式時指定的混合式 Runbook 背景工作群組的成員。  群組可包含單一代理程式，但您可以在群組中安裝多個代理程式以獲得高可用性。

在混合式 Runbook 背景工作上啟動 Runbook 時，您會指定將執行的群組。  群組的成員將會決定哪一個背景工作將會服務要求。  您無法指定特定的背景工作。

## <a name="hybrid-runbook-worker-requirements"></a>Hybrid Runbook Worker 的需求
您必須指定至少一台內部部署電腦，才能執行混合式 Runbook 作業。  這台電腦必須安裝下列軟體：

* Windows Server 2012 或更新版本
* Windows PowerShell 4.0 或更新版本。  我們建議在電腦上安裝 Windows PowerShell 5.0，以提升可靠性。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=50395)下載新版本
* 至少雙核心和 4 GB 的 RAM

針對混合式背景工作角色，請考慮下列建議：

* 在每個群組中指定多個混合式背景工作角色，以保留較高的可用性。  
* 混合式背景工作角色可以與 Service Management Automation 或 System Center Orchestrator Runbook 伺服器並存。
* 請考慮使用實際位於或接近自動化帳戶所在區域的電腦，因為當作業完成時，作業資料會送回到 Azure 自動化。

### <a name="configure-proxy-and-firewall-settings"></a>設定 Proxy 和防火牆設定
若要讓內部部署 Hybrid Runbook Worker 連線到 Microsoft Operations Management Suite (OMS) 服務並向其註冊，它必須能夠存取下述連接埠號碼和 URL。  這是除了 [Microsoft Monitoring Agent 所需的連接埠和 URL](../log-analytics/log-analytics-proxy-firewall.md#configure-proxy-and-firewall-settings-with-the-microsoft-monitoring-agent) 以連線至 OMS。 如果您使用 Proxy 伺服器在代理程式和 OMS 服務之間進行通訊，您必須確保可以存取適當的資源。 如果您使用防火牆來限制網際網路存取，您需要設定防火牆以允許存取。

以下資訊列出要讓 Hybrid Runbook Worker 與自動化進行通訊所需的連接埠和 URL。

* 連接埠︰只需要 TCP 443 以便進行傳出網際網路存取
* 全域 URL：*.azure-automation.net

如果您已定義特定區域的自動化帳戶，而且您想要限制與該區域資料中心的通訊，下表提供每個區域的 DNS 記錄。

| **區域** | **DNS 記錄** |
| --- | --- |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本東部 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亞東南部 |ase-jobruntimedata-prod-su1.azure-automation.net |

## <a name="installing-hybrid-runbook-worker"></a>安裝混合式 Runbook 背景工作
下列程序描述如何安裝和設定 Hybrid Runbook Worker。  對您的自動化環境執行一次前兩個步驟，再對每一台背景工作角色電腦重複其餘步驟。

### <a name="1-create-operations-management-suite-workspace"></a>1.建立 Operations Management Suite 工作區
如果您還沒有 Operations Management Suite 工作區，請使用 [設定工作區](https://technet.microsoft.com/library/mt484119.aspx)中的指示建立工作區。 如果您已經有工作區，可以使用現有的工作區。

### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2.將自動化解決方案加入至 Operations Management Suite 工作區
解決方案會將功能加入至 Operations Management Suite。  自動化解決方案會增加 Azure 自動化的功能，包括支援 Hybrid Runbook Worker。  將解決方案加入至工作區時，它會自動將背景工作角色元件往下推送給您在下一步將安裝的代理程式電腦。

請依照 [使用解決方案資源庫新增解決方案](../log-analytics/log-analytics-add-solutions.md) 中的指示，將 **自動化** 解決方案新增至 Operations Management Suite 工作區。

### <a name="3-install-the-microsoft-monitoring-agent"></a>3.安裝 Microsoft Monitoring Agent
Microsoft Monitoring Agent 可將電腦連線至 Operations Management Suite。  將代理程式安裝在內部部署電腦，並連接到您的工作區時，它會自動下載 Hybrid Runbook Worker 所需的元件。

請依照[將 Windows 電腦連接到 Log Analytics](../log-analytics/log-analytics-windows-agents.md) 中的指示，將代理程式安裝在內部部署電腦上。  您可以對多部電腦重複此程序，將多個背景工作角色加入至您的環境。

當代理程式成功連接到 Operations Management Suite 時，它會列在 Operations Management Suite [設定] 窗格的 [已連接的來源] 索引標籤上。  當 C:\Program Files\Microsoft Monitoring Agent\Agent 中出現 **AzureAutomationFiles** 資料夾時，就可確認代理程式已正確下載自動化解決方案。  若要確認 Hybrid Runbook Worker 版本，您可以瀏覽至 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\，並記下 \\version 子資料夾。   

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.安裝 Runbook 環境並連接到 Azure 自動化
將代理程式新增至 Operations Management Suite 時，自動化解決方案會往下推送包含 **Add-HybridRunbookWorker** Cmdlet 的 **HybridRegistration** PowerShell 模組。  您可以使用這個 Cmdlet 在電腦上安裝 Runbook 環境並向 Azure 自動化進行註冊。

以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令來匯入模組。

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

然後使用下列語法執行 **Add-HybridRunbookWorker** Cmdlet：

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

您可以從 Azure 入口網站的 [管理金鑰]  刀鋒視窗取得這個 Cmdlet 所需的資訊。  在自動化帳戶的 [元素] 面板按一下金鑰圖示，可開啟此刀鋒視窗。

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **名稱** 是混合式 Runbook 背景工作群組的名稱。 如果自動化帳戶中已有這個群組，那麼會將目前的電腦加入。  如果尚不存在，則會加入。
* **EndPoint** 是 [管理金鑰] 刀鋒視窗中的 [URL] 欄位。
* **Token** 是 [管理金鑰] 刀鋒視窗中的 [主要存取金鑰]。  

在 **Add-HybridRunbookWorker** 中新增 **-Verbose** 參數可接收安裝的詳細資訊。

### <a name="5-install-powershell-modules"></a>5.安裝 PowerShell 模組
Runbook 可以使用 Azure 自動化環境中安裝的模組中定義的任何活動和 Cmdlet。  不過，這些模組不會自動部署至內部部署機器，所以您必須手動安裝它們。  例外狀況是預設安裝的 Azure 模組，可提供 Azure 自動化所有 Azure 服務和活動的 Cmdlet 存取權。

由於「混合式 Runbook 背景工作」功能的主要目的是要管理本機資源，您很可能必須安裝支援這些資源的模組。  您可以參考[安裝模組](http://msdn.microsoft.com/library/dd878350.aspx)來取得安裝 Windows PowerShell 模組的詳細資訊。

## <a name="removing-hybrid-runbook-worker"></a>移除 Hybrid Runbook Worker
您可以移除群組中的一或多個 Hybrid Runbook 背景工作角色，或移除該群組，視您的需求而定。  若要從內部部署電腦移除 Hybrid Runbook 背景工作角色，以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令- **Remove-HybridRunbookWorker** Cmdlet。  使用 **-Verbose** 參數可取得移除程序的詳細記錄。

這不會移除電腦上的 Microsoft Monitoring Agent，只會移除 Hybrid Runbook 背景工作角色的功能和組態。  

若要移除群組，您必須先使用先前所示的命令，移除每一部群組成員電腦上的 Hybrid Runbook 背景工作角色，然後執行下列步驟移除群組。  

1. 在 Azure 入口網站中，開啟自動化帳戶。
2. 選取 [Hybrid 背景工作角色] 刀鋒視窗中的 [Hybrid 背景工作角色群組] 圖格，然後選取要刪除的群組。  選取特定的群組之後，[Hybrid 背景工作角色群組] 屬性刀鋒視窗隨即出現。<br> ![Hybrid Runbook 背景工作角色群組刀鋒視窗](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. 在所選群組的屬性刀鋒視窗中，按一下 [刪除]。  將會出現一則訊息要求您確認此動作，如果您確定要繼續請選取 [是]。<br> ![建立群組確認對話方塊](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> 此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作上啟動 Runbook
[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 描述啟動 Runbook 的不同方法。  混合式 Runbook 背景工作加入了 **RunOn** 選項，您可以在其中指定混合式 Runbook 背景工作群組的名稱。  如果未指定群組，則會擷取 Runbook，且由該群組中的背景工作執行。  如果未指定此選項，則會正常在 Azure 自動化中執行。

在 Azure 入口網站中啟動 Runbook 時，您會看到 [執行於]選項，您可以在此選取 [Azure] 或 [Hybrid 背景工作角色]。  如果選取 [Hybrid 背景工作角色]，則您可以從下拉式清單中選取群組。

使用 **RunOn** 參數。您可以使用 Windows PowerShell 以下列命令在 Hybrid Runbook 背景工作角色群組上啟動名為 Test-Runbook 的 Runbook。

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> 0.9.1 版 Microsoft Azure PowerShell 的 **Start-AzureAutomationRunbook** Cmdlet 已新增 **RunOn** 參數。  如果您安裝較早的版本，您應該 [下載最新版本](https://azure.microsoft.com/downloads/) 。  您只需要在將會從 Windows PowerShell 啟動 Runbook 的工作站上安裝此版本。  您不需要將它安裝在背景工作電腦上，除非您想要從該電腦啟動 Runbook。  您目前無法在混合式 Runbook 背景工作上從另一個 Runbook 啟動 Runbook，因為這項作業需要在您的自動化帳戶中安裝最新版本的 Azure PowerShell。  Azure 自動化中將自動更新為最新版本，並且很快將其自動往下推送到背景工作。
>
>

## <a name="runbook-permissions"></a>Runbook 權限
在混合式 Runbook 背景工作上執行的 Runbook 不能使用通常用於 Runbook 對 Azure 資源進行驗證的相同方法，因為它們將存取 Azure 外部的資源。  Runbook 可以提供自己的驗證給本機資源，或者您可以指定 RunAs 帳戶以對所有 Runbook 提供使用者內容。

### <a name="runbook-authentication"></a>Runbook 驗證
根據預設，Runbook 將在內部部署機器上的本機系統帳戶內容中執行，因此，它們必須對將會存取的資源提供自己的驗證。  

您可以在您的 Runbook 中使用[認證](http://msdn.microsoft.com/library/dn940015.aspx)和[憑證](http://msdn.microsoft.com/library/dn940013.aspx)資產，搭配可讓您指定認證的 Cmdlet，您就能夠向不同的資源驗證。  下列範例顯示會重新啟動電腦的 Runbook 的一部分。  它會從認證資產擷取認證和從變數資產擷取電腦的名稱，然後使用這些值搭配 Restart-Computer Cmdlet。

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

您也可以運用 [InlineScript](automation-powershell-workflow.md#inlinescript)，它可讓您利用 [PSCredential 一般參數](http://technet.microsoft.com/library/jj129719.aspx)指定的認證在另一部電腦上執行程式碼區塊。

### <a name="runas-account"></a>RunAs 帳戶
您不需要讓 Runbook 提供自己的驗證給本機資源，您可以針對混合式背景工作角色群組指定 **RunAs** 帳戶。  您指定具有本機資源存取權的 [認證資產](automation-credentials.md) ，在群組中的 Hybrid Runbook Worker 執行時，所有 Runbook 會在這些認證下執行。  

認證的使用者名稱必須是下列格式之一：

* 網域\使用者名稱
* username@domain
* 使用者名稱 (適用於內部部署機器的本機帳戶)

使用下列程序以針對混合式背景工作角色群組指定 RunAs 帳戶：

1. 建立具有本機資源存取權的 [認證資產](automation-credentials.md) 。
2. 在 Azure 入口網站中，開啟自動化帳戶。
3. 選取 [Hybrid Worker 群組]  圖格，然後選取群組。
4. 選取 [所有設定]，然後選取 [Hybrid 背景工作角色群組設定]。
5. 將 [執行身分] 從 [預設] 變更為 [自訂]。
6. 選取認證，然後按一下 [儲存] 。

## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>為混合式 Runbook 背景工作建立 Runbook
在 Azure 自動化和混合式 Runbook 背景工作上執行的 Runbook 結構中沒有任何差異。 您對每個使用的 Runbook 可能會有顯著差異，不過由於混合式 Runbook 背景工作的 Runbook 通常會管理資料中心內的本機資源，而 Azure 自動化中的 Runbook 通常管理 Azure 雲端中的資源。

您可以在 Azure 自動化中編輯混合式 Runbook 背景工作的 Runbook，但如果您嘗試在編輯器中測試 Runbook，可能會遇到困難。  存取本機資源的 PowerShell 模組可能未安裝在 Azure 自動化環境中，在此情況下，測試就會失敗。  如果您已安裝所需的模組，就會執行 Runbook，但它不能存取本機資源以進行完整測試。

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>在 Hybrid Runbook Worker 上進行 Runbook 疑難排解
[Runbook 輸出和訊息](automation-runbook-output-and-messages.md) 會從混合式背景工作角色傳送到 Azure 自動化，就像雲端中執行的 Runbook 工作一樣。  您也可以啟用詳細資訊和進度資料流，就像您在其他 Runbook 中的作法一樣。  

記錄檔儲存每一個混合式背景工作角色本機的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。

如果您的 Runbook 未成功完成，但作業摘要顯示 [暫止] 狀態，請檢閱疑難排解文章 [Hybrid Runbook Worker：Runbook 作業在暫止狀態下終止](automation-troubleshooting-hrw-runbook-terminates-suspended.md)。   

## <a name="relationship-to-service-management-automation"></a>與 Service Management Automation 的關聯性
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) 可讓您在本機資料中心內執行 Azure 自動化支援的相同 Runbook。 SMA 通常與 Windows Azure Pack 一同部署，因為 Windows Azure Pack 包含 SMA 管理的圖形化介面。 不同於 Azure 自動化，SMA 需要本機安裝，安裝中包含了裝載 API 的 Web 伺服器、包含 Runbook 和 SMA 組態的資料庫、用以執行 Runbook 作業的 Runbook 背景工作角色。 Azure 自動化在雲端中提供這些服務，並只要求您在本機環境中維護混合式 Runbook 背景工作。

如果您是現有 SMA 使用者，可以將您的 Runbook 移至 Azure 自動化，以搭配混合式 Runbook 背景工作使用，而不必變更，假設他們已依 [為混合式 Runbook 背景工作建立 Runbook](#creating-runbooks-for-hybrid-runbook-worker)中所述，向資源進行驗證 。  SMA 中的 Runbook 會在背景工作伺服器上服務帳戶的內容中執行，其可能為 Runbook 提供該驗證。

您可以使用下列準則來判斷 Azure 自動化搭配混合式 Runbook 背景工作或 Service Management Automation 更適合您的需求。

* SMA 需要本機安裝其基礎元件，如果需要圖形化管理介面，這些元件會連接到 Windows Azure Pack。 相較於 Azure 自動化，這需要更多的本機資源和更高的維護成本；Azure 自動化只需要在本機 Runbook 背景工作角色上安裝代理程式。 代理程式由 Operations Management Suite 管理，進而減少您的維護成本。
* Azure 自動化會將其 Runbook 儲存在雲端中，並將它們提供給內部部署的 Hybrid Runbook 背景工作角色。 如果您的安全性原則不允許這種行為，您應該使用 SMA。
* SMA 隨附於 System Center，因此需要 System Center 2012 R2 授權。 Azure 自動化是採用分層式的訂用帳戶模型。
* Azure 自動化具備 SMA 中並未提供的圖形 Runbook 等進階功能。

## <a name="next-steps"></a>後續步驟
* 若要深入了解可用來啟動 Runbook 的不同方法，請參閱 [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)
* 若要了解使用文字式編輯器在 Azure 自動化中使用 PowerShell 和 PowerShell 工作流程 Runbook 的不同程序，請參閱 [在 Azure 自動化中編輯 Runbook](automation-edit-textual-runbook.md)



<!--HONumber=Nov16_HO4-->


