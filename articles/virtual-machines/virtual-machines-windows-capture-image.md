---
title: "從一般化 Azure VM 擷取 VM 映像 | Microsoft Docs"
description: "了解如何從 Resource Manager 部署模型中建立的一般化 Azure VM 擷取 VM 映像"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: dc0753faefa59eef42046e0297a58276db086d18


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>如何從一般化 Azure VM 擷取 VM 映像
本文說明如何使用 Azure PowerShell 建立一般化 Azure VM的映像。 然後可以使用映像來建立另一個 VM。 此映像包含作業系統磁碟與連結到虛擬機器的資料磁碟。 映像不包含虛擬網路資源，因此您需要在建立新的 VM 時設定這些資源。 

## <a name="prerequisites"></a>必要條件
* 您必須擁有[一般化 VM](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 將 VM 一般化會移除您的所有個人帳戶資訊，以及其他項目，並準備電腦作為映像。
* 您需要安裝 Azure PowerShell 1.0.x 版或更新版本。 如果您尚未安裝 PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 以了解安裝步驟。

## <a name="log-in-to-azure-powershell"></a>登入 Azure PowerShell
1. 開啟 Azure PowerShell，並登入您的 Azure 帳戶。
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    這會開啟一個可供您輸入 Azure 帳戶認證的快顯視窗。
2. 取得您可用訂用帳戶的訂用帳戶識別碼。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 使用訂用帳戶識別碼來設定正確的訂用帳戶。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>解除配置 VM 並將狀態設定為一般化
1. 解除配置 VM 資源。
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Azure 入口網站中 VM 的 [狀態] 會從 [已停止] 變更為 [已停止 (已解除配置)]。
2. 將虛擬機器的狀態設定為 [一般化] 。 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. 檢查 VM 的狀態。 VM 的 [OSState/一般化] 區段中的 [DisplayStatus] 應設定為 [VM 一般化]。  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>建立映像
1. 使用這個命令，將虛擬機器映像複製到目的地儲存體容器中。 此映像會建立在與原始虛擬機器相同的儲存體帳戶中。 `-Path` 參數會在本機儲存 JSON 範本的複本。 `-DestinationContainerName` 參數是要用以保存映像的容器名稱。 如果此容器不存在，則會為您建立。
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    您可以從 JSON 檔案範本取得映像的 URL。 請依序前往 [資源]  >  [storageProfile]  >  [osDisk]  >  [映像]  >  [uri] 區段，取得您映像的完整路徑。 映像的 URL 如下所示： `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
   
    您也可以在入口網站中驗證 URI。 系統會將映像複製到儲存體帳戶中名為 **system** 的容器中。 

## <a name="next-steps"></a>後續步驟
* 現在您可以[從映像建立 VM](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。




<!--HONumber=Dec16_HO2-->


