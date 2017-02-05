---
title: "擷取 Azure Windows VM 的映像 | Microsoft Docs"
description: "擷取以傳統部署模型建立之 Azure Windows 虛擬機器的映像。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 6b68d41daeea780d70b5ce1389d05f1f4fdf65ea


---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>擷取以傳統部署模型建立之 Azure Windows 虛擬機器的映像。
> [!IMPORTANT] 
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 如需 Resource Manager 模型資訊，請參閱[建立在 Azure 中執行的複本 Windows VM](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

本文說明如何擷取執行 Windows 的 Azure 虛擬機器，以便用它作為映像來建立其他虛擬機器。 此映像包括作業系統磁碟和任何連結至虛擬機器的資料磁碟。 它並不包含網路組態，因此當您建立其他使用此映像的虛擬機器時，將需要設定這些組態。

Azure 會將映像儲存在 [我的映像] 之下。 這個位置與您已上傳映像的任何儲存位置相同。 如需映像的詳細資訊，請參閱 [有關虛擬機器的映像](virtual-machines-linux-classic-about-images.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

## <a name="before-you-begin"></a>開始之前
這些步驟假設您已建立 Azure 虛擬機器且設定好作業系統，包括連接任何資料磁碟。 如果您還沒這麼做，請參閱下列指示：

* [從映像建立虛擬機器](virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [如何將資料磁碟連結至虛擬機器](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* 請確定 Sysprep 支援伺服器角色。 如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

> [!WARNING]
> 此程序會在擷取原始虛擬機器後將其刪除。 
> 
> 

擷取 Azure 虛擬機器映像之前，建議先備份目標虛擬機器。 Azure 虛擬機器可使用 Azure 備份進行備份。 如需詳細資訊，請參閱 [備份 Azure 虛擬機器](../backup/backup-azure-vms.md)。 來自認證合作夥伴的其他解決方案也可供使用。 若要了解目前可用的項目，請搜尋 Azure Marketplace。

## <a name="capture-the-virtual-machine"></a>擷取虛擬機器
1. 在 [Azure 傳統入口網站](http://manage.windowsazure.com)中， **連線** 到虛擬機器。 如需指示，請參閱[如何登入執行 Windows Server 的虛擬機器][How to sign in to a virtual machine running Windows Server]。
2. 以系統管理員身分開啟 [命令提示字元] 視窗。
3. 切換至 `%windir%\system32\sysprep`目錄，然後執行 sysprep.exe。
4. [系統準備工具]  對話方塊隨即出現。 執行下列動作：
   
   * 在 [系統清理動作] 中選取 [Enter System Out-of-Box Experience (OOBE)]，並確認 [一般化] 已勾選。 如需 Sysprep 的詳細用法，請參閱[如何使用 Sysprep：簡介][How to Use Sysprep: An Introduction]。
   * 在 [關機選項] 中選取 [關機]。
   * 按一下 [確定] 。
   
   ![執行 Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)
5. Sysprep 會將虛擬機器關機，並會在 Azure 傳統入口網站中，將虛擬機器的狀態變更為 [ **已停止**]。
6. 在 Azure 傳統入口網站中，按一下 [ **虛擬機器** ] 並選取您想要擷取的虛擬機器。
7. 按一下命令列上的 [擷取] 。
   
   ![擷取虛擬機器](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)
   
   [擷取虛擬機器]  對話方塊隨即出現。
8. 在 [映像名稱] 中輸入新映像的名稱。
9. 將 Windows Server 映像新增到自訂映像組合之前，必須先如前述步驗所指示，執行 Sysprep 將它一般化。 按一下 [ **我已經在虛擬機器上執行 Sysprep** ] 來表示您已這麼做。
10. 按一下打勾記號以擷取映像。 新映像現在提供於 [映像] 底下。
    
    ![Image capture successful](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>後續步驟
映像已準備好用來建立虛擬機器。 若要這麼做，您將需要使用 [ **從資源庫** ] 功能表項目並選取您剛建立的映像，來建立虛擬機器。 如需指示，請參閱 [從映像建立虛擬機器](virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

[How to sign in to a virtual machine running Windows Server]: virtual-machines-windows-classic-connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png



<!--HONumber=Dec16_HO1-->


