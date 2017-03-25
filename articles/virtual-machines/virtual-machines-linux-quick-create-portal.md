---
title: "Azure 快速入門 - 建立 VM 入口網站 | Microsoft Docs"
description: "Azure 快速入門 - 建立 VM 入口網站"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 348407b57bbb3329d7d27a6f38623e052aecc58b
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>使用 Azure 入口網站建立 Linux 虛擬機器

您可以透過 Azure 入口網站建立 Azure 虛擬機器。 此方法可提供以瀏覽器為基礎的使用者介面，以便建立和設定 VM，以及所有相關的 Azure 資源。

開始之前，不需要私密和公開 SSH 金鑰。 如需建立適用於 Azure 之 SSH 金鑰的詳細資訊，請參閱[建立適用於 Azure 的 SSH 金鑰](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="log-in-to-azure"></a>登入 Azure 

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-virtual-machine"></a>Create virtual machine

2. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

3. 選取 [新增] 刀鋒視窗中的 [計算]，選取 [計算] 刀鋒視窗中的 [Ubuntu Server 16.04 LTS]，然後按一下 [建立] 按鈕。

4. 填寫虛擬機器 [基本資訊] 表單。 對於 [驗證類型]，建議使用 SSH。 貼上 [SSH 公開金鑰] 時，請謹慎地移除任何前置或尾端的空白字元。 對於 [資源群組]，建立新的資源群組。 資源群組是在其中建立和共同管理 Azure 資源的邏輯容器。 完成時，按一下 [確定]。

    ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

5. 選擇 VM 大小，然後按一下 [選取]。 

    ![在入口網站刀鋒視窗中選取 VM 的大小](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

6. 在 [設定] 刀鋒視窗中，選取 [使用受控磁碟] 之下的 [是]，保留其餘設定的預設值，然後按一下 [確定]。

7. 在 [摘要] 頁面上，按一下 [確定] 來啟動虛擬機器部署。

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

完成部署之後，建立與虛擬機器的 SSH 連線。

1. 按一下虛擬機器。 在 Azure 入口網站的主畫面上，或藉由選取左側功能表的 [虛擬機器]，即可找到此 VM。

2. 按一下 [連線]  按鈕。 [連線] 按鈕顯示的 SSH 連接字串可用來連線至虛擬機器。

    ![入口網站 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. 執行以下命令來建立 SSH 工作階段。 以您從 Azure 入口網站複製的連接字串取代此連接字串。

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>刪除虛擬機器

若不再需要，可刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器刀鋒視窗中的資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

[建立高可用性的虛擬機器教學課程](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[瀏覽 VM 部署 CLI 範例](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

