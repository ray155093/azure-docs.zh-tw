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
ms.date: 02/15/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 8f1d488fd8f71bf90c8bf7b7c1544445ffbd7686
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>如何從一般化 Azure VM 擷取 VM 映像
本文說明如何使用 Azure PowerShell 建立一般化 Azure VM的映像。 然後可以使用映像來建立另一個 VM。 此映像包含作業系統磁碟與連結到虛擬機器的資料磁碟。 映像不包含虛擬網路資源，因此您需要在建立新的 VM 時設定這些資源。 

## <a name="prerequisites"></a>必要條件
您需要安裝 Azure PowerShell 1.0.x 版或更新版本。 如果您尚未安裝 PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 以了解安裝步驟。

## <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 將 Windows VM 一般化

Sysprep 會移除您的所有個人帳戶資訊以及其他項目，並準備電腦以做為映像。 如需 Sysprep 的詳細資訊，請參閱 [如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

請確定 Sysprep 支援電腦上執行的伺服器角色。 如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 如果您是第一次在將 VHD 上傳至 Azure 之前執行 Sysprep，請確定您已[準備好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 再執行 Sysprep。 
> 
> 

1. 登入 Windows 虛擬機器。
2. 以系統管理員身分開啟 [命令提示字元] 視窗。 切換至 **%windir%\system32\sysprep** 目錄，然後執行 `sysprep.exe`。
3. 在 [系統準備工具] 對話方塊中，選取 [進入系統全新體驗 (OOBE)]，並確認已勾選 [一般化] 核取方塊。
4. 在 [關機選項] 中選取 [關機]。
5. 按一下 [確定] 。
   
    ![啟動 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep 完成時，會關閉虛擬機器。 不要重新啟動 VM。


您也可以使用 `sudo waagent -deprovision+user` 將 Linux VM 一般化，然後使用 PowerShell 來擷取該 VM. 如需有關使用 CLI 來擷取 VM 的資訊，請參閱[如何使用 Azure CLI 來一般化和擷取 Linux 虛擬機器](../linux/capture-image.md)

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
* 現在您可以[從映像建立 VM](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


