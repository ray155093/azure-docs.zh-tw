<properties
   pageTitle="適用於 Windows Server 的 Azure Hybrid Use Benefit | Microsoft Azure"
   description="了解如何發揮 Windows Server 軟體保證的最大效益，將內部部署授權帶到 Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="05/03/2016"
   ms.author="georgem"/>

# 適用於 Windows Server 的 Azure Hybrid Use Benefit

若您是搭配軟體保證使用 Windows Server 的客戶，可將內部部署 Windows Server 授權帶到 Azure，並以較少的成本在 Azure 中執行 Windows Server VM。Azure Hybrid Use Benefit 可讓您在 Azure 中執行 Windows Server VM，且只需支付基本計算費率。如需詳細資訊，請瀏覽 [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) 授權頁面。本文說明如何在 Azure 中部署 Windows Server VM，以利用此授權的權益。

> [AZURE.NOTE] 如果要採用 Azure Hybrid Use Benefit，您就不能使用 Azure Marketplace 映像來部署 Windows Server VM。您必須使用 PowerShell 或 Resource Manager 範本部署您的 VM，以將 VM 正確註冊為符合基本計算費率折扣的資格。

## 必要條件
若要將 Azure Hybrid Use Benefit 運用在 Azure 中的 Windows Server VM，有下列幾項必要條件：

- 安裝 Azure PowerShell 模組
- 將 Windows Server VHD 上傳到「Azure 儲存體」

### 安裝 Azure PowerShell
如需如何安裝最新版 Azure PowerShell 的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)，並選取您要使用的訂用帳戶，然後登入您的 Azure 帳戶。即使您要使用 Resource Manager 範本部署 VM，仍需要安裝 Azure PowerShell 才能上傳 Windows Server VHD (請參閱下方的下一個步驟)。

### 上傳 Windows Server VHD

若要在 Azure 中部署 Windows Server VM，您必須先建立包含基底 Windows Server 組建的 VHD。您必須先透過 Sysprep 妥善準備這個 VHD，再將其上傳至 Azure。您可以深入了解 [VHD 需求和 Sysprep 處理序](./virtual-machines-windows-upload-image.md)。備妥 VHD 之後，您即可使用 `Add-AzureRmVhd` Cmdlet，將 VHD 上傳到 Azure 儲存體帳戶，如下所示：

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

您也可以進一步了解[將 VHD 上傳至 Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account) 的程序。

> [AZURE.TIP] 本文會重點說明 Windows Server VM 的部署，不過您也可以透過相同的方式來部署 Windows 用戶端 VM。在下列範例中，請將 `Server` 妥善取代為 `Client`。

## 透過 PowerShell 快速入門部署 VM
透過 PowerShell 部署 Windows Server VM 時，您會有 `-LicenseType` 的額外參數。將 VHD 上傳至 Azure 之後，請使用 `New-AzureRmVM` 建立新的 VM 並指定授權類型，如下所示：

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm
    -LicenseType Windows_Server
```

您可於下方進一步了解[透過 PowerShell 在 Azure 中部署 VM 的詳細逐步解說](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough)，或閱讀[使用 Resource Manager 和 PowerShell 建立 Windows VM](./virtual-machines-windows-ps-create.md) 中不同步驟的描述說明。

## 透過 Resource Manager 部署 VM
在 Resource Manager 範本內，可以指定 `licenseType` 的額外參數。您可以進一步了解如何[製作 Azure Resource Manager 範本](../resource-group-authoring-templates.md)。將 VHD 上傳至 Azure 之後，請編輯 Resource Manager 範本以將授權類型納入計算提供者，之後再照常部署範本即可：

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## 確認您的 VM 可享受授權權益
透過 PowerShell 或 Resource Manager 部署方法部署 VM 之後，請使用 `Get-AzureRmVM` 驗證授權類型，如下所示：
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

您會看到類似下列的輸出：

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

下列 VM 在部署時未提供 Azure Hybrid Use Benefit 授權 (例如直接從 Azure 資源庫部署 VM)，您可看出其中的差異：

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## 詳細的 PowerShell 逐步解說

下列是說明完整部署 VM 的詳細 PowerShell 步驟。您可以在[使用 Resource Manager 和 PowerShell 建立 Windows VM](./virtual-machines-windows-ps-create.md) 中深入了解更多內容，包括實際的 Cmdlet 和所建立的不同元件。您將逐步進行建立資源群組、儲存體帳戶和虛擬網路，然後定義 VM 並完成建立 VM 的步驟。
 
首先，安全地取得認證，並設定位置和資源群組名稱：

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

建立公用 IP：

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

定義子網路、NIC 和 VNET：

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

命名 VM，並建立 VM 設定：

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

定義您的 OS：

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

將 NIC 加入 VM：

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

定義要使用的儲存體帳戶：

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

上傳備妥的 VHD，並附加至 VM 以供使用：

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

最後，建立 VM 並定義授權類型，以採用 Azure Hybrid Use Benefit：

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## 後續步驟

深入了解 [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) 授權。

深入了解[使用 Resource Manager 範本](../resource-group-overview.md)。

<!---HONumber=AcomDC_0601_2016-->