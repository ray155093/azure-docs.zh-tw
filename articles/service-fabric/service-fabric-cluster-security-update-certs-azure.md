<properties
   pageTitle="新增、變換和移除在 Azure 中 Service Fabric 叢集使用的憑證 | Microsoft Azure"
   description="說明如何上傳次要叢集憑證，然後變換舊的主要憑證。"
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
   ms.date="08/15/2016"
   ms.author="chackdan"/>

# 新增或移除 Azure 中 Service Fabric 叢集的憑證

建議您熟悉 Service Fabric 使用 X.509 憑證的方式，並參閱[叢集安全性案例](service-fabric-cluster-security.md)。您必須瞭解什麼是叢集憑證及其用途，方可繼續進行後續作業。

您在叢集建立期間設定憑證安全性時，Service Fabric 可讓您指定兩個叢集憑證：主要與次要。如需詳細資訊，請參閱[透過入口網站建立 Azure 叢集](service-fabric-cluster-creation-via-portal.md)或是 [透過 Azure Resource Manager 建立 Azure 叢集](service-fabric-cluster-creation-via-Resource Manager.md)。若您是透過 Resource Manager 部署，且僅指定一個叢集憑證，則會使用該憑證做為主要憑證。在叢集建立完成後，您可新增憑證做為次要憑證。

>[AZURE.NOTE] 針對安全叢集，您一律必須至少部署一個有效 (未撤銷或過期) 的憑證 (主要或次要)，否則叢集將停止運作。在所有有效憑證到達到期日的 90 天前，系統會針對節點產生警告追蹤與警告健康狀態事件。目前並無 Service Fabric 針對此主題送出的電子郵件或其他任何通知。


## 使用入口網站新增次要憑證
若要新增另一個憑證做為次要憑證，您必須將該憑證上傳到 Azure 金鑰保存庫，然後將它部署到叢集中的 VM。如需其他資訊，請參閱 [Deploy certificates to VMs from a customer-managed Key Vault (將憑證從客戶管理的金鑰保存庫部署到 VM)](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)。

1. 如需操作方式，請參閱[將 X.509 憑證上傳至金鑰保存庫](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)。

2. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至您想要新增此憑證的叢集資源。
3. 在 [設定] 下方，按一下 [安全性] 以顯示 [叢集安全性] 刀鋒視窗。
4. 按一下刀鋒視窗頂端的 [新增憑證]按鈕，以前往 [新增憑證] 刀鋒視窗。
5. 從下拉式清單中選取「次要憑證指紋」，並為您上傳至金鑰保存庫的次要憑證填寫憑證指紋。

>[AZURE.NOTE]
與叢集建立工作流程期間不同之處，在於我們不會在此記錄金鑰保存庫的詳細資訊，因為它會假設您當時已位於此刀鋒視窗並已將憑證部署至 VM，且憑證已在 VMSS 執行個體的本機憑證存放區中提供。

按一下 [憑證]。系統會開始執行部署，且會在叢集安全性刀鋒視窗顯示藍色狀態列。

![入口網站中的憑證指紋螢幕擷取畫面][SecurityConfigurations\_02]

在部署順利完成後，您就可以使用主要或次要憑證在叢集上執行管理作業。

![正在進行憑證部署的螢幕擷取畫面][SecurityConfigurations\_03]

以下螢幕擷取畫面顯示完成部署後的安全性刀鋒視窗外觀。

![部署完成後的憑證指紋螢幕擷取畫面][SecurityConfigurations\_08]


您現可使用剛剛新增的憑證來連線，並在叢集上執行作業。

>[AZURE.NOTE]
目前無法在入口網站上交換主要與次要憑證，此功能正在研發中。只要具備有效的叢集憑證，叢集就會正常運作。

## 新增次要憑證，並使用資源管理員 Powershell 將其交換為主要憑證

這些步驟假設您已熟悉資源管理員的運作方式，並已使用 Resource Manager 範本部署至少一個 Service Fabric 叢集，且已備妥用來設定叢集的範本。此外亦假設您可輕鬆自如地使用 JSON。

>[AZURE.NOTE]
若您正在尋找可用來遵循或做為起點的範例範本和參數，可自此 [git-儲存機制] 下載。(https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample)。

