---
title: "將 Windows VM 一般化以在 Azure 中使用 | Microsoft Docs"
description: "了解如何使用 Sysprep 將 Windows VM 一般化，以和 Resource Manager 部署模型搭配使用。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a745d400-c8be-48b4-a891-4a18495ef3fd
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b360a6cf0f8eb37099b02ffba8721ab3ec1a5f10
ms.lasthandoff: 03/31/2017


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>使用 Sysprep 將 Windows 虛擬機器一般化
本節說明如何將 Windows 虛擬機器一般化以做為映像。 Sysprep 會移除您的所有個人帳戶資訊以及其他項目，並準備電腦以做為映像。 如需 Sysprep 的詳細資訊，請參閱 [如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

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
6. Sysprep 完成時，會關閉虛擬機器。 

> [!IMPORTANT]
> 在您完成將 VHD 上傳到 Azure 或從 VM 建立映像之前，請勿重新啟動 VM。 如果 VM 意外重新啟動，請執行 Sysprep 來重新將它一般化。
> 
> 

## <a name="next-steps"></a>後續步驟
* 如果是內部部署 VM，您現在可以[將 VHD 上傳至 Azure](upload-image.md)。
* 如果 VM 已在 Azure 中，您現在可以[從一般化 VM 建立映像](capture-image.md)。


