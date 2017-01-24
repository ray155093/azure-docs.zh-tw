---
title: "在傳統入口網站中建立 VM | Microsoft Docs"
description: "在 Azure 傳統入口網站中建立 Windows 虛擬機器。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: ede78ff35ec6e7007e7a1c7b946c8e17bd425f37


---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>在 Azure 傳統入口網站中建立執行 Windows 的虛擬機器
> [!div class="op_single_selector"]
> * [Azure 傳統入口網站](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [PowerShell：傳統部署](virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br>

> [!IMPORTANT] 
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 了解如何使用**新的 Azure 入口網站**，[使用 Resource Manager 部署模型執行這些步驟](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

本教學課程示範如何在 Azure 傳統入口網站中建立執行 Windows 的 Azure 虛擬機器 (VM)。 我們將使用 Windows Server 映像做為範例，這只是 Azure 提供眾多映像中的一種。 請注意，您可以選擇何種映像取決於您的訂用帳戶。 例如，Windows 桌面映像可能可供 MSDN 訂閱者使用。

本節說明如何使用 Azure 傳統入口網站中的 [從組件庫]  選項建立虛擬機器。 此選項提供的組態選擇比 [快速建立]  選項還多。 例如，如果您要將虛擬機器加入虛擬網路中，您必須使用 [從組件庫]  選項。

您也可以使用 [您自己的映像](virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)來建立 VM。 若要了解上述方法與其他方法，請參閱 [建立 Windows 虛擬機器的其他方式](virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="video-walkthrough"></a>影片逐步解說
以下是本教學課程的逐步解說。

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Creating-a-Windows-VM-on-Microsoft-Azure-GA-Portal/player]


## <a id="createvirtualmachine"> </a>建立虛擬機器
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>後續步驟
* 深入了解如何在新的 Azure 入口網站，[使用 Resource Manager 部署模型建立 VM](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
* 登入虛擬機器。 如需相關指示，請參閱 [登入執行 Windows Server 的虛擬機器](virtual-machines-windows-classic-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
* 附加磁碟來儲存資料。 您可以附加空的磁碟和含有資料的磁碟。 如需相關指示，請參閱 [將資料磁碟連接至以傳統部署模型建立的 Windows 虛擬機器](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。




<!--HONumber=Dec16_HO1-->


