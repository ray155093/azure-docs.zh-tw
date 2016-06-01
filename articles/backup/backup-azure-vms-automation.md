<properties
   pageTitle="使用 PowerShell 部署和管理 ARM VM 的備份 | Microsoft Azure"
   description="使用 PowerShell 部署和管理 Azure 中的 ARM VM 備份"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="05/09/2016"
   ms.author="markgal; trinadhk"/>

# 使用 PowerShell 部署和管理 ARM VM 的備份

> [AZURE.SELECTOR]
- [Resource Manager PowerShell](backup-azure-vms-automation.md)
- [傳統 PowerShell](backup-azure-vms-classic-automation.md)

本文說明如何使用 Azure PowerShell 從復原服務保存庫備份和復原 Azure 虛擬機器 (VM)。復原服務保存庫是 Azure Resource Manager (ARM) 資源，它可以用來保護 Azure 備份和 Azure Site Recovery 服務中的資料和資產。在 ARM 部署中工作時，請使用復原服務保存庫。您可以使用復原服務保存庫來保護 Azure Service Manager (ASM) 部署 VM 和 ARM VM。

>[AZURE.NOTE] Azure 有兩種用來建立和使用資源的部署模型：[Resource Manager 和傳統](../resource-manager-deployment-model.md)。本文章適用於以 Resource Manager 模型建立的 VM。

本文章會引導您逐步完成使用 PowerShell 來保護 VM，以及從復原點還原資料的步驟。

## 概念

如果您不熟悉 Azure 備份服務，請參閱[何謂 Azure 備份](backup-introduction-to-azure-backup.md)來取得服務的概觀。 開始之前，請確定您已了解使用 Azure 備份需要的必要條件的重點，以及目前的 VM 備份解決方案的限制。

若要有效地使用 PowerShell，就必須了解物件的階層及從何處開始。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## 設定和註冊

開始：

