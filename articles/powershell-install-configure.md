---
title: "如何安裝和設定 Azure PowerShell"
description: "了解如何安裝和設定 Azure PowerShell。"
editor: tysonn
manager: dongill
documentationcenter: 
services: 
author: coreyp-at-msft
ms.assetid: 802b28e0-1a8c-4872-a9ea-c889b15b9498
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: coreyp
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f20adb27f41cdbd6918340ae688b4ce00dd2d593


---
# <a name="how-to-install-and-configure-azure-powershell"></a>如何安裝和設定 Azure PowerShell
<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

## <a name="what-is-azure-powershell"></a>什麼是 Azure PowerShell？
Azure PowerShell 是一套模組，提供各種 Cmdlet，讓您透過 Windows PowerShell 管理 Azure。 您可以使用 Cmdlet 來建立、測試、部署和管理透過 Azure 平台傳遞的解決方案和服務。 在大部分情況下，Cmdlet 可用於與 Azure 入口網站中相同的工作，例如建立和設定雲端服務、虛擬機器、虛擬網路和 Web Apps。

## <a name="how-versioning-works"></a>版本控制的運作方式
Azure PowerShell 使用語意版本設定，這表示，如果版本 A > 版本 B，則版本 A 會有最新的 API。 此外，這表示主要版本中的變更代表一或多個 Cmdlet 中的重大變更。  因此，例如，版本 1.7.0 為可以解決 Azure PowerShell 1.x 版中重大變更的 hotfix。

如需 Azure PowerShell 中語意版本設定作法的詳細資訊，請參閱語意版本設定規格：http://semver.org

若要取得最新的 API，您應該使用版本 2.x。 但如果您有針對版本 1.x撰寫的指令碼，而不希望合併 2.x [版本資訊](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md)中所述的版本 2.x 的重大變更，則應該安裝 1.7.0。

如果已安裝設定檔模組的最新版本，並且後續載入相依於它的舊版模組時，可能會造成版本不相符。 解決此問題最簡單的方式是從最新的 .msi 安裝。 .msi 會自動清除舊版的模組。

### <a name="installing-module-versions-side-by-side"></a>並存安裝模組版本
2.1.0 版本 (AzureStack 為 版本 1.2.6) 是設計能並行安裝及使用的第一個模組版本。 因為 Azure PowerShell 使用二進位模組，您必須開啟新的 PowerShell 視窗，並使用 **Import-Module** 來匯入 AzureRM Cmdlet 的特定版本：

    Import-Module AzureRM -RequiredVersion 2.1.0**

2.1.0 之前的版本 (非 1.2.6) 無法與其他 Azure PowerShell 模組版本並存運作。 使用類似以上的命令載入較早版本的 Azure PowerShell 模組時，將會載入 **AzureRM.Profile** 模組不相容的版本，導致每當您執行 Cmdlet 時，Cmdlet 便要求您登入，即使登入之後亦然。

解決此問題的最簡單方法是從 WebPI 摘要或 .msi 安裝最新的 Azure PowerShell - 這會移除從資源庫安裝的舊版模組。 

請注意，Azure 和 AzureRM 模組有共通的相依項目，因此如果您使用這兩個模組，更新其中一個時，您應該更新兩者。 較早版本的 Azure 模組具有較早版本 AzureRM 模組所具有的並行模組載入的相同問題。

<a id="Install"></a>

## <a name="step-1-install"></a>步驟 1：安裝
以下是可以安裝 Azure PowerShell 的兩種方法。 您可以從 PowerShell 資源庫或 WebPI 進行安裝。

