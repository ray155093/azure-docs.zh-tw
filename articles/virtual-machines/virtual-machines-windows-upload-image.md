---
title: "上傳 Resource Manager 的 Windows VHD | Microsoft Docs"
description: "了解如何使用 Resource Manager 部署模型將以 Windows 為基礎的虛擬機器 VHD 從內部部署上傳至 Azure。 您可以從一般化或特製化的 VM 上傳 VHD。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 192c8e5a-3411-48fe-9fc3-526e0296cf4c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: cc14f7747c4a24acea434f62b7615d10819bd619
ms.openlocfilehash: a2df2169acda706a0fc3e1b7daf743ce29e5f030


---
# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>從內部部署 VM 將 Windows VHD 上傳至 Azure
本文說明如何建立及上傳要用於建立 Azure VM 的 Windows 虛擬硬碟 (VHD)。 您可以從一般化的 VM 或特製化的 VM 上傳 VHD。 

如需 Azure 中磁碟和 VHD 的詳細資訊，請參閱 [關於虛擬機器的磁碟和 VHD](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="prepare-the-vm"></a>準備 VM
您可以將一般化或特製化的 VHD 上傳至 Azure。 每個類型都需要您在開始之前準備 VM。

* **一般化 VHD** - 一般化 VHD 已使用 Sysprep 移除您所有的個人帳戶資訊。 如果您想要使用 VHD 作為建立新 VM 映像的來源，您應該︰
  
  * [準備要上傳至 Azure 的 Windows VHD](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
  * [使用 Sysprep 將虛擬機器一般化](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* **特製化 VHD** - 特製化的 VHD 會從原始的 VM 維護使用者帳戶、應用程式和其他狀態資料。 如果您想要使用 VHD 現狀建立新的 VM，請確定完成下列步驟。 
  
  * [準備要上傳至 Azure 的 Windows VHD](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **不要**使用 Sysprep 一般化 VM。
  * 移除任何 VM 上 (也就是 VMware 工具) 已安裝的來賓虛擬化工具和代理程式。
  * 確認已透過 DHCP 設定 VM 提取其 IP 位址和 DNS 設定。 這可確保伺服器在啟動時取得 VNet 內的 IP 位址。 

## <a name="log-in-to-azure"></a>登入 Azure
如果尚未安裝 Azure PowerShell 1.4 版或更高版本，請參閱 [How to install and configure Azure PowerShell (如何安裝和設定 Azure PowerShell)](/powershell/azureps-cmdlets-docs)。

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

2. 使用 [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) Cmdlet，在此資源群組中建立名為 **mystorageaccount**的儲存體帳戶：
   
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
使用 [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) Cmdlet，將映像上傳到儲存體帳戶中的容器。 這個範例會將檔案 **myVHD.vhd** 從 `"C:\Users\Public\Documents\Virtual hard disks\"` 上傳至 **myResourceGroup** 資源群組中名為 **mystorageaccount** 的儲存體帳戶。 檔案會放入名為 **mycontainer** 的容器，新的檔案名稱會是 **myUploadedVHD.vhd**。

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

## <a name="next-steps"></a>後續步驟
* [在 Azure 中從一般化 VHD 建立 VM](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [在 Azure 中從特製化的 VHD 建立 VM](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，方法為當您建立新的 VM 時將其附加做為作業系統磁碟。




<!--HONumber=Feb17_HO3-->


