---
title: "將受管理的資料磁碟連結到 Windows VM - Azure | Microsoft Docs"
description: "如何在 Azure 入口網站中使用 Resource Manager 部署模型，將新的受管理資料磁碟連結到 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9c29390756ac2cd925ed7d2989393e63ed0a1239
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>如何在 Azure 入口網站中將受管理的資料磁碟連結到 Windows VM

本文示範如何透過 Azure 入口網站將新的受管理資料磁碟連結到 Windows 虛擬機器。 這麼做之前，請先檢閱下列提示：

* 虛擬機器的大小會控制您可以連接的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](sizes.md)。
* 對於新的磁碟，當您連接的時候 Azure 會建立該磁碟，所以您不需要建立它。

您也可以[使用 Powershell 連接資料磁碟](attach-disk-ps.md)。



## <a name="add-a-data-disk"></a>新增資料磁碟
1. 在左側功能表中，按一下 [虛擬機器]。
2. 然後從清單中選取虛擬機器。
3. 在 [虛擬機器] 刀鋒視窗中，按一下 [磁碟]。
   4. 在 [磁碟] 刀鋒視窗上，按一下 [+ 新增資料磁碟]。
5. 在新磁碟的下拉式清單中，選取 [建立空的]。
6. 在 [建立受管理磁碟] 刀鋒視窗中，輸入磁碟名稱並視需要調整其他設定。 完成之後，請按一下 [建立]。
7. 在 [磁碟] 刀鋒視窗中，按一下 [儲存] 以儲存 VM 的新磁碟設定。
6. 在 Azure 建立磁碟並將其連接至虛擬機器之後，該新磁碟就會列在虛擬機器之磁碟設定中的 [資料磁碟] 底下。


## <a name="initialize-a-new-data-disk"></a>初始化新的資料磁碟

1. 連線至 VM。
1. 按一下 VM 內部的 [開始] 功能表，然後輸入 **diskmgmt.msc** 並按下 **Enter** 鍵。 這將會啟動磁碟管理嵌入式管理單元。
2. 磁碟管理將會辨識出您擁有新的、未初始化的磁碟，且會隨即顯示「初始化磁碟」視窗。
3. 請確定已選取新磁碟，並按一下 [確定] 將磁碟初始化。
4. 新的磁碟現在將會顯示為 [未配置]。 以滑鼠右鍵按一下磁碟上的任何位置，並選取 [新增簡單磁碟區]。 [新增簡單磁碟區精靈] 將會隨即啟動。
5. 逐一執行精靈的所有步驟，保留所有預設值，並在完成後選取 [完成]。
6. 關閉磁碟管理。
7. 您將會看到快顯視窗，說明您必須先將新的磁碟格式化之後才能使用。 按一下 [格式化磁碟]。
8. 在 [格式化新磁碟] 對話方塊中，檢查設定，然後按一下 [開始]。
9. 系統會向您顯示警告，說明格式化磁碟將會清除所有資料，請按一下 [確定]。
10. 格式化完成之後，按一下 [確定]。

## <a name="next-steps"></a>後續步驟
如果您的應用程式需要使用 D: 磁碟機來儲存資料，可以[變更 Windows 暫存磁碟的磁碟機代號](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

