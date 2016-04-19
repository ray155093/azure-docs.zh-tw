<properties
    pageTitle="使用 Azure 入口網站建立 Linux VM | Microsoft Azure"
    description="使用 Azure 入口網站建立 Linux VM。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/05/2016"
    ms.author="v-livech"
/>

# 使用 Azure 入口網站建立 Linux VM

本文將說明如何使用 [Azure 入口網站](https://portal.azure.com/)立即建立 Linux VM，而不需要安裝任何項目。僅有的需求是 [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)和 [SSH 公開和私密金鑰檔案](virtual-machines-linux-mac-create-ssh-keys.md)。


1. 以您的 Azure 帳戶身分識別登入 Azure 入口網站，按一下左上角的 [+ 新增]︰

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. 按一下 [Marketplace] 中的 [虛擬機器]，然後按一下 [精選應用程式] 映像清單中的 [Ubuntu Server 14.04 LTS]，您應該會看到下列畫面︰

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. 確認下方的部署模型是 `Resource Manager`，然後按一下 [建立]。

4. 在 [基本概念] 頁面上，輸入︰
    - VM 的名稱
    - 系統管理員的使用者名稱
    - 要設定為 [SSH 公開金鑰] 的驗證類型
    - SSH 公開金鑰做為字串 (根據預設，來自您的 `~/.ssh/` 目錄)
    - 資源群組名稱 (以建立新的部署群組)，或選取現有的群組

    以及按一下 [確定] 繼續執行並選擇 VM 大小，看起來應該如下所示︰

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

5. 選擇 [DS1] 大小 (這會在進階 SSD 上安裝 Ubuntu)，然後按一下 [選取] 進行設定。

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

6. 在 [設定] 中，保留 [儲存體] 和 [網路] 的預設值，然後按一下 [確定] 來檢視摘要。

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

7. 確認新的 Ubuntu VM 的設定，然後按一下 [確定]。

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

8. 開啟入口網站儀表板並 [網路介面] 中選擇您的 NIC

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

9. 開啟 NIC 設定之下的公用 IP 位址功能表

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

10. 使用 SSH 公開金鑰透過 SSH 連接到公用 IP

```
user@slackware$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## 後續步驟

如果想要的話，請繼續進行並[新增磁碟](virtual-machines-linux-add-disk.md)。

<!---HONumber=AcomDC_0413_2016-->