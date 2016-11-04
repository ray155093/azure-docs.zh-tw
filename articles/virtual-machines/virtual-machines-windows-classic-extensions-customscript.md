---
title: Windows VM 上的自訂指令碼擴充功能 | Microsoft Docs
description: 使用自訂指令碼擴充功能，在遠端 Windows VM 上執行 PowerShell 指令碼，將 Azure VM 組態工作自動化
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/06/2015
ms.author: kundanap

---
# Windows 虛擬機器的自訂指令碼擴充功能
本文概要說明如何使用 Azure PowerShell Cmdlet 搭配「Azure 服務管理 API」，在 Windows VM 上使用「自訂指令碼」擴充功能。

Microsoft 和受信任的協力廠商發行者建置的虛擬機器 (VM) 延伸模組，可延伸 VM 的功能。如需 VM 擴充功能的概觀，請參閱 [Azure VM 擴充功能與功能](virtual-machines-windows-extensions-features.md)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

了解如何[使用 Resource Manager 模型執行這些步驟](virtual-machines-windows-extensions-customscript.md)。

## 自訂指令碼擴充功能概觀
有了適用於 Windows 的「自訂指令碼」擴充功能，您不須登入遠端 VM，即可在遠端 VM 上執行 PowerShell 指令碼。您可以在佈建 VM 之後或在 VM 生命週期中的任何時間執行指令碼，而不需開啟任何其他連接埠。執行「自訂指令碼」擴充功能的最常見使用案例包括：在於佈建 VM 之後，於 VM 上執行、安裝及設定其他軟體。

### 執行自訂指令碼擴充功能的先決條件
1. 安裝 <a href="http://azure.microsoft.com/downloads" target="_blank">Azure PowerShell Cmdlet</a> 0.8.0 版或更新版本。
2. 如果您想要讓指令碼在現有的 VM 上執行，請確定在該 VM 上啟用「VM 代理程式」。如果未安裝，請依照這些[步驟](virtual-machines-windows-classic-agents-and-extensions.md)操作。如果 VM 是從 Azure 入口網站建立的，則預設會安裝「VM 代理程式」。
3. 將您想要在 VM 上執行的指令碼上傳到 Azure 儲存體。指令碼可以來自單一容器或多個儲存體容器。
4. 撰寫指令碼時，應將其撰寫成讓入口指令碼 (由擴充功能啟動) 啟動其他指令碼。

## 自訂指令碼擴充功能案例
### 將檔案上傳到預設容器
下列範例示範當指令碼位於您訂用帳戶的預設帳戶儲存體容器中時，如何在 VM 上執行這些指令碼。您需將您的指令碼上傳到 ContainerName。您可以使用 **Get-AzureSubscription –Default** 命令來驗證預設的儲存體帳戶。

下列範例會建立 VM，但您也可以在現有的 VM 上執行相同的案例。

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### 將檔案上傳到非預設的儲存體容器
這個案例示範如何使用相同訂用帳戶內或不同訂用帳戶中的非預設儲存體容器，來上傳指令碼和檔案。此範例示範的是現有的 VM，但如果您要建立 VM，也可以執行相同的作業。

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### 將指令碼上傳到橫跨不同儲存體帳戶的多個容器
  如果指令碼檔案儲存在多個容器中，您就必須提供這些檔案的完整共用存取簽章 (SAS) URL，才能執行指令碼。

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### 從 Azure 入口網站新增自訂指令碼擴充功能
在 <a href="https://portal.azure.com/ " target="_blank">Azure 入口網站</a>中移至 VM，然後指定要執行的指令碼檔案來新增擴充功能。

  ![指定指令碼檔案][5]

### 將自訂指令碼擴充功能解除安裝
您可以使用下列命令，將「自訂指令碼」擴充功能從 VM 中解除安裝。

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### 搭配範本使用自訂指令碼擴充功能
若要了解如何搭配 Azure Resource Manager 範本使用「自訂指令碼」擴充功能，請參閱[使用 Windows VM 的自訂指令碼擴充功能搭配 Azure Resource Manager 範本](virtual-machines-windows-extensions-customscript.md)。

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png

<!---HONumber=AcomDC_0824_2016-->