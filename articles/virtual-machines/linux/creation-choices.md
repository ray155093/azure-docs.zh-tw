---
title: "在 Azure 中建立 Linux VM 的不同方式 | Microsoft Azure"
description: "了解在 Azure 上建立 Linux 虛擬機器的不同方式，包含每種方法的工具和教學課程連結。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7ff1317cdf6ccfe6b4e5035878fc4e096fcc0f9
ms.lasthandoff: 04/03/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>建立 Linux VM 的不同方式
您在 Azure 中可選擇使用您習慣的工具和工作流程來建立 Linux 虛擬機器 (VM)。 本文摘要說明這些差異以及建立 Linux VM 的範例，包括 Azure CLI 2.0。 您也可以檢視建立選項，包括 [Azure CLI 1.0](creation-choices-nodejs.md)。

[Azure CLI 2.0](/cli/azure/install-az-cli2) 可透過 npm 套件、散發版本提供的套件或 Docker 容器，在各個平台上提供使用。 安裝最適合環境的組建，並使用 [az login](/cli/azure/#login) 登入 Azure 帳戶

下列範例使用 Azure CLI 2.0。 如需以下命令的詳細資訊，請閱讀每篇文章。 有關 Linux 建立選項方面，您也可以找到使用 [Azure CLI 1.0](creation-choices-nodejs.md) 的範例。

* [使用 Azure CLI 2.0 來建立 Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 這個範例使用 [az group create](/cli/azure/group#create) 建立資源群組 `myResourceGroup`： 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * 這個範例使用 [az vm create](/cli/azure/vm#create)，以最新的 Debian 映像 (使用 Azure 受控磁碟) 和公用金鑰 `id_rsa.pub` 建立 VM `myVM`：

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * 如果您想要使用非受控磁碟，請新增 `--use-unmanaged-disks` 旗標到上述命令。 系統便會為您建立儲存體帳戶。 如需詳細資訊，請參閱 [Azure 受控磁碟概觀](../../storage/storage-managed-disks-overview.md)。

* [使用 Azure 範本建立受保護的 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 下列範例使用 [az group deployment create](/cli/azure/group/deployment#create)，以儲存 GitHub 上的範本建立 VM︰
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [使用 Azure CLI 建立完整的 Linux 環境](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 包含在可用性設定組中建立負載平衡器和多個 VM。

* [在 Linux VM 中新增磁碟](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 下列範例會使用 [az vm disk attach-new](/cli/azure/vm/disk#attach-new)，將 50 Gb 受控磁碟新增至名為 `myVM` 的現有 VM：
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Azure 入口網站
[Azure 入口網站](https://portal.azure.com) 可讓您快速建立 VM，因為您的系統不需安裝任何項目。 使用 Azure 入口網站來建立 VM：

* [使用 Azure 入口網站建立 Linux VM](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [使用 Azure 入口網站連接磁碟](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>作業系統和映像選項
建立 VM 時，根據您想要執行的作業系統來選擇映像。 Azure 與其合作夥伴提供許多映像，其中有些包括已預先安裝的應用程式和工具。 或者，上傳您自己的其中一個映像 (請參閱 [下一節](#use-your-own-image))。

### <a name="azure-images"></a>Azure 映像
使用 [az vm image](/cli/azure/vm/image) 命令，依發佈者、散發版本和組建來查看可用的映像。

列出可用的發佈者：

```azurecli
az vm image list-publishers --location WestUS
```

列出特定發佈者的可用產品 (提供項目)︰

```azurecli
az vm image list-offers --publisher Canonical --location WestUS
```

清單特定提供項目的可用 SKU (散發版本)︰

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

列出特定版本的所有可用映像︰

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

如需有關瀏覽和使用可用映像的更多範例，請參閱 [使用 Azure CLI 瀏覽和選取 Azure 虛擬機器映像](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

**az vm create** 命令有一些別名可用來快速存取更常見的散發版本及其最新版本。 使用別名通常比每次建立 VM 時指定發佈者、提供項目、SKU 和版本還要快速：

| Alias | 發佈者 | 提供項目 | SKU | 版本 |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |最新 |
| CoreOS |CoreOS |CoreOS |Stable |最新 |
| Debian |credativ |Debian |8 |最新 |
| openSUSE |SUSE |openSUSE |13.2 |最新 |
| RHEL |Redhat |RHEL |7.2 |最新 |
| SLES |SLES |SLES |12-SP1 |最新 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |最新 |

### <a name="use-your-own-image"></a>使用您自己的映像
如果您需要特定自訂項目，可以「擷取」  現有的 Azure VM，使用以該 VM 為基礎的映像。 也可以上傳在內部部署建立的映像。 如需有關支援的散發版本以及如何使用自己的映像的詳細資訊，請參閱下列文件：

* [Azure 背書的散發套件](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [非背書散發套件的資訊](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [如何擷取 Linux 虛擬機器以做為 Resource Manager 範本](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
  
  * 用來擷取現有 VM (使用非受控磁碟) 的快速入門 **az vm** 範例命令：
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>後續步驟
* 透過[入口網站](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [Azure Resource Manager 範本](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)建立 Linux VM。
* 在建立 Linux VM 後， [新增資料磁碟](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* [重設密碼或 SSH 金鑰及管理使用者](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

