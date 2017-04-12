---
title: "在 Azure 中從 VHD 建立受控映像 | Microsoft Docs"
description: "從 Azure 儲存體帳戶中目前使用 Resource Manager 部署模型的 VHD 建立受控磁碟。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 51dbf591138434f1639dd6d4e93758927cff6d33
ms.lasthandoff: 03/31/2017


---

# <a name="create-managed-disks-from-unmanaged-disks-in-a-storage-account"></a>從儲存體帳戶中的非受控磁碟建立受控磁碟

您可以從現有的資料磁碟或 Azure 儲存體帳戶中目前的 OS 磁碟，建立受控磁碟。 您也可建立空的磁碟，以便作為 VM 的新資料磁碟。 

## <a name="before-you-begin"></a>開始之前
如果您使用 PowerShell，請確定您擁有最新版的 AzureRM.Compute PowerShell 模組。 執行下列命令來安裝它。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)。


## <a name="create-a-managed-disk-from-a-vhd-in-an-azure-storage-account"></a>從 Azure 儲存體帳戶中的 VHD 建立受控映像

在範例中，我們會從 VHD 建立磁碟作為受控磁碟，並將它指派給 **$disk1** 參數以供日後使用。 

受控磁碟會建立於 **myResourceGroup** 資源群組中的 [美國西部] 位置。 此磁碟將會命名為 **myDisk**，並且將從我們先前上傳至 **mystorageaccount** 儲存體帳戶的 VHD 檔案 (名為 **myDisk.vhd**) 建立。 受控磁碟將會建立於進階本機備援儲存體 (LRS) 中。 StandardLRS 和 PremiumLRS 是受控磁碟唯一可用的 **-AccountType** 選項。 

1.  設定一些參數

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $diskName = "myDisk"
    $vhdUri = "https://mystorageaccount.blob.core.windows.net/vhds/myDisk.vhd"
    ```

2. 建立資料磁碟。 
    ```powershell
    $disk1 = New-AzureRmDisk -DiskName $diskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $vhdUri) -ResourceGroupName $rgName
    ```
    
    

## <a name="create-an-empty-data-disk-as-a-managed-disk"></a>建立空的資料磁碟作為受控磁碟

在範例中，我們會建立空的資料磁碟作為受控磁碟，並將它指派給 **$dataDisk2** 參數以供日後使用。 空的資料磁碟一旦附加至執行中的 VM，就必須加以初始化，才能登入 VM 並使用 diskmgmt.msc，或[從遠端使用 WinRM 和指令碼](attach-disk-ps.md#initialize-the-disk)。

空的資料磁碟會建立於 **myResourceGroup** 資源群組中的 [美國中西部] 位置。 磁碟將命名為 **myEmptyDataDisk**。 空的磁碟將會建立於進階本機備援儲存體 (LRS) 中。 StandardLRS 和 PremiumLRS 是受控磁碟唯一可用的 **-AccountType** 選項。

在此範例中的磁碟大小為 128GB，但您選擇的大小應符合在您的 VM 上執行之任何應用程式的需求。

1.  設定一些參數

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $dataDiskName = "myEmptyDataDisk"
    ```

2. 建立資料磁碟。
    ```powershell
    $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128) -ResourceGroupName $rgName
    ```
    
## <a name="next-steps"></a>後續步驟    
- 如果您已經有 VM，即可[附加資料磁碟](attach-disk-portal.md)。
