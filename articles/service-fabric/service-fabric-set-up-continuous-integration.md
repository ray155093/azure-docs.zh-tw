<properties
   pageTitle="Service Fabric 的連續整合 |Microsoft Azure"
   description="取得如何使用 Visual Studio Team Services (VSTS) 設定 Service Fabric 應用程式之持續整合的概觀。"
   services="service-fabric"
   documentationCenter="na"
   authors="mthalman-msft"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/29/2016"
   ms.author="mthalman" />

# 使用 Visual Studio Team Services 為 Service Fabric 應用程式設定持續整合

本文說明使用 Visual Studio Team Services (VSTS) 為 Azure Service Fabric 應用程式設定持續整合的步驟，以確保能夠自動建置、封裝及部署您的應用程式。請注意，這些指示每次都會從頭重新建立叢集。

這份文件反映目前的程序，而且應該隨著時間而改變。

## 必要條件

開始時，請先在 Visual Studio Team Services 上設定您的專案：

1. 如果您尚未建立 Team Services 帳戶，請使用您的 [Microsoft 帳戶](http://www.microsoft.com/account)設定它。

2. 使用 Microsoft 帳戶在 Team Services 上建立新的專案。

3. 將新的或現有 Service Fabric 應用程式的原始檔推送至這個專案。

如需使用 Team Services 專案的詳細資訊，請參閱[連接到 Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

## 設定服務主體

### 設定自動化驗證

您必須先建立組建代理程式用來向 Azure 進行驗證的[服務主體](../resource-group-create-service-principal-portal.md)，才能設定組建電腦。您也需要建立憑證，並將其上載至 Azure 金鑰保存庫，因為金鑰保存庫不支援服務主體驗證。您可以從任何電腦執行這些步驟。您的開發電腦是不錯的選擇。

### 安裝 Azure PowerShell 並登入

1.  安裝 PowerShellGet。

    a.如果您執行具有最新更新的 Windows 10，您可以略過此步驟 (已經安裝 PowerShellGet)。

    b.如果不是，請安裝 [Windows Management Framework 5.0](https://aka.ms/wmf5download)，其中包括 PowerShellGet。

2.	安裝和更新 AzureRM 模組。如果您已安裝任何舊版的 Azure PowerShell，請將它移除：

    a.以滑鼠右鍵按一下 [開始] 按鈕，然後選取 [新增/移除程式]。

    b.搜尋 "Azure PowerShell" 並將它解除安裝。

    c.以系統管理員身分開啟 PowerShell 命令提示字元。

    d.使用命令 `Install-Module AzureRM` 安裝 AzureRM 模組。

3.	停用 (或啟用) Azure 資料收集。

    Azure Cmdlet 會提示您選擇開啟或關閉資料收集，直到您選擇為止。這些提示在等候使用者輸入時將會封鎖自動化。若要隱藏這些提示，請執行下列其中一個命令來事先選擇：

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	登入 Azure PowerShell：

    a.執行命令 `Login-AzureRmAccount`。

    b.在顯示的對話方塊中，輸入您的 Azure 認證。

    c.執行命令 `Get-AzureRmSubscription`。

    d.找出您想要使用的訂用帳戶。

    e.執行命令 `Select-AzureRmSubscription -SubscriptionId <ID for your subscription>`。

### 建立服務主體

1. 請遵循[這些指示](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)，為您的專案建立服務主體和服務端點。

2. 請注意，值會列印在指令碼的輸出結尾。您需要這些值才能設定組建定義。

### 建立憑證並將它上傳到新的 Azure 金鑰保存庫

>[AZURE.NOTE] 此範例指令碼會產生自我簽署的憑證，這並非安全做法，僅有實驗時可以接受。請遵循組織的指導方針，改為取得合法的憑證。這些指示也會針對伺服器和用戶端使用單一憑證。在生產環境中，您應該使用不同的伺服器和用戶端憑證。

1. 下載並解壓縮 [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) 到這部電腦上的資料夾。

2. 在系統管理 PowerShell 命令提示字元中，將目錄變更為 `<extracted zip>/Manual`。

3. 以下列參數執行 PowerShell 指令碼 `CreateAndUpload-Certificate.ps1`：

| 參數 | 值 |
| --- | --- |
| KeyVaultLocation | 任何值。這個參數必須符合您打算建立叢集的位置。 |
| CertificateSecretName | 任何值。 |
| CertificateDnsName | 必須符合您的叢集的 DNS 名稱。範例：`mycluster.westus.cloudapp.azure.com` |
| SecureCertificatePassword | 任何值。當您在組建電腦上匯入憑證時會使用這個參數。 |
| KeyVaultName | 任何值。 |
| KeyVaultResourceGroupName | 任何值。不過，不要使用您計劃用於您的叢集的資源群組名稱。 |
| PfxFileOutputPath| 任何值。此檔案是用來將憑證匯入您的組建電腦。 |

當指令碼完成時，它會輸出下列三個值。請記下這些值，因為它們會用來做為組建變數。

 - `ServiceFabricCertificateThumbprint`
 - `ServiceFabricKeyVaultId`
 - `ServiceFabricCertificateSecretId`

## 設定組建電腦

### 安裝 Visual Studio 2015

如果您已經佈建電腦 (或打算提供您自己的電腦)，請在選取的電腦上安裝 [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)。

如果您還沒有電腦，您可以快速佈建一個預先安裝 Visual Studio 2015 的 Azure 虛擬機器 (VM)。作法：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取畫面左上角的 [新增] 命令。

3. 選取 [Marketplace]。

4. 搜尋 **Visual Studio 2015**。

5. 依序選取 [計算] -> [虛擬機器] -> [從資源庫]。

6. 選取映像 **Visual Studio Enterprise 2015 Update 2 with Universal Windows Tools 和 Azure SDK 2.9 on Windows Server 2012 R2**。

    >[AZURE.NOTE] Azure SDK 不是必要的元件，但是目前沒有任何可用的映像僅安裝 Visual Studio 2015。

7.	依照對話方塊中的指示建立您的 VM。

### 安裝 Service Fabric SDK

在您的電腦上安裝 [Service Fabric SDK](service-fabric-get-started.md#install-the-runtime-sdk-and-tools)。

### 安裝 Azure PowerShell

若要安裝 Azure PowerShell，請依照上一節「安裝 Azure PowerShell 並登入」中的步驟進行。略過「登入 Azure PowerShell」步驟。

### 以網路服務帳戶註冊 Azure PowerShell 模組

>[AZURE.NOTE] 執行這項操作，然後啟動組建代理程式。否則，它不會挑選新的環境變數。

1. 按 Windows 標誌鍵 + R、鍵入 **regedit**，然後按 Enter。

2. 以滑鼠右鍵按一下節點 `HKEY_Users\.Default\Environment`，然後選取 [新增] > [可擴充字串值]。

3. 輸入 `PSModulePath` 作為名稱，輸入 `%PROGRAMFILES%\WindowsPowerShell\Modules` 作為值。將 `%PROGRAMFILES%` 替換成 `PROGRAMFILES` 環境變數的值。

### 匯入自動化憑證

1.	將憑證匯入您的組建電腦。作法：

    a.將指令碼 CreateAndUpload-Certificate.ps1 建立的 PFX 檔案複製到您的組建電腦。

    b.開啟系統管理 PowerShell 命令提示字元，使用您稍早傳遞至 `CreateAndUpload-Certificate.ps1` 的密碼執行下列命令。

    ```powershell
    $password = Read-Host -AsSecureString
    Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
    ```

2.	執行憑證管理員。

    a.開啟 Windows 中的 [控制台]。以滑鼠右鍵按一下 [開始] 按鈕，然後選取 [控制台]。

    b.搜尋**憑證**。

    c.選取 [系統管理工具] > [管理電腦憑證]。

3.	授與網路服務帳戶權限以使用自動化憑證：

    a.在 [憑證 - 本機電腦] 底下，展開 [個人]，然後選取 [憑證]。

    b.在清單中尋找憑證。

    c.以滑鼠右鍵按一下您的憑證，然後選取 [所有工作] > [管理私密金鑰]。

    d.選取 [新增] 按鈕、輸入 [網路服務]，然後選取 [檢查名稱]。

    e.選取 [確定]。

    ![授與本機服務帳戶權限之步驟的螢幕擷取畫面](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

4.  將憑證複製到 `Trusted People` 資料夾。

    a.您的憑證已匯入 [個人/憑證]，但我們必須將它加入至 [受信任的人]。在憑證上按一下滑鼠右鍵，然後選取 [複製]。然後以滑鼠右鍵按一下 [受信任的人] 資料夾，接著選取 [貼上]。

### 註冊組建代理程式

1.	下載 agent.zip。作法：

    a.登入您的小組專案，例如 **https://[your-VSTS-account-name].visualstudio.com**。

    b.選取畫面右上角的齒輪圖示。

    c.選取 [代理程式集區] 索引標籤。

    d.選取 [下載代理程式] 以下載 agent.zip 檔案。

    >[AZURE.NOTE] 如果未開始下載，請檢查您的快顯封鎖程式。

    e.將 agent.zip 複製到您稍早建立的組建電腦。

    f.將 agent.zip 解壓縮至組建電腦上的 `C:\agent` (或具有簡短路徑的任何位置)。

    >[AZURE.NOTE] 如果您打算使用 ASP.NET 5 Web 服務，建議您為此資料夾選擇可能的最短名稱，以避免在部署期間發生 **PathTooLongExceptions** 錯誤。當 ASP.NET Core 發行時，即可緩和這個問題。

2.	從系統管理命令提示字元中，執行 `C:\agent\ConfigureAgent.cmd`。指令碼會提示您下列參數：

|參數|值|
|---|---|
|代理程式名稱|接受預設值，`Agent-[machine name]`。|
|TFS Url|輸入您的小組專案的 URL，例如 `https://[your-VSTS-account-name].visualstudio.com`。|
|代理程式集區|輸入代理程式集區的名稱。(如果您尚未建立代理程式集區，接受預設值。)|
|工作資料夾|接受預設值。這是組建代理程式實際建置您的應用程式的資料夾。如果您打算使用 ASP.NET 5 Web 服務，建議您為此資料夾選擇可能的最短名稱，以避免在部署期間發生 PathTooLongExceptions 錯誤。|
|安裝為 Windows 服務？|預設值為 N。將值變更為 **Y**。|
|執行服務的使用者帳戶|預設值為 `NT AUTHORITY\LOCAL SERVICE`。將預設值變更為 `NT AUTHORITY\NetworkService`。|
|`NT AUTHORITY\Network Service` 的密碼|網路服務帳戶沒有密碼，但會拒絕空白密碼。輸入任何非空白字串的密碼 (將會忽略您所輸入的資料)。|
|取消設定現有的代理程式？|接受預設值，**N**。|

3.  當系統提示您輸入認證時，請輸入具有您的小組專案權限的 Microsoft 帳戶的認證。

4.  請確認您的組建代理程式已註冊並設定其功能。作法：

    a.返回網頁瀏覽器 (`https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`)，重新整理頁面。

    b.選取您稍早執行 ConfigureAgent.ps1 時選取的代理程式集區。

    c.確認您的組建代理程式出現在清單中，並且具有綠色狀態反白顯示。若強調顯示是紅色，表示組建代理程式在連接到 Team Services 時發生問題。

    ![顯示組建代理程式之狀態的螢幕擷取畫面](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)

    d.選取組建代理程式，然後選取 [功能] 索引標籤。

    e.新增名為 **azureps** 的功能和任何值。這對 VSTS 表示這部電腦上已安裝 Azure PowerShell，而需有該模組才能使用 VSTS 提供的某些組建工作。


## 建立組建定義

>[AZURE.NOTE] 您根據這些指示建立的組建定義並不支援多個平行組建，即使是在不同的電腦上。這是因為每個組建會競用相同資源群組/叢集。如果您想要執行多個組建代理程式，您必須修改下列指示/指令碼來防止這種干擾。

### 將 Service Fabric Azure Resource Manager 範本新增至您的應用程式

1. 從[此範例](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)下載 `azuredeploy.json` 和 `azuredeploy.parameters.json`。

2. 開啟 `azuredeploy.parameters.json`，然後編輯下列參數︰

    |參數|值|
    |---|---|
    |clusterLocation|必須符合您的金鑰保存庫的位置。範例：`westus`|
    |clusterName|必須符合您憑證的 DNS 名稱。例如，如果憑證的 DNS 名稱是 `mycluster.westus.cloudapp.net`，則 `clusterName` 必須是 `mycluster`。|
    |adminPassword|8-123 個字元，至少包含下列其中 3 個類型的字元︰大寫、小寫、數字、特殊字元。|
    |certificateThumbprint|來自 `CreateAndUpload-Certificate.ps1` 的輸出|
    |sourceVaultValue|來自 `CreateAndUpload-Certificate.ps1` 的輸出|
    |certificateUrlvalue|來自 `CreateAndUpload-Certificate.ps1` 的輸出|

3. 將新檔案新增至原始檔控制，並推送到 VSTS。

### 建立組建定義

1.	建立空白組建定義。作法：

    a.在 Visual Studio Team Services 中開啟您的專案。

    b.選取 [組建] 索引標籤。

    c.選取綠色 **+** 符號以建立新的組建定義。

    d.選取 [空白]，然後選取 [下一步]。

    e.確認已選取正確的儲存機制和分支。

    f.核取 [連續整合] 核取方塊，以確保每次更新分支時都會觸發這個組建。

    g.選取已註冊組建代理程式的代理程式佇列。

2.	在 [變數] 索引標籤上，使用這些值建立下列變數。

    |變數|值|Secret|在佇列時間允許|
    |---|---|---|---|
    |BuildConfiguration|發行||X|
    |BuildPlatform|x64||||

3.  儲存組建定義並且給予名稱。您想要的話可以在稍後變更此名稱。

### 新增「還原 NuGet 封裝」步驟

1. 在 [組建] 索引標籤中，選擇 [新增組建步驟…] 命令。

2. 選擇 [封裝] > [NuGet 安裝程式]。

3. 選擇組建步驟的名稱旁的鉛筆圖示，並且將其重新命名為 [還原 NuGet 封裝]。

4. 選擇 [解決方案] 欄位旁的 [...] 按鈕，然後選擇 .sln 檔案。

5. 儲存組建定義。

### 新增「組建」步驟

1.	在 [組建] 索引標籤上，選取 [新增組建步驟] 命令。

2.	選取 [組建] > [MSBuild]。

3.	選取組建步驟的名稱旁的鉛筆圖示，並且將其重新命名為 [組建]。

4. 選取下列值：

    |設定名稱|值|
    |---|---|
    |方案|按一下 [...] 按鈕，然後選取您方案的 `.sln` 檔案。|
    |平台|`$(BuildPlatform)`|
    |組態|`$(BuildConfiguration)`|

5.	儲存組建定義。

### 新增「封裝」步驟

1.	在 [組建] 索引標籤上，選取 [新增組建步驟] 命令。

2.	選取 [組建] > [MSBuild]。

3.	選取組建步驟的名稱旁的鉛筆圖示，然後將其重新命名為 [封裝]。

4. 選取下列值：

    |設定名稱|值|
    |---|---|
    |方案|按一下 [...] 按鈕，然後選取您應用程式專案的 `.sfproj` 檔案。|
    |平台|`$(BuildPlatform)`|
    |組態|`$(BuildConfiguration)`|
    |MSBuild 引數|`/t:Package`|

5.	儲存組建定義。

### <a name="RemoveClusterResourceGroup"></a>加入「移除叢集資源群組」步驟

如果前一個組建未在本身之後清除 (例如，如果組建已在可以清除之前取消)，可能會留下現有的資源群組而與新的資源群組發生衝突。若要避免衝突，在建立新的資源群組之前，清除任何剩餘的資源群組 (和其相關聯的資源)。

1.	在 [組建] 索引標籤上，選取 [加入建置步驟...] 命令。

2.	選取 [部署] > [Azure 資源群組部署]。

3.	選取組建步驟的名稱旁的鉛筆圖示，然後將其重新命名為 [移除叢集資源群組]。

4. 選取下列值：

    |設定名稱|值|
    |---|---|
    |AzureConnectionType|**Azure Resource Manager**|
    |Azure RM 訂用帳戶|選取您在**建立服務主體**一節中建立的連接端點。|
    |動作|**刪除資源群組**|
    |資源群組|輸入任何未使用的名稱。您必須在下一個步驟中使用相同的名稱。|
    |發生錯誤時繼續|如果沒有資源群組，這個步驟就會失敗。啟用 [控制選項] 區段的 [發生錯誤時繼續] 以避免這種情況。|

5.	儲存組建定義。

### 新增「佈建安全叢集」步驟

1.	在 [組建] 索引標籤上，選取 [加入建置步驟...] 命令。

2.	選取 [部署] > [Azure 資源群組部署]。

3.	選取組建步驟名稱旁的鉛筆圖示，然後將其重新命名為 [佈建安全叢集]。

4. 選取下列值：

    |設定名稱|值|
    |---|---|
    |AzureConnectionType|**Azure Resource Manager**|
    |Azure RM 訂用帳戶|選取您在**建立服務主體**一節中建立的連接端點。|
    |動作|**建立或更新資源群組**|
    |資源群組|必須符合您在前一個步驟中使用的名稱。|
    |位置|必須符合您的金鑰保存庫的位置。|
    |範本|按一下 [...] 按鈕，然後選取 `azuredeploy.json`。|
    |範本參數|按一下 [...] 按鈕，然後選取 `azuredeploy.parameters.json`。|

5.	儲存組建定義。

### 新增「部署」步驟

1.	在 [組建] 索引標籤上，選取 [加入建置步驟...] 命令。

2.	選取 [公用程式] > [PowerShell]。

3.	選取組建步驟名稱旁的鉛筆圖示，然後將其重新命名為 [部署]。

4. 選取下列值：

    |設定名稱|值|
    |---|---|
    |類型|**檔案路徑**|
    |指令碼檔名|按一下 [...] 按鈕，然後瀏覽至應用程式專案內的 [指令碼] 目錄。選取 `Deploy-FabricApplication.ps1`。|
    |引數|`-PublishProfileFile path/to/MySolution/MyApplicationProject/PublishProfiles/MyPublishProfile.xml -ApplicationPackagePath path/to/MySolution/MyApplicationProject/pkg/$(BuildConfiguration)`|

5.	儲存組建定義。

### 加入「驗證」步驟

1. 當您第一次設定這個組建定義時，這個步驟是選擇性的。但是，只要成功執行組建並確保其他建置步驟正確，您就可以在這裡插入自己的驗證建置步驟。這會是應用程式專用的，也是確認已部署在叢集中的應用程式正確性。
  
### 加入最後的「清除」步驟

1. 遵循[加入「移除叢集資源群組」步驟](#RemoveClusterResourceGroup)中的相同指示。這會清除所有在建置期間所建立的已佈建 Azure 資源。

### 試試看

選取 [佇列組建] 來啟動組建。推送或簽入時也會觸發組建。

## 替代解決方案

先前的指示會為每個組建建立新叢集，並且在組建的結尾移除。如果您想要改為讓每個組建執行應用程式升級 (至現有的叢集)，請使用下列步驟：

1.	遵循[這些指示](service-fabric-cluster-creation-via-portal.md)，透過 Azure 入口網站或 Azure PowerShell 手動建立測試叢集。

2.	藉由遵循[這些指示](service-fabric-visualstudio-configure-upgrade.md)，設定發佈設定檔以支援應用程式升級。

4.	從您的組建定義移除 [移除叢集資源群組] 和 [佈建叢集] 組建步驟。

## 後續步驟

若要深入了解與 Service Fabric 應用程式的連續整合，請閱讀下列文章：

 - [建置文件首頁](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
 - [部署組建代理程式](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
 - [建立和設定組建定義](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0518_2016-->