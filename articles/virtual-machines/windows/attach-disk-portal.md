---
title: "將未受管理的資料磁碟連結到 Windows VM - Azure | Microsoft Docs"
description: "如何在 Azure 入口網站中使用 Resource Manager 部署模型，將新的或現有的未受管理資料磁碟連結到 Windows VM。"
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
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d02f92a8809efd6f58312af8cb40739299ea28f6
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-attach-an-unmanaged-data-disk-to-a-windows-vm-in-the-azure-portal"></a>如何在 Azure 入口網站中將未受管理的資料磁碟連結到 Windows VM

本文示範如何透過 Azure 入口網站將新的及現有的未受管理磁碟連結到 Windows 虛擬機器。 您也可以[使用 PowerShell 連結資料磁碟](./attach-disk-ps.md)。 這麼做之前，請先檢閱下列提示：

* 虛擬機器的大小會控制您可以連接的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](sizes.md)。
* 若要使用進階儲存體，您需要 DS 系列或 GS 系列的虛擬機器。 您可以使用進階或標準儲存體帳戶的磁碟搭配這些虛擬機器。 僅特定地區可用進階儲存體。 如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 對於新的磁碟，當您連接的時候 Azure 會建立該磁碟，所以您不需要建立它。


您也可以[使用 Powershell 連接資料磁碟](attach-disk-ps.md)。


## <a name="find-the-virtual-machine"></a>尋找虛擬機器
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在左側功能表中，按一下 [虛擬機器]。
3. 然後從清單中選取虛擬機器。
4. 在 [虛擬機器] 刀鋒視窗中，按一下 [磁碟]。
   
接著，依照指示來連接[新的磁碟](#option-1-attach-a-new-disk)或[現有的磁碟](#option-2-attach-an-existing-disk)。

## <a name="option-1-attach-and-initialize-a-new-disk"></a>選項 1：連接新磁碟並進行初始化
1. 在 [磁碟] 刀鋒視窗上，按一下 [+ 新增資料磁碟]。
2. 在 [連結受管理的磁碟] 刀鋒視窗中，於 [名稱] 中輸入磁碟名稱，然後在 [來源類型] 中選取 [新增 (空磁碟)]。
3. 在 [儲存體容器] 下，按一下 [瀏覽] 按鈕並瀏覽至您要用於儲存新 VHD 的儲存體帳戶與容器，然後按一下 [選取]。 
  
   ![檢閱磁碟設定](./media/attach-disk-portal/attach-empty-unmanaged.png)
   
3. 當您完成資料磁碟的設定之後，按一下 [確定]。
4. 回到 [磁碟] 刀鋒視窗，按一下 [儲存] 以將磁碟新增到 VM 設定。


### <a name="initialize-a-new-data-disk"></a>初始化新的資料磁碟

1. 連接至虛擬機器。 如需指示，請參閱 [如何連接和登入執行 Windows 的 Azure 虛擬機器](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
1. 按一下 VM 內部的 [開始] 功能表，然後輸入 **diskmgmt.msc** 並按下 **Enter** 鍵。 這會啟動磁碟管理嵌入式管理單元。
2. 磁碟管理會辨識出您擁有新的、未初始化的磁碟，且會隨即顯示「初始化磁碟」視窗。
3. 請確定已選取新磁碟，並按一下 [確定] 將磁碟初始化。
4. 新的磁碟現在會顯示為 [未配置]。 以滑鼠右鍵按一下磁碟上的任何位置，並選取 [新增簡單磁碟區]。 [新增簡單磁碟區精靈] 會隨即啟動。
5. 逐一執行精靈的所有步驟，保留所有預設值，並在完成後選取 [完成]。
6. 關閉磁碟管理。
7. 您會看到快顯視窗，說明您必須先將新的磁碟格式化之後才能使用。 按一下 [格式化磁碟]。
8. 在 [格式化新磁碟] 對話方塊中，檢查設定，然後按一下 [開始]。
9. 系統會向您顯示警告，說明格式化磁碟會清除所有資料，請按一下 [確定]。
10. 格式化完成之後，按一下 [確定]。


## <a name="option-2-attach-an-existing-disk"></a>選項 2：連接現有磁碟
1. 在 [磁碟] 刀鋒視窗上，按一下 [+ 新增資料磁碟]。
2. 在 [連結未受管理的磁碟] 刀鋒視窗中，於 [來源類型] 中選取 [現有 Blob]。

    ![檢閱磁碟設定](./media/attach-disk-portal/attach-existing-unmanaged.png)

    3. 按一下 [瀏覽] 以瀏覽至現有 VHD 所在的儲存體帳戶和容器。 按一下 VHD，然後按一下 [選取]。
4. 在 [連結未受管理的磁碟] 刀鋒視窗中按一下 [確定]。
5. 在 [磁碟] 刀鋒視窗中，按一下 [儲存] 以將磁碟新增到 VM 設定。
   


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
                
從磁碟中刪除資料之後，您可以使用 TRIM 來執行重組，以確保 TRIM 作業正確排清：

```
defrag.exe <volume:> -l
```

## <a name="next-steps"></a>後續步驟
如果您的應用程式需要使用 D: 磁碟機來儲存資料，可以[變更 Windows 暫存磁碟的磁碟機代號](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。


