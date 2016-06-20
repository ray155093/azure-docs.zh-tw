<properties
   pageTitle="以憑證保護 Service Fabric 叢集 | Microsoft Azure"
   description="如何使用 X.509 憑證來保護 Service Fabric 叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# 使用憑證保護 Azure 上的 Service Fabric 叢集

Azure Service Fabric 叢集是您擁有的資源。為了防止資源遭到未經授權的存取，您必須保護資源安全，尤其是當資源上有生產環境工作負載正在執行的時候。若要使用 X.509 憑證來設定安全的 Service Fabric 叢集，您至少需要一個伺服器 X.509 憑證，您需將此憑證上傳到「Azure 金鑰保存庫」，然後在叢集建立程序中使用它。

本文與叢集建立程序的[步驟 3︰設定安全性](service-fabric-cluster-creation-via-portal.md#step-3--configure-security)對應。如需有關 Service Fabric 如何使用 X.509 憑證的詳細資訊，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

其中共有三個不同的步驟：

1. 取得 X.509 憑證。
2. 將憑證上傳至 Azure 金鑰保存庫。
3. 將憑證的位置和詳細資料提供給 Service Fabric 叢集建立程序。

## 步驟 1：取得 X.509 憑證

對於執行生產環境工作負載的叢集，您必須使用[憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 簽署的 X.509 憑證來保護叢集。如需有關如何取得這些憑證的詳細資訊，請移至[做法：取得憑證](http://msdn.microsoft.com/library/aa702761.aspx)。

對於用於測試的叢集，您可以選擇使用自我簽署憑證。下面的步驟 2.5 會說明如何執行該作業。

## 步驟 2：將 X.509 憑證上傳至金鑰保存庫

這是複雜的程序，因此我們將 PowerShell 模組上傳至 Git 儲存機制，它會為您完成此程序。

### 步驟 2.1
確定您的電腦上已安裝 Azure PowerShell 1.0+。如果您從未安裝過，請依照[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 中所述的步驟操作。

### 步驟 2.2
將 [ServiceFabricRPHelpers] 資料夾從這個 [Git 儲存機制](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers) 複製到您的電腦。

### 步驟 2.3
開啟 PowerShell 視窗，然後移至您下載模組的目錄。接著，使用下列命令來匯入模組。

```powershell
Import-Module .\ServiceFabricRPHelpers.psm1
```

### 步驟 2.4
如果您要使用之前已取得的憑證，請依照此步驟中的程序操作。否則，請跳至步驟 2.5，該步驟將解釋如何建立自我簽署憑證，並將自我簽署憑證部署至金鑰保存庫。

您可以使用現有的資源群組和金鑰保存庫來存放憑證，或是如果資源群組和 (或) 金鑰保存庫不存在，則可以建立新的資源群組和 (或) 金鑰保存庫。必須先使用此指令碼來設定現有的金鑰保存庫，才能支援部署。

```powershell
Login-AzureRmAccount

Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

若要將憑證上傳到您的資源群組和金鑰保存庫，請執行下列指令碼。如果資源群組和金鑰保存庫尚未存在，此指令碼就會建立它們。

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
以下是已填入指令碼的範例。

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

指令碼順利完成時，您會看到類似下面的輸出，進行步驟 3 (設定安全的叢集) 時會用到這些資料。

```
Certificate Thumbprint: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

SourceVault /Resource ID of the key vault :  /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea
```

現在您已擁有設定安全叢集時所需的資訊。請移至步驟 3。

### 步驟 2.5
如果您「沒有」憑證，而想要建立新的自我簽署憑證並將它上傳到金鑰保存庫，請依照下列步驟操作。自我簽署憑證應該只用於測試叢集，不應該用於產品叢集。

您可以使用現有的資源群組和金鑰保存庫來存放憑證，或是如果資源群組和 (或) 金鑰保存庫不存在，則可以建立新的資源群組和 (或) 金鑰保存庫。必須先使用此指令碼來設定現有的金鑰保存庫，才能支援部署。

```powershell
Login-AzureRmAccount
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

下列指令碼會建立新的資源群組和 (或) 金鑰保存庫 (如果它們尚未存在)、建立自我簽署憑證並上傳到金鑰保存庫，然後將新憑證輸出到 *OutputPath*。

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```
*DnsName* 字串會指定一或多個 DNS 名稱，以在 CloneCert 參數中未指定要複製的憑證時，放到憑證的 subject-alternative-name 延伸中。第一個 DNS 名稱也會儲存為主體名稱。如果未指定任何簽署憑證，則第一個 DNS 名稱也會儲存為簽發者名稱。*Invoke AddCertToKeyVault* Cmdlet 會使用 [New-SelfSignedCertificate Cmdlet](https://technet.microsoft.com/library/hh848633.aspx) 來建立自我簽署憑證。

以下是已填入指令碼的範例。

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

指令碼順利完成時，您會看到類似下面的輸出。進行步驟 3 時需要用到這些資料。

```
Certificate Thumbprint: 64881409F4D86498C88EEC3697310C15F8F1540F

SourceVault /Resource ID of the key vault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea
```

## 步驟 3：設定安全的叢集

將您的憑證上傳到 Azure 金鑰保存庫之後，您便可以建立以這些憑證保護的叢集。此步驟與叢集建立程序的[步驟 3︰設定安全性](service-fabric-cluster-creation-via-portal.md#step-3--configure-security)對應，並說明如何設定您的安全性組態。

>[AZURE.NOTE]
需要的憑證會於 [安全性組態] 下的 [節點類型] 層級指定。您必須為您的叢集中的每個節點類型指定此項目。雖然本文件會逐步解說如何使用入口網站執行這項作業，但您可以使用 Azure 資源管理員範本來執行相同的作業。

![Azure 入口網站中的安全性組態螢幕擷取畫面][SecurityConfigurations_01]

### 必要參數

- **安全性模式**：選取 [X509 憑證] 來設定以 X.509 憑證保護的叢集。
- **叢集保護層級**：請參閱這份[保護層級文件](https://msdn.microsoft.com/library/aa347692.aspx)，以了解這當中每個值所代表的意義。雖然這邊允許使用的值有三個 (EncryptAndSign、Sign 和 None)，但除非您了解您所進行的作業，否則最好保留使用預設值 EncryptAndSign。
- **來源保存庫**：這是指金鑰保存庫的「資源識別碼」。其格式應該如下：

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **憑證 URL**：這是指您金鑰保存庫中做為憑證上傳位置的 URL。其格式應該如下：

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **憑證指紋**：這是指憑證的指紋，可在您稍早指定的 URL 中找到。

### 選擇性參數

 您可以選擇性地指定用戶端機器用來在叢集上執行作業的其他憑證。根據預設，您在必要參數中指定的指紋會加入至能夠執行用戶端作業的已授權指紋清單。

**系統管理用戶端**：此資訊是用來驗證連接到叢集管理端點的用戶端是否出示正確的認證，以在叢集上執行系統管理和唯讀動作。您可以指定多個想要授權其執行系統管理作業的憑證。

- **授權依據**：這會對 Service Fabric 指出它是否應該使用主體名稱或指紋來查詢此憑證。使用主體名稱來授權不是很好的安全性作法，不過卻可以增加作業彈性。
- **主體名稱**：只有當您已指定依主體名稱授權時才需要。
- **簽發者指紋**：這可讓伺服器在用戶端向其出示自己的認證時，執行額外一層的檢查。

**唯讀用戶端**：此資訊是用來驗證連接到叢集管理端點的用戶端是否出示正確的認證，以在叢集上執行唯讀動作。您可以指定多個想要授權其執行唯讀作業的憑證。

- **授權依據**：這會對 Service Fabric 指出它是否應該使用主體名稱或指紋來查詢此憑證。使用主體名稱來授權不是很好的安全性作法，不過卻可以增加作業彈性。
- **主體名稱**：只有當您已指定依主體名稱授權時才需要。
- **簽發者指紋**：這可讓伺服器在用戶端向其出示自己的認證時，執行額外一層的檢查。

## 後續步驟
設定叢集上的憑證安全性之後，請繼續進行[步驟 4︰完成叢集建立程序](service-fabric-cluster-creation-via-portal.md#step-4--complete-the-cluster-creation)中的叢集建立程序。

為叢集建立憑證安全性之後，您可以在稍後[更新憑證](service-fabric-cluster-security-update-certs-azure.md)。


<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0608_2016-->