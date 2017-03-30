---
title: "Azure 虛擬機器擴展集常見問題集 | Microsoft Docs"
description: "取得關於虛擬機器擴展集常見問題集的解答"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/17/2017
ms.author: negat
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 850459a79c723de0eb3249cfe9ea19bc988ca318
ms.lasthandoff: 03/18/2017


---

# <a name="azure-virtual-machine-scale-sets-faq"></a>Azure 虛擬機器擴展集常見問題集

本文包含關於擴展集常見問題集的解答。

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>什麼是 Azure 自動調整的最佳作法？

是。 請參閱 https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices

### <a name="where-do-i-find-the-metric-names-for-autoscaling-using-host-based-metrics"></a>哪裡可以找到使用主機型度量自動調整的計量名稱？

https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/

### <a name="are-there-any-examples-of-autoscaling-based-on-a-service-bus-topic-and-queue-length"></a>是否有任何根據根據服務匯流排主題和佇列長度自動調整的範例？

是。 請參閱：

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/

服務匯流排佇列：

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

儲存體佇列：

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

將這些範例值取代為適當的資源 URI。


### <a name="should-we-autoscale-with-host-based-metrics-or-use-a-diagnostics-extension"></a>我們應該要以主機型計量自動調整還是使用診斷延伸模組？

您可以在 VM 上建立自動調整設定來使用主機層級計量，或使用來賓 OS 型的計量。

請參閱這份支援的計量清單︰https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics。 以下是擴展集的完整範例 (在此案例中，我們使用主機層級 CPU 計量及訊息計數計量)︰

https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets

### <a name="how-can-i-set-alert-rules-on-a-scale-set"></a>如何在擴展集上設定警示規則？

您可以透過 PS 或 CLI 在擴展集上建立計量的警示。 請參閱：

https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules

https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts

擴展集的 TargetResourceId 看起來像：/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

您可以選擇任何 VM 做為要警示的計量︰

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-windows-vm-v2-as-a-guest-os

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-linux-vm-v2-as-a-guest-os

### <a name="how-can-i-set-up-autoscale-on-a-scale-set-using-powershell"></a>如何使用 PowerShell 在擴展集上設定自動調整規模？

請參閱 https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/




## <a name="certificates"></a>憑證

### <a name="how-do-you-securely-ship-a-certificate-into-the-vm--is-there-an-example-of-provisioning-a-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration--the-common-certificate-rotation-operation-would-amount-to-a-configuration-update-operation"></a>如何安全地送出憑證至 VM？  是否有佈建執行網站 (其中網站的 SSL 會安全地從憑證組態送出) 的擴展集之範例？  常見的憑證旋轉作業會相當於組態更新作業。

我們支援直接從客戶的 Key Vault 中將客戶憑證安裝至 Windows 憑證存放區。

在擴展集的內容中...

https://msdn.microsoft.com/library/mt589035.aspx

```json
        "secrets": [ {
              "sourceVault": {
                      "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
          },
          "vaultCertificates": [ {
                      "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                  "certificateStore": "certificateStoreName"
          } ]
        } ]
```

它同時支援 Windows 和 Linux。

### <a name="self-signed-certificate-example"></a>自我簽署憑證的範例︰

#### <a name="create-a-self-signed-cert-in-a-keyvault"></a>在 KeyVault 中建立自我簽署憑證

在 KeyVault 中建立自我簽署憑證的方法之一，是在這裡使用來自本 Service Fabric 文中的指示︰https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/

PowerShell 命令：

```powershell
Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

Login-AzureRmAccount

Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
```

上述命令可讓您輸入 Resource Manager 範本。

#### <a name="change-resource-manager-template"></a>變更 Resource Manager 範本

將這個屬性新增至 "virtualMachineProfile” 做為擴展集資源的一部分︰

```json 
"osProfile": {
            "computerNamePrefix": "[variables('namingInfix')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]",
            "secrets": [
              {
                "sourceVault": {
                  "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                  {
                    "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                    "certificateStore": "My"
                  }
                ]
              }
            ]
          }
```
 

### <a name="is-there-a-way-to-specify-an-ssh-key-pair-that-i-want-to-use-for-ssh-authentication-with-a-linux-scale-set-from-a-resource-manager-template"></a>是否有方法可指定我想要利用 Resource Manager 範本中的 Linux 擴展集來進行 SSH 驗證使用的 SSH 金鑰組嗎？  

