---
title: "Azure 快速入門 - 建立 Windows VM 入口網站 | Microsoft Docs"
description: "Azure 快速入門 - 建立 Windows VM 入口網站"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a13ac5ab425ccbbe53d77cb9f5a8ebf02d009370
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>使用 Azure 入口網站建立 Windows 虛擬機器

您可以透過 Azure 入口網站建立 Azure 虛擬機器。 此方法可提供以瀏覽器為基礎的使用者介面，以便建立和設定虛擬機器，以及所有相關的資源。 本快速入門會逐步說明如何使用 Azure 入口網站建立虛擬機器。 

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-azure"></a>登入 Azure 

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-virtual-machine"></a>Create virtual machine

2. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

3. 選取 [新增] 刀鋒視窗中的 [計算]，選取 [計算] 刀鋒視窗中的 [Windows Server 2016 Datacenter]，然後按一下 [建立] 按鈕。

4. 填寫虛擬機器 [基本資訊] 表單。 在此輸入的使用者名稱和密碼用於登入虛擬機器。 對於 [資源群組]，建立新的資源群組。 資源群組是在其中建立和共同管理 Azure 資源的邏輯容器。 完成時，按一下 [確定]。

    ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. 選擇 VM 大小，然後按一下 [選取]。 

6. 在 [設定] 刀鋒視窗中，選取 [使用受控磁碟] 之下的 [是]，保留其餘設定的預設值，然後按一下 [確定]。

7. 在 [摘要] 頁面上，按一下 [確定] 來啟動虛擬機器部署。

8. 若要監視部署狀態，請按一下虛擬機器。 在 Azure 入口網站儀表板上，或藉由選取左側功能表的 [虛擬機器]，即可找到此 VM。 建立 VM 後，狀態會從 [部署中] 變更為 [執行中]。

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

完成部署之後，建立與虛擬機器的遠端桌面連線。

1. 按一下虛擬機器刀鋒視窗上的 [連線] 按鈕。 隨即建立並下載遠端桌面通訊協定檔案 (.rdp 檔案)。

    ![入口網站 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. 若要連線至您的 VM，請開啟下載的 RDP 檔案。 出現提示時，按一下 [連線]。 在 Mac 上，您需要 RDP 用戶端，例如來自 Mac App Store 的[遠端桌面用戶端](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)。

3. 輸入在建立虛擬機器時指定的使用者名稱和密碼，然後按一下 [確定]。

4. 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以繼續進行連線。

## <a name="delete-virtual-machine"></a>刪除虛擬機器

若不再需要，可刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器刀鋒視窗中的資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

[安裝角色和設定防火牆的教學課程](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[瀏覽 VM 部署 CLI 範例](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
