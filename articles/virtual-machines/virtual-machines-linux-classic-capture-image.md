---
title: "擷取 Linux VM 的映像 | Microsoft Docs"
description: "了解如何對以傳統部署模型建立的 Linux Azure 虛擬機器 (VM) 擷取映像。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 7453b7b5938a15564110cae31fa878ee3616620a


---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>如何將傳統 Linux 虛擬機器擷取成映像
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

了解如何[使用 Resource Manager 模型執行這些步驟](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

本文說明如何將執行 Linux 的傳統 Azure 虛擬機器擷取成映像，以建立其他虛擬機器。 此映像包含作業系統磁碟和連結至虛擬機器的資料磁碟。 它並不包含網路組態，因此當您從此映像建立其他虛擬機器時，將需要設定該組態。

Azure 會將映像儲存在 [映像] 底下，連同您已上傳的任何映像。 如需映像的詳細資訊，請參閱[關於 Azure 中的虛擬機器映像][關於 Azure 中的虛擬機器映像]。

## <a name="before-you-begin"></a>開始之前
這些步驟假設您已使用傳統部署模型建立 Azure 虛擬機器，並設定好作業系統，包括連接任何資料磁碟。 如果您需要建立 VM，請閱讀[如何建立 Linux 虛擬機器][如何建立 Linux 虛擬機器]。

## <a name="capture-the-virtual-machine"></a>擷取虛擬機器
1. 使用您所選擇的 SSH 用戶端來[連線到虛擬機器](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
2. 在 SSH 視窗中，輸入下列命令。 來自 `waagent` 的輸出可能會依此公用程式的版本而稍有不同：
   
    `sudo waagent -deprovision+user`
   
    此命令會嘗試清除系統，使之適合重新佈建。 這項作業會執行下列工作：
   
   * 移除 SSH 主機金鑰 (如果組態檔中的 Provisioning.RegenerateSshHostKeyPair 是 'y')
   * 清除 /etc/resolv.conf 中的名稱伺服器設定
   * 移除 /etc/shadow 中的 `root` 使用者密碼 (如果組態檔中的 Provisioning.DeleteRootPassword 是 'y')
   * 移除快取的 DHCP 用戶端租用
   * 將主機名稱重設為 localhost.localdomain
   * 刪除最後佈建的使用者帳戶 (取自於 /var/lib/waagent) **和相關聯的資料**。
     
     > [!NOTE]
     > 解除佈建會將檔案和資料刪除以將映像「一般化」。 請只在您想要擷取做為新映像範本的虛擬機器上執行這個命令。 這不能保證映像檔中的所有機密資訊都會清除完畢或適合轉散發給第三方。
     > 
     > 
3. 輸入 **y** 繼續。 您可以加入 `-force` 參數，便不用進行此確認步驟。
4. 輸入 **Exit** 關閉 SSH 用戶端。
   
   > [!NOTE]
   > 其餘步驟會假設您已經在用戶端電腦上 [安裝 Azure CLI](../xplat-cli-install.md) 。 您也可以在 [Azure 傳統入口網站][Azure 傳統入口網站]中完成接下來的所有步驟。
   > 
   > 
5. 從用戶端電腦，開啟 Azure CLI 並登入您的 Azure 訂用帳戶。 如需詳細資料，請閱讀 [從 Azure CLI 連接到 Azure 訂用帳戶](../xplat-cli-connect.md)。
6. 請確定您是處於服務管理模式中：
   
    `azure config mode asm`
7. 使用下列程式碼，將已在先前步驟中取消佈建的虛擬機器關機：
   
    `azure vm shutdown <your-virtual-machine-name>`
   
   > [!NOTE]
   > 您可以使用 `azure vm list`，找出訂用帳戶中建立的所有虛擬機器
   > 
   > 
8. 當虛擬機器停止時，使用下列命令來擷取映像：
   
    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`
   
    輸入您想要的映像名稱，以取代 new-image-name。 此命令會建立通用的 OS 映像。 `-t` 子命令會刪除原本的虛擬機器。
9. 新的映像現在可從映像清單中取得，且可用來設定任何新的虛擬機器。 您可以使用下列命令進行檢視：
   
   `azure vm image list`
   
   在 [Azure 傳統入口網站][Azure 傳統入口網站]上，它會出現在 [映像] 清單中。
   
   ![Image capture successful](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>後續步驟
映像已準備好用來建立虛擬機器。 您可以使用 Azure CLI 命令 `azure vm create` ，並提供您已建立的映像名稱。 如需有關命令的詳細資料，請參閱 [搭配傳統部署模型使用 Azure CLI](../virtual-machines-command-line-tools.md) 。 或者，您也可以使用 [Azure 傳統入口網站][Azure 傳統入口網站]，利用 [從資源庫] 方法並選取您已建立的映像，來建立自訂虛擬機器。 如需詳細資料，請參閱[如何建立自訂虛擬機器][如何建立自訂虛擬機器]。

**另請參閱：** [Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure 傳統入口網站]: http://manage.windowsazure.com
[關於 Azure 中的虛擬機器映像]: virtual-machines-linux-classic-about-images.md
[如何建立自訂虛擬機器]: virtual-machines-linux-classic-create-custom.md
[如何將資料磁碟連接至虛擬機器]: virtual-machines-windows-classic-attach-disk.md
[如何建立 Linux 虛擬機器]: virtual-machines-linux-classic-create-custom.md



<!--HONumber=Nov16_HO3-->


