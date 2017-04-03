---
title: "使用封包擷取搭配 Azure Functions 進行主動式網路監視 | Microsoft Docs"
description: "本文說明如何使用 Azure 網路監看員建立警示觸發的封包擷取"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5f31b24a0d46b9d557a55b3c9d0cd7748ecb9c33
ms.lasthandoff: 03/21/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>使用封包擷取搭配 Azure Functions 進行主動式網路監視

網路監看員封包擷取可建立擷取工作階段來追蹤虛擬機器的流入和流出流量。 擷取檔案可以有定義來只追蹤您想要監視之流量的篩選。 接著，此資料會儲存在儲存體 blob 或本機客體機器上。 這項功能可以從其他的自動化案例遠端啟動，例如 Azure Functions。 封包擷取會根據定義的網路異常狀況提供執行主動擷取的功能。 其他用途包括收集網路統計資料、取得有關網路入侵的資訊，以及偵錯用戶端與伺服器間的通訊等等。

在 Azure 中部署的資源為 24/7 執行。 您或您的人員無法 24/7 主動監視所有資源的狀態。 如果凌晨 2 點發生問題，會發生什麼事？

藉由使用 Azure 生態系統內的網路監看員、警示及函式，您可以主動使用資料和工具回應網路中的問題以解決問題。

## <a name="before-you-begin"></a>開始之前

在此範例中，您的 VM 正在比平常傳送更多個 TCP 區段，且您想要接獲通知。 會使用 TCP 區段做為範例，您可以使用任何警示的條件。 當您收到警示時，您需要有封包層級的資料，以了解通訊增加的原因，以便您可以採取步驟，讓機器回復到一般的通訊。
此案例假設您有現有的網路監看員執行個體，並且具有可供使用的有效虛擬機器的資源群組。

## <a name="scenario"></a>案例

若要自動化此程序，我們會在 VM 上建立並連線警示，以在事件發生時觸發，以及呼叫網路監看員的 Azure 函式。

此案例：

* 建立啟動封包擷取的 Azure 函式。
* 在虛擬機器上建立警示規則
* 設定警示規則以便呼叫 Azure 函式

## <a name="creating-an-azure-function-and-overview"></a>建立 Azure 函式與概觀

第一個步驟是建立 Azure 函式來處理警示，以及建立封包擷取。 

下列清單是所進行工作流程的概觀。

1. 會在您的 VM 上觸發警示。
1. 警示會透過 webhook 呼叫您的 Azure 函式。
1. 您的 Azure 函式會處理警示，並啟動網路監看員封包擷取工作階段。
1. 封包擷取會在 VM 上執行並收集流量。 
1. 擷取檔案會上傳至儲存體帳戶以供您檢閱和診斷 

遵循[建立您的第一個 Azure 函式](../azure-functions/functions-create-first-azure-function.md)，可在入口網站中建立 Azure 函式。 在此範例中，我們選擇 HttpTrigger-PowerShell 類型函式。 此範例需要進行自訂，下列步驟會有相關說明︰

![函式範例][functions1]

> [!NOTE]
> PowerShell 範本是實驗性功能，因此並未擁有完整支援。

## <a name="adding-modules"></a>新增模組

若要使用網路監看員 PowerShell Cmdlet，您必須將最新的 PowerShell 模組上傳至函式應用程式。

1. 在已安裝最新 Azure PowerShell 模組的本機電腦上，執行下列 PowerShell 命令︰

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    這可讓您獲得 Azure PowerShell 模組的本機路徑。 在接下來的步驟中會用到這些資料夾。 此案例中使用的模組有︰

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![powershell 資料夾][functions5]

1. 瀏覽至 [函式應用程式設定] > [前往 App Service 編輯器]。

    ![函式 kudu][functions2]

