---
title: "使用 Azure 入口網站建立 Linux VM | Microsoft Docs"
description: "使用 Azure 入口網站建立 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b1446cd8892e14988ff428eaa03233f8e9aefb8a


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>使用入口網站在 Azure 上建立 Linux VM
本文將說明如何使用 [Azure 入口網站](https://portal.azure.com/)建立 Linux 虛擬機器。

這些需求包括：

* [一個 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)
* [SSH 公開金鑰和私密金鑰檔案](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="sign-in"></a>Sign in
以您的 Azure 帳戶身分識別登入 Azure 入口網站，按一下左上角的 [+ 新增]  ︰

![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>選擇 VM
按一下 [Marketplace] 中的 [虛擬機器]，然後按一下 [精選應用程式] 映像清單中的 [Ubuntu Server 14.04 LTS]。  確認下方的部署模型是 `Resource Manager`，然後按一下 [建立]。

![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>輸入 VM 選項
在 **基本概念** 頁面上，輸入︰

* VM 的名稱
* 系統管理員的使用者名稱
* 要設定為 [SSH 公開金鑰] 
* SSH 公開金鑰做為字串 (來自您的 `~/.ssh/` 目錄)
* 資源群組名稱或選取現有的群組

並按一下 [確定] 以繼續，然後選擇 VM 大小；您應該會看到類似下列螢幕擷取畫面的內容：

![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>選擇 VM 大小
選擇 [DS1] 大小 (這會在進階 SSD 上安裝 Ubuntu)，然後按一下 [選取] 進行設定。

![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>儲存體和網路
在 [設定] 中，保留 [儲存體] 和 [網路] 的預設值，然後按一下 [確定] 來檢視摘要。  請注意，磁碟類型已藉由選擇 DS1 (**S** 表示 SSD) 設定為進階 SSD。

![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>確認 VM 的設定並啟動
確認新的 Ubuntu VM 的設定，然後按一下 [確定] 。

![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>尋找 VM NIC
開啟入口網站儀表板並在 [網路介面]  中選擇您的 NIC

![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>尋找公用 IP
開啟 NIC 設定之下的公用 IP 位址功能表

![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>透過 SSH 連接到 VM
使用 SSH 公開金鑰透過 SSH 連接到公用 IP。  從 Mac 或 Linux 工作站，您可以直接從終端機進行 SSH。 如果您位於 Windows 工作站上，您需要使用 PuTTY、MobaXTerm 或 Cygwin 來透過 SSH 連接到 Linux。  如果您還沒有這麼做，這裡有文件可讓您的 Windows 工作站準備好透過 SSH 連接到 Linux。

[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](virtual-machines-linux-ssh-from-windows.md)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>後續步驟
現在您已經快速建立一個用於測試或示範用途的 Linux VM。 若要建立為您的基礎結構自訂的 Linux VM，您可以依照以下任何文章執行。

* [使用範本在 Azure 上建立 Linux VM](virtual-machines-linux-cli-deploy-templates.md)
* [使用範本在 Azure 上建立受 SSH 保護的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [使用 CLI 建立 Linux VM](virtual-machines-linux-create-cli-complete.md)




<!--HONumber=Nov16_HO2-->


