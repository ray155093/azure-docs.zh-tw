<properties
   pageTitle="使用 CLI 在 Azure 上建立 Linux VM | Microsoft Azure"
   description="使用 CLI 在 Azure 上建立 Linux VM。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/03/2016"
   ms.author="v-livech"/>


# 使用 CLI 在 Azure 上建立 Linux VM

本文示範如何使用 Azure CLI 的 `azure vm quick-create` 命令，在 Azure 上快速部署 Linux 虛擬機器。`quick-create` 命令會部署周圍具有基本基礎結構的 VM，可讓您非常快速地建立原型或測試概念 (您可以將它視為達到 Linux bash 殼層的最快速方式)。本文需要 Azure 帳戶 ([取得免費試用](https://azure.microsoft.com/pricing/free-trial/))，及已登入 (`azure login`) 和處於資源管理員模式的 [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`)。您也可以使用 [Azure 入口網站](virtual-machines-linux-quick-create-portal.md)快速部署 Linux VM。

## 快速命令摘要

一個可部署 CoreOS VM 並附加 SSH 金鑰的命令：

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## 部署 Linux VM

使用與上方相同的命令，下列會顯示每個提示以及您應該會看到的輸出，但使用 RHEL 別名來建立 RedHat Enteprise Linux 7.2 VM。

## 使用 ImageURN 別名

Azure CLI `quick-create` 命令有對應至最常見 OS 散發套件的別名。下表列出散發套件別名 (從 Azure CLI 0.10 版起)。所有使用 `quick-create` 的部署都會預設為 SSD 儲存體支援的 VM，以提供高效能體驗。

| Alias | 發佈者 | 提供項目 | SKU | 版本 |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7\.2 | 最新 |
| CoreOS | CoreOS | CoreOS | Stable | 最新 |
| Debian | credativ | Debian | 8 | 最新 |
| openSUSE | SUSE | openSUSE | 13\.2 | 最新 |
| RHEL | Redhat | RHEL | 7\.2 | 最新 |
| SLES | SLES | SLES | 12-SP1 | 最新 |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | 最新 |



針對 **ImageURN** 選項 (`-Q`)，我們將使用 `RHEL` 來部署 RedHat Enterprise Linux 7.2 VM。(這 7 個別名代表 Azure 上可用 OS 的一小部分；藉由[搜尋映像](virtual-machines-linux-cli-ps-findimage.md)在 Marketplace 中尋找更多映像，您也可以[上傳自己的自訂映像](virtual-machines-linux-create-upload-generic.md))。

在下列命令逐步解說中，請以您自己環境的值取代提示，我們使用「範例」值。

遵循提示並輸入自己的名稱

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

輸出應該看起來像下列的輸出區塊。

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

您現在可以在預設 SSH 連接埠 22 和上面輸出中所列的完整網域名稱 (FQDN) 上，使用 SSH 登入您的 VM。(您也可以使用所列的 IP 位址)。

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
登入程序應該類似下面這樣：

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## 後續步驟

`azure vm quick-create` 可快速部署 VM，因此您可以登入 bash 殼層並開始運作。使用 `vm quick-create` 不會提供複雜環境的其他優點。若要部署為您的基礎結構自訂的 Linux VM，您可以依照以下任何文章執行。

- [使用 Azure Resource Manager 範本來建立特定的部署](virtual-machines-linux-cli-deploy-templates.md)
- [直接使用 Azure CLI 命令來建立您自己的 Linux VM 自訂環境](virtual-machines-linux-create-cli-complete.md)。
- [使用範本在 Azure 上建立受 SSH 保護的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

這些文章可幫您開始建置 Azure 基礎結構，以及多種專屬和開放原始碼基礎結構部署、組態和協調流程工具。

<!---HONumber=AcomDC_0518_2016-->