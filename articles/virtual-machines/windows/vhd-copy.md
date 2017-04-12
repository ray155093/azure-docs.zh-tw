---
title: "建立 Azure 中特製化 VM 的複本 | Microsoft Docs"
description: "了解在 Resource Manager 部署模型中，如何建立在 Azure 中執行之特製化 Windows VM 的複本"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ce7e6cd3-6a4a-4fab-bf66-52f699b1398a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 1e897d98f561b24eafb95dfb620c3b5c7622ba0a
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>建立在 Azure 中執行之特製化 Windows VM 的複本
本文說明如何使用 AZCopy 工具，建立「在 Azure 中執行的特製化 Windows」的 VHD 複本。 然後您可以使用此 VHD 複本來建立另新的 VM。 

* 如果要複製通用的 VM，請參閱[如何從現有的一般化 Azure VM建立 VM 映像](capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 如果您想要從內部部署 VM 上傳 VHD，例如使用 Hyper-V 建立的 VHD，請參閱[從內部部署 VM上傳 Windows VHD 至 Azure](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="before-you-begin"></a>開始之前
請確定您︰

* 已取得**來源和目的地儲存體帳戶**的相關資訊。 針對來源 VM，您需要有儲存體帳戶和容器名稱。 容器名稱通常會是 **vhd**。 您也需要有一個目的地儲存體帳戶。 如果您還沒有，可以使用入口網站 ([更多服務] > [儲存體帳戶] > [新增])，或使用 [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) Cmdlet 建立一個。 
* 已安裝 Azure [PowerShell 1.0](/powershell/azureps-cmdlets-docs) (或更新版本)。
* 已下載並安裝 [AzCopy 工具](../../storage/storage-use-azcopy.md)。 

## <a name="deallocate-the-vm"></a>解除配置 VM
解除配置 VM，這會釋出要複製的 VHD。 

* **入口網站**︰ 按一下 [虛擬機器]  >  [myVM] > [停止]
* **Powershell**：`Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` 會解除配置 **myResourceGroup** 資源群組中名為 **myVM** 的 VM。

Azure 入口網站中 VM 的 [狀態] 會從 [已停止] 變更為 [已停止 (已解除配置)]。

## <a name="get-the-storage-account-urls"></a>取得儲存體帳戶 URL
您需要來源和目的地儲存體帳戶的 URL。 URL 看起來像是：`https://<storageaccount>.blob.core.windows.net/<containerName>/`。 如果您已經知道儲存體帳戶和容器名稱，可以直接取代方括號中的資訊來建立您的 URL。 

您可以使用 Azure 入口網站或 Azure PowerShell 來取得 URL：

* **入口網站**︰按一下 [更多服務]  >  [儲存體帳戶]  > <storage account> [Blob]，您的來源 VHD 檔可能在 **vhd** 容器中。 按一下容器的 [屬性]，複製標示為[URL] 的文字。 您會需要來源和目的地容器的 URL。 
* **Powershell**：`Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` 會取得 **myResourceGroup** 資源群組中 **myVM** VM 的資訊。 在結果中，查看 [儲存體設定檔] 區段的 [VHD URI]。 URI 的第一個部分是容器的 URL，最後一個部分是 VM 的作業系統 VHD 名稱。

## <a name="get-the-storage-access-keys"></a>取得儲存體存取金鑰
找出來源和目的地儲存體帳戶的存取金鑰。 如需存取金鑰的詳細資訊，請參閱 [關於 Azure 儲存體帳戶](../../storage/storage-create-storage-account.md)。

* **入口網站**︰按一下 [更多服務]  >  [儲存體帳戶]  > <storage account> [所有設定]  >  [存取金鑰]。 複製標示為 [金鑰1] 的金鑰。
* **Powershell**：`Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` 會取得 **myResourceGroup** 資源群組中儲存體帳戶 **mystorageaccount** 的儲存體金鑰。 複製標示為 [金鑰1] 的金鑰。

## <a name="copy-the-vhd"></a>複製 VHD
您可以使用 AzCopy 在儲存體帳戶之間複製檔案。 針對目的地容器，如果指定的容器不存在，它會為您建立。 

若要使用 AzCopy，在您的本機電腦上開啟命令提示字元，瀏覽至安裝 AzCopy 的資料夾。 它會類似 C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy。 

若要複製容器內的所有檔案，使用 **/S** 選項。 這可以用來複製作業系統 VHD 及其所有的資料磁碟 (如果它們是在相同容器中)。 這個範例示範如何將 **mysourcestorageaccount** 儲存體帳戶中**mysourcecontainer** 容器中的所有檔案，複製到 **mydestinationstorageaccount** 儲存體帳戶中的 **mydestinationcontainer**。 儲存體帳戶和容器的名稱請取代為您自己的。 使用您自己的金鑰取代 `<sourceStorageAccountKey1>` 和 `<destinationStorageAccountKey1>`。

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

如果要複製包含多個檔案之容器中的特定 VHD，也可以使用 /Pattern 選項指定檔案名稱。 在此範例中，只會複製名為 **myFileName.vhd** 的檔案。

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


完成時，您將收到如下的訊息：

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>疑難排解
* 當您使用 AZCopy 時，如果您看到「伺服器無法驗證要求」錯誤，請確定授權標頭值的格式正確 (包含簽章)。 如果您正在使用金鑰 2 或次要儲存體金鑰，請嘗試使用主要或第 1 個儲存體金鑰。

## <a name="next-steps"></a>後續步驟
* 您可以[將 VHD 的複本附加至 VM 作為 OS 磁碟](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，藉此建立新的 VM。


