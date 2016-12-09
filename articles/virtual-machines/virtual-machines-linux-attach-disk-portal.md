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
ms.date: 07/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 743f6abb871bdc4f65f302f0c1efdc4603c52626


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>如何在 Azure 入口網站中將資料磁碟連結到 Linux VM
本文示範如何透過 Azure 入口網站將新的及現有的磁碟連結到 Linux 虛擬機器。 您也可以[在 Azure 入口網站中將資料磁碟連結到 Windows VM](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 這麼做之前，請先檢閱下列提示：

* 虛擬機器的大小會控制您可以連接的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要使用進階儲存體，您需要 DS 系列或 GS 系列的虛擬機器。 您可以使用進階或標準儲存體帳戶的磁碟搭配這些虛擬機器。 僅特定地區可用進階儲存體。 如需詳細資訊，請參閱 [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage.md)。
* 連接至虛擬機器的磁碟實際上是 Azure 儲存體帳戶中的 .vhd 檔案。 如需詳細資訊，請參閱 [有關虛擬機器的磁碟和 VHD](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 對於新的磁碟，當您連接的時候 Azure 會建立該磁碟，所以您不需要建立它。
* 對於現有的磁碟，該 .vhd 檔案必須可在 Azure 儲存體帳戶中取得。 您可以使用現有的 .vhd 檔案 (若尚未連接至其他虛擬機器)，或上傳您自己的 .vhd 檔案至儲存體帳戶。

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>後續步驟
加入磁碟後，您需要準備它以便使用。 請參閱此篇[文章](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux)中的虛擬機器作業系統：＜如何：在 Linux 中初始化新的資料磁碟＞。



<!--HONumber=Nov16_HO3-->


