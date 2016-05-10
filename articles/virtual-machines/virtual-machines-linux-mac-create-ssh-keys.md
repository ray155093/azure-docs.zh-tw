<properties
	pageTitle="在 Linux 和 Mac 上建立 SSH 金鑰 |Microsoft Azure"
	description="在 Linux 和 Mac 上產生和使用 SSH 金鑰，搭配 Azure 上 Resource Manager 和傳統部署模型。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/02/2016"
	ms.author="v-livech"/>

# 在 Linux 和 Mac 上為 Azure 中的 Linux VM 建立 SSH 金鑰

若要建立受密碼保護的 SSH 公開和私密金鑰，您需要在工作站上開啟終端機。擁有 SSH 金鑰後，預設可以使用該金鑰建立新 VM，或使用 Azure CLI 和 Azure 範本將公開金鑰新增至現有 VM。

## 快速命令清單

在下列命令範例中，請將 &lt; 和 &gt; 之間的值取代為您自己環境中的值。

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

輸入將儲存在 `~/.ssh/` 目錄中之檔案的名稱︰

```bash
<azure_fedora_id_rsa>
```

輸入 azure\_fedora\_id\_rsa 的複雜密碼︰

```bash
<correct horse battery staple>
```

將新建立的索引鍵加入至 Linux 和 Mac 上的 `ssh-agent` (也會加入到 OSX 金鑰鏈)︰

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

將 SSH 公開金鑰複製到您的 Linux 伺服器︰

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

使用金鑰而非密碼來測試登入︰

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## 簡介

