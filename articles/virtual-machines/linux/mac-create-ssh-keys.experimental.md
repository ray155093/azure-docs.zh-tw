---
title: "在 Azure 上建立 Linux VM 的 SSH 金鑰組 | Microsoft Docs"
description: "安全地建立 Azure Linux VM 的 SSH 公開和私密金鑰組。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: rasquill
experiment_id: rasquill-ssh-20170308
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19acd4efca7ef043f31b436b96f9129caee9591b
ms.lasthandoff: 04/03/2017



---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>建立 Linux VM 的 SSH 公用和私用金鑰組

本文說明如何產生可與 Linux VM 搭配使用的 SSH 通訊協定第 2 版 RSA 公開和私密金鑰檔案組。  您可以利用 SSH 金鑰組，在預設使用 SSH 金鑰進行驗證的 Azure 上建立虛擬機器，進而免除登入密碼的需求。  密碼有可能被猜到，讓您的 VM 遭到持續不斷的暴力密碼破解嘗試來猜測您的密碼。 使用 Azure 範本或 `azure-cli` 建立的 VM 可以將 SSH 公開金鑰納入部署中，進而免於對 SSH 停用密碼登入進行後置部署設定。

## <a name="quick-commands"></a>快速命令

透過 Bash 殼層執行下列命令中，以您自己的選項取代範例。

SSH 公開金鑰檔預設會建立在 `~/.ssh/id_rsa.pub`。 使用下列命令時若出現提示，您應該建立「複雜密碼」來保護您的私密金鑰  (此複雜密碼是用來加密私密金鑰的密碼)。

```bash
ssh-keygen -t rsa -b 2048 
```

將新建立的索引鍵新增至 `ssh-agent`：

```bash
ssh-add ~/.ssh/id_rsa
```

> [!IMPORTANT] 
> 上述命令可在幾乎所有發行版本的 Linux 作業系統上運作，但不一定可在容器中運作，因為環境可能徹底受限。 

## <a name="detailed-walkthrough"></a>詳細的逐步解說