osProfile 的 REST API 看起來類似於一般的 VM 案例︰
 
https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration
 
包含 `osProfile` 在您的範本，如下列範例所示︰

```json 
"osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUserName')]",
          "linuxConfiguration": {
            "disablePasswordAuthentication": "true",
            "ssh": {
              "publicKeys": [
                {
                  "path": "[variables('sshKeyPath')]",
                  "keyData": "[parameters('sshKeyData')]"
                }
              ]
            }
          }
        }
```
 
此 JSON 區塊可以用於下列的快速入門範本︰
 
https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
也在此範本看看 OS 設定檔︰
 
https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json

### <a name="how-do-i-remove-deprecated-certificates"></a>如何移除已被取代的憑證？ 

您必須從保存庫憑證清單中移除舊的憑證，但您想要保留在您電腦上的所有憑證例外。 這樣不會從所有的 VM 移除憑證，但也不會將憑證新增至擴展集中所建立的新 VM。 若要從現有的 VM 中移除憑證，您必須撰寫從您的憑證存放區手動移除憑證的自訂指令碼延伸模組。
 
### <a name="how-do-i-take-an-existing-ssh-public-key-and-inject-it-into-the-scale-set-ssh-layer-during-provisioning--i-would-like-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-utilize-them-in-my-resource-manager-template"></a>如何採取現有的 SSH 公開金鑰並在佈建期間將其插入至擴展集 SSH 層？  我想要將 SSH 公開金鑰值儲存在 Azure Key Vault 中，並在我的 Resource Manager 範本中使用它們。

如果您只提供 VM 一個公開 SSH 金鑰，則沒有理由將公開金鑰放在 Key Vault 中，因為公開金鑰不是密碼。
 
您可以在建立 Linux VM 時以純文字提供 SSH 公開金鑰。
範例可以在這裡找到：

https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
具體而言：

```json
"linuxConfiguration": {  
          "ssh": {  
            "publicKeys": [ {  
              "path": "path",
              "keyData": "publickey"
            } ]
          }
```
 
linuxConfiguration 元素名稱 | 必要 | 類型 | 說明
--- | --- | --- | --- |  ---
ssh | 否 | 集合 | 指定 Linux OS 的 SSH 金鑰組態。
路徑 | 是 | String | 指定 SSH 金鑰或憑證必須放置的 Linux 檔案路徑。
keyData | 是 | String | 指定 base64 編碼的 SSH 公開金鑰。
 
### <a name="when-i-run-update-azurermvmss-after-more-than-one-certificate-from-the-same-keyvault-i-get-the-following-error"></a>當我在來自相同 KeyVault 的一個以上憑證之後執行 Update-AzureRmVmss 時，收到下列錯誤︰
 
Update-AzureRmVmss：清單密碼包含重複的執行個體 /subscriptions/<我的訂用帳戶識別碼>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev，其為不允許。 為什麼無法從相同的 KeyVault 新增兩個憑證？
 
如果您正嘗試新增相同的保存庫兩次，而不是對現有 sourceVault 新增 vaultCertificate，就會發生這種行為。 Add-AzureRmVmssSecret 新增額外密碼運作不正常。
 
如果您想要從相同的 Key Vault 中新增更多密碼，您應該更新清單 $vmss.properties.osProfile.secrets[0].vaultCertificates
 
您可以看到預期的輸入結構如下︰https://msdn.microsoft.com/library/azure/mt589035.aspx
 
您需要在擴展集物件中尋找具有相同包含 Key Vault 的密碼。 然後您必須將憑證參考 (URL 以及密碼儲存名稱) 新增至與保存庫相關聯的清單。

注意︰目前不支援透過擴展集 API 從 VM 移除憑證。
 
新的 VM 不具有舊的憑證，但具有已部署之憑證仍須舊的憑證。
 
### <a name="is-there-a-way-to-get-certificates-pushed-to-the-scale-set-without-providing-the-password-when-the-certificate-is-in-secretstore-currently"></a>是否有方法可當憑證目前在 SecretStore 中時，將憑證推送至擴展集而無須提供密碼？

