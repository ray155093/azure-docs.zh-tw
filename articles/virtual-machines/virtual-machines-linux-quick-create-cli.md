---
title: "使用 Azure CLI 2.0 建立 Linux VM (預覽) | Microsoft Azure"
description: "使用 Azure CLI 2.0 建立 Linux VM (預覽)。"
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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 95b924257c64a115728c66956d5ea38eb8764a35
ms.openlocfilehash: 70592ac773aced0bfcec5c7418a6dc53555fab33


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>使用 Azure CLI 2.0 建立 Linux VM 預覽 (az.py)
本文示範如何使用 Azure CLI 2.0 (預覽) 中的 [az vm create](/cli/azure/vm#create) 命令，在 Azure 上快速部署 Linux 虛擬機器 (VM)。 

> [!NOTE] 
> Azure CLI 2.0 Preview 是我們的新一代多重平台 CLI。 [立即試用。](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)
>
> 我們的文件的其餘部分使用現有 Azure CLI。 若要使用現有的 Azure CLI 1.0 而不是 CLI 2.0 Preview 建立 VM，請參閱[使用 Azure CLI 建立 VM](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

若要建立 VM，您需要： 

* 一個 Azure 帳戶 ([取得免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/))
* 安裝 [Azure CLI v.2.0 (預覽)](/cli/azure/install-az-cli2)
* 以登入您的 Azure 帳戶 (類型 [az login](/cli/azure/#login))

您也可以使用 [Azure 入口網站](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)來快速部署 Linux VM。

以下範例示範如何部署 Debian VM，並附加您的安全殼層 (SSH) 金鑰 (您的引數可能會不同)；如果您想要不同的映象，您[可以搜尋一個映像](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。

## <a name="create-a-resource-group"></a>建立資源群組

首先，請輸入 [az group create](/cli/azure/group#create) 以建立資源群組，其中包含所有已部署的資源︰

```azurecli
az group create -n myResourceGroup -l westus
```

輸出看起來如下所示 (您可以視需要選擇不同的 `--output` 選項)：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>使用最新的 Debian 映像建立您的 VM

現在您可以建立您的 VM 和其環境。 請務必將 `----public-ip-address-dns-name` 值取代為唯一的值，以下的值可能已被採用。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


輸出顯示如下。 請注意 `publicIpAddress` 或 `fqdn` 值到 **ssh** 至您的 VM。


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

使用輸出中所列的公用 IP 位址登入您的 VM。 您也可以使用其中列出的完整網域名稱 (FQDN)。

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

您應該會看到類似下列的輸出，取決於您所選擇的散發︰

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>後續步驟
`az vm create` 命令是快速部署 VM 的方法，讓您可以登入 bash 殼層並開始使用。 不過，使用 `az vm create` 不會提供您進一步的控制權，也無法讓您能夠建立更複雜的環境。  若要部署為您的基礎結構自訂的 Linux VM，您可以依照下列任一篇文章執行：

* [使用 Azure Resource Manager 範本和 Azure CLI 部署和管理虛擬機器](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令，建立自訂的 Linux VM 環境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用範本在 Azure 上建立 SSH 保護的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

您也可以[搭配使用`docker-machine` Azure 驅動程式與各種命令以快速建立當作 Docker 主機的 Linux VM](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，如果您使用 Java，則嘗試 [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) 方法。




<!--HONumber=Jan17_HO1-->


