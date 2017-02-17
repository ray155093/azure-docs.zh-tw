---
title: "將資料磁碟連接到 Windows VM | Microsoft Docs"
description: "如何在 Azure 入口網站中使用 Resource Manager 部署模型，將新的或現有的資料磁碟連接到 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: b84e07b26506149cf9475491b32b9ff3ea9ae80d
ms.openlocfilehash: 40375aa411920f966aa6923f0899ca2f88a9ed39


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>如何在 Azure 入口網站中將資料磁碟連接到 Windows VM
本文示範如何透過 Azure 入口網站將新的及現有的磁碟連接到 Windows 虛擬機器。 您也可以[在 Azure 入口網站中將資料磁碟連接到 Linux VM](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 這麼做之前，請先檢閱下列提示：

* 虛擬機器的大小會控制您可以連接的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 若要使用進階儲存體，您需要 DS 系列或 GS 系列的虛擬機器。 您可以使用進階或標準儲存體帳戶的磁碟搭配這些虛擬機器。 僅特定地區可用進階儲存體。 如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 對於新的磁碟，當您連接的時候 Azure 會建立該磁碟，所以您不需要建立它。
* 對於現有的磁碟，該 .vhd 檔案必須可在 Azure 儲存體帳戶中取得。 您可以使用現有的 .vhd 檔案 (若尚未連接至其他虛擬機器)，或上傳您自己的 .vhd 檔案至儲存體帳戶。

您也可以[使用 Powershell 連接資料磁碟](virtual-machines-windows-attach-disk-ps.md)。


## <a name="find-the-virtual-machine"></a>尋找虛擬機器
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表上，按一下 [虛擬機器] 。
3. 然後從清單中選取虛擬機器。
4. 在 [虛擬機器] 刀鋒視窗上的 [程式集] 中，按一下 [磁碟]。
   
    ![開啟磁碟設定](./media/virtual-machines-windows-attach-disk-portal/find-disk-settings.png)

接著，依照指示來連接[新的磁碟](#option-1-attach-a-new-disk)或[現有的磁碟](#option-2-attach-an-existing-disk)。

## <a name="option-1-attach-and-initialize-a-new-disk"></a>選項 1：連接新磁碟並進行初始化
1. 在 [磁碟] 刀鋒視窗上，按一下 [連接新項目]。
2. 檢閱預設設定，視需要進行更新，然後按一下 [確定] 。
   
   ![檢閱磁碟設定](./media/virtual-machines-windows-attach-disk-portal/attach-new.png)
3. 在 Azure 建立磁碟並將其連接至虛擬機器之後，該新磁碟就會列在虛擬機器之磁碟設定中的 [資料磁碟] 底下。

### <a name="initialize-a-new-data-disk"></a>初始化新的資料磁碟

1. 連接至虛擬機器。 如需指示，請參閱[如何連接和登入執行 Windows 的 Azure 虛擬機器](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
2. 登入虛擬機器之後，開啟 [伺服器管理員]。 在左窗格中，選取 [File and Storage Services] 。
   
    ![開啟伺服器管理員](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. 展開功能表，然後選取 [ **磁碟**]。
4. [磁碟]  區段會列出磁碟。 在大部分情況下，會有磁碟 0、磁碟 1 和磁碟 2。 磁碟 0 是作業系統磁碟、磁碟 1 是暫存磁碟，磁碟 2 則是您剛連接至 VM 的資料磁碟。 新的資料磁碟會將磁碟分割列為 [未知] 。 在磁碟上按一下滑鼠右鍵，然後選取 [初始化] 。
5. 初始化磁碟時，您會收到將清除所有資料的通知。 按一下 [是]，認可此警告並初始化磁碟。 完成之後，即會將磁碟分割列為 [GPT]。 再次於磁碟上按一下滑鼠右鍵，然後選取 新增磁碟區 。
6. 使用預設值完成精靈。 當精靈完成時，[ **磁碟區** ] 區段會列出新的磁碟區。 磁碟此時將上線，可用來儲存資料。

    ![成功初始化磁碟區](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)


## <a name="option-2-attach-an-existing-disk"></a>選項 2：連接現有磁碟
1. 在 [磁碟] 刀鋒視窗上，按一下 [連接現有項目]。
2. 在 [連結現有磁碟] 底下，按一下 [VHD 檔案]。
   
   ![連接現有磁碟](./media/virtual-machines-windows-attach-disk-portal/attach-existing.png)
3. 在 [儲存體帳戶] 底下，選取持有該 .vhd 檔案的帳戶和容器。
   
   ![尋找 VHD 位置](./media/virtual-machines-windows-attach-disk-portal/find-storage-container.png)
4. 選取 .vhd 檔案。
5. 在 [連結現有磁碟] 底下，您剛才選取的檔案會列在 [VHD 檔案] 底下。 按一下 [確定] 。
6. Azure 將磁碟連接至虛擬機器之後，該磁碟會列在虛擬機器磁碟設定中的 [資料磁碟] 下面。



## <a name="use-trim-with-standard-storage"></a>搭配使用 TRIM 與標準儲存體

如果您使用標準儲存體 (HDD)，您應該啟用 TRIM。 TRIM 會捨棄磁碟上未使用的區塊，因此您只需支付實際使用的儲存體。 如果您建立大型檔案，然後將它們刪除，這便可節省成本。 

您可以執行此命令來檢查 TRIM 設定。 在您的 Windows VM 上開啟命令提示字元並輸入︰

```
fsutil behavior query DisableDeleteNotify
```

如果命令傳回 0，則已正確啟用 TRIM。 如果傳回 1，請執行下列命令來啟用 TRIM：
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>後續步驟
如果您的應用程式需要使用 D: 磁碟機來儲存資料，可以[變更 Windows 暫存磁碟的磁碟機代號](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。




<!--HONumber=Feb17_HO2-->