您不需要在指令碼中硬式編碼密碼；您可以利用您所擁有之部署指令碼執行之任何權限以動態方式擷取。 如果您有會從密碼存放區移動憑證的 Key Vault 之指令碼，密碼存放區取得憑證命令也會輸出 pfx 檔案的密碼。
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-of-a-scale-set-work-why-do-you-need-sourcevault-when-you-have-to-specify-the-absolute-uri-to-a-certificate-with-certificateurl"></a>擴展集的 VirtualMachineProfile.osProfile 的密碼屬性如何運作？ 當您必須指定絕對 URI 至具有 certificateUrl 的憑證時，為什麼需要 sourceVault？ 

必須在 OS 設定檔的密碼屬性中顯示 Win RM 憑證參考。 

指出來源保存庫的目的是要能夠強制執行 CSM 中存在的 ACL 規則。 如果沒有指定來源保存庫，沒有部署/存取密碼至 Key Vault 權限的使用者能夠透過 CRP。 甚至不存在的資源也會有 ACL。

如果您提供不正確的 sourceVault 識別碼但為有效的 Key Vault URL，在輪詢作業時我們會報告錯誤
 
### <a name="if-i-add-secrets-to-an-existing-scale-set-does-it-inject-them-in-existing-instances-or-only-new-ones"></a>如果我將密碼新增至現有的擴展集，它會將這些密碼插入現有的執行個體還是只有新的執行個體？ 

憑證會新增到所有的 VM，甚至是既有的。 如果您的擴展集 upgradePolicy 屬性設定為「手動」，當您在 VM 上執行手動更新時，憑證會新增至 VM。
 
### <a name="where-do-certificates-go-for-linux-vms"></a>Linux VM 的憑證會移至何處？

請參閱 https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/
  
### <a name="how-do-you-add-a-new-vault-certificate-to-a-new-certificate-object"></a>您要如何將新的保存庫憑證新增至新的憑證物件？

如果您想要將保存庫憑證新增至現有的密碼，其應該只有一個秘密物件，您可以如下列 powershell 範例所示執行它︰
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>如果您重新安裝映像 VM，憑證會發生什麼事？

如果重新安裝映像 VM，憑證便會消失，因為重新安裝映像會刪除其整個作業的作業系統磁碟。 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>如果您從 Key Vault 中刪除憑證會發生什麼事？

如果在 Key Vault 中刪除密碼，且您停止解除配置您所有的 VM，然後再次啟動它們，您會發生失敗。 此失敗是因為 CRP 需要從 Key Vault 擷取密碼，但無法完成。 在此案例中，您可以從擴展集模型刪除憑證。 

CRP 元件不會保存任何客戶密碼。 如果您停止解除配置擴展集中所有的 VM，則會刪除快取。 在此案例中，會從 Key Vault 中擷取密碼。

這個問題不會在向外延展架構中叫用，因為在網狀架構中 (至少在單一網狀架構租用戶模型中) 有一個快取的密碼副本。
 
### <a name="why-do-we-have-to-specify-the-exact-location-for-the-certificate-url-as-referenced-here-per-httpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>為什麼我們必須指定憑證 URL 的確切位置，如此處所參考︰https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/， 
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
 
根據 KeyVault 文件，get-secret REST API 應該傳回最新版本的密碼 (如果未指定版本)︰
 
方法 | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{密碼-名稱}/{密碼-版本}?api-version={api-版本}

將 {密碼-名稱} 取代為名稱，以及 {密碼-版本} 取代為您想要擷取的密碼版本。 可能會排除密碼版本，在此情況下會擷取目前的版本。
  
### <a name="why-does-certificate-version-have-to-be-specified-when-using-key-vault"></a>使用 Key Vault 時，為何必須指定憑證版本？

這項要求的原因是要讓使用者清楚了解其 VM 上所部署的憑證。

如果您建立 VM，然後更新您 Key Vault 中的密碼，該新的憑證會下載至您的 VM。 但您的 VM 會出現以參考這個位址，且新的 VM 會取得新的密碼。 若要避免混淆，則需要您參考明確的密碼版本。

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-is-for-deployment-of-these-certs-to-a-scale-set"></a>我的小組會使用發佈給我們做為 .cer 公開金鑰的多個憑證。 將這些憑證部署至擴展集的建議方法是什麼？

您可以使用 X509ContentType = Pfx 產生僅包含 .cer 檔案的 pfx 檔案。 例如，載入 .cer 檔案做為 C# 或 PowerShell 中的 x509Certificate2 物件，並呼叫這個方法：https://msdn.microsoft.com/library/24ww6yzk(v=vs.110).aspx

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-that-most-other-resource-providers-provide"></a>我看不到可供使用者傳入大部分其他資源提供者所提供的憑證做為 base64 字串的選項。