#### 編輯您的 Resource Manager 範本 

若您曾使用來自 [git-儲存機制](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample)的範例遵循操作，將會在範例 5-VM-1-NodeTypes-Secure\_Step2.JSON 中找到這些變更。使用 5-VM-1-NodeTypes-Secure\_Step1.JSON 部署安全叢集

1. 開啟您用來部署叢集的 Resource Manager 範本。
2. 加入「字串」類型的新參數「secCertificateThumbprint」。若您使用在建立時透過入口網站下載，或是透過快速入門範本取得的 Resource Manager 範本，則只要搜尋該參數，應會發現它已經定義。
3. 尋找「Microsoft.ServiceFabric/clusters」資源定義。您會在屬性下方找到「憑證」JSON 標籤，看起來應該會像以下 JSON 片段。
```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
        }
``` 

4. 加入新標籤「thumbprintSecondary」，並為它提供值「[parameters('secCertificateThumbprint')]」。

資源定義現在看起來應該像這樣 (視您的範本來源而定，它看起來可能不會和下面的片段完全相同)。您可在下方看到目前在此執行的作業，是指定新憑證做為主要憑證，並將目前的主要憑證移作次要憑證。這可讓您在單一部署步驟中，將目前的憑證變換為新憑證。

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### 編輯您的範本檔案，以反映先前加入的新參數

若您曾使用來自 [git-儲存機制](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample)的範例遵循操作，就可開始在範例 5-VM-1-NodeTypes-Secure.paramters\_Step2.JSON 中進行變更


編輯 Resource Manager 範本參數檔案，為 secCertificate 加入新參數，然後將現有的主要憑證詳細資料與次要憑證交換，並使用新的憑證詳細資料取代主要憑證詳細資料。

```JSON
    "secCertificateThumbprint": {
      "value": "OLD Primary Certificate Thumbprint"
    },
   "secSourceVaultValue": {
      "value": "OLD Primary Certificate Key Vault location"
    },
    "secCertificateUrlValue": {
      "value": "OLD Primary Certificate location in the key vault"
     },
    "certificateThumbprint": {
      "value": "New Certificate Thumbprint"
    },
    "sourceVaultValue": {
      "value": "New Certificate Key Vault location"
    },
    "certificateUrlValue": {
      "value": "New Certificate location in the key vault"
     },

```

### 將範本部署到 Azure

1. 您現在已可將範本部署至 Azure。開啟 Azure PS 版本 1+ 命令提示字元。
2. 登入您的 Azure 帳戶，並選取特定的 Azure 訂用帳戶。對於擁有多個 Azure 訂用帳戶存取權的使用者而言，這是一個重要步驟。


```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

部署範本前先進行測試。使用您目前在其中部署叢集的同一個資源群組。

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

將範本部署至您的資源群組。使用您目前在其中部署叢集的同一個資源群組。執行 New-AzureRmResourceGroupDeployment 命令。您無須指定模式，因為預設值為**增量**。

>[AZURE.NOTE]
若您將 [模式] 設為 [完整]，您可能會無意間刪除不在您範本中的資源。因此請勿在此案例中使用該模式。
   

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

以下是已填入資料的相同 Powershell 範例。

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

部署完成之後，使用新的憑證連線至叢集，並執行一些查詢。若您可執行動作。然後您可以刪除舊的主要憑證。

若您是使用自我簽署憑證，請務必將它們匯入至本機 TrustedPeople 憑證存放區。

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
以下提供連線至安全叢集的命令的快速參考
```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
以下提供取得叢集健康情況的命令的快速參考
```powershell
Get-ServiceFabricClusterHealth 
```
 
## 使用入口網站移除舊憑證
以下是移除舊憑證的程序，以讓叢集不會使用它：

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至您叢集的安全性設定。
2. 以滑鼠右鍵按一下您想要移除的憑證
3. 選取 [刪除] 並依照提示執行。

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## 後續步驟
如需有關叢集管理的詳細資訊，請參閱下列文件︰

- [Service Fabric 叢集升級程序與您的期望](service-fabric-cluster-upgrade.md)
- [設定用戶端的角色型存取](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png [SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png [SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png [SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png

<!---HONumber=AcomDC_0817_2016-->