想要登入 Linux 伺服器，最簡單的方式是使用 SSH 公開和私密金鑰。 [公開金鑰密碼編譯](https://en.wikipedia.org/wiki/Public-key_cryptography) 會安全得多，因為密碼非常容易遭到暴力破解。

> [!IMPORTANT]
> 公開金鑰可以與任何人共用；但只有您 (或您的本機安全性基礎結構) 會擁有私密金鑰。  SSH 私密金鑰應該有[非常安全的密碼](https://www.xkcd.com/936/) (來源︰[xkcd.com](https://xkcd.com)) 來保護它。  此密碼只能用於存取 SSH 私密金鑰，而 **不是** 使用者帳戶密碼。  當您將密碼新增至 SSH 金鑰時，便會使用 128位元的 AES 來加密私密金鑰，而未利用密碼解密的私密金鑰沒有用處。  如果攻擊者竊取您的私密金鑰，而且該金鑰沒有密碼，他們就能使用該私密金鑰來登入有對應公開金鑰的伺服器。  如果私密金鑰受密碼保護，攻擊者就無法使用該金鑰，進而為您在 Azure 上的基礎結構提供額外的安全性。

本文會建立 SSH 通訊協定第 2 版 RSA 公開和私密金鑰檔案，這些檔案建議用於 Resource Manager 上的部署。  ssh-rsa  金鑰是在[入口網站](https://portal.azure.com)上進行傳統部署和 Resource Manager 部署時的必要項目。

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>藉由設定 SSH 金鑰來停用 SSH 密碼

Azure 需要至少 2048 位元的 ssh-rsa 格式公開和私密金鑰。 若要建立金鑰，請使用 `ssh-keygen`，在詢問一系列問題後，它便會編寫私密金鑰和對應的公開金鑰。 建立 Azure VM 時，公開金鑰會複製到 `~/.ssh/authorized_keys`。  `~/.ssh/authorized_keys` 中的 SSH 金鑰用於挑戰用戶端，以符合 SSH 登入連線上的對應私密金鑰。  使用用於驗證的 SSH 金鑰進行建立 Azure Linux VM 時，Azure 會將 SSHD 伺服器設定為不允許密碼登入，僅允許以 SSH 金鑰登入。  因此，建立具 SSH 金鑰的 Azure Linux VM，即可協助保護 VM 部署的安全，並免除在 sshd_config 組態檔中停用密碼的標準部署後設定步驟。

## <a name="using-ssh-keygen"></a>使用 ssh-keygen

此命令會使用 2048 位元 RSA 建立密碼保護 (加密) 的 SSH 金鑰組，並為其加上註解以供輕鬆識別。  

依預設，SSH 金鑰會保留在 `~/.ssh` 目錄中。  如果您沒有 `~/.ssh` 目錄，`ssh-keygen` 命令會使用正確的權限為您建立。

```bash
ssh-keygen \
-t rsa \
-b 2048 
```

*命令的說明*

`ssh-keygen` = 用來建立金鑰的程式

`-t rsa` = 要建立的金鑰類型，也就是 RSA 格式 [wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = 金鑰的位元


## <a name="classic-portal-and-x509-certs"></a>傳統入口網站和 X.509 憑證

如果您使用 Azure [傳統入口網站](https://manage.windowsazure.com/)，則需要適用於 SSH 金鑰的 X.509 憑證。  不允許任何其他類型的 SSH 公開金鑰，而「必須」是 X.509 憑證。

若要從現有的 SSH-RSA 私密金鑰建立 X.509 憑證︰

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>使用 `asm` 進行傳統部署

如果您使用傳統部署模型 (Azure 服務管理 CLI `asm`)，您可以在 **.pem** 容器中使用 SSH-RSA 公開金鑰或 RFC4716 格式化的金鑰。  SSH-RSA 公開金鑰是稍早在本文中使用 `ssh-keygen` 建立的金鑰。

若要從現有的 SSH 公開金鑰建立 RFC4716 格式的金鑰︰

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>ssh-keygen 的範例

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ahmet/.ssh/id_rsa.
Your public key has been saved in /home/ahmet/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

已儲存金鑰檔案：

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

本文中的金鑰組名稱。  預設會有名為 **id_rsa** 的金鑰組，而且有些工具可能會預期私密金鑰的檔案名稱為 **id_rsa**，所以最好有此金鑰組。 `~/.ssh/` 目錄是 SSH 金鑰組和 SSH 組態檔的預設位置。  如果未指定完整路徑，則 `ssh-keygen` 會在目前的工作目錄 (而不是預設 `~/.ssh`) 中建立金鑰。

`~/.ssh` 。

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

金鑰密碼︰

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` 將用來加密私密金鑰的密碼稱為「複雜密碼」。  「強烈」建議為金鑰組加上複雜密碼。 若沒有使用複雜密碼來保護私密金鑰，任何人只要擁有該金鑰檔案，就可以用它登入具有對應公開金鑰的任何伺服器。 新增複雜密碼可提升防護能力，以防有人能夠取得您的私密金鑰檔案，讓您有時間變更用來對您進行驗證的金鑰。

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>使用 ssh-agent 來儲存您的私密金鑰密碼

若要避免在每次 SSH 登入時輸入私密金鑰檔案複雜密碼，您可以使用 `ssh-agent` 來快取您的私密金鑰檔案密碼。 如果您使用 Mac，則 OSX 金鑰鏈會在您叫用 `ssh-agent`時安全地儲存私密金鑰密碼。

確認並使用 `ssh-agent` 和 `ssh-add` 將金鑰檔案通知 SSH 系統，才不需要以互動方式使用複雜密碼。

```bash
eval "$(ssh-agent -s)"
```

現在使用 `ssh-add` 命令將私密金鑰加入至 `ssh-agent`。

```bash
ssh-add ~/.ssh/id_rsa
```

私密金鑰密碼現已儲存在 `ssh-agent` 中。

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>使用 `ssh-copy-id` 安裝新的金鑰
如果您已建立 VM，您可以使用下列命令並以您自己的值取代 VM 使用者名稱和伺服器位址，來將新的 SSH 公開金鑰安裝至 Linux VM︰

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>建立和設定 SSH 組態檔

最佳做法是建立及設定 `~/.ssh/config` 檔案來加速登入，並且最佳化您的 SSH 用戶端行為。

下列範例將示範標準組態。

### <a name="create-the-file"></a>建立檔案

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>編輯檔案以加入新的 SSH 組態：

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>範例 `~/.ssh/config` 檔案︰

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

此 SSH 組態可讓您將各個伺服器分段，讓它們各自擁有專用的金鑰組。 預設設定 (`Host *`) 適用於不符合組態檔中任何提升之特定主機的任何主機。

### <a name="config-file-explained"></a>組態檔的說明

`Host` = 在終端機上所呼叫的主機名稱。  `ssh fedora22` 會指示 `SSH` 使用設定區塊中標示為 `Host fedora22` 的值。注意︰這可以是符合您用途的任何標籤，並不代表任何伺服器的實際主機名稱。

`Hostname 102.160.203.241` = 所存取伺服器的 IP 位址或 DNS 名稱。

`User ahmet` = 登入伺服器時要使用的遠端使用者帳戶。

`PubKeyAuthentication yes` = 向 SSH 指出您想要使用 SSH 金鑰來登入。

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = 要用於驗證的 SSH 私密金鑰和對應公開金鑰。

## <a name="ssh-into-linux-without-a-password"></a>使用 SSH 登入 Linux 而不提供密碼

您現在已擁有 SSH 金鑰組和設定好的 SSH 組態檔，因此能夠快速且安全地登入 Linux VM。 第一次使用 SSH 金鑰登入伺服器時，命令會提示您輸入該金鑰檔案的複雜密碼。

```bash
ssh fedora22
```

### <a name="command-explained"></a>命令的說明

在執行 `ssh fedora22` 後，SSH 會先從 `Host fedora22` 區塊中找出所有設定並加以載入，再從最後一個區塊 `Host *` 中載入所有其餘設定。

## <a name="next-steps"></a>後續步驟

接下來是使用新的 SSH 公開金鑰建立 Azure Linux VM。  相較於以預設登入方法密碼建立的 VM，以 SSH 公開金鑰作為登入所建立的 Azure VM 比較安全。  使用 SSH 金鑰建立的 Azure VM 預設會設定為停用密碼，避免遭到暴力猜測嘗試。 如果您需要更多關於建立 SSH 金鑰組的協助，或需要其他憑證，例如以便用於傳統入口網站，請參閱[建立 SSH 金鑰組和憑證的詳細步驟](create-ssh-keys-detailed.md)。

* [使用 Azure 範本建立安全的 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 入口網站建立安全的 Linux VM](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure CLI 建立安全的 Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

