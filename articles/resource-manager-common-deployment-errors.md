<properties
   pageTitle="針對常見的 Azure 部署錯誤進行疑難排解 |Microsoft Azure"
   description="說明如何解決使用 Azure Resource Manager 將資源部署至 Azure 時的常見錯誤。"
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="部署錯誤, azure 部署, 部署至 azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="tomfitz"/>

# 使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解

本主題說明如何解決可能會遇到的一些常見 Azure 部署錯誤。如果您需要有關您的部署問題出在哪裡的詳細資訊，請先參閱[檢視部署作業](resource-manager-troubleshoot-deployments-portal.md)，然後再回到本文以取得解決錯誤的協助。

## 範本或資源無效

在部署範本時，您可能會收到︰

    Code=InvalidTemplate 
    Message=Deployment template validation failed

如果您收到錯誤指出範本或資源屬性無效，您的範本中可能有語法錯誤。此錯誤很容易發生，因為範本運算式可能很複雜。例如，儲存體帳戶的下列名稱指派包含一組方括號、三個函式、三組圓括號、一組單引號和一個屬性︰

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

如果您未提供所有相符的語法，範本將會產生與您所要的值截然不同的值。

當您收到此類錯誤時，請仔細檢閱運算式語法。請考慮使用 [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 或 [Visual Studio Code](resource-manager-vs-code.md) 等 JSON 編輯器，它們可以警告您有語法錯誤。

## 不正確的區段長度

資源名稱的格式不正確時，就會發生另一種無效範本錯誤。

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

根層級資源的名稱必須比資源類型少一個區段。每個區段是以斜線區分。在下列範例中，類型具有 2 個區段，而名稱則有 1 個區段，因此是**有效名稱**。

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",

但下一個範例則**不是有效名稱**，因為它的區段數目和類型相同。

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",

對於子資源來說，類型和名稱必須具有相同的區段數目。這個要求很合理，因為子資源的完整名稱和類型包含父資源的名稱和類型，因此完整名稱仍會比完整類型少一個區段。

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",

對於跨資源提供者套用的 Resource Manager 類型而言，要讓區段保持正確尤為棘手。例如，將資源鎖定套用至網站需要具有 4 個區段的類型。因此，名稱會是 3 個區段︰

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",

## 資源名稱已採用，或已由另一個資源使用

對於某些資源 (最顯著的儲存體帳戶、資料庫伺服器和網站)，您必須提供在整個 Azure 中是唯一的資源名稱。如果您未提供唯一的名稱，您可能會收到下列類似錯誤︰

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

您可以將您的命名慣例與 [uniqueString](resource-group-template-functions.md#uniquestring) 函式的結果串連，以建立一個唯一名稱。

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

## 在物件上找不到成員「複本」

當您將 **copy** 元素套用到不支援此元素的範本時，您會遇到此錯誤。您只可以將複製元素套用到資源類型。您不能將複製套用到資源類型內的屬性。例如，您將複製套用到虛擬機器，但無法將它套用到虛擬機器的作業系統磁碟。在某些情況下，您可以將子資源轉換成父資源，以建立複製迴圈。如需有關使用複製的詳細資訊，請參閱[在 Azure Resource Manager 中建立資源的多個執行個體](resource-group-create-multiple.md)。

## 無法使用 SKU

在部署資源 (通常是虛擬機器) 時，您可能會收到下列錯誤碼和錯誤訊息︰

    Code: SkuNotAvailable
    Message: The requested tier for resource '<resource>' is currently not available in location '<location>' for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.

當您選取的資源 SKU (例如 VM 大小) 不適用於您選取的位置時，您會收到這個錯誤。您有兩種選向可以解決這個問題︰

1.	透過 UI 登入入口網站並開始加入新的資源。當您設定值時，您會看到該資源可用的 SKU。

    ![可用的 sku](./media/resource-manager-common-deployment-errors/view-sku.png)

2.	如果您在該區域或符合您業務需求的替代區域中找不到適當的 SKU，請向 [Azure 支援服務](https://portal.azure.com/#create/Microsoft.Support)尋求協助。


## 找不到已註冊的提供者

在部署資源時，您可能會收到下列錯誤代碼和訊息︰

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location '<location>' and API version '<api-version>' for type '<resource-type>'.

您會因為下列其中一個原因而收到此錯誤︰

1. 此資源類型不支援位置
2. 此資源類型不支援 API 版本
3. 尚未向您的訂用帳戶註冊此資源提供者

錯誤訊息應可提供給您支援的位置和 API 版本建議。您可以將您的範本變更為其中一個建議的值。Azure 入口網站或正在使用的命令列介面會自動註冊大部分的提供者；但並非全部。如果您未曾使用特定的資源提供者，您可能需要註冊該提供者。您可以透過下列命令，深入探索資源提供者。

### PowerShell

若要查看您的註冊狀態，請使用 **Get-AzureRmResourceProvider**。

    Get-AzureRmResourceProvider -ListAvailable

若要註冊提供者，請使用 **Register-AzureRmResourceProvider**，並提供您想要註冊的資源提供者名稱。

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

若要取得支援特定資源類型的位置，請使用︰

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

若要取得支援特定資源類型的 API 版本，請使用︰

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

若要查看是否已註冊該提供者，請使用 `azure provider list` 命令。

    azure provider list

若要註冊資源提供者，請使用 `azure provider register` 命令，然後指定要註冊的「命名空間」。

    azure provider register Microsoft.Cdn

若要查看資源提供者支援的位置和 API 版本的，請使用︰

    azure provider show -n Microsoft.Compute --json > compute.json

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

或者，您可以在 PowerShell 中使用 **Get-AzureRmVMUsage** Cmdlet。

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

如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

除了角色型存取控制之外，您的部署動作也可能受到訂用帳戶的相關原則限制。透過原則，系統管理員可以強制執行訂用帳戶中所有資源部署的慣例。例如，系統管理員可以要求的資源類型提供特定的標記值。如果您未達成原則的需求，便會在部署期間收到錯誤。如需有關原則的詳細資訊，請參閱[使用原則來管理資源和控制存取](resource-manager-policy.md)。

## 對虛擬機器進行疑難排解

| 錯誤 | 文章 |
| -------- | ----------- |
| 自訂指令碼擴充功能錯誤 | [Windows VM 擴充失敗](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />或<br />[Linux VM 擴充失敗](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| OS 映像佈建錯誤 | [新的 Windows VM 錯誤](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />或<br />[新的 Linux VM 錯誤](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md) |
| 配置失敗 | [Windows VM 配置失敗](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />或<br />[Linux VM 配置失敗](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| 嘗試連線時發生安全殼層 (SSH) 錯誤 | [Linux VM 的安全殼層連線](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| 連接至在 VM 上執行的應用程式時發生錯誤 | [在 Windows VM 上執行的應用程式](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />或<br />[在 Linux VM 上執行的應用程式](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| 遠端桌面連線錯誤 | [Windows VM 的遠端桌面連線](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| 藉由重新部署解決的連線錯誤 | [將虛擬機器重新部署至新的 Azure 節點](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| 雲端服務錯誤 | [雲端服務部署問題](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## 對其他服務進行疑難排解

下表不是完整的 Azure 疑難排解主題清單，而是將重點放在部署或設定資源的相關問題。如果您需要對資源執行階段問題進行疑難排解的說明，請參閱該 Azure 服務的文件。

| 服務 | 文章 |
| -------- | -------- |
| 自動化 | [Azure 自動化中常見錯誤的疑難排解秘訣](./automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack | [Microsoft Azure Stack 疑難排解](./azure-stack/azure-stack-troubleshooting.md) |
| Azure Stack | [Web Apps 和 Azure Stack](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md) |
| Data Factory | [對 Data Factory 問題進行疑難排解](./data-factory/data-factory-troubleshoot.md) |
| Service Fabric | [針對您在 Azure Service Fabric 上部署服務時的常見問題進行疑難排解](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| 站台復原 | [監視和疑難排解虛擬機器與實體伺服器的保護](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| 儲存體 | [監視、診斷與疑難排解 Microsoft Azure 儲存體](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [StorSimple 裝置部署問題的疑難排解](./storsimple/storsimple-troubleshoot-deployment.md) |
| SQL Database | [針對 Azure SQL Database 連線問題進行疑難排解](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL 資料倉儲 | [針對 Azure SQL 資料倉儲問題進行疑難排解](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## 了解部署何時就緒

Azure Resource Manager 會部署成功傳回所有提供者時，報告部署成功。不過，這不一定表示您的資源群組「作用中且準備好供使用者使用」。例如，部署可能需要下載升級、等候非範本資源、或是安裝複雜的指令碼或 Azure 不知道的某個其他可執行活動，因為它不是提供者正在追蹤的活動。在這些情況下，可能需要一些時間，您的資源才能用於實際使用。因此，您應該預期部署狀態成功一段時間後，才能使用部署。

不過，建立自訂範本的自訂指令碼 (例如，使用 [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/))，即可防止 Azure 報告部署成功，而此自訂指令碼知道如何監視整個部署以了解系統是否準備就緒，並只在使用者可以與整個部署互動時才會順利傳回。如果您想要確保最後才執行您的延伸模組，請在範本中使用 **dependsOn** 屬性。

## 後續步驟

- 若要了解稽核動作，請參閱[使用 Resource Manager 來稽核作業](resource-group-audit.md)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱[檢視部署作業](resource-manager-troubleshoot-deployments-portal.md)。

<!---HONumber=AcomDC_0720_2016-->