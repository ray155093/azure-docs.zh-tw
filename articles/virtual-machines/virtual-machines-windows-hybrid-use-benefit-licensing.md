---
title: "適用於 Windows Server 的 Azure Hybrid Use Benefit | Microsoft Docs"
description: "了解如何發揮「Windows Server 軟體保證」的最大效益來將內部部署授權帶到 Azure"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: georgem
translationtype: Human Translation
ms.sourcegitcommit: 7167048a287bee7c26cfc08775dcb84f9e7c2eed
ms.openlocfilehash: df86e73814ceb0c5137c654bce84c8d42ae41820


---
# <a name="azure-hybrid-use-benefit-for-windows-server"></a>適用於 Windows Server 的 Azure Hybrid Use Benefit
若您是搭配軟體保證使用 Windows Server 的客戶，可將內部部署 Windows Server 授權帶到 Azure，並以較少的成本在 Azure 中執行 Windows Server VM。 Azure Hybrid Use Benefit 可讓您在 Azure 中執行 Windows Server 虛擬機器 (VM)，且只需支付基本計算費率。 如需詳細資訊，請瀏覽 [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)授權頁面。 本文說明如何在 Azure 中部署 Windows Server VM，以利用此授權的權益。


## <a name="ways-to-use-azure-hybrid-use-benefit"></a>如何使用 Azure Hybrid Use Benefit
有幾種不同方式可部署 Windows VM 與 Azure Hybrid Use Benefit︰

1. 如果您有具備 Enterprise 合約支援，您可以[從預先設有 Azure Hybrid Use Benefit 的特定 Marketplace 映象部署 VM](#deploy-a-vm-using-the-azure-marketplace)。
2. 若您沒有 Enterprise 合約，則可使用 Resource Manager 範本或 [Azure PowerShell](#detailed-powershell-deployment-walkthrough)，[上傳自訂 VM](#upload-a-windows-server-vhd) 及[進行部署](#deploy-a-vm-via-resource-manager)。

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>使用 Azure Marketplace 部署 VM
對於具備 [Enterprise 合約訂用帳戶](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)的客戶，可以在預先設有 Azure Hybrid Use Benefit 的 Marketplace 使用映象。 這些映像可以直接從 Azure 入口網站、Resource Manager 範本或 Azure PowerShell 部署，例如。 Marketplace 中的映像會以 `[HUB]` 名稱註明，如下所示︰

![Azure Marketplace 中的 Azure Hybrid Use Benefit 映像](./media/virtual-machines-windows-hybrid-use-benefit/ahub-images-portal.png)

您可以直接從 Azure 入口網站部署這些映像。 若要在 Resource Manager 範本中使用，並與 Azure PowerShell 搭配使用，請檢視映像的清單，如下所示︰

```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "WindowsServer-HUB"
```

如果您沒有 Enterprise 合約訂用帳戶，請繼續閱讀如何上傳自訂 VM 並使用 Azure Hybrid Use Benefit 進行部署的指示。


## <a name="upload-a-windows-server-vhd"></a>上傳 Windows Server VHD
若要在 Azure 中部署 Windows Server VM，您必須先建立包含基底 Windows Server 組建的 VHD。 您必須先透過 Sysprep 妥善準備這個 VHD，再將其上傳至 Azure。 您可以深入了解 [VHD 需求和 Sysprep 處理序](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)及 [伺服器角色的 Sysprep 支援](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。 執行 Sysprep 前，請先備份 VM。 

確定您已 [安裝並設定最新的 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 備妥 VHD 之後，請使用 `Add-AzureRmVhd` Cmdlet，將 VHD 上傳到 Azure 儲存體帳戶，如下所示：

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server、SharePoint 伺服器、Dynamics 也可以使用您的軟體保證授權。 您仍然需要安裝應用程式元件，並提供相應的授權金鑰，然後上傳磁碟映像至 Azure，藉此準備 Windows Server 映像。 檢閱以您的應用程式執行 Sysprep 的相關文件，例如 [使用 Sysprep 安裝 SQL Server 的考量](https://msdn.microsoft.com/library/ee210754.aspx)或 [建置 SharePoint Server 2016 參照映像 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)。
>
>

您也可以深入了解 [將 VHD 上傳至 Azure 的程序](virtual-machines-windows-upload-image.md#upload-the-vhd-to-your-storage-account)


## <a name="deploy-an-uploaded-vm-via-resource-manager"></a>透過 Resource Manager 部署已上傳的 VM
在 Resource Manager 範本內，可以指定 `licenseType` 的額外參數。 您可以進一步了解如何 [製作 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。 將 VHD 上傳至 Azure 之後，請編輯 Resource Manager 範本以將授權類型納入計算提供者，之後再照常部署範本即可：

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```


## <a name="deploy-an-uploaded-vm-via-powershell-quickstart"></a>透過 PowerShell 快速入門部署已上傳的 VM
透過 PowerShell 部署 Windows Server VM 時，您會有 `-LicenseType`的額外參數。 將 VHD 上傳至 Azure 之後，使用 `New-AzureRmVM` 建立 VM 並指定授權類型，如下所示：

```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

您可於下方進一步了解[透過 PowerShell 在 Azure 中部署 VM 的詳細逐步解說](virtual-machines-windows-hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough)，或閱讀[使用 Resource Manager 和 PowerShell 建立 Windows VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 中不同步驟的描述說明。


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>確認您的 VM 可享受授權權益
透過 PowerShell 或 Resource Manager 部署方法部署 VM 之後，請依下列方式使用 `Get-AzureRmVM` 來驗證授權類型：

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

輸出類似於下列範例：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

下列 VM 在部署時未提供 Azure Hybrid Use Benefit 授權 (例如直接從 Azure 資源庫部署 VM)，您可看出此輸出的差異：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-deployment-walkthrough"></a>詳細的 PowerShell 部署逐步解說
下列是說明完整部署 VM 的詳細 PowerShell 步驟。 您可以閱讀 [使用 Resource Manager 和 PowerShell 建立 Windows VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)深入了解更多內容，包括實際的 Cmdlet 和所建立的不同元件。 您將逐步進行建立資源群組、儲存體帳戶和虛擬網路，然後定義 VM 並完成建立 VM 的步驟。

首先，安全地取得認證，並設定位置和資源群組名稱：

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

建立公用 IP：

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

定義子網路、NIC 和 VNET：

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

命名 VM，並建立 VM 設定：

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

定義您的 OS：

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

將 NIC 加入 VM：

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

定義要使用的儲存體帳戶：

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

上傳備妥的 VHD，並附加至 VM 以供使用：

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

最後，建立 VM 並定義授權類型，以採用 Azure Hybrid Use Benefit：

```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

## <a name="next-steps"></a>後續步驟
深入了解 [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)授權。

深入了解如何[使用 Resource Manager 範本](../azure-resource-manager/resource-group-overview.md)。



<!--HONumber=Jan17_HO1-->


