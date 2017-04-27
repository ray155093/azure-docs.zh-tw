---
title: "建立以 Azure VM 為基礎的 Azure RemoteApp 映像 | Microsoft Docs"
description: "了解如何開始使用 Azure 虛擬機器來建立 Azure RemoteApp 映像。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d41583ef-6cd8-4115-8dcb-b2cd5b3d301a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: c7ecd590503fcd2bc3ba06919a2f1a5c0fd0943e
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>建立以 Azure 虛擬機器為基礎的 Azure RemoteApp 映像
> [!IMPORTANT]
> Azure RemoteApp 即將於 2017 年 8 月 31 日停止服務。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

您可以從 Azure 虛擬機器建立 Azure RemoteApp 映像 (其中保存您在集合中共用的應用程式)。 您也可以使用我們已新增至 Azure VM 映像資源庫的虛擬機器映像，其符合所有 Azure RemoteApp 映像需求。如果您想要的話，您可以將該 VM 映像做為您自己 VM 的起點使用。 只需在映像庫中尋找「Windows Server 遠端桌面工作階段主機」。

根據 Azure VM 建立您的專屬映像共有兩個步驟：建立映像，然後將它從 Azure VM 映像庫上傳至 Azure RemoteApp。

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>建立以 Azure VM 為基礎的自訂映像
使用這些步驟來建立以 Azure VM 為基礎的映像。

1. 建立 Azure 虛擬機器。 您可以使用 Azure 虛擬機器映像庫中的「Windows Server 遠端桌面工作階段主機」或「Windows Server 遠端桌面工作階段主機與 Microsoft Office 365 ProPlus」映像。 此映像符合所有的 Azure RemoteApp 範本映像需求。
   
    如需詳細資訊，請參閱[建立執行 Windows 的 VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
2. 連接至 VM，並安裝和設定您想要透過 RemoteApp 共用的應用程式。 請務必執行您應用程式所需的任何其他 Windows 設定。
   
    如需詳細資訊，請參閱[如何登入執行 Windows Server 的虛擬機器](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
3. 如果您打算使用 Windows Server 遠端桌面工作階段主機映像之一，它包含了可確保您的 VM 符合 RemoteApp 先決條件的驗證指令碼。 若要執行指令碼，請按兩下桌面上的 **ValidateRemoteAppImage** 。 在繼續進行下一個步驟之前，請確定已修正指令碼所報告的所有錯誤。
4. SYSPREP 一般化和擷取映像。 如需相關指示，請參閱[如何擷取 Windows 虛擬機器作為範本使用](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) 。

## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>將映像匯入 Azure RemoteApp 映像庫
使用下列步驟將新的映像匯入 Azure RemoteApp：

1. 在 [ **範本映像** ] 索引標籤中：
   
   * 如果您沒有現有的映像，請按一下 [ **上傳或匯入範本映像**]。
   * 如果您已經有一個以上的映像，請按一下 [ **+** ] 以新增映像。
2. 選取 [從您的虛擬機器映像庫匯入映像]，然後按 [下一步]。
3. 在下一個頁面上，從清單中選取自訂映像，並確認您已遵循建立映像時的所列步驟進行。 按 [下一步] 。
4. 輸入新 RemoteApp 映像的名稱，並挑選一個位置，然後按一下核取記號以開始匯入程序。

> [!NOTE]
> 您可以將映像從 Azure 虛擬機器支援的任何 Azure 位置，匯入到 Azure RemoteApp 支援的任何 Azure 位置。 視位置而定，匯入可能需要多達 25 分鐘的時間。
> 
> 

現在您已經準備好開始建立新的收藏 ([雲端](remoteapp-create-cloud-deployment.md)收藏或[混合式](remoteapp-create-hybrid-deployment.md))，視您的需求而定。


