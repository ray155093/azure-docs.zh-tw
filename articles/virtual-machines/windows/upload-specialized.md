---
title: "將特製化 VHD 上傳至 Azure 以用來建立新的 VM | Microsoft Docs"
description: "將特製化 VHD 上傳至 Azure 以用來建立新的 VM。"
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
ms.date: 02/05/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0abff0e82aeb02b73ab21cd297abd1178e49944d
ms.lasthandoff: 04/27/2017


---

# <a name="how-to-upload-a-specialized-vhd-to-create-a-vm-in-azure"></a>如何在 Azure 中上傳特製化 VHD 以建立 VM

特製化的 VHD 會從原始的 VM 維護使用者帳戶、應用程式和其他狀態資料。 您可以將特製化的 VHD 上傳至 Azure，並用來建立使用受控磁碟或未受管理儲存體帳戶的 VM。 我們建議您改用[受控磁碟](../../storage/storage-managed-disks-overview.md)，以簡化管理及利用受控磁碟提供的其他功能。


> [!IMPORTANT]
> 將任何 VHD 上傳至 Azure 之前，您應該遵循[準備 Windows VHD 或 VHDX 以上傳至 Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


* 如需各種 VM 大小的價格，請參閱[虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)。
* 如需儲存體價格的相關資訊，請參閱[儲存體價格](https://azure.microsoft.com/pricing/details/storage/blobs/)。 
* 如需了解 Azure 區域中的 VM 大小可用性，請參閱 [依區域提供的產品](https://azure.microsoft.com/regions/services/)。
* 若要查看 Azure VM 的一般限制，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../../azure-subscription-service-limits.md)。

## <a name="before-you-begin"></a>開始之前
如果您使用 PowerShell，請確定您擁有最新版的 AzureRM.Compute PowerShell 模組。 執行下列命令來安裝它。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](/powershell/azure/overview)。


## <a name="prepare-the-vm"></a>準備 VM
 
如果您想要將特製化 VHD 依現狀用來建立新的 VM，請確定完成下列步驟。 
  * 如果您要使用受控磁碟，請檢閱[規劃移轉至受控磁碟](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)。
  * [準備要上傳至 Azure 的 Windows VHD](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **不要**使用 Sysprep 一般化 VM。
  * 移除任何 VM 上 (也就是 VMware 工具) 已安裝的來賓虛擬化工具和代理程式。
  * 確認已透過 DHCP 設定 VM 提取其 IP 位址和 DNS 設定。 這可確保伺服器在啟動時取得 VNet 內的 IP 位址。 
  * 關閉 VM 後再繼續。

## <a name="log-in-to-azure"></a>登入 Azure
如果尚未安裝 Azure PowerShell 1.4 版或更高版本，請參閱 [How to install and configure Azure PowerShell (如何安裝和設定 Azure PowerShell)](/powershell/azure/overview)。

1. 開啟 Azure PowerShell，並登入您的 Azure 帳戶。 這會開啟一個可供您輸入 Azure 帳戶認證的快顯視窗。
   
    ```powershell
    Login-AzureRmAccount
    ```
2. 取得您可用訂用帳戶的訂用帳戶識別碼。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 使用訂用帳戶識別碼來設定正確的訂用帳戶。 使用正確訂用帳戶的識別碼取代 `<subscriptionID>`。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>取得儲存體帳戶
您需要一個 Azure 中的儲存體帳戶來裝載上傳的 VM 映像。 您可以使用現有的儲存體帳戶或建立新帳戶。 

如果您要使用 VHD 為 VM 建立受控磁碟，儲存體帳戶位置與您將建立 VM 的位置必須相同。

若要顯示可用的儲存體帳戶，請輸入︰

```powershell
Get-AzureRmStorageAccount
```

如果您想要使用現有的儲存體帳戶，請移至[上傳 VM 映像](#upload-the-vm-vhd-to-your-storage-account)一節。

如果您需要建立儲存體帳戶，請依照下列步驟操作：

1. 您需要在當中建立儲存體帳戶的資源群組名稱。 若要找出您訂用帳戶中的所有資源群組，請輸入︰
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    若要在**美國中部**區域建立名為 **myResourceGroup** 的資源群組，請輸入︰

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. 使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) Cmdlet，在此資源群組中建立名為 **mystorageaccount**的儲存體帳戶：
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    -SkuName 的有效值包括：
   
   * **Standard_LRS** - 本地備援儲存體。 
   * **Standard_ZRS** - 區域備援儲存體。
   * **Standard_GRS** - 異地備援儲存體。 
   * **Standard_RAGRS** - 讀取權限異地備援儲存體。 
   * **Premium_LRS** - 進階本地備援儲存體。 

## <a name="upload-the-vhd-to-your-storage-account"></a>將 VHD 上傳至儲存體帳戶

使用 [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) Cmdlet，將 VHD 上傳至儲存體帳戶中的容器。 這個範例會將檔案 **myVHD.vhd** 從 `"C:\Users\Public\Documents\Virtual hard disks\"` 上傳至 **myResourceGroup** 資源群組中名為 **mystorageaccount** 的儲存體帳戶。 檔案會放入名為 **mycontainer** 的容器，新的檔案名稱會是 **myUploadedVHD.vhd**。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


如果成功，您會得到看起來如以下的回應：

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

視您的網路連線和 VHD 檔案大小而定，此命令可能需要一些時間才能完成

儲存 [目的地 URI] 路徑，以便日後想要使用上傳的 VHD 建立受控磁碟或新的 VM 時使用。

### <a name="other-options-for-uploading-a-vhd"></a>上傳 VHD 的其他選項

您也可以使用下列其中一種方法將 VHD 上傳至儲存體帳戶：

-   [Azure 儲存體複製 Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Azure 儲存體總管上傳 Blob](https://azurestorageexplorer.codeplex.com/)

-   [儲存體匯入/匯出服務 REST API 參考](https://msdn.microsoft.com/library/dn529096.aspx)

    如果預估的上傳時間長度超過 7 天，我們建議使用匯入/匯出服務。 您可以使用 [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) 從資料大小和傳輸單位來評估時間。 

    匯入/匯出可用來複製到標準儲存體帳戶。 您必須使用 AzCopy 之類的工具，從 Standard 儲存體帳戶複製到進階儲存體帳戶。

    
## <a name="create-the-subnet-and-vnet"></a>建立子網路和 VNet

建立 [虛擬網路](../../virtual-network/virtual-networks-overview.md)的 vNet 和 subNet。

1. 建立子網路。 此範例會在資源群組 **myResourceGroup** 中建立名為 **mySubnet** 的子網路，並將子網路位址首碼設定為 **10.0.0.0/24**。
   
    ```powershell
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 建立 vNet。 此範例會將虛擬網路名稱設定為 **myVnetName**、位置設定為 **美國西部**，及虛擬網路的位址首碼設定為 **10.0.0.0/16**。 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>建立公用 IP 位址和 NIC
若要能夠與虛擬網路中的虛擬機器進行通訊，您需要 [公用 IP 位址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和網路介面。

1. 建立公用 IP。 在此範例中，公用 IP 位址名稱會設定為 **myIP**。
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. 建立 NIC。 在此範例中，NIC 名稱會設定為 **myNicName**。
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>建立網路安全性群組和 RDP 規則
若要能夠使用 RDP 登入 VM，您必須有可在連接埠 3389 上允許 RDP 存取的安全性規則。 因為新 VM 的 VHD 是從現有的特製化 VM 所建立，在建立 VM 之後，您可以從具有使用 RDP 之登入權限的來源虛擬機器使用現有帳戶。

此範例會將 NSG 名稱設定為 **myNsg** 以及將 RDP 規則名稱設定為 **myRdpRule**。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

如需端點和 NSG 規則的詳細資訊，請參閱[使用 PowerShell 對 Azure 中的 VM 開啟連接埠](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="set-the-vm-name-and-size"></a>設定 VM 名稱和大小

此範例將 VM 名稱設定為 "myVM"，將 VM 大小設定為 "Standard_A2"。

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>新增 NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>設定 OS 磁碟

特製化 OS 可以是您[上傳至 Azure](upload-image.md) 的 VHD，或[現有 Azure VM 中的 VHD 複本](vhd-copy.md)。 

您有兩個選項可選擇：
- **選項 1**︰從現有儲存體帳戶中的特製化 VHD 建立特製化受控磁碟，作為 OS 磁碟。

或 

- **選項 2**︰使用您自己的儲存體帳戶中儲存的特製化 VHD (非受控磁碟)。 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>選項 1︰從非受控的特製化磁碟建立受控磁碟

1. 從儲存體帳戶中現有的特製化 VHD 建立受控磁碟。 這個範例使用 **myOSDisk1** 作為磁碟名稱，將磁碟放入 **StandardLRS** 儲存體，並使用 **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** 作為來源 VHD 的 URI。

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri $urlOfUploadedImageVhd -ResourceGroupName $rgName
    ```

2. 將 OS 磁碟新增至組態。 這個範例將磁碟大小設定為 **128GB**，並附加受控磁碟作為 **Windows** OS 磁碟。
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -ManagedDiskStorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

選擇性︰附加其他受控磁碟作為資料磁碟。 此選項假設您已依照[建立受控資料磁碟](create-managed-disk-ps.md)來建立受控資料磁碟。 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>選項 2︰附加現有儲存體帳戶中的 VHD

1. 為您想要使用的 VHD 設定 URI。 在此範例中，VHD 檔案 **myOsDisk.vhd** 保存在容器 **myContainer** 中的儲存體帳戶 **myStorageAccount**。

    ```powershell
    $osDiskUri = $urlOfUploadedImageVhd
    ```
2. 使用所複製 OS VHD 的 URL 新增 OS 磁碟。 在此範例中，建立 OS 磁碟時，"osDisk" 一詞會附加至 VM 名稱而形成 OS 磁碟名稱。 這個範例也指定這個以 Windows 為基礎的 VHD，應該附加至 VM 作為 OS 磁碟。
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

選擇性︰如果您有資料磁碟需要附加至 VM，請使用資料 VHD 的 URL 和適當的邏輯單元編號 (Lun) 新增資料磁碟。

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

使用儲存體帳戶時，資料和作業系統磁碟的 URL 近似於︰`https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`。 您可以藉由下列方法在入口網站上找到它：瀏覽至目標儲存體容器，按一下複製的作業系統或資料 VHD，然後複製 URL 的內容。


## <a name="create-the-vm"></a>建立 VM

使用我們剛才建立的組態建立 VM。

```powershell
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

如果此命令成功，您會看到如下的輸出︰

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>確認已建立 VM
從 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器]下，或是使用下列 PowerShell 命令，應可看到新建立的 VM：

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>後續步驟
若要登入新的虛擬機器，請瀏覽至[入口網站](https://portal.azure.com)中的 VM，按一下 [連接] 並開啟遠端桌面 RDP 檔案。 使用原始虛擬機器的帳戶認證來登入新的虛擬機器。 如需詳細資訊，請參閱 [如何連接和登入執行 Windows 的 Azure 虛擬機器](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

