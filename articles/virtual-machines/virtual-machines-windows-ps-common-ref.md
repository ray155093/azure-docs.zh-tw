---
title: "VM 的常用 PowerShell 命令 | Microsoft Docs"
description: "常用的 PowerShell 命令，可讓您開始在 Windows 上建立及管理 Azure 中的 VM"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 39739902815dacd40d4d57d503344217d4fa112d


---
# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>用於建立及管理 VM 的常用 PowerShell 命令
本文涵蓋一些 Azure PowerShell 命令，您可用來建立及管理您的 Azure 訂用帳戶中的虛擬機器。  如需特定命令列參數和選項的詳細說明，您可以使用 **Get-Help** *命令*。

如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## <a name="create-a-vm"></a>建立 VM
| Task | 命令 |
| --- | --- |
| 建立 VM 組態 |$vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "vm_name" -VMSize "vm_size"<BR></BR><BR></BR>VM 組態用來定義或更新 VM 的設定。 系統會使用 VM 的名稱及其 [大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)初始化組態。 |
| 新增組態設定 |$vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "computer_name" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>包含[認證](https://technet.microsoft.com/library/hh849815.aspx)的作業系統設定會新增至您先前使用 New-AzureRmVMConfig 建立的組態物件。 |
| 新增網路介面 |$vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM 必須有[網路介面](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)才可在虛擬網路中進行通訊。 您也可以使用 [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) 擷取現有的網路介面物件。 |
| 指定平台映像 |$vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[映像資訊](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 會新增至您先前使用 New-AzureRmVMConfig 建立的組態物件。 只有在您設定作業系統磁碟使用平台映像時，才會使用此命令傳回的物件。 |
| 設定作業系統磁碟使用平台映像 |$vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "disk_name" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage<BR></BR><BR></BR>作業系統磁碟的名稱及其在[儲存體](../storage/storage-powershell-guide-full.md)中的位置會新增至您先前建立的組態物件。 |
| 設定作業系統磁碟使用一般化映像 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "disk_name" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>作業系統磁碟的名稱、來源映像的位置，以及磁碟在[儲存體](../storage/storage-powershell-guide-full.md)中的位置會新增至組態物件。 |
| 設定作業系統磁碟使用一般化映像 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "name_of_disk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| 建立 VM |[New-AzureRmVM]() -ResourceGroupName "resource_group_name" -Location "location_name" -VM $vm<BR></BR><BR></BR>所有資源都會在[資源群組](../powershell-azure-resource-manager.md)中建立。 VM 必須建立於與資源群組相同的 [位置](https://msdn.microsoft.com/library/azure/dn495177.aspx) 。 執行此命令之前，請執行 New-AzureRmVMConfig、Set-AzureRmVMOperatingSystem、Set-AzureRmVMSourceImage、Add-AzureRmVMNetworkInterface 和 Set-AzureRmVMOSDisk。 |

## <a name="get-information-about-vms"></a>取得 VM 的相關資訊
| Task | 命令 |
| --- | --- |
| 列出訂用帳戶中的 VM |[Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx) |
| 列出資源群組中的 VM |Get-AzureRmVM -ResourceGroupName "resource_group_name"<BR></BR><BR></BR>若要取得您的訂用帳戶中的資源群組清單，請使用 [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx)初始化組態。 |
| 取得 VM 的相關資訊 |Get-AzureRmVM -ResourceGroupName "resource_group_name" -Name "vm_name" |

## <a name="manage-vms"></a>管理 VM
| Task | 命令 |
| --- | --- |
| 啟動 VM |[Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| 停止 VM |[Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| 重新啟動執行中的 VM |[Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| 刪除 VM |[Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| 一般化 VM |[Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "vm_name" -Generalized<BR></BR><BR></BR>您在執行 Save-AzureRmVMImage 前需執行此命令。 |
| 擷取 VM |[Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "resource_group_name" -VMName "vm_name" -DestinationContainerName "image_container" -VHDNamePrefix "image_name_prefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>必須[關閉並一般化](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)虛擬機器以用來建立映像。 請在執行此命令前，執行 Set-AzureRmVm。 |
| 更新 VM |[Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "resource_group_name" -VM $vm<BR></BR><BR></BR>使用 Get-AzureRmVM 取得目前的 VM 組態，變更 VM 物件上的組態設定，然後執行此命令。 |
| 將資料磁碟新增至 VM |[Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "disk_name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>使用 Get-AzureRmVM 來取得 VM 物件。 指定磁碟的 LUN 編號和大小。 執行 Update-AzureRmVM 以將組態變更套用至 VM。 您新增的磁碟尚未初始化。 如需在新增磁碟時將其初始化的相關資訊，請參閱[使用 Resource Manager 和 PowerShell 來管理 Azure 虛擬機器](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 |
| 從 VM 移除資料磁碟 |[Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "disk_name"<BR></BR><BR></BR>使用 Get-AzureRmVM 來取得 VM 物件。 執行 Update-AzureRmVM 以將組態變更套用至 VM。 |
| 將延伸模組新增至 VM |[Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "resource_group_name" -Location "azure_location" -VMName "vm_name" -Name "extension_name" -Publisher "publisher_name" -Type "extension_type" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>使用您要安裝之擴充功能的適當[組態資訊](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)執行這個命令。 |
| 移除 VM 延伸模組 |[Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "resource_group_name" -Name "extension_name" -VMName "vm_name" |

## <a name="next-steps"></a>後續步驟
* 請參閱 [使用 Resource Manager 和 PowerShell 建立 Windows VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中建立虛擬機器的基本步驟。




<!--HONumber=Nov16_HO3-->


