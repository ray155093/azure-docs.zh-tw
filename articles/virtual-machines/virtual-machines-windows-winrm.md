<properties
	pageTitle="為 Azure Resource Manager 中的虛擬機器設定 WinRM 存取 | Microsoft Azure"
	description="如何設定 WinRM 存取來搭配使用 Azure Resource Manager 虛擬機器"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="singhkay"/>

# 在 Azure Resource Manager 中設定虛擬機器的 WinRM 存取

## Azure Service Management 與 Azure Resource Manager 中的 WinRM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型

* 如需 Azure Resource Manager 的概觀，請參閱[本文章](../resource-group-overview.md)
* 如需 Azure Service Management 與 Azure Resource Manager 之間的差異性，請參閱[本文章](../resource-manager-deployment-model.md)

在兩個堆疊之間設定 WinRM 組態的主要差異在於如何在 VM 上安裝憑證。在 Azure Resource Manager 堆疊中，憑證會模型化為「金鑰保存庫資源提供者」所管理的資源。因此，使用者必須提供自己的憑證，並在 VM 中使用憑證之前先將它上傳至金鑰保存庫。

以下是您對 VM 設定 WinRM 連線必須採取的步驟

1. 建立金鑰保存庫
2. 建立自我簽署憑證
3. 將您的自我簽署憑證上傳至金鑰保存庫
4. 取得金鑰保存庫中您的自我簽署憑證的 URL
5. 在建立 VM 時參考您的自我簽署憑證的 URL

## 步驟 1︰建立金鑰保存庫

您可以使用下列命令來建立金鑰保存庫

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## 步驟 2：建立自我簽署憑證
您可以使用下列 PowerShell 指令碼建立自我簽署憑證

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName "$certificateName" -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## 步驟 3：將自我簽署憑證上傳至金鑰保存庫

在將憑證上傳至於步驟 1 中建立的金鑰保存庫之前，必須先將它轉換成 Microsoft.Compute 資源提供者可以了解的格式。下面的 PowerShell 指令碼可讓您這麼做

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = get-content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureRmKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## 步驟 4：取得金鑰保存庫中您的自我簽署憑證的 URL

Microsoft.Compute 資源提供者在佈建 VM 時，需要金鑰保存庫內密碼的 URL。這可讓 Microsoft.Compute 資源提供者下載密碼，並在 VM 上建立對等憑證。

>[AZURE.NOTE]密碼的 URL 也必須包含版本。範例 URL 如下所示：https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### 範本

您可以使用下列程式碼取得範本中的 URL 連結

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### PowerShell

您可以使用下列 PowerShell 命令取得此 URL

	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## 步驟 5：在建立 VM 時參考您的自我簽署憑證的 URL

#### Azure Resource Manager 範本

透過範本建立 VM 時，憑證會在密碼區段和 winRM 區段中參考，如下所示

	"osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

上述的範例範本可以在這裡找到：[201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

此範本的原始程式碼位於 [Github](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### PowerShell

	$vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
	$credential = Get-Credential
	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
	$vm = Set-AzureRmVMOperatingSystem -VM $vm  -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
	$sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
	$CertificateStore = "My"
	$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## 步驟 6︰連接到 VM
在您可以連接至 VM 之前，您必須確定您的電腦已設定 WinRM 遠端管理。以系統管理員身分啟動 PowerShell 並執行下列命令來確認您要安裝程式。

    Enable-PSRemoting -Force

>[AZURE.NOTE] 如果上述程式碼無法運作，您可能需要確定 WinRM 服務正在執行。您可以使用 `Get-Service WinRM` 來這麼做

安裝完成之後，您可以使用下列命令連接到 VM

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate

<!---HONumber=AcomDC_0622_2016-->