您可以在 Resource Manager 範本中擷取最新版本的 URL 來模擬您描述的行為。 您可以在 Resource Manager 範本中包含下列的 JSON 屬性︰

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-we-have-to-wrap-certs-in-json-objects-in-keyvaults"></a>我們必須在 keyvaults 中包裝 JSON 物件中的憑證嗎？

這是擴展集/VM 需求。 我們也支援內容類型應用程式/x-pkcs12。 可在此處找到指示：http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/
 
我們目前不支援 .cer 檔案，您必須將 .cer 檔案匯出至 pfx 容器。





## <a name="compliance"></a>法規遵循

### <a name="are-scale-sets-pci-compliant"></a>擴展集是否符合 PCI 規範？

擴展集為計算資源提供者上的精簡型 API 層，也就是 Azure 服務樹狀結構內所有「計算平台」區域的一部分。

因此，相容性的觀點而言，擴展集是 Azure 計算平台的基本部分。 同樣地，它們會共用相同的小組、工具、程序、部署方法、安全性控制 JIT、監控、警示等做為計算資源提供者 (CRP) 本身。  擴展集符合 PCI 規範，因為計算資源提供者屬於目前 PCI DSS 證明︰

如需詳細資訊，請參閱︰[https://www.microsoft.com/TrustCenter/Compliance/PCI](https://www.microsoft.com/TrustCenter/Compliance/PCI)。






## <a name="extensions"></a>擴充功能

### <a name="how-do-you-delete-a-scale-set-extension"></a>如何刪除擴展集延伸模組？

使用 PowerShell 的範例如下︰

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
可在 `$vmss`中找到 extensionName。
   
### <a name="is-there-a-scale-set-template-example-that-integrates-with-oms"></a>是否有與 OMS 整合的擴展集範本範例？

查看此處的第二個範例︰

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric
   
### <a name="extensions-seem-to-run-in-parallel-on-scale-sets-causing-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this-behavior"></a>延伸模組似乎在擴展集上擴充集合以平行方式執行，導致我的自訂指令碼延伸模組失敗。 可以做什麼來修正這個問題？

請參閱 https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/ 
 
 
### <a name="how-do-i-reset-the-password-for-scale-set-vms"></a>如何重設擴展集 VM 的密碼？

使用 VM 存取延伸模組

使用 PowerShell 的範例如下︰

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-scale-set"></a>如何將延伸模組新增至擴展集中的所有 VM？

- 將更新原則設定為自動時，使用新的延伸模組屬性重新部署範本便會更新每個 VM。
- 如果更新原則設定為手動，您必須更新延伸模組，然後在所有執行個體上執行 manualUpdate。
  
### <a name="if-the-extensions-associated-with-an-existing-scale-set-are-updated-would-they-affect-already-existing-vms-that-is-would-the-vms-show-up-as-not-matching-the-scale-set-model-or-would-they-be-ignored-when-an-existing-machine-is-service-healed--reimaged--etc-would-the-scripts-that-are-currently-configured-on-the-scale-set-be-executed-or-would-the-ones-that-were-configured-when-the-machine-was-first-created-be-used"></a>如果與現有擴展集相關聯的延伸模組更新時，是否會影響現有的 VM？ (也就是，VM 是否會出現為不相符的擴展集模型)？ 或者，是否會忽略它們？ 現有的機器為服務修復 / 重新安裝映像 / 等等時，是否會執行目前在擴展集上設定的指令碼，或是否會使用第一次建立機器時已設定的指令碼？

- 更新擴展集模型中的延伸模組定義時，如果 upgradePolicy 設定為自動執行，則它會更新 VM，且如果 upgradePolicy 設為手動，則會將它們標示為不符合模型。 

- 如果現有的 VM 已服務修復，它應出現類似於重新開機，且不會重新執行延伸模組。 如果它重新安裝映像，可能會類似從最新的模型以來源映像取代作業系統磁碟機和任何特製化，例如延伸模組可能會執行。
 
### <a name="how-do-i-get-a-scale-set-to-join-an-ad-domain"></a>如何取得要加入 AD 網域的擴展集？

您可以像這樣定義延伸模組，例如使用 JsonADDomainExtension︰
```json
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "joindomain",
                                "properties": {
                                    "publisher": "Microsoft.Compute",
                                    "type": "JsonADDomainExtension",
                                    "typeHandlerVersion": "1.0",
                                    "settings": {
                                        "Name": "[parameters('domainName')]",
                                        "OUPath": "[variables('ouPath')]",
                                        "User": "[variables('domainAndUsername')]",
                                        "Restart": "true",
                                        "Options": "[variables('domainJoinOptions')]"
                                    },
                                    "protectedsettings": {
                                        "Password": "[parameters('domainJoinPassword')]"
                                    }
                                }
                            }
                        ]
                    }
```
 
### <a name="my-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-instance-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>我的擴展集延伸模組正在嘗試安裝需要重新開機的項目，例如："commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

您可以使用 DSC 延伸模組。 如果作業系統是 2012 R2，則 Azure 會提取 WMF5.0 安裝程式、重新啟動，並繼續進行組態。 
 
### <a name="how-can-i-enable-antimalware-on-my-scale-set"></a>如何啟用我擴展集上的反惡意程式碼？

下列是 PowerShell 範例︰

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# retrieve the most recent version number of the extension
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-hosted-on-a-private-storage-account-i-have-no-problems-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signaturesas-it-fails-with-the-error-missing-mandatory-parameters-for-valid-shared-access-signature-i-know-that-linksas-works-fine-from-my-local-browser"></a>我需要執行裝載在私人儲存體帳戶上的自訂指令碼。 當儲存體為公用時沒有問題，但當我嘗試使用共用存取簽章 (SAS) 時會失敗並出現錯誤訊息：「遺失有效共用存取簽章的必要參數」。 我知道 link+SAS 可從我的本機瀏覽器運作正常。

您必須使用儲存體帳戶金鑰和名稱設定受保護的設定，以便這個案例可以運作。 請參閱 https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings







## <a name="networking"></a>網路
 
### <a name="how-do-i-do-vip-swap-for-scale-sets-in-the-same-subscription-and-same-region"></a>如何在相同訂用帳戶和相同區域中進行擴展集的 VIP 交換？

請參閱︰https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/ 
 
  
### <a name="what-is-the-resourceguid-property-on-a-nic-for"></a>NIC 上的 resourceGuid 屬性作用為何？

它是唯一的識別碼。 較低層級會在未來的某個時間點記錄此識別碼。 
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-for-static-private-ip-address-allocation"></a>如何指定靜態私人 IP 位址配置的私人 IP 位址範圍？

會從您指定的子網路選取 IP。 

擴展集 IP 的配置方法一律為「動態」。 但它並不表示可以變更這些 IP。 它只表示您沒有在 PUT 要求中指定 IP。 換句話說，您可以使用子網路指定靜態設定。 
    
### <a name="how-do-i-deploy-a-scale-set-into-an-existing-vnet"></a>我要如何將擴展集部署到現有的 VNET？ 

請參閱 https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet 

### <a name="how-do-i-add-a-scale-sets-first-vms-ip-address-to-the-output-of-a-template"></a>如何將擴展集中第一個 VM 的 IP 位址新增至範本的輸出？

請參閱︰http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips



## <a name="scale"></a>調整

### <a name="why-would-you-ever-create-a-scale-set-with-fewer-than-2-vms"></a>為什麼您會使用少於 2 部 VM 來建立擴展集？

其中一個原因就是使用擴展集的彈性屬性。 例如，您可以使用 0 部 VM 來部署擴展集，以定義您的基礎結構而不必付出成本。 然後，當您準備好要部署 VM 時，可藉由將擴展集的「容量」增加至生產執行個體計數來這麼做。

另一個原因是當您使用擴展集進行一些動作，而您不在意同樣使用可用性設定組與離散 VM。 擴展集新增使用可取代無差異計算單位的方法。 這個一致性是擴展集與可用性設定組的關鍵區別指標。 許多無狀態工作負載不在意個別的單位，且若工作負載減少時可以相應減少為一個計算單位，然後當工作負載增加時再回到許多計算單位。

### <a name="how-do-you-change-the-number-of-vms-in-a-scale-set"></a>如何變更擴展集中的 VM 數目？

請參閱︰https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/

### <a name="how-can-you-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>當觸達特定的臨界值時，您可以如何定義自訂警示？

處理警示上有一些彈性；例如，您可以如下列範例從 Resource Manager 範本定義自訂的 webhook︰
```json
   {
         "type": "Microsoft.Insights/autoscaleSettings",
           "apiVersion": "[variables('insightsApi')]",
                 "name": "autoscale",
                   "location": "[parameters('resourceLocation')]",
                     "dependsOn": [
                         "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
                 ],
                 "properties": {
                         "name": "autoscale",
                     "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                     "enabled": true,
                     "notifications": [{
                               "operation": "Scale",
                              "email": {
                                     "sendToSubscriptionAdministrator": true,
                                     "sendToSubscriptionCoAdministrators": true,
                                     "customEmails": [
                                        "youremail@address.com"
                                     ]},
                              "webhooks": [{
                                    "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                                    "properties": {
                                        "key1": "custommetric",
                                        "key2": "scalevmss"
                                    }
                                    }
                              ]}],
```

在此範例中，當觸達臨界值時，警示會進入 Pagerduty。



## <a name="troubleshooting"></a>疑難排解

### <a name="how-do-i-enable-boot-diagnostics"></a>如何啟用開機診斷？

建立儲存體帳戶並將此 JSON 區塊放在您的擴展集 virtualMachineProfile，並更新擴展集︰
```json
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "http://yourstorageaccount.blob.core.windows.net"
        }
      }
```

然後當建立新的 VM 時，VM 的 InstanceView 會顯示螢幕擷取畫面等的詳細資料。例如：
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```

 

## <a name="updates"></a>更新

### <a name="how-to-i-update-my-scale-set-to-a-new-image-and-manage-patching"></a>如何將我的擴展集更新為新的映像並管理修補？

請參閱︰https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set

### <a name="can-you-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>您可以使用重新安裝映像作業重設 VM 而不須變更映像嗎？ (也就是將 VM 重設為原廠設定，而不是新的映像)？

是。 請參閱︰https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set

不過，如果您的擴展集使用版本 =「最新」參考平台映像，當您呼叫重新安裝映像時，您的 VM 可以更新至較新版本的作業系統映像。







## <a name="vm-properties"></a>VM 屬性

### <a name="how-do-i-get-property-information-for-each-vm-without-having-to-make-multiple-calls-for-example-getting-the-fault-domain-for-each-vm-in-my-100-scale-set"></a>如何取得每個 VM 的屬性資訊而不需要進行多次呼叫？ 例如︰在我的 100 個擴展集中，取得每個 VM 的容錯網域？

您可以藉由在下列資源 URI 上進行 REST API `GET` 來呼叫 ListVMInstanceViews：

`/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView`

### <a name="are-there-ways-to-pass-different-extension-arguments-to-different-vms-in-a-scale-set"></a>有辦法將不同的延伸模組引數傳遞至擴展集中的不同 VM 嗎？

沒有，但延伸模組可根據它們所執行的 VM 上之唯一屬性來行動，例如電腦名稱。 此外，延伸模組可以在 http://169.254.169.254 查詢執行個體中繼資料以取得詳細資訊。

### <a name="why-are-there-gaps-between-my-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>為何我的擴展集 VM 電腦名稱與 VM 識別碼之間會有間隔？ 例如：0、1、3...

有間隔是因為您的擴展集過度佈建屬性設定為 true 的預設值。 若過度佈建為 true，會建立比要求更多的 VM，且接下來會刪除額外的 VM。 您會取得更高的部署可靠性，代價為連續命名和連續的 NAT 規則。 您可以將此屬性設定為 false，針對小型擴展集，它並不會對部署可靠性造成太大影響。

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-scale-set-vs-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>刪除擴展集中的 VM 與解除配置 VM 之間的差異為何？ 我何時應該選擇優先其中一個？

主要差異在於解除配置並不會刪除 VHD，因此會產生與停止解除配置相關聯的儲存體成本。 您可以使用其中一個的原因包括︰

- 您想要停止支付計算，但要保留 VM 的磁碟狀態。
- 您想要比相應放大擴展集更快速地啟動一組 VM。
  - 關於這種情況，您已建立自己的自動調整引擎，並想要更快速的端對端擴展。
  - 您有平均地分散到 FD/UD (因選擇性地刪除 VM 或因過度佈建後刪除的 VM) 的擴展集。 在開始擴展集後隨即停止解除配置會在 FD/UD 中平均地分配 VM。








 
   

