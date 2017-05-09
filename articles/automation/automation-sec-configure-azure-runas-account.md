---
title: "設定 Azure 執行身分帳戶 | Microsoft Docs"
description: "本教學課程會逐步引導您在 Azure 自動化中建立、測試和舉例使用安全性主體驗證。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "服務主體名稱, setspn, azure 驗證"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: magoedte
ROBOTS: NOINDEX
redirect_url: /azure/automation/automation-offering-get-started
redirect_document_id: TRUE
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 90570886b3a1ae0c48683691cb438b5a61195d76
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>使用 Azure 執行身分帳戶驗證 Runbook
本文說明如何在 Azure 入口網站中設定 Azure 自動化帳戶。 若要進行此作業，請使用執行身分帳戶功能來驗證在 Azure Resource Manager 或 Azure 服務管理中管理資源的 Runbook。

當您在 Azure 入口網站中建立自動化帳戶時，您會自動建立兩個帳戶：

* 執行身分帳戶。 此帳戶會在 Azure Active Directory (Azure AD) 中建立服務主體，並建立一個憑證。 此外，也會指定參與者角色型存取控制 (RBAC)，此控制可使用 Runbook 來管理 Resource Manager 資源。
* 傳統執行身分帳戶。 此帳戶會上傳管理憑證，利用此憑證就能使用 Runbook 來管理服務管理或傳統資源。

建立自動化帳戶可為您簡化程序，協助您快速開始建置和部署 Runbook 以支援您的自動化需求。

使用執行身分和傳統執行身分帳戶，您可以︰

* 當您在 Azure 入口網站中透過 Runbook 管理 Resource Manager 或服務管理資源時，提供標準化的 Azure 驗證方式。
* 自動使用可在 Azure 警示中設定的全域 Runbook。

> [!NOTE]
> 若要搭配自動化全域 Runbook 使用 [Azure 警示整合功能](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)，您需要具備使用執行身分帳戶和傳統執行身分帳戶所設定的自動化帳戶。 您可以選取已定義執行身分和傳統執行身分帳戶的自動化帳戶，或是選擇建立新的自動化帳戶。
>  

本文示範如何從 Azure 入口網站建立自動化帳戶、如何使用 Azure PowerShell 更新自動化帳戶、如何管理帳戶組態，以及如何在 Runbook 中進行驗證。

在開始建立自動化帳戶之前，您最好先了解並考慮下列事項︰

* 建立自動化帳戶並不會影響您可能已在傳統或 Resource Manager 部署模型中建立的自動化帳戶。
* 此程序只適用於在 Azure 入口網站中建立的自動化帳戶。 嘗試從 Azure 傳統入口網站建立帳戶，並不會複寫執行身分帳戶組態。
* 如果您已備有 Runbook 與資產 (例如排程或變數) 來管理傳統資源，且想要讓 Runbook 使用新的傳統執行身分帳戶進行驗證，請執行下列任一操作︰

  * 若要建立傳統執行身分帳戶，請遵循＜管理執行身分帳戶＞一節中的指示。 
  * 若要更新現有帳戶，請使用＜使用 PowerShell 更新自動化帳戶＞一節中的 PowerShell 指令碼。