1. [下載最新版本的 PowerShell](https://github.com/Azure/azure-powershell/releases) (所需的基本版本為：1.0.0)

2. 輸入下列命令，以找到可用的 Azure 備份 PowerShell Cmdlet：

```
PS C:\WINDOWS\system32> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.0.7      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.0.7      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.0.7      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
```


PowerShell 可以自動化下列工作：

- 建立復原服務保存庫
- 備份或保護 Azure VM
- 觸發備份作業
- 監視備份作業
- 還原 Azure VM

## 建立復原服務保存庫

> [AZURE.TIP] 對於第一次使用 Azure 備份的客戶，您必須註冊 Azure 復原服務提供者以搭配您的訂用帳戶使用。這可以透過執行下列命令來完成：Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"

您可以使用 **New-AzureRmRecoveryServicesVault** Cmdlet 來建立新的復原服務保存庫。復原服務保存庫是 ARM 資源，因此您必須將它放在資源群組內。在提高權限的 Azure PowerShell 主控台中，執行下列命令：

```
PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
```

若要檢視訂用帳戶中的所有復原服務保存庫清單，請使用 **Get-AzureRmRecoveryServicesVault** Cmdlet。

### 設定儲存體備援
在建立復原服務保存庫時，請指定要使用的儲存體備援類型：本地備援儲存體 (LRS) 或異地備援儲存體 (GRS)。以下範例示範 testVault 設定為 GeoRedundant 的 BackupStorageRedundancy 選項。

```
PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```


> [AZURE.TIP] 許多 Azure 備份 Cmdlet 都需要將復原服務保存庫物件當做輸入。基於這個理由，將備份復原服務保存庫物件儲存在變數中會是方便的做法。


## 備份 Azure VM

啟用 VM 保護前，您必須設定保存庫內容。內容會套用到所有後續的 Cmdlet。

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### 建立保護原則

當您建立新的保存庫時，它會隨附預設原則。這個原則會在每天下午 9:30 觸發備份作業。備份快照會保留 30 天。您可以使用預設原則來快速地保護 VM，並在之後編輯原則的各種詳細資料。

若要檢視保存庫中的可用原則清單，請使用 Get-AzureRmRecoveryServicesBackupProtectionPolicy Cmdlet︰

```
PS C:\WINDOWS\system32> get-AzureRMRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] PowerShell 中 BackupTime 欄位的時區是 UTC。不過，當備份時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。

備份保護原則至少與一個保留原則相關聯。保留原則定義復原點在 Azure 備份中保留的時間長度。您可以使用 Get-AzureRmRecoveryServicesBackupRetentionPolicyObject 檢視預設保留原則。同樣地，您可以使用 Get-AzureRmRecoveryServicesBackupSchedulePolicyObject 取得預設排程原則。排程和保留原則物件可當做 New-AzureRmRecoveryServicesBackupProtectionPolicy Cmdlet 的輸入。

備份保護原則定義項目備份的時間和頻率。New-AzureRmRecoveryServicesBackupProtectionPolicy Cmdlet 會建立可儲存備份原則資訊的 PowerShell 物件。備份原則可用來當做 Enable-AzureRmRecoveryServicesBackupProtection Cmdlet 的輸入。

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### 啟用保護。

啟用保護牽涉到兩個物件：項目和原則。這兩個物件是啟用保存庫保護的必要項目。一旦原則與項目相關聯，備份工作流程將依照原則排程定義的時間觸發。

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

對於以 ASM 為基礎的 VM

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### 修改保護原則

若要修改原則，請修改 BackupSchedulePolicyObject 或 BackupRetentionPolicy 物件，以及使用 Set-AzureRmRecoveryServicesBackupProtectionPolicy 修改原則

以下範例會將保留計數變更為 365。

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRMRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## 執行初始備份。

備份排程會在項目的初始備份進行時觸發完整備份。在後續的備份作業中，備份會是增量複本。如果您想要在特定時間強制進行初始備份，甚至是立即開始，請使用 Backup-AzureRmRecoveryServicesBackupItem Cmdlet：

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM";
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM";
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item;
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM            cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE]：PowerShell 中的 StartTime 和 EndTime 欄位時區為 UTC。不過，當時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。

## 監視備份工作

Azure 備份中長時間執行的大部分作業都模擬成工作。如此就很容易追蹤進度，而不需要一直開啟 Azure 入口網站。

若要取得進行中作業的最新狀態，請使用 Get-AzureRMRecoveryservicesBackupJob Cmdlet。

```
PS C:\ > $joblist = Get-AzureRMRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

因為需要額外的程式碼且並非必要，所以不輪詢這些作業是否完成，而是更簡單地改用 Wait-AzureRmRecoveryServicesBackupJob Cmdlet。在指令碼中使用此 Cmdlet 會暫停執行，直到工作完成或達到指定的逾時值為止。

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## 還原 Azure VM

若要還原備份資料，您需要識別備份的項目和保存時間點資料的復原點。這項資訊會提供給 Restore-AzureRMRecoveryServicesBackupItem Cmdlet，開始從保存庫將資料還原到客戶的帳戶。

### 選取 VM

若要取得可識別正確備份項目的 PowerShell 物件，請從保存庫中的容器開始，向下深入物件階層。若要選取代表 VM 的容器，請使用 Get-AzureRmRecoveryServicesBackupContainer Cmdlet，並透過管道將其傳送到 Get-AzureRmRecoveryServicesBackupItem Cmdlet。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem=Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### 選擇復原點

您現在可以使用 Get-AzureRMRecoveryServicesBackupRecoveryPoint Cmdlet，列出備份項目的所有復原點，並選擇要還原的復原點。使用者通常會從清單中選擇最近的 AppConsistent 點。

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRMRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM
                              /recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

變數 $rp 是所選取之備份項目的復原點陣列，以時間的回推順序排序 - 最近的復原點位於索引 0。使用標準 PowerShell 陣列索引來挑選復原點。例如：$rp[0] 將會選取最新的復原點。

### 還原磁碟

透過 Azure 入口網站或透過 Azure PowerShell 執行還原作業，兩者之間有一項重要差異。如果使用 PowerShell，從復原點還原磁碟及組態資訊時，還原作業會停止。不會建立虛擬機器。

> [AZURE.WARNING] Restore-AzureRMRecoveryServicesBackupItem 不會建立 VM，它只會將磁碟還原到指定的儲存體帳戶。這與 Azure 入口網站中的情況不同。

```
PS C:\> $restorejob = Restore-AzureRMRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount
 -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Restore               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

完成還原作業之後，您可以使用 Get-AzureRmRecoveryServicesBackupJobDetails Cmdlet 來取得還原作業的詳細資料。JobDetails 屬性會有重建 VM 所需的資訊。

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

## 向復原服務保存庫註冊 Windows Server 或 DPM

建立復原服務保存庫之後，請下載最新版本的代理程式和保存庫認證，並將它們儲存在方便的位置 (如 C:\\Downloads)。

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

在 Windows Server 或 DPM 伺服器上，執行 [Start-obregistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) Cmdlet 以向保存庫註冊電腦。

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

<!---HONumber=AcomDC_0518_2016-->