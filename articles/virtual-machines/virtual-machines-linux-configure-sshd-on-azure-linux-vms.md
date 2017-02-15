---
title: "在 Azure Linux 虛擬機器上設定 SSHD | Microsoft Docs"
description: "設定 SSHD 以提供安全性最佳做法和封鎖以 SSH 登入 Azure Linux 虛擬機器。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 08499c4242fdc59ef932d6b8f2e8442e5cdc55b2
ms.openlocfilehash: a606f06de55b1db4392d066a3e1d026eb3b54d74


---

# <a name="configure-sshd-on-azure-linux-vms"></a>在 Azure Linux VM 上設定 SSHD

本文說明如何在 Linux 上鎖定 SSH 伺服器、提供最佳做法安全性，以及使用 SSH 金鑰 (而不是密碼) 來加速 SSH 登入程序。  為了進一步鎖定 SSHD，我們將禁止根使用者登入、限制允許透過核准群組清單登入的使用者、停用 SSH 通訊協定第 1 版、設定最多金鑰位元數，以及設定自動登出閒置使用者。  這篇文章的需求如下︰Azure 帳戶 ([取得免費試用](https://azure.microsoft.com/pricing/free-trial/)) 和 [SSH 公開金鑰和私密金鑰檔案](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="quick-commands"></a>快速命令

使用下列設定來設定 `/etc/ssh/sshd_config`︰

### <a name="disable-password-logins"></a>停用密碼登入

```bash
PasswordAuthentication no
```

### <a name="disable-login-by-the-root-user"></a>停用根使用者登入

```bash
PermitRootLogin no
```

### <a name="allowed-groups-list"></a>允許的群組清單

```bash
AllowGroups wheel
```

### <a name="allowed-users-list"></a>允許的使用者清單

```bash
AllowUsers ahmet ralph
```

### <a name="disable-ssh-protocol-version-1"></a>停用 SSH 通訊協定第 1 版

```bash
Protocol 2
```

### <a name="minimum-key-bits"></a>最少金鑰位元數

```bash
ServerKeyBits 2048
```

### <a name="disconnect-idle-users"></a>中斷連接閒置使用者

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說

SSHD 是在 Linux VM 上執行的 SSH 伺服器。  SSH 是從 MacBook 的殼層、Linux 工作站或 Windows 的 Bash 執行的用戶端。  SSH 也是用來保護和加密工作站與 Linux VM 之間通訊的通訊協定，這使得 SSH 也成為 VPN (虛擬私人網站)。

在本文中，務必為整個逐步解說開放 Linux VM 的一個登入。  建立 SSH 連接之後，只要未關閉視窗，就會保持為開啟的工作階段。  只要登入一個終端機，就可以變更 SSHD 服務，而不會因為錯誤的變更而遭鎖定。  如果您因為錯誤的 SSHD 設定而被鎖在 Linux VM 外，Azure 可讓您使用 [Azure VM 存取擴充](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)來重設錯誤的 SSHD 設定。

有鑑於此，我們開啟兩個終端機，並同時從這兩者以 SSH 登入 Linux VM。  我們使用第一個終端機來變更 SSHD 設定檔，以及重新啟動 SSHD 服務。  等到服務重新啟動後，我們會使用第二個終端機來測試這些變更。  由於我們要停用 SSH 密碼並重度依賴 SSH 金鑰，所以如果您的 SSH 金鑰不正確而您關閉 VM 連線，VM 將永久遭到鎖定，而且沒有人能夠登入。如此一來，您只能將它刪除後再重新建立。

## <a name="disable-password-logins"></a>停用密碼登入

保護 Linux VM 的最快方式就是停用密碼登入。  密碼登入啟用時，耙梳 Web 的 Bot 會立即開始嘗試暴力猜測 Linux VM (使用 SSH) 的密碼。  完全停用密碼登入可讓 SSH 伺服器忽略所有密碼登入嘗試。

```bash
PasswordAuthentication no
```

## <a name="disable-login-by-the-root-user"></a>停用根使用者登入

根據 Linux 最佳做法，`root` 使用者永遠不可能透過 SSH 或使用 `sudo su` 來登入。  應該永遠透過 `sudo` 命令執行需要根層級權限的所有命令，此命令會記錄所有動作供未來稽核。  禁止 `root` 使用者透過 SSH 登入是一項安全性最做作法步驟，可確保只有授權的使用者才能透過 SSH 登入。

```bash
PermitRootLogin no
```

## <a name="allowed-groups-list"></a>允許的群組清單

SSH 提供方法來限制允許或不允許透過 SSH 登入的使用者和群組。  這項功能使用清單來核准或拒絕特定使用者和群組登入。  將 wheel 群組設為 `AllowGroups` 清單可限制只有 wheel 群組中的使用者帳戶，才核准透過 SSH 登入。

```bash
AllowGroups wheel
```

## <a name="allowed-users-list"></a>允許的使用者清單

同樣是完成這件工作，但將 SSH 登入限制為使用者比 `AllowGroups` 的做法更具體。  

```bash
AllowUsers ahmet ralph
```

## <a name="disable-ssh-protocol-version-1"></a>停用 SSH 通訊協定第 1 版

SSH 通訊協定第 1 版不安全，應該停用。  SSH 通訊協定第 2 版是目前的版本，提供安全的方式來透過 SSH 登入伺服器。  停用 SSH 第 1 版會拒絕任何 SSH 用戶端嘗試使用 SSH 第 1 版來建立 SSH 伺服器的連接。  只允許 SSH 第 2 版與 SSH 伺服器交涉連接。

```bash
Protocol 2
```

## <a name="minimum-key-bits"></a>最少金鑰位元數

根據安全性最佳做法，密碼 SSH 登入會停用，只允許使用 SSH 金鑰向 SSH 伺服器進行驗證。  這些 SSH 金鑰可能以不同長度的金鑰建立 (以位元為單位)。  最佳做法規定長度 2048 位元的金鑰是可接受的最小金鑰強度。  少於 2048 位元的金鑰在理論上會被破解。  將 `ServerKeyBits` 設定為 `2048` 可允許任何使用 2048 位元或更大金鑰的連接，並拒絕少於 2048 位元的連接。

```bash
ServerKeyBits 2048
```

## <a name="disconnect-idle-users"></a>中斷連接閒置使用者

如果使用者開啟的連接已閒置超過一段設定的秒數，SSH 能夠中斷連接這些使用者。  只讓作用中的使用者保持開啟的工作階段可限制 Linux VM 暴露。

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="restart-sshd"></a>重新啟動 SSHD

若要啟用 `/etc/ssh/sshd_config` 中的設定，請重新啟動 SSHD 處理序以重新啟動 SSH 伺服器。  您用來重新啟動 SSH 伺服器的終端機視窗會保持開啟，不會遺失已開啟的 SSH 工作階段。  若要測試新的 SSH 伺服器設定，請使用第二個終端機視窗或索引標籤。  如果使用不同的終端機來測試 SSH 連接，您可以返回第一個終端機來進一步變更 `/etc/ssh/sshd_config`，不會因為 SSHD 變更錯誤而遭鎖定。  

### <a name="on-redhat-centos-and-fedora"></a>在 Redhat、Centos 和 Fedora 上

```bash
service sshd restart
```

### <a name="on-debian--ubuntu"></a>在 Debian 和 Ubuntu 上

```bash
service ssh restart
```

## <a name="reset-sshd-using-azure-reset-access"></a>使用 Azure reset-access 重設 SSHD

如果您因為錯誤變更 SSHD 設定而遭鎖定，您可以使用 Azure VM 存取擴充，將 SSHD 設定重設回原始設定。

將任何範例名稱換成您自己的名稱。

```azurecli
azure vm reset-access \
--resource-group myResourceGroup \
--name myVM \
--reset-ssh
```

## <a name="install-fail2ban"></a>安裝 Fail2ban

強烈建議安裝和設定開放原始碼應用程式 Fail2ban，它會封鎖重複嘗試以暴力密碼破解透過 SSH 來登入 Linux VM。  Fail2ban 會記錄重複嘗試透過 SSH 登入的失敗情況，然後建立防火牆規則來封鎖這些嘗試的來源 IP 位址。

* [Fail2ban 首頁](http://www.fail2ban.org/wiki/index.php/Main_Page)

## <a name="next-steps"></a>後續步驟

既然您已在 Linux VM 上設定並鎖定 SSH 伺服器，您還可以採取更多的安全性最佳做法。  

* [管理使用者、SSH，並使用 VMAccess 擴充功能檢查或修復 Azure Linux VM 上的磁碟](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [使用 Azure CLI 將 Linux VM 上的磁碟加密](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Azure Resource Manager 範本中的存取和安全性](virtual-machines-linux-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO2-->


