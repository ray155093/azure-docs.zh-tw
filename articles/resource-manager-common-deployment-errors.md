<properties
   pageTitle="針對常見的 Azure 部署錯誤進行疑難排解 |Microsoft Azure"
   description="說明如何解決以 Azure Resource Manager 進行部署時的常見錯誤。"
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="tomfitz"/>

# 針對使用 Azure Resource Manager 將資源部署至 Azure 時常見的錯誤進行疑難排解

本主題說明如何解決將資源部署至 Azure 時一些可能會遇到的常見錯誤。希望您已經看過有用的錯誤訊息。如果您還沒看過好的錯誤訊息，或者您需要更多有關您的部署為何失敗的詳細資料，首先請參閱[檢視部署作業](resource-manager-troubleshoot-deployments-portal.md)，然後再回來本文章以取得解決錯誤的協助。

## 範本或資源無效

如果您收到錯誤指出範本或資源屬性無效，您的範本中可能有遺漏的字元。使用範本運算式時很容易造成此錯誤，因為運算式會包在引號中，所以 JSON 仍會驗證且您的編輯器可能無法偵測到此錯誤。例如，儲存體帳戶的下列名稱指派包含一組方括號、三個函式、三組圓括號、一組單引號和一個屬性︰

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

如果您未提供所有相符的語法，範本將會產生與您所要的值截然不同的值。

視遺漏的字元是否位於您的範本中而定，您會收到錯誤指出範本或資源無效。此錯誤也指出部署程序無法處理範本語言運算式。當您收到此類錯誤時，請仔細檢閱運算式語法。

## 資源名稱已經存在。