想要登入 Linux 伺服器，最簡單的方式是使用 SSH 公開和私密金鑰，但比起使用密碼來登入 Azure 中的 Linux 或 BSD VM，使用[公開金鑰密碼編譯](https://en.wikipedia.org/wiki/Public-key_cryptography)會安全得多，因為密碼非常容易遭到暴力破解。公開金鑰可以與任何人共用；但只有您 (或您的本機安全性基礎結構) 會擁有私密金鑰。建立的 SSH 私密金鑰將會有[安全密碼](https://www.xkcd.com/936/)來保護它，此密碼只能用於存取 SSH 私密金鑰，而**不是**使用者帳戶密碼。任何擁有私密金鑰但沒有密碼的人員都可以使用所安裝的公開金鑰來存取任何伺服器。如果沒有密碼，就不能使用私密金鑰。


本文會建立 ssh-rsa 格式的金鑰檔案，因為它們是 Resource Manager 上的部署所建議使用的檔案，而且也是在[入口網站](https://portal.azure.com)上進行傳統部署和資源管理員部署時所必須使用的檔案。


## 建立 SSH 金鑰

Azure 需要至少 2048 位元的 ssh-rsa 格式公開和私密金鑰。為了建立金鑰組，我們將使用 `ssh-keygen`，在詢問一系列問題後，它便會編寫私密金鑰和對應的公開金鑰。您在建立 Azure VM 時會傳遞公開金鑰內容，此內容會複製到 Linux VM，而且當您登入時，此內容會與安全儲存在本機的私密金鑰搭配使用來對您進行驗證。

## 使用 ssh-keygen

此命令會使用 2048 位元 RSA 建立密碼保護的 SSH 金鑰組，並為其加上註解以供輕鬆識別。

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_命令的說明_

`ssh-keygen` = 用來建立金鑰的程式

`-t rsa` = 要建立的金鑰類型，也就是 [RSA 格式](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = 金鑰的位元

`-C "ahmet@fedoraVMAzure"` = 附加至公開金鑰檔案結尾以便輕鬆識別的註解。通常會以一封電子郵件做為註解，但您可以使用任何最適合您的基礎結構的事物。

## ssh-keygen 的逐步解說

詳細說明每個步驟。從執行 `ssh-keygen` 開始。

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
The key's randomart image is:
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

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

本文中的金鑰組名稱。預設會有名為 **id\_rsa** 的金鑰組，而且有些工具可能會預期私密金鑰的檔案名稱為 **id\_rsa**，所以最好有此金鑰組 (`~/.ssh/` 是所有 SSH 金鑰組和 SSH 組態檔的典型預設位置)。

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
這會顯示新的金鑰組和其權限。`ssh-keygen` 會建立 `~/.ssh` (如果不存在)，並設定正確的擁有權和檔案模式。

金鑰密碼︰

`Enter passphrase (empty for no passphrase):`

強烈建議為金鑰組加上密碼 (`ssh-keygen` 將此密碼稱為「複雜密碼」)。若沒有使用密碼來保護金鑰組，任何人只要擁有私密金鑰檔案，就可以用它登入擁有相對應公開金鑰的任何伺服器 (您的伺服器)。新增密碼進而提升防護能力以防有人能夠取得您的私密金鑰檔案，可讓您有時間變更用來對您進行驗證的金鑰。

## 使用 ssh-agent 來儲存您的私密金鑰密碼

若要避免在每次 SSH 登入時輸入私密金鑰檔案密碼，您可以使用 `ssh-agent` 來快取您的私密金鑰檔案密碼，以便快速又安全地登入 Linux VM。如果您使用 OSX，金鑰鏈會在您叫用 `ssh-agent` 時安全地儲存您的私密金鑰密碼。

請先確認 `ssh-agent` 正在執行

```bash
eval "$(ssh-agent -s)"
```

現在使用 `ssh-add` 命令將私密金鑰加入至 `ssh-agent`，而在 OSX 上，這會啟動將存放認證的金鑰鏈。

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

現在會儲存私密金鑰密碼，所以您不需要在每次 SSH 登入時輸入金鑰密碼。

## 建立和設定 SSH 組態檔

雖然對於讓 Linux VM 啟動並執行來說不一定需要，但最好還是建立和設定 `~/.ssh/config` 檔案，以避免不小心使用密碼來登入 VM、自動為不同的 Azure VM 使用不同的金鑰組，以及也設定其他程式 (例如 **git**) 來鎖定多部伺服器。

下列範例將示範標準組態。

### 建立檔案

```bash
touch ~/.ssh/config
```

### 編輯檔案以加入新的 SSH 組態：

```bash
vim ~/.ssh/config
```

### 範例 `~/.ssh/config` 檔案︰

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

此 SSH 組態可讓您將各個服務分段，讓它們各自擁有專用的金鑰組。預設設定則適用於您所登入、但不符合上述任何群組的任何主機。


### 組態檔的說明

`Host` = 在終端機上所呼叫的主機名稱。`ssh fedora22` 會指示 `SSH` 使用設定區塊中標示為 `Host fedora22` 的值。注意︰這可以是符合您用途的任何標籤，並不代表任何伺服器的實際主機名稱。

`Hostname 102.160.203.241` = 所登入伺服器的 IP 位址或 DNS 名稱。這會用來路由傳送至伺服器，而且可以是與內部 IP 對應的外部 IP。

`User git` = 登入 Azure VM 時要使用的遠端使用者帳戶。

`PubKeyAuthentication yes` = 這會向 SSH 指出您想要使用 SSH 金鑰來登入。

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = 這會向 SSH 指出要向伺服器出示以供驗證登入的金鑰組。


## 使用 SSH 登入 Linux 而不提供密碼

現在您已擁有 SSH 金鑰組和設定好的 SSH 組態檔，因此您可以快速且安全地登入 Linux VM。第一次使用 SSH 金鑰登入伺服器時，命令會提示您輸入該金鑰檔案的複雜密碼。

```bash
ssh fedora22
```

### 命令的說明

在執行 `ssh fedora22` 後，SSH 會先從 `Host fedora22` 區塊中找出所有設定並加以載入，再從最後一個區塊 `Host *` 中載入所有其餘設定。

## 後續步驟

接下來是使用新的 SSH 公開金鑰建立 Azure Linux VM。相較於以預設登入方法密碼建立的 Azure VM，以 SSH 公開金鑰作為登入所建立的 Azure VM 比較安全。使用 SSH 金鑰作為登入的 Azure VM 預設會設定為停用密碼登入，避免在嘗試時遭到暴力中斷。

- [使用 Azure 範本建立安全的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [使用 Azure 入口網站建立安全的 Linux VM](virtual-machines-linux-quick-create-portal.md)
- [使用 Azure CLI 建立安全的 Linux VM](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0504_2016-->