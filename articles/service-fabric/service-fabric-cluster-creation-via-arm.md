
<properties
   pageTitle="使用 Azure Resource Manager 建立安全的 Service Fabric 叢集 | Microsoft Azure"
   description="本文說明如何使用 Azure Resource Manager、Azure 金鑰保存庫和用戶端驗用的 Azure Active Directory (AAD)，在 Azure 中設定安全的 Service Fabric 叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>


# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>使用 Azure Resource Manager 在 Azure 中建立 Service Fabric 叢集

> [AZURE.SELECTOR]
- [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
- [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)

這是一個逐步指南，可逐步引導您使用 Azure Resource Manager 在 Azure 中設定安全的 Azure Service Fabric 叢集。 本指南將逐步引導您完成下列步驟：

 - 設定金鑰保存庫來管理叢集和應用程式安全性的金鑰。
 - 以 Azure Resource Manager 在 Azure 中建立受保護的叢集。
 - 以適用於叢集管理的 Azure Active Directory (AAD) 驗證使用者。

安全的叢集是會防止未經授權存取管理作業的叢集，那些作業包括部署、升級，及刪除應用程式、服務和它們包含的資料。 不安全的叢集是任何人都可以隨時連線並執行管理作業的叢集。 雖然可以建立不安全的叢集，但 **強烈建議您建立安全的叢集**。 不安全的叢集 **無法在事後保護其安全** - 必須建立新的叢集。

不論叢集是 Linux 叢集或 Windows 叢集，建立安全叢集的概念都一樣。 如需建立安全 Linux 叢集的詳細資訊和協助程式指令碼，請參閱 [在 Linux 上建立安全叢集](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>登入 Azure
本指南使用 [Azure PowerShell][azure-powershell]。 開始新的 PowerShell 工作階段時，請先登入您的 Azure 帳戶並選取您的訂用帳戶，然後再執行 Azure 命令。

登入您的 Azure 帳戶：

```powershell
Login-AzureRmAccount
```

選取您的訂用帳戶：

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>設定金鑰保存庫

本節將逐步引導您為 Azure 中的 Service Fabric 叢集和為 Service Fabric 應用程式建立金鑰保存庫。 如需金鑰保存庫的完整指引，請參閱[金鑰保存庫入門指南][key-vault-get-started]。

Service Fabric 會使用 X.509 憑證來保護叢集，並提供應用程式的安全性功能。 Azure 金鑰保存庫是用來管理 Azure 中 Service Fabric 叢集的憑證。 在 Azure 中部署叢集時，負責建立 Service Fabric 叢集的 Azure 資源提供者會從金鑰保存庫提取憑證，並將它們安裝在叢集 VM 上。

下圖說明金鑰保存庫、Service Fabric 叢集，以及 Azure 資源提供者 (會在建立叢集時使用金鑰保存庫中所存憑證) 之間的關係：

![憑證安裝][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>建立資源群組

第一個步驟是特別針對金鑰保存庫建立資源群組。 建議您將金鑰保存庫放入其自己的資源群組中。 這可讓您移除計算和儲存體資源群組，包括含有 Service Fabric 叢集的資源群組，而不會遺失您的金鑰和密碼。 擁有您金鑰保存庫的資源群組必須和正在使用它的叢集位於相同區域。

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>建立金鑰保存庫 

在新的資源群組中建立金鑰保存庫。 金鑰保存庫 **必須啟用以用於部署** ，才能讓 Service Fabric 資源提供者從中取得憑證並安裝在叢集節點上：

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

如果您有現有金鑰保存庫，可以使用 Azure CLI 將它啟用以用於部署：

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>新增憑證至金鑰保存庫

憑證是在 Service Fabric 中用來提供驗證與加密，以保護叢集和其應用程式的各個層面。 如需如何在 Service Fabric 中使用憑證的詳細資訊，請參閱 [Service Fabric 叢集安全性案例][service-fabric-cluster-security]。

### <a name="cluster-and-server-certificate-(required)"></a>叢集和伺服器憑證 (必要) 

需要此憑證來保護叢集安全及防止未經授權存取叢集。 它會透過幾種方式提供叢集安全性：
 
 - **叢集驗證：** 驗證叢集同盟的節點對節點通訊。 只有可使用此憑證提供其身分識別的節點可以加入叢集。
 - **伺服器驗證：** 向管理用戶端驗證叢集管理端點，管理用戶端就能知道它正在交談的對象是真正的叢集。 此憑證也會為 HTTPS 管理 API，以及為透過 HTTPS 使用的 Service Fabric Explorer 提供 SSL。

為用於這些用途，憑證必須符合下列要求：

 - 憑證必須包含私密金鑰。
 - 憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換檔 (.pfx)。
 - 憑證的主體名稱必須符合用來存取 Service Fabric 叢集的網域。 必須如此符合，才能為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 SSL。 您無法向憑證授權單位 (CA) 取得 `.cloudapp.azure.com` 網域的 SSL 憑證。 您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須符合用於您叢集的自訂網域名稱。

### <a name="application-certificates-(optional)"></a>應用程式憑證 (選用)

您可以針對應用程式安全性目的，在叢集上安裝任何數目的其他憑證。 在建立您的叢集之前，請考量需要在節點上安裝憑證的應用程式安全性案例，例如：

 - 加密和解密應用程式組態值
 - 在複寫期間跨節點加密資料 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>格式化憑證以供 Azure 資源提供者使用

私密金鑰檔案 (.pfx) 可以直接透過金鑰保存庫來新增及使用。 但是，Azure 資源提供者需要以特殊 JSON 格式儲存金鑰，該格式包含 .pfx 作為 Base-64 編碼字串和私密金鑰密碼。 為符合這些要求，金鑰必須放入 JSON 字串中，然後在金鑰保存庫中儲存為密碼  。

若要讓這個程序更容易，可使用 PowerShell 模組 ([GitHub 上有提供][service-fabric-rp-helpers])。 請依照這些步驟使用模組：

  1. 將儲存機制的完整內容下載到本機目錄中。 
  2. 在您的 PowerShell 視窗中匯入模組：

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
此 PowerShell 模組中的 `Invoke-AddCertToKeyVault` 命令會自動將憑證私密金鑰的格式設定為 JSON 字串，並將它上傳到金鑰保存庫。 請用它來將叢集憑證與任何其他應用程式憑證新增到金鑰保存庫。 請為您想在叢集中安裝的任何其他憑證重複這個步驟。

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

先前的字串是設定 Service Fabric 叢集 Resource Manager 範本時的所有金鑰保存庫必要條件，該範本會安裝用於節點驗證、管理端點安全性和驗證，以及使用 X.509 憑證的任何其他應用程式安全性功能的憑證。 此時，您應該已經在 Azure 中建立以下項目：

 - 金鑰保存庫資源群組
   - 金鑰保存庫
     - 叢集伺服器驗證憑證
     - 應用程式憑證

## <a name="set-up-azure-active-directory-for-client-authentication"></a>設定用戶端驗用的 Azure Active Directory

AAD 可讓組織 (稱為租用戶) 管理使用者對應用程式的存取，這分成具有 Web 型登入 UI 的應用程式以及具有原生用戶端體驗的應用程式。 在本文中，我們假設您已經建立租用戶。 否則，請從閱讀 [如何取得 Azure Active Directory 租用戶][active-directory-howto-tenant]開始進行。

Service Fabric 叢集提供其管理功能的各種進入點 (包括 Web 型 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 和 [Visual Studio][service-fabric-manage-application-in-visual-studio])。 因此，您將建立兩個 AAD 應用程式來控制對叢集的存取：一個 Web 應用程式和一個原生應用程式。

為了簡化與設定 AAD 與 Service Fabric 叢集相關的一些步驟，我們建立了一組 Windows PowerShell 指令碼。

>[AZURE.NOTE] 您必須在建立叢集「之前」執行這些步驟；因此，在指令碼預期叢集名稱和端點的情況下，這些應該是計劃的值，而不是您所建立的值。

1. [下載指令碼][sf-aad-ps-script-download]到您的電腦。

2. 以滑鼠右鍵按一下 zip 檔案，選擇 [屬性]，然後核取 [解除封鎖] 核取方塊並套用。

3. 解壓縮 zip 檔案。

4. 提供 TenantId、ClusterName 和 WebApplicationReplyUrl 作為參數，來執行 `SetupApplications.ps1`。 例如：

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    在 Azure 傳統入口網站中查看租用戶的 URL，就可以找到 **TenantId** 。 內嵌在 URL 中的 GUID 就是 TenantId。 例如：

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    在指令碼所建立的 AAD 應用程式前面會加上 **ClusterName** 。 它不需要完全符合實際的叢集名稱，因為它只是用來讓您更輕鬆地將 AAD 構件對應到與之搭配使用的 Service Fabric 叢集。

    **WebApplicationReplyUrl** 是 AAD 在完成登入程序之後傳回給使用者的預設端點。 您應該將此設定為您叢集的 Service Fabric Explorer 端點，其預設值為︰

    https://&lt;cluster_domain&gt;:19080/Explorer

    系統會提示您登入具有 AAD 租用戶系統管理權限的帳戶。 這樣做之後，指令碼將繼續建立 Web 和原生應用程式來代表 Service Fabric 叢集。 如果您在 [Azure 傳統入口網站][azure-classic-portal]中查看租用戶的應用程式，則應該會看到兩個新項目︰

    - *ClusterName*\_叢集
    - *ClusterName*\_用戶端

    此指令碼將會列印下節建立叢集時 Azure Resource Manager 範本所需的 Json，因此請讓 PowerShell 視窗持續開啟。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>建立 Service Fabric 叢集 Resource Manager 範本

本節中，上述 PowerShell 命令的輸出將用於 Service Fabric 叢集 Resource Manager 範本。

您可以從 [GitHub 上的 Azure 快速啟動範本資源庫][azure-quickstart-templates]取得範例 Resource Manager 範本。 這些範本可以用作叢集範本的起點。 

### <a name="create-the-resource-manager-template"></a>建立 Resource Manager 範本

本指南使用[五節點安全叢集][service-fabric-secure-cluster-5-node-1-nodetype-wad]範例範本和範本參數。 下載 `azuredeploy.json` 和 `azuredeploy.parameters.json` 到您的電腦並在您最愛的文字編輯器中開啟這兩個檔案。

### <a name="add-certificates"></a>新增憑證

憑證會藉由參考包含憑證金鑰的金鑰保存庫，新增至叢集 Resource Manager 範本。 建議您將這些金鑰保存庫值都置於 Resource Manager 範本參數檔案中，以讓 Resource Manager 範本檔案保持可重複使用，並避免其套用部署特定的值。

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>將所有憑證都新增至 VMSS osProfile

需要安裝在叢集中的每個憑證，都必須在 VMSS 資源的 osProfile 區段中設定 (Microsoft.Compute/virtualMachineScaleSets)。 這會指示資源提供者在 VM 上安裝憑證。 這包括叢集憑證，以及您打算用於應用程式的任何應用程式安全性憑證︰

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>設定 Service Fabric 叢集憑證

叢集驗證憑證也必須在 Service Fabric 叢集資源 (Microsoft.ServiceFabric/clusters) 中，以及 VMSS 資源中的 VMSS Service Fabric 延伸模組中設定。 這可讓 Service Fabric 資源提供者對其進行設定，以用於叢集驗證及管理端點的伺服器驗證。

##### <a name="vmss-resource:"></a>VMSS 資源︰

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource:"></a>Service Fabric 資源︰

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>插入 AAD 組態

稍早建立的 AAD 組態可以直接插入您的Resource Manager 範本，不過建議最好是先將值擷取到到參數檔案中的參數，以讓 Resource Manager 範本檔案保持可重複使用，並避免其套用部署特定的值。

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="<a-"configure-arm"-></a>configure-resource-manager-template-parameters"></a><a "configure-arm" ></a>設定 Resource Manager 範本參數

最後，使用金鑰保存庫和 AAD PowerShell 命令的輸出值來填入參數檔案︰

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
此時，您應該已經具有以下項目：

 - 金鑰保存庫資源群組
    - 金鑰保存庫
    - 叢集伺服器驗證憑證
    - 資料編密憑證
 - Azure Active Directory 租用戶 
    - Web 型管理和 Service Fabric Explorer 的 AAD 應用程式
    - AAD 應用程式的原生用戶端管理
    - 已指派角色的使用者 
 - Service Fabric 叢集 Resource Manager 範本
    - 透過金鑰保存庫設定的憑證
    - 已設定 Azure Active Directory 

下圖說明金鑰保存庫和 AAD 組態在 Resource Manager 範本中的作用。

![Resource Manager 相依性對應][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>建立叢集

您現在已準備好使用 [ARM 部署][resource-group-template-deploy]來建立叢集。

#### <a name="test-it"></a>進行測試

使用下列 PowerShell 命令，以參數檔案來測試 Resource Manager 範本︰

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>進行部署

如果 Resource Manager 範本測試通過，使用下列 PowerShell 命令，以參數檔案來部署 Resource Manager 範本︰

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>將使用者指派給角色

建立應用程式來代表您的叢集之後，需要將使用者指派給 Service Fabric 所支援的角色︰唯讀和系統管理員。 您可以使用 [Azure 傳統入口網站][azure-classic-portal]來執行這項作業。

1. 瀏覽至您的租用戶，然後選擇 [應用程式]。
2. 選擇 Web 應用程式，而其名稱類似 `myTestCluster_Cluster`。
3. 按一下 [使用者] 索引標籤。
4. 選擇要指派的使用者，然後按一下畫面底部的 [指派]  按鈕。

    ![將使用者指派給角色按鈕][assign-users-to-roles-button]

5. 選取要指派給使用者的角色。

    ![將使用者指派給角色][assign-users-to-roles-dialog]

>[AZURE.NOTE] 如需 Service Fabric 中角色的詳細資訊，請參閱 [角色型存取控制 (適用於 Service Fabric 用戶端)](service-fabric-cluster-security-roles.md)。

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>在 Linux 上建立安全叢集

為了簡化此程序， [這裡](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux)提供一個協助程式指令碼。 在使用此協助程式指令碼時，我們假設您已安裝 Azure CLI，而且它位於您的路徑中。 下載指令碼後，請執行 `chmod +x cert_helper.py` ，以確定指令碼有執行權限。 第一個步驟是使用 CLI 輸入 `azure login` 命令，登入您的 Azure 帳戶。 登入 Azure 帳戶之後，使用協助程式並指定您的 CA 簽署憑證，如下列命令所示︰

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

此命令會傳回下列三個字串做為輸出︰ 

1. SourceVaultID：這是它為您建立的新 KeyVault ResourceGroup 的識別碼。 

2. CertificateUrl：用於存取憑證。

3. CertificateThumbprint：用於驗證。


下列範例示範如何使用此命令︰

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
執行上述命令會提供下列三個字串給您︰

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 憑證的主體名稱必須符合用來存取 Service Fabric 叢集的網域。 這是必要的，以便為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 SSL。 您無法向憑證授權單位 (CA) 取得 `.cloudapp.azure.com` 網域的 SSL 憑證。 您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須符合用於您叢集的自訂網域名稱。

這些是用於建立安全 Service Fabric 叢集 (不含 AAD) 的必要項目，如 [設定 Resource Manager 範本參數](#configure-arm)所述。 您可以透過 [驗證用戶端對叢集的存取權](service-fabric-connect-to-secure-cluster.md)中的指示連線到安全叢集。 Linux 預覽叢集不支援 AAD 驗證。 您可以指派系統管理員和用戶端角色，如 [指派角色給使用者](#assign-roles)一節所述。 在為 Linux 預覽叢集指定系統管理員和用戶端角色時，您必須提供用於驗證的憑證指紋 (而不是主體名稱，因為此預覽版本中不會執行鏈結驗證或撤銷)。


如果您想要使用自我簽署的憑證進行測試，您可以使用相同的指令碼，並提供旗標 `ss` 而不是提供憑證名稱與憑證路徑，以產生自我簽署的憑證，並將它上傳至 KeyVault。 例如，請參閱下列命令來建立及上傳自我簽署的憑證︰

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

此命令會傳回相同的三個字串，即 SourceVault、CertificateUrl 和 CertificateThumbprint (用來建立安全的 Linux 叢集)，以及放置自我簽署憑證的位置。 您需要有自我簽署的憑證才能連線到叢集。  您可以透過 [驗證用戶端對叢集的存取權](service-fabric-connect-to-secure-cluster.md)中的指示連線到安全叢集。 憑證的主體名稱必須符合用來存取 Service Fabric 叢集的網域。 這是必要的，以便為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 SSL。 您無法向憑證授權單位 (CA) 取得 `.cloudapp.azure.com` 網域的 SSL 憑證。 您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須符合用於您叢集的自訂網域名稱。

協助程式指令碼所提供的參數可以在入口網站填入，如 [在 Azure 入口網站中建立叢集](service-fabric-cluster-creation-via-portal.md#create-cluster-portal)一節所述。

## <a name="next-steps"></a>後續步驟

此時，您已具有以 Azure Active Directory 提供管理驗證的安全叢集。 接下來，請[連線到您的叢集](service-fabric-connect-to-secure-cluster.md)並了解如何[管理應用程式密碼](service-fabric-application-secret-management.md)。

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>針對設定用戶端驗用的 Azure Active Directory 進行疑難排解

如果設定用戶端驗證的 Azure Active Directory 時發生問題，請參閱下列關於可能解決方案的建議。

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Service Fabric Explorer 會提示您選取憑證

#### <a name="problem"></a>問題

在 Service Fabric Explorer 的 AAD 登入頁面上成功登入後，瀏覽器會返回首頁，但是會出現提示選取憑證的對話方塊。

![SFX 選取憑證對話方塊][sfx-select-certificate-dialog]

#### <a name="reason"></a>原因

使用者並未被指派 AAD 叢集應用程式中的角色。 因此，Service Fabric 叢集的 AAD 驗證失敗。 Service Fabric Explorer 會回復到憑證驗證。

#### <a name="solution"></a>方案

請依照設定 AAD 的指示進行，並指派使用者角色。 另外，建議和 `SetupApplications.ps1` 一樣啟動「存取應用程式需要使用者指派」。

### <a name="connect-with-powershell-fails-with-error:-the-specified-credentials-are-invalid"></a>與 PowerShell 連線時失敗，出現錯誤：指定的認證無效

#### <a name="problem"></a>問題

以 “AzureActiveDirectory” 安全模式使用 PowerShell 連線到叢集時，成功登入 AAD 登入頁面後，連線失敗，出現錯誤：指定的認證無效。

#### <a name="solution"></a>方案

同上。

### <a name="service-fabric-explorer-signing-in-return-failure:-aadsts50011"></a>Service Fabric Explorer 登入傳回失敗：AADSTS50011

#### <a name="problem"></a>問題

在 Service Fabric Explorer 的 AAD 登入頁面上成功登入後，頁面傳回登入失敗 - AADSTS50011：回覆地址 &lt;url&gt; 不符合對於應用程式設定的回覆地址：&lt;guid&gt;。 

![SFX 回覆地址不相符][sfx-reply-address-not-match]

#### <a name="reason"></a>原因

代表 Service Fabric Explorer 的叢集 (Web) 應用程式嘗試依照提供重新導向傳回 URL 的要求之中的 AAD 進行驗證。 但是它並未列在 AAD 應用程式 ‘REPLY URL’ 清單中。

#### <a name="solution"></a>方案

將 Service Fabric Explorer 的 url 新增到叢集 (Web) 應用程式的「設定」索引標籤之中的 ‘REPLY URL’，或取代清單內的其中一個項目。 然後儲存。

![Web 應用程式回覆 url][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters?"></a>能否將相同的 AAD 租用戶用於多個叢集？

#### <a name="answer"></a>Answer

是。 但是務必將 Service Fabric Explorer 的 URL 新增到叢集 (Web) 應用程式，否則 Service Fabric Explorer 無法運作。

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled?"></a>為什麼 AAD 啟用時仍然需要次服器憑證？

#### <a name="answer"></a>Answer

FabricClient 和 FabricGateway 執行相互驗證。 對於 AAD 驗證，AAD 整合將用戶端身分識別提供給伺服器，而且伺服器憑證用於驗證伺服器身分識別。 如需有關憑證如何在 Service Fabric 上運作的詳細資訊，請參閱 [X.509 憑證和 Service Fabric][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png


<!--HONumber=Oct16_HO2-->