對於某些資源 (最顯著的儲存體帳戶、資料庫伺服器和網站)，您必須提供在整個 Azure 中是唯一的資源名稱。您可以將您的命名慣例與 [uniqueString](resource-group-template-functions.md#uniquestring) 函數的結果串連，以建立一個唯一名稱。
 
    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 

## 在部署期間找不到資源

如果可能，Resource Manager 會以平行方式建立資源，將部署最佳化。如果一個資源必須在另一個資源之後部署，您就必須在範本中使用 **dependsOn** 元素來建立與該另一個資源的相依性。例如，在部署 Web 應用程式時，App Service 方案必須存在。如果您沒有指定該 Web 應用程式與 App Service 方案相依， Resource Manager 會同時建立這兩個資源。您會收到錯誤訊息，指出找不到 App Service 方案的資源，因為嘗試在 Web 應用程式上設定屬性時它尚未存在。您可以設定 Web 應用程式的相依性，以避免此錯誤。

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

## 資源無法使用位置

指定資源的位置時，您必須使用其中一個支援此資源的位置。在您輸入資源的位置之前，請使用下列其中一個命令來確認該位置是否支援此資源類型。

### PowerShell

使用 **Get-AzureRmResourceProvider** 可取得特定資源提供者的支援類型和位置。

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

您會得到該資源提供者的資源類型清單。

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

您可以利用下列命令聚焦於特定類型的資源︰

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

它會傳回支援的位置︰

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Azure CLI

針對 Azure CLI，您可以使用 **azure location list**。因為位置清單可能很長，而且有多個提供者，所以您可以先使用工具來檢查提供者和位置，再使用尚未使用的位置。下列指令碼使用 **jq** 來探索可取得 Azure 虛擬機器之資源提供者的位置。

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    
它會傳回支援的位置︰
    
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### REST API

針對 REST API，請參閱[取得資源提供者的相關資訊](https://msdn.microsoft.com/library/azure/dn790534.aspx)。

## 超過配額

當部署超過配額時 (也許是每個資源群組、訂用帳戶、帳戶及其他範圍的配額)，可能會發生問題。例如，您的訂用帳戶可能設定為要限制區域的核心數目。如果您嘗試部署超過允許核心數目的虛擬機器，您會收到錯誤訊息指出已超過配額。如需完整的配額資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](azure-subscription-service-limits.md)。

若要檢查您訂用帳戶的核心配額，可以在 Azure CLI 中使用 `azure vm list-usage` 命令。以下範例示範核心配額為 4 的免費試用帳戶：

    azure vm list-usage
    
它會傳回：
    
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

如果您嘗試部署的範本會在上述訂用帳戶內，於美國西部區域中建立 4 個以上的核心，則會發生部署錯誤，該錯誤看起來如下 (可能是在入口網站中，也可能是調查部署記錄時發現的)：

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

或是在 PowerShell 中，您可使用 **Get-AzureRmVMUsage** Cmdlet。

    Get-AzureRmVMUsage
    
它會傳回：

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

在這些情況下，您應該移至入口網站，並提出支援問題，以針對您想要部署的區域提高配額。

> [AZURE.NOTE] 請記住，對於資源群組，配額適用於每個個別區域，而不是整個訂用帳戶。如果您需要在美國西部部署 30 個核心，就必須要求在美國西部擁有 30 個資源管理員核心。如果您需要在任何具有存取權限的區域中部署 30 個核心，就應該要求在所有區域中擁有 30 個資源管理員核心。


## 授權失敗

您可能會在部署期間收到錯誤，因為嘗試部署資源的帳戶或服務主體並有執行這些動作的存取權。Azure Active Directory 可讓您或您的系統管理員最精確地控制哪些身分識別可以存取哪些資源。例如，如果您的帳戶被指派「讀取者」角色，它將無法建立新資源。在此情況下，您應該會看到錯誤訊息，指出授權失敗。

如需有關角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

除了角色型存取控制之外，您的部署動作也可能受到訂用帳戶的相關原則限制。透過原則，系統管理員可以強制執行訂用帳戶中所有資源部署的慣例。例如，系統管理員可以要求的資源類型提供特定的標記值。如果您未達成原則的需求，便會在部署期間收到錯誤。如需有關原則的詳細資訊，請參閱[使用原則來管理資源和控制存取](resource-manager-policy.md)。

## 檢查資源提供者註冊

資源是由資源提供者所管理，並且必須註冊帳戶或訂用帳戶以便使用特定提供者。Azure 入口網站或正在使用的命令列介面會自動註冊大部分的提供者；但並非全部。

### PowerShell

若要查看您的註冊狀態，請使用 **Get-AzureRmResourceProvider**。

    Get-AzureRmResourceProvider -ListAvailable

會傳回所有可用的資源提供者及您的註冊狀態：

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

若要註冊提供者，請使用 **Register-AzureRmResourceProvider**，並提供您想要註冊之資源提供者的名稱。

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

系統會要求您確認註冊，然後傳回狀態。

    Confirm
    Are you sure you want to register the provider 'Microsoft.Cdn'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

    ProviderNamespace RegistrationState ResourceTypes
    ----------------- ----------------- -------------
    Microsoft.Cdn     Registering       {profiles, profiles/endpoints,...

### Azure CLI

若要使用 Azure CLI 查看是否註冊提供者以供使用，請使用 `azure provider list` 命令 (下列是截斷的輸出範例)。

    azure provider list
        
會傳回所有可用的資源提供者及您的註冊狀態：
        
    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.Compute                Registered
    data:    Microsoft.Network                Registered  
    data:    Microsoft.Storage                Registered
    data:    microsoft.visualstudio           Registered
    ...
    info:    provider list command OK

若要註冊資源提供者，請使用 `azure provider register` 命令，然後指定要註冊的「命名空間」。

    azure provider register Microsoft.Cdn

### REST API

若要取得註冊狀態，請參閱[取得資源提供者的相關資訊](https://msdn.microsoft.com/library/azure/dn790534.aspx)。

若要註冊提供者，請參閱[向資源提供者註冊訂用帳戶](https://msdn.microsoft.com/library/azure/dn790548.aspx)。

## 自訂指令碼擴充功能錯誤

如果在部署虛擬機器時遇到自訂指令碼擴充功能錯誤，請參閱[針對 Azure Windows VM 擴充功能的失敗進行疑難排解](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)或[針對 Azure Linux VM 擴充功能的失敗進行疑難排解](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md)。

## 虛擬機器佈建和配置失敗

如果您正在嘗試部署虛擬機器，而遇到作業系統映像佈建錯誤或配置錯誤，請參閱[針對建立新虛擬機器的問題進行疑難排解](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)和[針對配置失敗進行疑難排解](./virtual-machines/virtual-machines-windows-allocation-failure.md)。

## 了解部署何時就緒 

Azure Resource Manager 會部署成功傳回所有提供者時，報告部署成功。不過，這不一定表示您的資源群組「作用中且準備好供使用者使用」。例如，部署可能需要下載升級、等候非範本資源、或是安裝複雜的指令碼或 Azure 不知道的某個其他可執行活動，因為它不是提供者正在追蹤的活動。在這些情況下，可能需要一些時間，您的資源才能用於實際使用。因此，您應該預期部署狀態成功一段時間後，才能使用部署。

不過，建立自訂範本的自訂指令碼 (例如，使用 [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/))，即可防止 Azure 報告部署成功，而此自訂指令碼知道如何監視整個部署以了解系統是否準備就緒，並只在使用者可以與整個部署互動時才會順利傳回。如果您想要確保最後才執行您的延伸模組，請在範本中使用 **dependsOn** 屬性。

## 後續步驟

- 若要了解稽核動作，請參閱[使用 Resource Manager 來稽核作業](resource-group-audit.md)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱[檢視部署作業](resource-manager-troubleshoot-deployments-portal.md)。
- 若要針對以 Windows 為基礎之虛擬機器的「遠端桌面通訊協定」錯誤進行疑難排解，請參閱[針對遠端桌面連線問題進行疑難排解](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md)。
- 若要針對以 Linux 為基礎之虛擬機器的「安全殼層」錯誤進行疑難排解，請參閱[針對安全殼層問題進行疑難排解](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md)。

<!---HONumber=AcomDC_0601_2016-->