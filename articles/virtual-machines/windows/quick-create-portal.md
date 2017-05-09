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
ms.date: 04/13/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: df6f1b86b706d58a5c07a4f3de43a1872da61511
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>使用 Azure 入口網站建立 Windows 虛擬機器

您可以透過 Azure 入口網站建立 Azure 虛擬機器。 此方法可提供以瀏覽器為基礎的使用者介面，以便建立和設定虛擬機器，以及所有相關的資源。 本快速入門會逐步說明如何使用 Azure 入口網站建立虛擬機器。 部署完成後，我們會連線至伺服器並安裝 IIS。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-virtual-machine"></a>Create virtual machine

2. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

3. 選取 [新增] 刀鋒視窗中的 [計算]，選取 [計算] 刀鋒視窗中的 [Windows Server 2016 Datacenter]，然後按一下 [建立] 按鈕。

4. 填寫虛擬機器 [基本資訊] 表單。 在此輸入的使用者名稱和密碼用於登入虛擬機器。 對於 [資源群組]，建立新的資源群組。 資源群組是在其中建立和共同管理 Azure 資源的邏輯容器。 完成時，按一下 [確定]。

    ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

5. 選擇 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 

    ![顯示 VM 大小的螢幕擷取畫面](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

6. 在 [設定] 刀鋒視窗中，選取 [使用受控磁碟] 之下的 [是]，保留其餘設定的預設值，然後按一下 [確定]。

7. 在 [摘要] 頁面上，按一下 [確定] 來啟動虛擬機器部署。

8. 若要監視部署狀態，請按一下虛擬機器。 在 Azure 入口網站儀表板上，或藉由選取左側功能表的 [虛擬機器]，即可找到此 VM。 建立 VM 後，狀態會從 [部署中] 變更為 [執行中]。

## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80 

若要允許 IIS 的流量，您需要對 Web 流量開啟連接埠 80。 此步驟會引導您建立網路安全性群組 (NSG) 規則，以允許連接埠 80 上的輸入連線。

1. 在虛擬機器刀鋒視窗的 [基本資訊] 區段中，按一下 [資源群組] 的名稱。
2. 在資源群組刀鋒視窗中，按一下資源清單中的 [網路安全性群組]。 NSG 名稱應該是 VM 名稱結尾附加 -nsg。
3. 按一下 [輸入安全性規則] 標題，以開啟輸入規則清單。 您應會看到清單中已經有 RDP 的規則。
4. 按一下 [+ 新增] 以開啟 [新增輸入安全性規則] 刀鋒視窗。
5. 在 [名稱] 中輸入 *IIS*。 確定 [連接埠範圍] 已設為 80 且 [動作] 已設為 [允許]。 按一下 [確定] 。


## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

完成部署之後，建立與虛擬機器的遠端桌面連線。

1. 按一下虛擬機器刀鋒視窗上的 [連線] 按鈕。 隨即建立並下載遠端桌面通訊協定檔案 (.rdp 檔案)。

    ![入口網站 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. 若要連線至您的 VM，請開啟下載的 RDP 檔案。 出現提示時，按一下 [連線]。 在 Mac 上，您需要 RDP 用戶端，例如來自 Mac App Store 的[遠端桌面用戶端](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)。

3. 輸入在建立虛擬機器時指定的使用者名稱和密碼，然後按一下 [確定]。

4. 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以繼續進行連線。


## <a name="install-iis-using-powershell"></a>使用 PowerShell 安裝 IIS

在虛擬機器上，開啟 PowerShell 提示字元並執行下列命令來安裝 IIS，並啟用本機防火牆規則以允許 Web 流量通過：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>檢視 IIS 歡迎使用頁面

安裝 IIS 後，現在經由網際網路在您的 VM 上開啟連接埠 80 - 您可以使用所選的網頁瀏覽器來檢視預設 IIS 歡迎使用畫面。 從 VM 的刀鋒視窗中取得 [公用 IP 位址] 並使用它來瀏覽預設網頁。 

![IIS 預設網站](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>刪除虛擬機器

若不再需要，可刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器刀鋒視窗中的資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

[安裝角色和設定防火牆的教學課程](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[瀏覽 VM 部署 CLI 範例](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