* 若要使用新的執行身分帳戶和傳統執行身分自動化帳戶進行驗證，您必須以[自動化程式碼範例](#authentication-code-examples)一節中提供的範例程式碼修改現有的 Runbook。

    >[!NOTE]
    >執行身分帳戶適用於使用以憑證為基礎的服務主體對 Resource Manager 資源進行驗證。 傳統執行身分帳戶適用於使用管理憑證對服務管理資源進行驗證。

## <a name="create-an-automation-account-from-the-azure-portal"></a>從 Azure 入口網站建立自動化帳戶
在這一節中，您會從 Azure 入口網站建立 Azure 自動化帳戶，接著再建立執行身分帳戶和傳統執行身分帳戶。

>[!NOTE]
>若要建立自動化帳戶，您必須是服務管理員角色的成員，或是獲得訂用帳戶存取權的訂用帳戶共同管理員。 您必須已新增成為該訂用帳戶之預設 Active Directory 執行個體的使用者。 此帳戶不需要獲派特殊權限角色。
>
>若您在新增至訂用帳戶的共同管理員角色之前，並非訂用帳戶之 Active Directory 執行個體的成員，您在新增至 Active Directory 時會成為來賓身分。 在此情況下，您會在 [新增自動化帳戶] 刀鋒視窗上看到 「您沒有權限建立...」的警告。
>
>新增至共同管理員角色的使用者可以先從訂用帳戶的 Active Directory 執行個體中移除並重新新增，使其成為 Active Directory 中的完整使用者。 若要確認這種情況，請從 Azure 入口網站的 [Azure Active Directory] 窗格，選取 [使用者和群組]、選取 [所有使用者]，然後在選取特定使用者之後選取 [設定檔]。 使用者設定檔之下 [使用者類型] 屬性的值不得等於 [來賓]。
>

1. 以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。

2. 選取 [自動化帳戶] 。

3. 在 [自動化帳戶] 刀鋒視窗上，按一下 [新增]。
[新增自動化帳戶] 刀鋒視窗隨即開啟。

 ![[新增自動化帳戶] 刀鋒視窗](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > 如果您的帳戶不是訂用帳戶管理員角色的成員和訂用帳戶的共同管理員，則 [新增自動化帳戶] 刀鋒視窗上會顯示下列警告：
   >
   >![加入自動化帳戶警告](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. 在 [新增自動化帳戶] 刀鋒視窗的 [名稱] 方塊中，輸入新的自動化帳戶名稱。

5. 如果您有多個訂用帳戶，請執行下列動作：

    a. 在 [訂用帳戶] 底下，為新帳戶指定一個訂用帳戶。

    b. 在 [資源群組] 底下，按一下 [建立新的] 或 [使用現有的]。

    c. 在 [位置] 底下，指定 Azure 資料中心。

6. 在 [建立 Azure 執行身分帳戶] 底下選取 [是]，然後按一下 [建立]。

   > [!NOTE]
   > 如果您選取 [否] 以選擇不要建立執行身分帳戶，則 [新增自動化帳戶] 刀鋒視窗中會顯示一則警告訊息。 雖然此帳戶已在 Azure 入口網站中建立，但在傳統或 Resource Manager 訂用帳戶目錄服務內並沒有對應的驗證身分識別。 因此，這個帳戶無法存取訂用帳戶中的資源。 此情況會導致參考此帳戶的任何 Runbook 無法進行驗證，也無法對這些部署模型中的資源執行工作。
   >
   > ![[新增自動化帳戶] 刀鋒視窗上的警告訊息](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > 此外，因為服務主體並未建立，因此系統不會指派參與者角色。
   >

7. 在 Azure 建立自動化帳戶時，您可以在功能表的 [通知]  底下追蹤進度。

### <a name="resources"></a>資源
順利建立自動化帳戶時，系統會自動為您建立幾項資源。 下面兩個資料表會摘要說明這些資源：

#### <a name="run-as-account-resources"></a>執行身分帳戶資源

| 資源 | 說明 |
| --- | --- |
| AzureAutomationTutorial Runbook | 此為圖形化 Runbook 範例，示範如何使用執行身分帳戶進行驗證以及取得所有 Resource Manager 資源。 |
| AzureAutomationTutorialScript Runbook | 此為 PowerShell Runbook 範例，示範如何使用執行身分帳戶進行驗證以及取得 Resource Manager 資源。 |
| AzureRunAsCertificate | 在建立自動化帳戶時自動建立，或使用下列適用於現有帳戶的 PowerShell 指令碼建立的憑證資產。 此憑證可讓您向 Azure 進行驗證，以便從 Runbook 管理 Azure Resource Manager 資源。 此憑證有一年的有效期。 |
| AzureRunAsConnection | 在建立自動化帳戶時自動建立，或使用適用於現有帳戶的 PowerShell 指令碼建立的連線資產。 |

#### <a name="classic-run-as-account-resources"></a>傳統執行身分帳戶資源

| 資源 | 說明 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook | 此為圖形化 Runbook 範例，可使用傳統執行身分帳戶 (憑證) 來取得訂用帳戶中使用傳統部署模型所建立的所有 VM，然後寫入 VM 名稱和狀態。 |
| AzureClassicAutomationTutorial 指令碼 Runbook | 此為 PowerShell Runbook 範例，可使用傳統執行身分帳戶 (憑證) 來取得訂用帳戶中的所有傳統 VM，然後寫入 VM 名稱和狀態。 |
| AzureClassicRunAsCertificate | 自動建立的憑證資產，可供您向 Azure 進行驗證，以便從 Runbook 管理 Azure 傳統資源。 此憑證有一年的有效期。 |
| AzureClassicRunAsConnection | 自動建立的連線資產，可供您向 Azure 進行驗證，以便從 Runbook 管理 Azure 傳統資源。 |

## <a name="verify-run-as-authentication"></a>確認執行身分驗證
執行一項小測試，以確認您可以使用新的執行身分帳戶成功進行驗證。

1. 在 Azure 入口網站中，開啟先前建立的自動化帳戶。

2. 按一下 [Runbook]  磚以開啟 Runbook 的清單。

3. 選取 **AzureAutomationTutorialScript** Runbook，然後按一下 [啟動] 來啟動 Runbook。 隨即會發生下列事件︰
 * 建立 [Runbook 作業](automation-runbook-execution.md)、顯示 [作業] 刀鋒視窗，而作業狀態會顯示在 [作業摘要] 圖格中。
 * 作業狀態一開始會顯示為 [已排入佇列] ，表示其正在等候雲端中的 Runbook 背景工作變為可用狀態。
 * 當背景工作宣告該作業時，狀態會變成 [開始]。
 * 當 Runbook 開始執行時，狀態會變成 [執行中]。
 * 當 Runbook 作業執行完成時，您應該會看到 [完成] 狀態。

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. 若要查看 Runbook 的詳細結果，請按一下 [輸出] 圖格。  
隨即會顯示 [輸出] 刀鋒視窗，其中顯示 Runbook 已驗證成功並傳回資源群組中的所有可用資源清單。

5. 關閉 [輸出] 刀鋒視窗以返回 [作業摘要] 刀鋒視窗。

6. 關閉 [作業摘要] 刀鋒視窗和對應的 **AzureAutomationTutorialScript** Runbook 刀鋒視窗。

## <a name="verify-classic-run-as-authentication"></a>確認傳統執行身分驗證
執行一項類似的小測試，以確認您可以使用新的傳統執行身分帳戶成功進行驗證。

1. 在 Azure 入口網站中，開啟先前建立的自動化帳戶。

2. 按一下 [Runbook]  磚以開啟 Runbook 的清單。

3. 選取 **AzureClassicAutomationTutorialScript** Runbook，然後按一下 [啟動] 來啟動 Runbook。 隨即會發生下列事件︰

 * 建立 [Runbook 作業](automation-runbook-execution.md)、顯示 [作業] 刀鋒視窗，而作業狀態會顯示在 [作業摘要] 圖格中。
 * 作業狀態一開始會顯示為 [已排入佇列] ，表示其正在等候雲端中的 Runbook 背景工作變為可用狀態。
 * 當背景工作宣告該作業時，狀態會變成 [開始]。
 * 當 Runbook 開始執行時，狀態會變成 [執行中]。
 * 當 Runbook 作業執行完成時，您應該會看到 [完成] 狀態。

    ![安全性主體 Runbook 測試](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. 若要查看 Runbook 的詳細結果，請按一下 [輸出] 圖格。  
隨即會顯示 [輸出] 刀鋒視窗，其中顯示 Runbook 已驗證成功並傳回訂用帳戶中的所有傳統 VM 清單。

5. 關閉 [輸出] 刀鋒視窗以返回 [作業摘要] 刀鋒視窗。

6. 關閉 [作業摘要] 刀鋒視窗和對應的 **AzureAutomationTutorialScript** Runbook 刀鋒視窗。

## <a name="managing-your-run-as-account"></a>管理執行身分帳戶
有時您必須在自動化帳戶到期前更新憑證。 如果您認為執行身分帳戶遭到盜用，您可加以刪除並重新建立。 本節會討論如何執行這些作業。

### <a name="self-signed-certificate-renewal"></a>自我簽署憑證更新
您為執行身分帳戶建立的自我簽署憑證，會在建立日起算一年後到期。 您可以在該憑證到期前隨時更新憑證。 當您更新憑證時，目前的有效憑證會予以保留，以確保已排入佇列或正在執行以及使用該執行身分帳戶進行驗證的任何 Runbook 不會受到負面影響。 憑證在到期日之前會保持有效。

> [!NOTE]
> 如果您已設定您的自動化執行身分帳戶以使用您的企業憑證授權單位所核發的憑證，而且您使用此選項，該企業憑證將會由自我簽署憑證所取代。

若要更新憑證，請執行下列步驟︰

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在 [自動化帳戶] 刀鋒視窗的 [帳戶屬性] 窗格中，於 [帳戶設定] 底下選取 [執行身分帳戶]。

    ![自動化帳戶的屬性窗格](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. 在 [執行身分帳戶] 的屬性刀鋒視窗中，選取您想要更新憑證的執行身分帳戶或傳統執行身分帳戶。

4. 在所選帳戶的 [屬性] 刀鋒視窗上，按一下 [更新憑證]。

    ![更新執行身分帳戶的憑證](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. 更新憑證時，您可以在功能表的 [通知] 底下追蹤進度。

### <a name="delete-a-run-as-or-classic-run-as-account"></a>刪除執行身分或傳統執行身分帳戶
本節說明如何刪除並重新建立執行身分或傳統執行身分帳戶。 當您執行此動作時，系統不會保留自動化帳戶。 刪除執行身分或傳統執行身分帳戶之後，您可以在 Azure 入口網站中重新建立它。

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在 [自動化帳戶] 刀鋒視窗的 [帳戶屬性] 窗格中，選取 [執行身分帳戶]。

3. 在 [執行身分帳戶] 的屬性刀鋒視窗中，選取您想要刪除的執行身分帳戶或傳統執行身分帳戶。 然後，在所選帳戶的 [屬性] 刀鋒視窗上，按一下 [刪除]。

 ![刪除執行身分帳戶](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. 刪除帳戶時，您可以在功能表的 [通知] 底下追蹤進度。

5. 帳戶刪除之後，您可以在 [執行身分帳戶] 的屬性刀鋒視窗中，選取建立選項 [Azure 執行身分帳戶]來重新建立它。

 ![重新建立自動化執行身分帳戶](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>設定錯誤
在初始設定期間，您可能會以不正確的方式刪除或建立要讓執行身分或傳統執行身分帳戶正常運作所需的某些設定項目。 這些項目包括︰

* 憑證資產
* 連線資產
* 已從參與者角色移除了執行身分帳戶
* Azure AD 中的服務主體或應用程式

在上述設定錯誤和其他這類情況中，自動化帳戶會偵測到變更，並在帳戶的 [執行身分帳戶] 屬性刀鋒視窗上顯示 [不完整] 狀態。

![不完整的執行身分帳戶設定狀態](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

當您選取執行身分帳戶時，帳戶的 [屬性] 窗格會顯示下列錯誤訊息：

![不完整的執行身分設定警告訊息](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png)。

您可以藉由刪除並重新建立這些執行身分帳戶，來快速解決帳戶的問題。

## <a name="update-your-automation-account-by-using-powershell"></a>使用 PowerShell 更新自動化帳戶
您可以使用 PowerShell 來更新現有的自動化帳戶，但前提是︰

* 您已建立一個自動化帳戶，但拒絕建立執行身分帳戶。
* 您已使用自動化帳戶來管理 Resource Manager 資源，而且您想要更新此帳戶以包含可供 Runbook 驗證的執行身分帳戶。
* 您已使用自動化帳戶來管理傳統資源，而且您想要更新此帳戶以使用傳統執行身分，而不是建立新的帳戶並將 Runbook 和資產移轉至該帳戶。   
* 您想要使用企業憑證授權單位 (CA) 所核發的憑證，來建立執行身分和傳統執行身分帳戶。

此指令碼有下列先決條件：

* 此指令碼只能在具有 Azure Resource Manager 模組 2.01 和更新版本的 Windows 10 與 Windows Server 2016 上執行。 不支援在舊版 Windows 上執行。
* Azure PowerShell 1.0 和更新版本。 如需有關 PowerShell 1.0 版本的資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。
* 自動化帳戶，系統會將其參照作為下列 PowerShell 指令碼中 –AutomationAccountName 和 -ApplicationDisplayName 參數的值。

若要取得指令碼所需參數 SubscriptionID、ResourceGroup 和 AutomationAccountName 的值，請執行下列動作︰
1. 在 Azure 入口網站中，於 [自動化帳戶] 刀鋒視窗上選取您的自動化帳戶，然後選取 [所有設定]。 
2. 在 [所有設定] 刀鋒視窗上，選取 [帳戶設定] 之下的 [屬性]。 
3. 請記下 [屬性] 刀鋒視窗上的值。

![自動化帳戶的 [屬性] 刀鋒視窗](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>建立執行身分帳戶 PowerShell 指令碼
這個 PowerShell 指令碼包含下列組態的支援︰

* 使用自我簽署憑證建立執行身分帳戶。
* 使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。
* 使用企業憑證建立執行身分帳戶和傳統執行身分帳戶。
* 在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。

視您選取的組態選項，指令碼會建立下列項目。

**若為執行身分帳戶︰**

* 建立可使用自我簽署憑證或企業憑證的公開金鑰進行匯出的 Azure AD 應用程式、建立此應用程式在 Azure AD 中的服務主體帳戶，並在目前的訂用帳戶中為此帳戶指派參與者角色。 您可以將此設定變更為擁有者或任何其他角色。 如需詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
* 在指定的自動化帳戶中，建立名為 AzureRunAsCertificate 的自動化憑證資產。 憑證資產會保存 Azure AD 應用程式所使用的憑證私密金鑰。
* 在指定的自動化帳戶中，建立名為 AzureRunAsConnection 的自動化連線資產。 連線資產會保存 applicationId、tenantId、subscriptionId 和憑證指紋。

**若為傳統執行身分帳戶：**

* 在指定的自動化帳戶中，建立名為 AzureClassicRunAsCertificate 的自動化憑證資產。 憑證資產會保存管理憑證所使用的憑證私密金鑰。
* 在指定的自動化帳戶中，建立名為 AzureClassicRunAsConnection 的自動化連線資產。 連線資產會保存訂用帳戶名稱、subscriptionId 和憑證資產名稱。

>[!NOTE]
> 如果您選取任一選項來建立傳統執行方式帳戶，在指令碼執行之後，請將公開憑證 (.cer 副檔名) 上傳至自動化帳戶建立所在之訂用帳戶的管理存放區中。
> 

若要執行指令碼並上傳憑證，請執行下列作業︰

1. 將下列指令碼儲存到電腦。 在此範例中，請以檔案名稱 *New-RunAsAccount.ps1*進行儲存。

        #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. 在電腦上按一下 [啟動]，然後以提高的使用者權限啟動 **Windows PowerShell**。

3. 從提高權限的 PowerShell 命令列殼層，移至包含您在步驟 1 中建立的指令碼的資料夾。

4. 使用所需設定的參數值來執行指令碼。

    **使用自我簽署憑證建立執行身分帳戶**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **使用企業憑證建立執行身分帳戶和傳統執行身分帳戶**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > 指令碼執行之後，您會收到向 Azure 進行驗證的提示。 請以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。
    >
    >

指令碼執行成功之後，請注意下列事項︰
* 如果您使用自我簽署的公開憑證 (.cer 檔案) 建立了傳統執行身分帳戶，指令碼會建立該帳戶並將其儲存在電腦上的暫存檔案資料夾中，用來執行 PowerShell 工作階段的使用者設定檔 %USERPROFILE%\AppData\Local\Temp 底下。
* 如果您使用企業公開憑證 (.cer 檔案) 建立了傳統執行身分帳戶，請使用此憑證。 請遵循[將管理 API 憑證上傳至 Azure 傳統入口網站](../azure-api-management-certs.md)的指示，然後使用[用來向服務管理資源進行驗證的程式碼範例](#sample-code-to-authenticate-with-service-management-resources)來驗證服務管理資源的認證組態。 
* 如果您「並未」建立傳統執行身分帳戶，請使用[用來向服務管理資源進行驗證的程式碼範例](#sample-code-to-authenticate-with-resource-manager-resources)向 Resource Manager 資源進行驗證並驗證認證組態。

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>用來向 Resource Manager 資源進行驗證的範例程式碼
您可以使用下列已更新的範例程式碼 (取自 AzureAutomationTutorialScript 範例 Runbook)，以執行身分帳戶進行驗證來使用 Runbook 管理 Resource Manager 資源。

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }

為了協助您輕鬆地在多個訂用帳戶之間進行工作，指令碼另外包含兩行程式碼來支援訂用帳戶內容的參考。 名為 SubscriptionId 的變數資產包含訂用帳戶的識別碼。 在 `Add-AzureRmAccount` Cmdlet 陳述式後面，[`Set-AzureRmContext`](/powershell/module/azurerm.profile/set-azurermcontext) Cmdlet 會以參數集 -SubscriptionId 來指定。 如果變數名稱太過一般，您可加以修改使其包含前置詞，或使用其他命名慣例讓名稱能夠更容易地識別。 或者，您可以使用參數集 -SubscriptionName (而非 -SubscriptionId) 與對應的變數資產。

Runbook 中用來驗證的 Cmdlet (`Add-AzureRmAccount`) 會使用 ServicePrincipalCertificate 參數集。 它藉由使用服務主體憑證 (而非使用者認證) 進行驗證。

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>用來向服務管理資源進行驗證的範例程式碼
您可以使用下列已更新的範例程式碼 (取自 AzureClassicAutomationTutorialScript Runbook 範例)，使用傳統執行身分帳戶進行驗證以使用 Runbook 管理傳統資源。

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>後續步驟
* [Azure AD 中的應用程式和服務主體物件](../active-directory/active-directory-application-objects.md)
* [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)
* [Azure 雲端服務的憑證概觀](../cloud-services/cloud-services-certs-create.md)