1. 以滑鼠右鍵按一下 AlertPacketCapturePowershell 資料夾，並建立名為 **azuremodules** 的資料夾。 繼續為每個所需模組建立子資料夾。

    ![函式 kudu][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. 以滑鼠右鍵按一下 **AzureRM.Network** 子資料夾，然後按一下 [上傳檔案]。 瀏覽至 Azure 模組的安裝位置，然後在本機 AzureRM.Network 資料夾中，選取其中的所有檔案，然後按一下 [確定]。  針對 AzureRM.Profile 和 AzureRM.Resources 重複這些步驟。

    ![上傳檔案][functions6]

1. 完成時，每個資料夾應該都會有來自本機電腦的 PowerShell 模組檔案。

    ![PowerShell 檔案][functions7]

## <a name="authentication"></a>驗證

若要使用 PowerShell Cmdlet，您必須進行驗證。 函式應用程式中必須設定驗證機制。 為此，您必須設定環境變數，並將加密金鑰檔案上傳至函式應用程式。

> [!note]
> 此案例只是提供了其中一種如何對 Azure Functions 實作驗證的範例，能夠執行這項操作的方法不只這一種。

### <a name="encrypted-credentials"></a>加密的認證

下列 PowerShell 指令碼會建立名為 **PassEncryptKey.key** 的金鑰檔，並提供所提供密碼的加密版本。  此密碼和為了讓 Azure AD 應用程式能夠進行驗證而定義的密碼相同。

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

在函式應用程式的 App Service 編輯器中，於 **AlertPacketCapturePowerShell** 底下建立一個稱為 **keys** 的資料夾，並上傳前面的 PowerShell 範例所建立的 **PassEncryptKey.key** 檔案。

![函式金鑰][functions8]

### <a name="retrieving-values-for-environment-variables"></a>擷取環境變數的值

所需進行的最後設定是設定所需的環境變數，以存取驗證值。 以下是所建立之環境變數的清單。

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

用戶端識別碼是 Azure Active Directory 中應用程式的應用程式識別碼。

1. 如果您還沒有要使用的應用程式，請執行下列範例來建立應用程式。

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > 建立應用程式時所使用的密碼，應該和稍早在儲存金鑰檔案時所建立的密碼相同。

1. 在 Azure 入口網站中，瀏覽至 [訂用帳戶] > 選擇要使用的訂用帳戶 > 存取控制 (IAM)。

    ![函式 iam][functions9]

1. 選擇要使用的帳戶，然後按一下 [屬性]。 複製應用程式識別碼。

    ![函式應用程式識別碼][functions10]

#### <a name="azuretenant"></a>AzureTenant

執行下列 PowerShell 範例即可取得租用戶識別碼︰

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

AzureCredPassword 環境變數的值，是執行下列 PowerShell 範例所得到的值。 這個範例和前面的**加密的認證**一節所顯示的範例相同。 所需的值是 `$Encryptedpassword` 變數的輸出。  這是我們使用 PowerShell 指令碼所加密的服務主體密碼。

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="storing-the-environment-variables"></a>儲存環境變數

1. 瀏覽至函式應用程式，然後按一下 [函式應用程式設定] > [設定應用程式設定]

    ![設定應用程式設定][functions11]

1. 將環境變數及其值新增至應用程式設定，然後按一下 [儲存]

    ![app settings][functions12]

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>處理警示和啟動封包擷取工作階段

現在是時候從 Azure 函式中對網路監看員進行呼叫了。 根據需求，此函式的實作會有所不同。 不過，程式碼的一般流程是這類︰

1. 處理程序輸入參數
2. 查詢現有封包擷取會驗證限制，並且解決名稱衝突
3. 使用適當的參數建立封包擷取
4. 定期輪詢封包擷取直到完成
5. 通知使用者封包擷取工作階段已完成

下列範例是可在 Azure 函式中使用的 PowerShell。 subscriptionId、resourceGroupName 和 storageAccountName 的值需要更換。

```powershell
#Import Azure PowerShell modules required to make calls to Network Watcher
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

#Process Alert Request Body
$requestBody = Get-Content $req -Raw | ConvertFrom-Json

#Storage Account Id to save captures in
$storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

#Packet Capture Vars
$packetcapturename = "PSAzureFunction"
$packetCaptureLimit = 10
$packetCaptureDuration = 10

#Credentials
$tenant = $env:AzureTenant
$pw = $env:AzureCredPassword
$clientid = $env:AzureClientId
$keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

#Authentication
$secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
$credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
Add-AzureRMAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


#Get the VM that fired the Alert
if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
{
    Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
    Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
    Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
    Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

    #Get the Network Watcher in the VM's Region
    $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
    $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

    #Get existing packetCaptures
    $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

    #Remove existing packet capture created by the function if it exists
    $packetCaptures | %{if($_.Name -eq $packetCaptureName)
    { 
        Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
    }}

    #Initiate Packet Capture on the VM that fired the alert
    if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
        echo "Initiating Packet Capture"
        New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
        Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
    }
} 
``` 

一旦建立您的函式後，您需要設定您的警示以呼叫與函式相關聯的 URL。 若要取得此值，請按一下 [</> 取得函式 URL] 

![尋找函式 url 1][functions13]

複製應用程式函式的函式 URL。

![尋找函式 url 2][2]

如果您在 Webhook POST 要求的承載中需要自訂屬性，請參閱[針對 Azure 度量警示設定 Webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="configure-an-alert-on-a-vm"></a>在 VM 上設定警示

警示可以設定為當特定計量超出指派給它的臨界值時通知個人。 在此範例中，警示會在 TCP 區段上傳送，但可針對許多其他計量觸發警示。 在此範例中，警示會設定為呼叫 webhook 來呼叫函式。

### <a name="create-the-alert-rule"></a>建立警示規則

瀏覽至現有的虛擬機器，並新增警示規則。 如需設定警示相關的詳細文件，請參閱[建立 Azure 服務警示的使用者 Azure 入口網站](../monitoring-and-diagnostics/insights-alerts-portal.md)。 

![新增 VM 警示規則至虛擬機器][1]

> [!NOTE]
> 預設不會啟用計量。 深入了解如何啟用新增計量，請造訪[啟用監視和診斷](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)

最後從上一個步驟將 URL 貼製警示的 [webhook] 文字方塊中。 按一下 [確定] 以儲存警示規則。

![將 URL 貼到警示規則][3]

## <a name="downloading-and-viewing-the-capture-file"></a>下載和檢視擷取檔案

如果您將擷取儲存至儲存體帳戶，則擷取檔案可透過入口網站或以程式設計的方式下載。 如果擷取檔案儲存在本機，可藉由登入虛擬機器來擷取檔案。 

如需從 Azure 儲存體帳戶下載檔案的指示，請參閱[以 .NET 開始使用 Azure Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)。 另一項可用工具為儲存體總管。 有關儲存體總管的詳細資訊可以在下列連結找到︰[儲存體總管](http://storageexplorer.com/)

一旦下載擷取後，您可以使用可讀取 **.cap** 檔案的任何工具來檢視它。 以下是兩個工具的連結︰

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>後續步驟

瀏覽[使用 Wireshark 的封包擷取分析](network-watcher-alert-triggered-packet-capture.md)，了解如何檢視封包擷取

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
