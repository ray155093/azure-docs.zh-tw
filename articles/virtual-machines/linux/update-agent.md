---
title: "從 GitHub 更新 Azure Linux 代理程式 | Microsoft Docs"
description: "了解如何將 Azure 中 Linux VM 的 Azure Linux 代理程式更新成 GitHub 中的最新版本"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2015
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c9cea69fc6462f69c8627219aca11272bbdc493f
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>如何在 VM 上從 Github 更新至最新版的 Azure Linux 代理程式
若要更新 Azure 中 Linux VM 的 [Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent) ，您必須準備：

1. 在 Azure 中執行的 Linux VM。
2. 對該 Linux VM 的 SSH 連線。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [!NOTE]
> 如果您會從 Windows 電腦執行這項工作，可以使用 PuTTY 來 SSH 到 Linux 機器。 如需詳細資訊，請參閱[如何登入執行 Linux 的虛擬機器](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
> 
> 

支援 Azure 的 Linux 散發版本有將 Azure Linux 代理程式套件放在其儲存機制，因此請先從這個 Distro 儲存機制檢查並安裝最新版本。  

對於 Ubuntu，只要輸入：

```bash
sudo apt-get install walinuxagent
```

在 CentOS 上請輸入：

```bash
sudo yum install waagent
```

針對 Oracle Linux，請確定已啟用 `Addons` 儲存機制。 視您的情況，編輯檔案 `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) 或 `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux)，將此檔案中 **[ol6_addons]** 或 **[ol7_addons]** 底下的 `enabled=0` 一行變更為 `enabled=1`。

接下來，若要安裝最新版的 Azure Linux 代理程式，請輸入：

```bash
sudo yum install WALinuxAgent
```

如果找不到附加元件儲存機制，只需根據您的 Oracle Linux 版本，將這幾行加入 .repo 檔案結尾處︰

Oracle Linux 6 虛擬機器︰

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Oracle Linux 7 虛擬機器︰

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

然後輸入：

```bash
sudo yum update WALinuxAgent
```

通常您要做就是這幾個步驟了，但如果因為其他原因造成您必須直接從 https://github.com 安裝，請執行以下步驟。

## <a name="install-wget"></a>安裝 wget
使用 SSH 登入 VM。

在命令列輸入 `#sudo yum install wget` 來安裝 wget (有一些散發版本基本上是不會安裝的，例如 Redhat、CentOS、Oracle Linux 6.4 和 6.5 版)。

## <a name="download-the-latest-version"></a>下載最新版本
在網頁中開啟 [Github 中的 Azure Linux 代理程式版本](https://github.com/Azure/WALinuxAgent/releases) ，然後查明最新的版本號碼。 (您可以輸入 `#waagent --version`，即可找到目前的版本 )。

### <a name="for-version-20x-type"></a>針對 2.0.x 版本，請輸入：

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent
```

下列一行使用 2.0.14 版做為範例：

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
```

### <a name="for-version-21x-or-later-type"></a>針對 2.1.x 版本或較新版本，請輸入：
```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
unzip WALinuxAgent-[version].zip
cd WALinuxAgent-[version]
```

下面這一行會使用 2.1.0 版本做為範例：

```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
unzip WALinuxAgent-2.1.0.zip  
cd WALinuxAgent-2.1.0
```

## <a name="install-the-azure-linux-agent"></a>安裝 Azure Linux 代理程式
### <a name="for-version-20x-use"></a>針對 2.0.x 版本，請使用：
讓 waagent 變成可執行檔：

```bash
chmod +x waagent
```

將新的可執行檔複製到 /usr/sbin/。

針對大部分的 Linux，請使用：

```bash
sudo cp waagent /usr/sbin
```

針對 CoreOS，請使用：

```bash
sudo cp waagent /usr/share/oem/bin/
```

如果是新安裝 Azure Linux 代理程式，請執行：

```bash
sudo /usr/sbin/waagent -install -verbose
```

### <a name="for-version-21x-use"></a>針對 2.1.x 版本，請使用：
您可能需要先安裝套件 `setuptools` ，請參閱 [這裡](https://pypi.python.org/pypi/setuptools)。 然後，執行：

```bash
sudo python setup.py install
```

## <a name="restart-the-waagent-service"></a>重新啟動 waagent 服務
針對大多數的 Linux 散發版本：

```bash
sudo service waagent restart
```

針對 Ubuntu，使用：

```bash
sudo service walinuxagent restart
```

針對 CoreOS，請使用：

```bash
sudo systemctl restart waagent
```

## <a name="confirm-the-azure-linux-agent-version"></a>確認 Azure Linux 代理程式版本
    
```bash
waagent -version
```

針對 CoreOS，上述命令可能無效。

您會看到 Azure Linux 代理程式版本已更新為新的版本。

如需有關「Azure Linux 代理程式」的詳細資訊，請參閱 [Azure Linux 代理程式讀我檔案](https://github.com/Azure/WALinuxAgent)。


