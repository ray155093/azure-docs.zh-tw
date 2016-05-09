<properties
    pageTitle="在建立期間使用 cloud-init 自訂 Linux VM | Microsoft Azure"
    description="在建立期間使用 cloud-init 自訂 Linux VM。"
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
    ms.topic="article"
    ms.date="04/22/2016"
    ms.author="v-livech"
/>

# 在建立期間使用 cloud-init 自訂 Linux VM

在本文中，我們會建立 cloud-init 指令碼來設定主機名稱、更新已安裝的封裝及管理使用者帳戶。接著使用 [Azure CLI](../xplat-cli-install.md)，在 Linux VM 的建立期間啟動這些 cloud-init 指令碼。

## 必要條件

必要條件︰[Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)、[SSH 公開金鑰與私密金鑰](virtual-machines-linux-mac-create-ssh-keys.md)、啟動 Linux VM 的 Azure 資源群組，且已安裝 Azure CLI 同時使用 `azure config mode arm` 切換至 ARM 模式。

## 簡介

啟動新的 Linux VM 時，您會取得標準的 Linux VM，沒有任何自訂也不會事事符合您的需求。[Cloud-init](https://cloudinit.readthedocs.org) 第一次啟動時，會是在 Linux VM 中插入指令碼或組態設定的標準方法。

Azure 有三種不同的方法可在 Linux VM 啟動時進行變更。

- 您可以使用 cloud-init 插入指令碼。
- 您可以使用 Azure [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) 插入指令碼。
- 您可以在 Azure 範本中指定自訂設定，並且用它來啟動及自訂您的 Linux VM，這個範本中包括 cloud-init 支援以及 CustomScript VM 擴充功能和許多其他功能。

若要隨時插入指令碼，您可以︰

- 使用 SSH 直接執行命令，您可以命令方式或在 Azure 範本中使用 Azure [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md)，或者使用 Ansible、Salt、Chef 及 Puppet 等通用組態管理工具，這些工具在 VM 開機完成後會在 SSH 上工作。

注意︰雖然 [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) 和使用 SSH 時一樣只將指令碼當成根執行，但是使用 VM 擴充功能可讓 Azure 提供的幾項功能發揮功用，視案例而定。

## 快速命令

```bash
# Create a hostname cloud-init script
#cloud-config
hostname: exampleServerName

# Create an update Linux on first boot cloud-init script for Debian Family
#cloud-config
apt_upgrade: true

# Create an add a user cloud-init script
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa
AAAAB3NzaC1yc2EAAAADAQABAAABAQDf0q4PyG0doiBQYV7OlOxbRjle<snip />== exampleuser@slackwarelaptop

```

## 詳細的逐步解說

### 將 cloud-init 指令碼加入使用 Azure CLI 建立 VM 的作業中

在 Azure 中建立 VM 時，若要啟動 cloud-init 指令碼，請使用 Azure CLI `--custom-data` 切換來指定 cloud-init 檔案。

注意︰本文討論內容雖然是使用 `--custom-data` switch 處理 cloud-init 檔案，但您也可以使用這個切換來傳送任意程式碼或檔案。如果 Linux VM 已經知道要用這類檔案做什麼，它們就會自動執行。

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_init_script.txt
```

### 建立 cloud-init 指令碼設定 Linux VM 的主機名稱

對任何 Linux VM 而言，其中一個最簡單且最重要的設定就是主機名稱。使用 cloud-init 和這個指令碼就可以輕鬆地設定這個項目。

#### 範例 cloud-init 指令碼名為 `cloud_config_hostname.txt`。

``` bash
#cloud-config
hostname: exampleServerName
```

在 VM 初始啟動期間，這個 cloud-init 指令碼會將主機名稱設定為 `exampleServerName`。

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_hostname.txt
```

登入並驗證新 VM 的主機名稱。

```bash
bill@slackware$ ssh exampleVM
bill@ubuntu$ hostname
bill@ubuntu$ exampleServerName
```

### 建立 cloud-init 指令碼以更新 Linux

基於安全性，您希望您的 Ubuntu VM 能在第一次開機時進行更新。我們可以使用 cloud-init 和下列指令碼執行這個作業，視您使用的 Linux 散發套件而定。

#### 適用於 Debian 系列的範例 cloud-init 指令碼 `cloud_config_apt_upgrade.txt`

```bash
#cloud-config
apt_upgrade: true
```

新的 Linux VM 啟動之後，它會透過 `apt-get` 立即更新所有已安裝的封裝。

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_apt_upgrade.txt
```

登入並驗證所有封裝是否皆已更新。

```bash
bill@slackware$ ssh exampleVM
bill@ubuntu$ sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### 建立 cloud-init 指令碼將使用者加入 Linux 中

任何新 Linux VM 的首批工作之一，就是將您自己加入為使用者或避免使用 `root`。一旦將 SSH 公開金鑰加入該使用者的 `~/.ssh/authorized_keys` 檔案中，進行密碼較少且安全的 SSH 登入，對安全性和使用性而言都是絕佳的做法。

#### 適用於 Debian 系列的範例 cloud-init 指令碼 `cloud_config_add_users.txt`

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa
AAAAB3NzaC1yc2EAAAADAQABAAABAQDf0q4PyG0doiBQYV7OlOxbRjle<snip />== exampleuser@slackwarelaptop
```

新的 Linux VM 開機後，會建立新的使用者並將它加入 sudo 群組中。

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_add_users.txt
```

登入並驗證新建立的使用者。

```bash
bill@slackware$ cat /etc/group
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

<!---HONumber=AcomDC_0427_2016-->