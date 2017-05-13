---
title: "使用 PowerShell 部署和管理 Resource Manager 部署之 VM 的備份 | Microsoft Docs"
description: "使用 PowerShell，在 Azure 中部署和管理 Resource Manager 部署之 VM 的備份"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/05/2017
ms.author: markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 6b90f0232d0fc527d0232a19f9251519250687f0
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>使用 AzureRM.RecoveryServices.Backup Cmdlet 備份虛擬機器
> [!div class="op_single_selector"]
> * [資源管理員](backup-azure-vms-automation.md)
> * [傳統](backup-azure-vms-classic-automation.md)
>
>

本文說明如何使用 Azure PowerShell Cmdlet 從復原服務保存庫備份和復原 Azure 虛擬機器 (VM)。 復原服務保存庫是一項 Azure Resource Manager 資源，可用來保護 Azure 備份和 Azure Site Recovery 服務中的資料和資產。 您可以使用復原服務保存庫，來保護 Azure Service Manager 部署的 VM 以及 Azure Resource Manager 部署的 VM。

> [!NOTE]
> Azure 有兩種用來建立和使用資源的部署模型： [Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文章適用於以 Resource Manager 模型建立的 VM。
>
>

本文章會引導您逐步完成使用 PowerShell 來保護 VM，以及從復原點還原資料的步驟。

## <a name="concepts"></a>概念
如果您不熟悉 Azure 備份服務，若需服務的概觀，請參閱[何謂 Azure 備份？](backup-introduction-to-azure-backup.md) 開始之前，請確定您已了解使用 Azure 備份需要的必要條件的重點，以及目前的 VM 備份解決方案的限制。

若要有效地使用 PowerShell，就必須了解物件的階層及從何處開始。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

若要檢視 AzureRmRecoveryServicesBackup PowerShell Cmdlet 參考文件，請參閱 Azure 文件庫中的 [Azure 備份 - 復原服務 Cmdlet](https://msdn.microsoft.com/library/mt723320.aspx) 。
若要檢視 AzureRmRecoveryServicesVault PowerShell Cmdlet 參考文件，請參閱 [Azure 復原服務 Cmdlet](https://msdn.microsoft.com/library/mt643905.aspx)。

## <a name="setup-and-registration"></a>設定和註冊
開始：

1. [下載最新版本的 PowerShell](https://github.com/Azure/azure-powershell/releases) (所需的最低版本為：1.4.0)
2. 輸入下列命令，以找到可用的 Azure 備份 PowerShell Cmdlet：

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```


PowerShell 可以自動化下列工作：

* 建立復原服務保存庫
* 備份或保護 Azure VM
* 觸發備份作業
* 監視備份作業
* 還原 Azure VM

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫
下列步驟將引導您完成建立復原服務保存庫。 復原服務保存庫不同於備份保存庫。

1. 如果您是第一次使用 Azure 備份，您必須使用 **[Register-AzureRMResourceProvider](https://msdn.microsoft.com/library/mt679020.aspx)** Cmdlet 利用您的訂用帳戶來註冊 Azure 復原服務提供者。

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. 復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt678985.aspx)** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. 使用 **[New-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643910.aspx)** Cmdlet 來建立保存庫。 請務必為保存庫指定與用於資源群組相同的位置。

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. 指定要使用的儲存體備援類型；您可以使用[本地備援儲存體 (LRS)](../storage/storage-redundancy.md#locally-redundant-storage) 或[異地備援儲存體 (GRS)](../storage/storage-redundancy.md#geo-redundant-storage)。 以下範例示範 testVault 設定為 GeoRedundant 的 BackupStorageRedundancy 選項。

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > 許多 Azure 備份 Cmdlet 都需要將復原服務保存庫物件當做輸入。 基於這個理由，將備份復原服務保存庫物件儲存在變數中會是方便的做法。
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫
使用 **[Get-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643907.aspx)** 來檢視目前訂用帳戶中所有保存庫的清單。 您可以使用此命令來檢查是否已建立新的保存庫，或查看訂用帳戶中有哪些保存庫可用。

執行命令時，會列出 Get-AzureRmRecoveryServicesVault 以及訂用帳戶中的所有保存庫。

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>備份 Azure VM
使用新的「復原服務」保存庫來保護您的虛擬機器。 在您套用保護之前，請設定保存庫內容 (保存庫中所保護的資料類型)，並確認保護原則。 保護原則是備份工作何時執行，以及每個備份快照之保留時間長度的排程。

啟用 VM 保護前，您必須設定保存庫內容。 內容會套用到所有後續的 Cmdlet。

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>建立保護原則
當您建立保存庫時，它會隨附預設原則。 此原則就會每天在指定時間觸發備份工作。 根據預設原則，備份快照會保留 30 天。 您可以使用預設原則來快速地保護 VM，並在之後編輯原則的各種詳細資料。

使用 **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://msdn.microsoft.com/library/mt723300.aspx)** 來檢視保存庫中可用的原則清單：

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell 中 BackupTime 欄位的時區是 UTC。 不過，當備份時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。
>
>

備份保護原則至少與一個保留原則相關聯。  保留原則定義復原點在 Azure 備份中保留的時間長度。 您可以使用 **Get-AzureRmRecoveryServicesBackupRetentionPolicyObject** 檢視預設保留原則。  同樣地，您可以使用 **Get-AzureRmRecoveryServicesBackupSchedulePolicyObject** 取得預設排程原則。 排程和保留原則物件可當做 **New-AzureRmRecoveryServicesBackupProtectionPolicy** Cmdlet 的輸入。

備份保護原則定義項目備份的時間和頻率。 New-AzureRmRecoveryServicesBackupProtectionPolicy Cmdlet 會建立可儲存備份原則資訊的 PowerShell 物件。 備份原則可用來當做 Enable-AzureRmRecoveryServicesBackupProtection Cmdlet 的輸入。

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>啟用保護。
啟用保護牽涉到兩個物件：項目和原則。 這兩個物件是啟用保存庫保護的必要項目。 一旦原則與保存庫相關聯，備份工作流程將依照原則排程定義的時間觸發。

在非加密 Resource Manager VM 上啟用保護

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

若要在加密的 VM 上 [使用 BEK 與 KEK 加密] 啟用保護，您必須提供 Azure 備份服務權限，來讀取金鑰保存庫中的金鑰和密碼。

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'KeyVaultName' -ResourceGroupName 'RGNameOfKeyVault' -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> 如果您在 Azure Government 雲端，請在 Set-AzureRmKeyVaultAccessPolicy Cmdlet 中使用 ff281ffe-705c-4f53-9f37-a40e6f2c68f3 作為 **-ServicePrincipalName** 參數的值。
>
>

若為傳統 VM

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>修改保護原則
若要修改原則，請修改 BackupSchedulePolicyObject 或 BackupRetentionPolicy 物件，並使用 Set-AzureRmRecoveryServicesBackupProtectionPolicy 修改原則

以下範例會將保留計數變更為 365。

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## <a name="run-an-initial-backup"></a>執行初始備份。
備份排程會在項目的初始備份進行時觸發完整備份。 在後續的備份作業中，備份會是增量複本。 如果您想要在特定時間強制進行初始備份，甚至是立即開始，請使用 **[Backup-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723312.aspx)** Cmdlet：

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName 'V2VM'
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell 中 StartTime 和 EndTime 欄位的時區是 UTC。 不過，當時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。
>
>

## <a name="monitoring-a-backup-job"></a>監視備份工作
Azure 備份中長時間執行的大部分作業都以作業形式執行。 如此就很容易追蹤進度，而不需要讓 Azure 入口網站一直維持開啟。

若要取得進行中作業的最新狀態，請使用 Get-AzureRmRecoveryservicesBackupJob Cmdlet。

```
PS C:\ > $joblist = Get-AzureRmRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

因為需要額外的程式碼且並非必要，所以不輪詢這些作業是否完成 - 而是改用 **[Wait-AzureRmRecoveryServicesBackupJob](https://msdn.microsoft.com/library/mt723321.aspx)** Cmdlet。 此 Cmdlet 會暫停執行，直到工作完成，或達到指定的逾時值為止。

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>還原 Azure VM
使用 Azure 入口網站還原 VM 和使用 PowerShell 還原 VM 之間有一個主要差異。 使用 PowerShell 時，建立磁碟和復原點組態資訊之後，還原作業即完成。 還原作業不會建立虛擬機器。 會提供從磁碟建立虛擬機器的指示。 不過，若要完整還原 VM，您需要完成下列程序：

* 選取 VM
* 選擇復原點
* 還原磁碟
* 從預存的磁碟建立 VM

下圖顯示從 RecoveryServicesVault 到 BackupRecoveryPoint 的物件階層。

![顯示 BackupContainer 的復原服務物件階層](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

若要還原備份資料，請識別已備份的項目和保存時間點資料的復原點。 然後使用 **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)**，將資料從保存庫還原至客戶的帳戶。

### <a name="select-the-vm"></a>選取 VM
若要取得可識別正確備份項目的 PowerShell 物件，請從保存庫中的容器開始，向下深入物件階層。 若要選取代表 VM 的容器，請使用 **[Get-AzureRmRecoveryServicesBackupContainer](https://msdn.microsoft.com/library/mt723319.aspx)** Cmdlet，並透過管道將它傳送到 **[Get-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723305.aspx)** Cmdlet。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -FriendlyName 'V2VM'
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>選擇復原點
使用 **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://msdn.microsoft.com/library/mt723308.aspx)** Cmdlet 來列出備份項目的所有復原點。 接下來選擇要還原的復原點。 如果您不確定要使用哪一個復原點，在清單中選擇最近的 RecoveryPointType = AppConsistent 點是好的做法。

在下列指令碼中，變數 **$rp**是已選取備份項目的復原點陣列。 陣列是以相反時間順序排序，最新復原點位於索引 0。 使用標準 PowerShell 陣列索引來挑選復原點。 例如：$rp[0] 會選取最新的復原點。

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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



### <a name="restore-the-disks"></a>還原磁碟
使用 **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** Cmdlet 將備份項目的資料和組態還原至復原點。 在您識別復原點之後，請使用它作為 **-RecoveryPoint** 參數的值。 在先前的範例程式碼中，**$rp[0]** 是要使用的復原點。 在接下來的範例程式碼中，**$rp[0]** 是要用來還原磁碟的復原點。

若要還原磁碟和組態資訊

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

您可以使用 **[Wait-AzureRmRecoveryServicesBackupJob](https://msdn.microsoft.com/library/mt723321.aspx)** 來等候還原作業完成。

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

完成還原作業之後，您可以使用 **[Get-AzureRmRecoveryServicesBackupJobDetails](https://msdn.microsoft.com/library/mt723310.aspx)** Cmdlet 來取得還原作業的詳細資料。 JobDetails 屬性具有重建 VM 所需的資訊。

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

一旦您還原磁碟，請移至下一節以取得建立 VM 的資訊。

## <a name="create-a-vm-from-restored-disks"></a>從還原的磁碟建立 VM
在您還原磁碟之後，使用下列步驟來從磁碟建立及設定虛擬機器。

> [!NOTE]
> 如果您要使用還原的磁碟來建立加密的 VM，您的 Azure 角色必須具備可執行 **Microsoft.KeyVault/vaults/deploy/action** 動作的權限。 如果您的角色並沒有此權限，請使用此動作來建立自訂角色。 如需更多詳細資料，請參閱 [Azure RBAC 中的自訂角色](../active-directory/role-based-access-control-custom-roles.md)。
>
>

1. 查詢工作詳細資料的已還原磁碟內容。

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. 設定 Azure 儲存體內容，並還原為 JSON 組態檔。

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName testvault
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. 使用 JSON 組態檔來建立 VM 組態。

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.HardwareProfile.VirtualMachineSize -VMName "testrestore"
    ```

4. 連接作業系統磁碟與資料磁碟。

  針對非加密的 VM，

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -CreateOption “Attach”
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType
    PS C:\> foreach($dd in $obj.StorageProfile.DataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach
     }
    ```

    針對加密的 VM，您必須在您連接磁碟之前，指定 [金鑰保存庫資訊](https://msdn.microsoft.com/library/dn868052.aspx) 。

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -DiskEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007" -DiskEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -KeyEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007" -KeyEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType
    PS C:\> foreach($dd in $obj.StorageProfile.DataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach
     }
    ```

5. 設定網路設定。

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. 建立虛擬機器。

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="next-steps"></a>後續步驟
如果您偏好使用 PowerShell 來與 Azure 資源互動，請參閱 PowerShell 文章：[部署和管理 Windows Server 的備份](backup-client-automation.md)。 另請參閱管理 DPM 備份的 PowerShell 文章：[部署和管理 DPM 的備份](backup-dpm-automation.md)。 這兩篇文章都有適用於 Resource Manager 部署和傳統部署的版本。  

