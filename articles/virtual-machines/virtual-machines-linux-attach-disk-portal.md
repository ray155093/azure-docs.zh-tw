---
title: "將資料磁碟連結到 Linux VM | Microsoft Docs"
description: "如何在 Azure 入口網站中，使用資源管理員部署模型，將新的或現有的資料磁碟連結到 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 7c481cfae5f97b71c0ab184419ceaa46ab3f5a5b
ms.openlocfilehash: 394d82e444bdbc8b07243d92743ceb660f142509


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>如何在 Azure 入口網站中將資料磁碟連結到 Linux VM
本文示範如何透過 Azure 入口網站將新的及現有的磁碟連結到 Linux 虛擬機器。 您也可以[在 Azure 入口網站中將資料磁碟連結到 Windows VM](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 這麼做之前，請先檢閱下列提示：

* 虛擬機器的大小會控制您可以連接的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要使用進階儲存體，您需要 DS 系列或 GS 系列的虛擬機器。 您可以使用進階或標準儲存體帳戶的磁碟搭配這些虛擬機器。 僅特定地區可用進階儲存體。 如需詳細資訊，請參閱 [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 連接至虛擬機器的磁碟實際上是 Azure 儲存體帳戶中的 .vhd 檔案。 如需詳細資訊，請參閱 [有關虛擬機器的磁碟和 VHD](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 對於新的磁碟，當您連接的時候 Azure 會建立該磁碟，所以您不需要建立它。
* 對於現有的磁碟，該 .vhd 檔案必須可在 Azure 儲存體帳戶中取得。 您可以使用現有的 .vhd 檔案 (若尚未連接至其他虛擬機器)，或上傳您自己的 .vhd 檔案至儲存體帳戶。


## <a name="find-the-virtual-machine"></a>尋找虛擬機器
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表上，按一下 [虛擬機器] 。
3. 然後從清單中選取虛擬機器。
4. 在 [虛擬機器] 刀鋒視窗上的 [程式集] 中，按一下 [磁碟]。
   
    ![開啟磁碟設定](./media/virtual-machines-linux-attach-disk-portal/find-disk-settings.png)

接著，依照指示來連接[新的磁碟](#option-1-attach-a-new-disk)或[現有的磁碟](#option-2-attach-an-existing-disk)。

## <a name="option-1-attach-a-new-disk"></a>選項 1：連接新的磁碟
1. 在 [磁碟] 刀鋒視窗上，按一下 [連接新項目]。
2. 檢閱預設設定，視需要進行更新，然後按一下 [確定] 。
   
   ![檢閱磁碟設定](./media/virtual-machines-linux-attach-disk-portal/attach-new.png)
3. 在 Azure 建立磁碟並將其連接至虛擬機器之後，該新磁碟就會列在虛擬機器之磁碟設定中的 [資料磁碟] 底下。

## <a name="option-2-attach-an-existing-disk"></a>選項 2：連接現有磁碟
1. 在 [磁碟] 刀鋒視窗上，按一下 [連接現有項目]。
2. 在 [連結現有磁碟] 底下，按一下 [VHD 檔案]。
   
   ![連接現有磁碟](./media/virtual-machines-linux-attach-disk-portal/attach-existing.png)
3. 在 [儲存體帳戶] 底下，選取持有該 .vhd 檔案的帳戶和容器。
   
   ![尋找 VHD 位置](./media/virtual-machines-linux-attach-disk-portal/find-storage-container.png)
4. 選取 .vhd 檔案。
5. 在 [連結現有磁碟] 底下，您剛才選取的檔案會列在 [VHD 檔案] 底下。 按一下 [確定] 。
6. Azure 將磁碟連接至虛擬機器之後，該磁碟會列在虛擬機器磁碟設定中的 [資料磁碟] 下面。



## <a name="next-steps"></a>後續步驟
加入磁碟後，您需要準備它以便使用。 如需詳細資訊，請參閱這篇[做法：在 Linux 中初始化新的資料磁碟](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux)。



<!--HONumber=Nov16_HO5-->


