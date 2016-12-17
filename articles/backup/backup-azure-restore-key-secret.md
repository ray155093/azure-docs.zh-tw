---
title: "使用 Azure 備份還原已加密 VM 的金鑰保存庫金鑰與密碼 | Microsoft Docs"
description: "了解如何使用 PowerShell 以 Azure 備份還原金鑰保存庫金鑰與密碼"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c5b2969ddc943b2b15826003f5a9e686e84e1c4


---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>使用 Azure 備份還原已加密 VM 的金鑰保存庫金鑰與密碼
本文將討論如果您的金鑰和密碼不存在於金鑰保存庫中時，如何使用 Azure VM 備份還原已加密的 Azure VM。 這些步驟也可用於您想要為已還原的 VM 另外維護一份金鑰 (金鑰加密金鑰) 與密碼 (BitLocker 加密金鑰) 時。

## <a name="pre-requisites"></a>必要條件
1. **備份已加密的 VM** - 已使用 Azure 備份將已加密的 Azure VM 備份。 請參閱[使用 PowerShell 部署和管理 Resource Manager 部署之 VM 的備份](backup-azure-vms-automation.md)一文，以取得如何將已加密 Azure VM 備份的相關詳細資料。
2. **設定 Azure 金鑰保存庫** – 先確定金鑰保存庫已存在，才能將金鑰和密碼還原至該金鑰保存庫。 請參閱[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)一文，以取得金鑰保存庫管理的相關詳細資料。

## <a name="setup-recovery-services-vault"></a>設定復原服務保存庫
使用下列步驟登入 PowerShell 並設定復原服務保存庫內容

### <a name="log-in-to-azure-powershell"></a>登入 Azure PowerShell
使用下列 Cmdlet 登入 Azure 帳戶

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>設定復原服務保存庫內容
登入後，使用下列 Cmdlet 取得您的可用訂用帳戶清單

```
PS C:\> Get-AzureRmSubscription
```

選取資源可供使用的訂用帳戶

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

使用復原服務保存庫 (已為已加密的 VM 啟用該保存庫中的備份) 設定保存庫內容

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>取得復原點
在該保存庫中選取代表已加密 Azure 虛擬機器的容器

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

使用此容器取得對應虛擬機器的備份項目

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

使用變數 rp 取得所選備份項目的復原點陣列

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>還原加密的虛擬機器
使用下列步驟還原已加密的 VM、其金鑰及密碼。

### <a name="restore-key"></a>還原金鑰
上述的陣列 $rp 是以逆時間序排序的，亦即 index 0 是最新的復原點。 例如：$rp[0] 會選取最新的復原點。

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [!NOTE]
> 此 Cmdlet 成功執行後，系統便會在 blob 檔案執行的機器內的指定資料夾中產生該檔案。 此 blob 檔案以加密形式表示金鑰加密金鑰。
> 
> 

使用下列 Cmdlet 將金鑰還原回金鑰保存庫。 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>還原密碼
從上述取得的復原點還原密碼資料

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [!NOTE]
> vault.azure.net 之前的文字代表原始金鑰保存庫名稱。 secrets/ 後的文字代表密碼名稱。 
> 
> 

如果您想要使用相同的密碼名稱，請從上述的 Cmdlet 執行結果輸出中取得密碼名稱與值。 否則，應更新下述的 $secretname，以使用新的密碼名稱。 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

如果也需要還原 VM，請為密碼設定標籤。 對於標籤 DiskEncryptionKeyFileName，值應該包含您打算使用之密碼的名稱。 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [!NOTE]
> DiskEncryptionKeyFileName 的值與上述取得的密碼名稱相同。 還原回金鑰並使用 [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) Cmdlet 後，便可從金鑰保存庫取得 DiskEncryptionKeyEncryptionKeyURL 的值    
> 
> 

將密碼設定回到金鑰保存庫

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>還原虛擬機器
如果您已使用 Azure VM 備份將已加密的 VM 備份，上述的 PowerShell Cmdlet 可幫助您將金鑰與密碼還原回金鑰保存庫。 還原金鑰與密碼後，請參閱[使用 PowerShell 部署和管理 Resource Manager 部署之 VM 的備份](backup-azure-vms-automation.md)一文還原已加密的 VM。




<!--HONumber=Nov16_HO3-->


