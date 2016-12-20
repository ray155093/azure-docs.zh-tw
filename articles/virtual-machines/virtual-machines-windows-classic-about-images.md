---
title: "關於 Windows 虛擬機器的映像 | Microsoft Docs"
description: "了解如何將映像與 Azure 中的 Windows 虛擬機器搭配使用。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 28720bcedb4df66c2f7434f25ed251d4e116c415


---
# <a name="about-images-for-windows-virtual-machines"></a>關於 Windows 虛擬機器的映像
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

如需在 Resource Manager 模型中尋找和使用映像的詳細資訊，請參閱[這裡](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>使用映像
您可以使用 Azure PowerShell 模組來管理您 Azure 訂用帳戶可使用的映像。 您也可以使用 Azure 傳統入口網站進行部分映像工作，但命令列則提供您更多選項。

* **取得所有映像**：`Get-AzureVMImage`會傳回一份所有目前訂用帳戶中可用的映像︰您的映像，以及 Azure 或協力廠商所提供的映像。 這表示您可能會收到龐大的清單。 下面的範例示範如何取得較短的清單。
* **取得映像系列**：`Get-AzureVMImage | select ImageFamily`顯示字串 **ImageFamily** 屬性以取得映像系列清單。
* **取得特定系列中的所有映像**：`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **尋找 VM 映像**：`Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName`運作的方式是篩選 DataDiskConfiguration 屬性，且僅適用於 VM 映像。 此範例也會篩選輸出並僅列出標籤和映像名稱。
* **儲存一般化映像**：`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **儲存特殊化映像**：`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
  >[Azure.Tip] 如果您想要建立 VM 映像，其中包含資料磁碟以及作業系統磁碟，您就需要 OSState 參數。 如果您不使用參數，Cmdlet 就會建立 OS 映像。 根據作業系統磁碟是否準備為重複使用，參數的值會表示是否要將映像一般化或特殊化。
* **删除映像**：`Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>後續步驟
您也可以[使用傳統入口網站來建立 Windows 機器](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)




<!--HONumber=Nov16_HO3-->