### <a name="installing-azure-powershell-from-the-powershell-gallery"></a>從 PowerShell 資源庫安裝 Azure PowerShell
慣用的方法是使用 PowerShell 資源庫。 您需要 PowerShellGet 模組才能使用 PowerShell 資源庫。 可在這裡取得：[PowerShellGallery.com](https://www.powershellgallery.com/)

以提高權限的 Windows PowerShell 或 PowerShell 整合式指令碼環境 (ISE) 的提示字元，使用下列命令從 PowerShell 資源庫安裝 Azure PowerShell 1.3.0 或更高版本︰

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

#### <a name="more-about-these-commands"></a>這些命令的詳細資訊
* **Install-Module AzureRM** 會為 Azure Resource Manager Cmdlet 安裝彙總套件模組。 AzureRM 模組基於 
* 每個 Azure Resource Manager 模組的特定版本範圍。 包含的版本範圍可以確保在安裝同一主要版本的 AzureRM 模組時，不含重大的模組變更。 當您安裝 AzureRM 模組時，會從 PowerShell 資源庫下載並安裝先前未安裝的所有 Azure Resource Manager 模組。 如需有關 Azure PowerShell 模組使用的語意版本設定的詳細資訊，請參閱 [semver.org](http://semver.org)。 
* **Install-Module Azure** 會安裝 Azure 模組。 此模組為 Azure PowerShell 0.9.x 中的服務管理模組。 這應該沒有任何主要變更，而且可與前一版的 Azure 模組互換。

### <a name="installing-azure-powershell-from-webpi"></a>從 WebPI 安裝 Azure PowerShell
從 WebPI 安裝 Azure PowerShell 1.0 或更新版本如同於安裝 0.9.x。 下載 [Azure Powershell](http://aka.ms/webpi-azps) 並開始安裝。 如果您已安裝 Azure PowerShell 0.9.x，升級將解除安裝 0.9.x 版。 如果您是從 PowerShell 資源庫安裝 Azure PowerShell 模組，安裝程式會在安裝前自動移除模組，以確保 Azure PowerShell 環境保持一致。

> [!NOTE]
> 如果您已從 PowerShell 資源庫安裝 Azure PowerShell 模組，安裝程式會自動移除這些模組。 這是為了防止混淆您已安裝的模組版本與其所在的位置。 PowerShell 資源庫模組通常會安裝在 **%ProgramFiles%\WindowsPowerShell\Modules**。 相反地，WebPI 安裝程式會將 Azure 模組安裝在 **%ProgramFiles(x86)%\Microsoft SDKs\Azure\PowerShell\**。如果安裝期間發生錯誤，請手動移除 **%ProgramFiles%\WindowsPowerShell\Modules** 資料來中的 Azure* 資料夾，再重試安裝。
> 
> 

一旦完成安裝，您的 ```$env:PSModulePath``` 設定中應會有包含 Azure PowerShell Cmdlet 的目錄。

> [!NOTE]
> 從 WebPI 安裝時，PowerShell **$env:PSModulePath** 會發生一個已知的問題。 如果您的電腦因為系統更新或其他安裝而需要重新啟動，可能會導致 **$env:PSModulePath** 更新不包含 Azure PowerShell 的安裝路徑。 如果發生這種情況，當您在安裝或升級之後嘗試使用 Azure PowerShell Cmdlet 時，您可能會看到「無法辨識 Cmdlet」訊息。 如果發生此情況，重新啟動電腦應該可修正問題。
> 
> 

如果您嘗試載入或執行 Cmdlet，會收到類似下列訊息︰

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

重新啟動機器或從 C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ 的路徑匯入 Cmdlet (其中 XXXX 是安裝的 PowerShell 版本：

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>步驟 2：啟動
您可以從標準的 Windows PowerShell 主控台，或是從 PowerShell 整合式指令碼環境 (ISE) 執行 Cmdlet。
開啟這些主控台的方法視您正在執行的 Windows 版本而定。

* 在至少執行 Windows 8 或 Windows Server 2012 的電腦上，可使用內建的 [搜尋]。 請在 [開始] 畫面中輸入 power。 這會傳回目標範圍內的應用程式清單，其中包括 Windows PowerShell。 若要開啟主控台，請按一下任一應用程式。 (如要將應用程式釘選到 [開始] 畫面，請用滑鼠右鍵按一下圖示。)
* 在執行比 Windows 8 或 Windows Server 2012 更舊版本的電腦上，使用 [開始] 功能表。 按一下 [開始] 功能表中的 [所有程式]，依序按一下 [附屬應用程式]、[Windows PowerShell] 資料夾，然後按一下 [Windows PowerShell]。

您也可以執行 **Windows PowerShell ISE** 以使用功能表項目和鍵盤快速鍵，來執行許多您會在 Windows PowerShell 主控台中執行的相同工作。 如要使用 ISE，請在 Windows PowerShell 主控台、Cmd.exe 或 [執行] 方塊中輸入 **powershell_ise.exe**。

### <a name="commands-to-help-you-get-started"></a>可協助您開始使用的命令
    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 

    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>步驟 3：連線
Cmdlet 需要您的訂用帳戶，使得它們可以管理您的服務。 如果您還沒有 Azure 訂用帳戶的話，可以購買一個。 如需指示，請參閱[如何購買 Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795)。

1. 輸入 **Login-AzureRmAccount**
2. 輸入與您帳戶相關聯的電子郵件地址和密碼。 Azure 會驗證並儲存認證資訊，然後關閉視窗。

--或--

登入您的工作或學校帳戶：

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [!NOTE]
> 如果您的組織帳戶有多個相關聯的租用戶，請指定 TenantId 參數：
> 
> 

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [!NOTE]
> 此非互動式登入方法僅適用於公司或學校帳戶。 公司或學校帳戶是由您的公司或學校所管理的使用者，並為您的公司或學校定義於 Azure Active Directory 執行個體中。 如果您目前沒有公司帳戶或學校帳戶，而是使用 Microsoft 帳戶登入您的 Azure 訂用帳戶，則您可以透過下列步驟輕鬆地建立帳戶。
> 
> 1. 登入 [Azure 傳統入口網站][](https://manage.windowsazure.com)，然後按一下 [Active Directory]。
> 2. 如果不存在任何目錄，請選取 [建立目錄] 並提供要求的資訊。
> 3. 選取您的目錄並新增使用者。 這個新的使用者可以使用公司或學校帳戶登入。 在建立使用者期間，系統會提供您該使用者的電子郵件地址與臨時密碼。 請儲存此資訊，在以下的步驟 5 將會用到。
> 4. 在 Azure 傳統入口網站中選取 [設定]，然後選取 [管理員]。 選取 [新增]，然後將新使用者新增為共同管理員。 這麼做可讓公司或學校帳戶管理您的 Azure 訂用帳戶。
> 5. 最後，登出 Azure 傳統入口網站，然後使用公司或學校帳戶重新登入。 如果這是您第一次使用此帳戶登入，系統會提示您變更密碼。
> 
> 如需使用公司或學校帳戶註冊 Microsoft Azure 的詳細資訊，請參閱[以組織身分註冊 Microsoft Azure](active-directory/sign-up-organization.md)。
> 
> 如需 Azure 中的驗證與訂用帳戶管理的詳細資訊，請參閱[管理帳戶、訂用帳戶和管理角色](http://go.microsoft.com/fwlink/?LinkId=324796)。
> 
> 

### <a name="view-account-and-subscription-details"></a>檢視帳戶和訂用帳戶詳細資料
您可以有多個帳戶和訂用帳戶供 Azure PowerShell 使用。 執行 **Add-AzureRmAccount** 數次就能新增多個帳戶。

若要顯示可用的 Azure 帳戶，請輸入 **Get-AzureAccount**。

若要顯示您的 Azure 訂用帳戶，請輸入 **Get-AzureRmSubscription**。

## <a name="a-idhelpagetting-help"></a><a id="Help"></a>取得說明
下列資源提供特定 Cmdlet 的說明：

* 從主控台中，您可以使用內建的說明系統。 **Get-Help** Cmdlet 可用來存取此系統。 
* 如需從社群獲得說明，請試試下列熱門論壇：
  
  * [MSDN 上的 Azure 論壇](http://go.microsoft.com/fwlink/p/?LinkId=320212)
  * [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

## <a name="learn-more"></a>詳細資訊
若要深入了解如何使用 Cmdlet，請參閱下列資源：

如需使用 Windows PowerShell 的基本指示，請參閱[使用 Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939)。

如需有關 Cmdlet 的參考資訊，請參閱 [Azure Cmdlet 參考](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx)。

如需可協助您了解如何使用指令碼來管理 Azure 的範例指令碼和指示，請參閱[指令碼中心](http://go.microsoft.com/fwlink/p/?LinkId=321940)。




<!--HONumber=Nov16_HO3-->


