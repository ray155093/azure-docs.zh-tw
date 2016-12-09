---
title: "登入傳統 Azure VM | Microsoft Docs"
description: "使用 Azure 傳統入口網站來登入以傳統部署模型建立的 Windows 虛擬機器。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: b2a66b134d42e0b2d965c1f3ae83f93bed9cdfba


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>使用 Azure 傳統入口網站登入 Windows 虛擬機器
在 Azure 傳統入口網站中，使用 [連接]  按鈕來啟動遠端桌面工作階段，並登入 Windows VM。

您想要連線至 Linux VM 嗎？ 請參閱[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

了解如何[使用 Azure 入口網站來執行這些步驟](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

如需如何使用 Resource Manager 模型登入 VM 的詳細資訊，請參閱[這裡](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="video-walkthrough"></a>影片逐步解說
以下是本教學課程的逐步解說影片。 它還包括可用來連接到 Azure 中 Windows VM 的端點及公用和私人連接埠。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器
1. 登入 Azure 傳統入口網站。
2. 按一下 [虛擬機器] ，然後選取虛擬機器。
3. 在頁面底部的命令列中，按一下 [連接] 。
   
    ![登入虛擬機器](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> 如果 [連接] 按鈕無法使用，請參閱本文結尾處的疑難排解提示。
> 
> 

## <a name="log-on-to-the-virtual-machine"></a>登入虛擬機器
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>後續步驟
* 如果 [連接] 按鈕無法使用，或是有其他「遠端桌面」連線問題，請嘗試重設組態。 從虛擬機器儀表板的**快速概覽**底下，按一下 [重設遠端組態]。
* 如果是您的密碼有問題，請嘗試重設密碼。 從虛擬機器儀表板的**快速概覽**底下，按一下 [重設密碼]。

如果這些秘訣無效，或者不是您所需要的，請參閱[針對以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線進行疑難排解](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 本文會逐步帶領您診斷及解決常見的問題。




<!--HONumber=Nov16_HO3-->


