---
title: "使用 Azure CLI 2.0 來建立 Linux VM | Microsoft Azure"
description: "使用 Azure CLI 2.0 來建立 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: cc51b04c31c02aabf25c9efb1e9cd975077811a4
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-linux-vm-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 來建立 Linux VM
本文說明如何使用 Azure CLI 2.0，在同時使用受控磁碟及原生儲存體帳戶中磁碟的情況下，以 [az vm create](/cli/azure/vm#create) 命令在 Azure 上快速部署 Linux 虛擬機器 (VM)。 您也可以使用 [Azure CLI 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來執行這些步驟。

若要建立 VM，您需要： 

* 一個 Azure 帳戶 ([取得免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/))
* 安裝 [Azure CLI 2.0](/cli/azure/install-az-cli2)
* 登入您的 Azure 帳戶 (輸入 [az login](/cli/azure/#login))

(您也可以使用 [Azure 入口網站](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)部署 Linux VM。)

以下範例示範如何部署 Debian VM 並使用安全殼層 (SSH) 金鑰進行連接。 您的引數可能不同；如果您想要不同的映像，您[可搜尋一個](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="using-managed-disks"></a>使用受控磁碟

若要使用 Azure 受控磁碟，您必須使用支援它們的區域。 首先，請輸入 [az group create](/cli/azure/group#create) 以建立資源群組，其中包含所有已部署的資源︰

```azurecli
 az group create -n myResourceGroup -l westus
```

輸出看起來如下所示 (如果您想要查看不同的格式﹐可以指定不同的 `--output` 選項)：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>建立您的 VM 
現在您可以建立您的 VM 和其環境。 請務必將 `--public-ip-address-dns-name` 值取代為唯一的值，以下的值可能已被採用。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


輸出顯示如下。 請注意 `publicIpAddress` 或 `fqdn` 值到 **ssh** 至您的 VM。


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

使用輸出中所列的公用 IP 位址或完整網域名稱 (FQDN)，登入您的 VM。

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

您應該會看到類似下列的輸出，取決於您所選擇的散發︰

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

請參閱[後續步驟](#next-steps)﹐以了解您可以透過使用受控磁碟的新 VM 執行的其他作業。

## <a name="using-unmanaged-disks"></a>使用非受控磁碟 

使用未受管理儲存體磁碟的 VM 會具有未受管理的儲存體帳戶。 首先，請輸入 [az group create](/cli/azure/group#create) 來建立資源群組，以包含所有已部署的資源︰

```azurecli
az group create --name nativedisks --location westus
```

輸出看起來如下所示 (您可以視需要選擇不同的 `--output` 選項)：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>建立您的 VM 

現在您可以建立您的 VM 和其環境。 使用 `--use-unmanaged-disk` 旗標來建立具有未受管理磁碟的 VM。 這也會建立一個未受管理的儲存體帳戶。 請務必將 `--public-ip-address-dns-name` 值取代為唯一的值，以下的值可能已被採用。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-unmanaged-disk
```

輸出顯示如下。 請注意 `publicIpAddress` 或 `fqdn` 值到 **ssh** 至您的 VM。

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

使用以上輸出中所列的公用 IP 位址、完整網域名稱 (FQDN) 或兩者，登入您的 VM。

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

您應該會看到類似下列的輸出，取決於您所選擇的散發︰

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>後續步驟
`az vm create` 命令是快速部署 VM 的方法，讓您可以登入 bash 殼層並開始使用。 不過，使用 `az vm create` 不會提供您進一步的控制權，也無法讓您能夠建立更複雜的環境。  若要部署為您的基礎結構自訂的 Linux VM，您可以依照下列任一篇文章執行：

* [使用 Azure Resource Manager 範本和 Azure CLI 部署和管理虛擬機器](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令，建立自訂的 Linux VM 環境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用範本在 Azure 上建立 SSH 保護的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

您也可以[搭配使用`docker-machine` Azure 驅動程式與各種命令以快速建立當作 Docker 主機的 Linux VM](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，如果您使用 Java，則嘗試 [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) 方法。


