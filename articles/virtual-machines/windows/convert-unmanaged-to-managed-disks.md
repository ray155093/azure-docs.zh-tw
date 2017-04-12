---
title: "將 VM 從非受控磁碟轉換為受控磁碟 - Azure | Microsoft Docs"
description: "在 Resource Manager 部署模型中使用 PowerShell 將 VM 從非受控磁碟轉換為非受控磁碟"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6530da96cd6e6ccd90714a9d3c9f00f88afe853e
ms.lasthandoff: 04/03/2017


---
# <a name="convert-a-vm-from-unmanaged-disks-to-managed-disks"></a>將 VM 從非受控磁碟轉換為受控磁碟

如果現有的 Azure VM 使用儲存體帳戶中的非受控磁碟，而且您希望能夠利用[受控磁碟](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，您可以轉換 VM。 此程序會將 OS 磁碟和任何附加的資料磁碟，從使用儲存體帳戶中的非受控磁碟轉換為使用受控磁碟。 VM 會關閉並解除配置，然後您可使用 Powershell 將 VM 轉換為使用受控磁碟。 轉換之後，您會重新啟動 VM，而 VM 現在將使用受控磁碟。

開始之前，請務必先檢閱[規劃移轉至受控磁碟](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)。
因為移轉程序無法反轉，所以請在生產環境中執行移轉之前，藉由移轉測試虛擬機器來測試移轉程序。


> [!IMPORTANT] 
> 在轉換期間，您將會解除配置 VM。 解除備置 VM 表示在轉換後啟動 VM 時，VM 將有新的 IP 位址。 如果您相依於固定 IP，應使用保留的 IP。


## <a name="managed-disks-and-azure-storage-service-encryption-sse"></a>受控磁碟和 Azure 儲存體服務加密 (SSE)

如有任何附加的非受控磁碟位於使用 (或曾經使用) [Azure 儲存體服務加密 (SSE)](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 加密的儲存體帳戶，您就無法將在 Resource Manager 部署模型中建立的非受控 VM 轉換為受控磁碟。 下列步驟詳細說明如何轉換位於 (曾經位於) 已加密儲存體帳戶中的非受控 VM︰

**資料磁碟**：
1.    從 VM 卸離資料磁碟。
2.    將 VHD 複製到具有從未啟用過 SSE 的儲存體帳戶。 若要將磁碟複製到另一個儲存體帳戶，請使用[AzCopy](../../storage/storage-use-azcopy.md)：`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myDataDisk.vhd`
3.    將所複製的磁碟附加至 VM 並轉換 VM。

**OS 磁碟**：
1.    停止已解除配置的 VM。 視需要儲存 VM 組態。
2.    將 OS VHD 複製到具有從未啟用過 SSE 的儲存體帳戶。 若要將磁碟複製到另一個儲存體帳戶，請使用[AzCopy](../../storage/storage-use-azcopy.md)：`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myVhd.vhd`
3.    建立使用受控磁碟的 VM，並且在建立期間附加該 VHD 檔案作為 OS 磁碟。

## <a name="convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set"></a>將可用性設定組中的 VM 轉換為受控可用性設定組中的受控磁碟

如果您想要轉換為受控磁碟的 VM 位於可用性設定組中，您必須先將此可用性設定組轉換為受控可用性設定組。

下列指令碼會將可用性設定組更新為受控可用性設定組，接著解除配置、轉換磁碟，然後重新啟動可用性設定組中的每個 VM。

```powershell
$rgName = 'myResourceGroup'
$avSetName = 'myAvailabilitySet'

$avSet =  Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Managed

foreach($vmInfo in $avSet.VirtualMachinesReferences)
    {
   $vm =  Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}

   Stop-AzureRmVM -ResourceGroupName $rgName -Name  $vm.Name -Force

   ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   
    }
```

## <a name="convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type"></a>將現有的 Azure VM 轉換為相同儲存體類型的受控磁碟

本節說明當您要使用相同的儲存體類型時，如何將現有的 Azure VM 從儲存體帳戶中的非受控磁碟轉換為受控磁碟。 您可以使用此程序從進階 (SDD) 非受控磁碟移至進階受控磁碟，或從標準 (HDD) 非受控磁碟移至標準受控磁碟。 

1. 建立變數及解除配置 VM。 這個範例會將資源群組名稱設定為 **myResourceGroup**，以及將 VM 名稱設定為 **myVM**。

    ```powershell
    $rgName = "myResourceGroup"
    $vmName = "myVM"
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
   
    Azure 入口網站中 VM 的 [狀態] 會從 [已停止] 變更為 [已停止 (已解除配置)]。
    
2. 轉換 VM 的所有相關磁碟，包括 OS 磁碟和任何資料磁碟。

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
    ```


## <a name="migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks"></a>將使用標準非受控磁碟的現有 Azure VM 移轉到進階受控磁碟

本節說明如何將標準非受控磁碟上的現有 Azure VM 轉換為進階受控磁碟。 若要使用進階受控磁碟，您的 VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。


1.  首先，設定一般參數。 確保您選取的 [VM 大小](sizes.md)支援進階儲存體。

    ```powershell
    $resourceGroupName = 'YourResourceGroupName'
    $vmName = 'YourVMName'
    $size = 'Standard_DS2_v2'
    ```
1.  取得具有非受控磁碟的 VM

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
    ```
    
1.  停止 (解除配置) VM。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```

1.  將 VM 大小更新為 VM 所在區域中進階儲存體可支援的大小。

    ```powershell
    $vm.HardwareProfile.VmSize = $size
    Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroupName
    ```

1.  將具有非受控磁碟的虛擬機器轉換為受控磁碟。 

    如果您收到內部伺服器錯誤，請在聯繫我們的支援小組之前，重試 2-3 次。

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
1. 停止 (解除配置) VM。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```
2.  將所有磁碟升級為進階儲存體。

    ```powershell
    $vmDisks = Get-AzureRmDisk -ResourceGroupName $resourceGroupName 
    foreach ($disk in $vmDisks) 
        {
        if($disk.OwnerId -eq $vm.Id)
            {
             $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
             Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $resourceGroupName `
             -DiskName $disk.Name
            }
        }
    ```
1. 啟動 VM。

    ```powershell
    Start-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    ```
    
您也可以有混合使用標準和進階儲存體的磁碟。
    

## <a name="next-steps"></a>後續步驟

使用[快照集](snapshot-copy-managed-disk.md)來取得 VM 的唯讀複本。


