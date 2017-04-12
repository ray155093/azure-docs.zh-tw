---
title: "在第一個 Windows VM 上安裝 IIS | Microsoft Docs"
description: "藉由安裝 IIS 並使用 Azure 入口網站來開啟連接埠 80，試驗您的第一個 Windows 虛擬機器。"
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6334ea45-db6b-4e08-abb5-1f98927ebc34
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b11ce1eab0c26a802c31bc418cdf725cbc4fba30
ms.lasthandoff: 03/31/2017


---
# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>試驗在 Windows VM 上安裝角色
在讓您的第一部虛擬機器 (VM) 啟動並正常執行之後，您可以接著安裝軟體和服務。 針對本教學課程，我們將使用 Windows Server VM 上的「伺服器管理員」來安裝 IIS。 接著，我們將使用 Azure 入口網站對 IIS 流量開啟連接埠 80 來建立「網路安全性群組」(NSG)。 

如果您尚未建立第一個 VM，您應該先返回 [在 Azure 入口網站中建立第一個 Windows 虛擬機器](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，然後再繼續此教學課程。

## <a name="make-sure-the-vm-is-running"></a>確認 VM 正在執行
1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，按一下 [虛擬機器] 。 然後從清單中選取虛擬機器。
3. 如果狀態為 [已停止 (已解除配置)]，請按一下 VM [基本資訊] 刀鋒視窗上的 [啟動] 按鈕。 如果狀態為 [正在執行] ，則您可以移至下一個步驟。

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>連線至虛擬機器並登入
1. 在 [中樞] 功能表上，按一下 [虛擬機器] 。 然後從清單中選取虛擬機器。
2. 在虛擬機器的刀鋒視窗中，按一下 [ **連線**]。 這會建立並下載遠端桌面通訊協定檔案 (.rdp 檔案)，該檔案就像是用來連接到您的電腦的捷徑。 您可能想要將此檔案儲存至桌面，以便存取。 **開啟** 此檔案以連接到您的 VM。
   
    ![顯示如何連接至 VM 的 Azure 入口網站螢幕擷取畫面](./media/hero-role/connect.png)
3. 您會收到警告，表示 .rdp 來自未知的發行者。 這是正常現象。 在 [遠端桌面] 視窗中按一下 [連接]  以繼續。
   
    ![未知發行者相關警告的螢幕擷取畫面](./media/hero-role/rdp-warn.png)
4. 在 [Windows 安全性] 視窗中，針對您建立 VM 時所建立的本機帳戶，輸入使用者名稱和密碼。 使用者名稱會輸入為 vmname&#92;username，然後按一下 [確定]。
   
    ![輸入 VM 名稱、使用者名稱及密碼時的螢幕擷取畫面](./media/hero-role/credentials.png)
5. 您會收到一個指出無法驗證憑證的警告。 這是正常現象。 按一下 [是]  來確認虛擬機器的身分識別，並完成登入。
   
   ![顯示驗證 VM 身分識別相關訊息的螢幕擷取畫面](./media/hero-role/cert-warning.png)

如果嘗試連線時遇到問題，請參閱 [針對執行 Windows 之 Azure 虛擬機器的遠端桌面連線進行疑難排解](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="install-iis-on-your-vm"></a>在您的 VM 上安裝 IIS
您現在已登入 VM，我們將安裝一個伺服器角色，以便您進行更多試驗。

1. 開啟 [伺服器管理員]  \(如果尚未開啟)。 按一下 [啟動] 功能表，然後按一下 [伺服器管理員]。
2. 在 [伺服器管理員] 中，選取左窗格中的 [本機伺服器]。 
3. 在功能表中，選取 [管理] > [新增角色及功能]。
4. 在「新增角色及功能精靈」的 [安裝類型] 頁面上，選擇 [角色型或功能型安裝]，然後按 [下一步]。
   
    ![顯示 [安裝類型] 的 [新增角色及功能精靈] 索引標籤的螢幕擷取畫面](./media/hero-role/role-wizard.png)
5. 從伺服器集區中選取 VM，然後按 [下一步] 。
6. 在 [伺服器角色] 頁面上，選取 [Web 伺服器 (IIS)]。
   
    ![顯示 [伺服器角色] 的 [新增角色及功能精靈] 索引標籤的螢幕擷取畫面](./media/hero-role/add-iis.png)
7. 在新增 IIS 所需功能的相關快顯視窗中，確定已選取 [包含管理工具]，然後按一下 [新增功能]。 當快顯視窗關閉時，請在精靈中按 [下一步]  。
   
    ![顯示用以確認新增 IIS 角色之快顯視窗的螢幕擷取畫面](./media/hero-role/confirm-add-feature.png)
8. 在功能頁面上，按 [下一步] 。
9. 在 [Web 伺服器角色 (IIS)] 頁面上，按 [下一步]。 
10. 在 [角色服務] 頁面上，按 [下一步]。 
11. 在 [確認] 頁面中上，按一下 [安裝]。 
12. 安裝完成時，按一下精靈上的 [關閉]  。

## <a name="open-port-80"></a>開啟連接埠 80
為了讓您的 VM 透過連接埠 80 接收輸入流量，您必須將輸入規則新增至網路安全性群組。 

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在 [虛擬機器]  中，選取您所建立的 VM。
3. 在虛擬機器設定中，選取 [網路介面]  ，然後選取現有的網路介面。
   
    ![顯示網路介面的虛擬機器設定的螢幕擷取畫面](./media/hero-role/network-interface.png)
4. 在網路介面的 [基本資訊] 中，按一下 [網路安全性群組]。
   
    ![顯示網路介面的 [基本資訊] 區段的螢幕擷取畫面](./media/hero-role/select-nsg.png)
5. 在 NSG 的 [基本資訊] 刀鋒視窗中，您應該會有一個 **default-allow-rdp** 的現有預設輸入規則可讓您登入 VM。 您將加入另一個允許 IIS 流量的輸入規則。 按一下 [輸入安全性規則] 。
   
    ![顯示 NSG 的 [基本資訊] 區段的螢幕擷取畫面](./media/hero-role/inbound.png)
6. 在 [輸入安全性規則] 中，按一下 [新增]。
   
    ![顯示用以新增安全性規則之按鈕的螢幕擷取畫面](./media/hero-role/add-rule.png)
7. 在 [輸入安全性規則] 中，按一下 [新增]。 在連接埠範圍中輸入 **80**，請確定已選取 [允許]。 完成時按一下 [確定]。
   
    ![顯示用以新增安全性規則之按鈕的螢幕擷取畫面](./media/hero-role/port-80.png)

如需 NSG、輸入和輸出規則的詳細資訊，請參閱 [允許使用 Azure 入口網站從外部存取您的 VM](nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="connect-to-the-default-iis-website"></a>連接到預設 IIS 網站
1. 在 Azure 入口網站中，按一下 [虛擬機器]  ，然後選取您的 VM。
2. 在 [基本資訊] 刀鋒視窗中，複製您的 [公用 IP 位址]。
   
    ![顯示何處尋找 VM 公用 IP 位址的螢幕擷取畫面](./media/hero-role/ipaddress.png)
3. 開啟瀏覽器並在網址列中，輸入您的公用 IP 位址︰http://<publicIPaddress>，然後按一下 **Enter** 前往該位址。
4. 您的瀏覽器應該會開啟預設的 IIS 網頁。 它看起來會像這樣：
   
    ![顯示預設 IIS 頁面在瀏覽器中樣貌的螢幕擷取畫面](./media/hero-role/iis-default.png)

## <a name="next-steps"></a>後續步驟
* 您也可以體驗 [附加資料磁碟](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 至虛擬機器。 資料磁碟可為虛擬機器提供更多的儲存空間。


