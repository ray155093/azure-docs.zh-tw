<properties 
   pageTitle="VM 的常用 PowerShell 命令 | Microsoft Azure"
   description="常用的 PowerShell 命令，可讓您開始在 Windows 上建立及管理 Azure 中的 VM"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="davidmu" />

# 用於建立及管理 VM 的常用 PowerShell 命令

本文涵蓋一些 Azure PowerShell 命令，您可用來建立及管理您的 Azure 訂用帳戶中的虛擬機器。如需特定命令列參數和選項的詳細說明，您可以使用 **Get-Help** 命令。

## 使用 Azure PowerShell 建立資源

如需如何安裝最新版 Azure PowerShell 的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)，並選取您要使用的訂用帳戶，然後登入您的 Azure 帳戶。

工作 | 命令
-------------- | -------------------------
建立 VM 組態 | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "vm\_name" -VMSize "vm\_size"<BR></BR><BR></BR>VM 組態用來定義或更新 VM 的設定。系統會使用 VM 的名稱及其[大小](virtual-machines-windows-sizes.md)初始化組態。
新增組態設定 | $vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "computer\_name" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>作業系統設定包括加入至您先前使用 New-AzureRmVMConfig 建立之組態物件的[認證](https://technet.microsoft.com/library/hh849815.aspx)。
新增網路介面 | $vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM 必須有[網路介面](virtual-machines-windows-ps-create.md)才能在虛擬網路中通訊。您也可以使用 [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) 擷取現有的網路介面物件。
指定平台映像 | $vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "publisher\_name" -Offer "publisher\_offer" -Skus "product\_sku" -Version "latest"<BR></BR><BR></BR>[映像資訊](virtual-machines-windows-cli-ps-findimage.md)會加入至您先前使用 New-AzureRmVMConfig 建立的組態物件。只有在您設定作業系統磁碟使用平台映像時，才會使用此命令傳回的物件。
設定作業系統磁碟使用平台映像 | $vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "disk\_name" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption FromImage<BR></BR><BR></BR>作業系統磁碟的名稱及它將位於[儲存體](../storage/storage-powershell-guide-full.md)中的位置會加入至您先前建立的組態物件。
設定作業系統磁碟使用一般化映像 | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "disk\_name" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>作業系統磁碟的名稱、來源映像的位置，以及磁碟將位於[儲存體](../storage/storage-powershell-guide-full.md)中的位置會加入至您先前建立的組態物件。
設定作業系統磁碟使用一般化映像 | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "name\_of\_disk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows
建立 VM | [New-AzureRmVM]() -ResourceGroupName "resource\_group\_name" -Location "location\_name" -VM $vm<BR></BR><BR></BR>所有資源都會建立於[資源群組](../powershell-azure-resource-manager.md)中。VM 必須建立於與資源群組相同的[位置](https://msdn.microsoft.com/library/azure/dn495177.aspx)。執行此命令之前，請執行 New-AzureRmVMConfig、Set-AzureRmVMOperatingSystem、Set-AzureRmVMSourceImage、Add-AzureRmVMNetworkInterface 和 Set-AzureRmVMOSDisk。
列出訂用帳戶中的 VM| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
列出資源群組中的 VM | Get-AzureRmVM -ResourceGroupName "resource\_group\_name"<BR></BR><BR></BR>若要取得您的訂用帳戶中的資源群組清單，請使用 [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx)。
取得 VM 的相關資訊 | Get-AzureRmVM -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
啟動 VM | [Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
停止 VM | [Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
重新啟動執行中的 VM | [Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
刪除 VM | [Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
一般化 VM | [Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "vm\_name" -Generalized<BR></BR><BR></BR>您必須在執行 Save-AzureRmVMImage 前執行此命令。
擷取 VM | [Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "resource\_group\_name" -VMName "vm\_name" -DestinationContainerName "image\_container" -VHDNamePrefix "image\_name\_prefix" -Path "C:\\filepath\\filename.json"<BR></BR><BR></BR>虛擬機器必須[關閉並一般化](virtual-machines-windows-capture-image.md)，才能用來建立映像。請在執行此命令前，執行 Set-AzureRmVm。
更新 VM | [Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "resource\_group\_name" -VM $vm<BR></BR><BR></BR>使用 Get-AzureRmVM 取得目前的 VM 組態，變更 VM 物件上的組態設定，然後執行此命令。
將資料磁碟新增至 VM | [Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "disk\_name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>使用 Get-AzureRmVM 來取得 VM 物件。指定磁碟的 LUN 編號和大小。執行 Update-AzureRmVM 以將組態變更套用至 VM。您加入的磁碟並未初始化，若要這麼做，請參閱[使用 Resource Manager 和 PowerShell 管理 Azure 虛擬機器](virtual-machines-windows-ps-manage.md)。
從 VM 移除資料磁碟 | [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "disk\_name"<BR></BR><BR></BR>使用 Get-AzureRmVM 來取得 VM 物件。執行 Update-AzureRmVM 以將組態變更套用至 VM。
將延伸模組新增至 VM | [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "resource\_group\_name" -Location "azure\_location" -VMName "vm\_name" -Name "extension\_name" -Publisher "publisher\_name" -Type "extension\_type" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>使用您要安裝之延伸模組的適當[組態資訊](virtual-machines-windows-extensions-configuration-samples.md)，執行此命令。
移除 VM 延伸模組 | [Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "resource\_group\_name" -Name "extension\_name" -VMName "vm\_name"

## 後續步驟

- 請參閱[使用 Resource Manager 和 PowerShell 建立 Windows VM](virtual-machines-windows-ps-create.md) 中建立虛擬機器的基本步驟。

<!---HONumber=AcomDC_0629_2016-->