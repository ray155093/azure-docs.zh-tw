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
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 756711abb014829971af126c5cb60c12e79c920e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>建立 Linux VM 的不同方式
您在 Azure 中可選擇使用您習慣的工具和工作流程來建立 Linux 虛擬機器 (VM)。 本文摘要說明這些差異以及建立 Linux VM 的範例，包括 Azure CLI 2.0。 您也可以檢視建立選項，包括 [Azure CLI 1.0](creation-choices-nodejs.md)。

[Azure CLI 2.0](/cli/azure/install-az-cli2) 可透過 npm 套件、散發版本提供的套件或 Docker 容器，在各個平台上提供使用。 安裝最適合環境的組建，並使用 [az login](/cli/azure/#login) 登入 Azure 帳戶

* [使用 Azure CLI 2.0 來建立 Linux VM](quick-create-cli.md)
  
  * 使用名為 myResourceGroup 的 [az group create](/cli/azure/group#create) 來建立資源群組： 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * 使用最新的 UbuntuLTS 映像，透過名為 myVM 的 [az vm create](/cli/azure/vm#create) 來建立 VM，如果 ~/.ssh 中還沒有 SSH 金鑰，則請產生 SSH 金鑰：

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [使用 Azure 範本建立 Linux VM](create-ssh-secured-vm-from-template.md)
  
  * 下列範例使用 [az group deployment create](/cli/azure/group/deployment#create)，從儲存在 GitHub 上的範本建立 VM︰
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [建立 Linux VM，並使用 cloud-init 進行自訂](tutorial-automate-vm-deployment.md)

* [在多個 Linux VM 上建立負載平衡和高可用性的應用程式](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Azure 入口網站
[Azure 入口網站](https://portal.azure.com) 可讓您快速建立 VM，因為您的系統不需安裝任何項目。 使用 Azure 入口網站來建立 VM：

* [使用 Azure 入口網站建立 Linux VM](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>作業系統和映像選項
建立 VM 時，根據您想要執行的作業系統來選擇映像。 Azure 與其合作夥伴提供許多映像，其中有些包括已預先安裝的應用程式和工具。 或者，上傳您自己的其中一個映像 (請參閱 [下一節](#use-your-own-image))。

### <a name="azure-images"></a>Azure 映像
使用 [az vm image](/cli/azure/vm/image) 命令，依發佈者、散發版本和組建來查看可用的映像。

列出可用的發佈者：

```azurecli
az vm image list-publishers --location eastus
```

列出特定發佈者的可用產品 (提供項目)︰

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

清單特定提供項目的可用 SKU (散發版本)︰

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

列出特定版本的所有可用映像︰

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

如需有關瀏覽和使用可用映像的更多範例，請參閱 [使用 Azure CLI 瀏覽和選取 Azure 虛擬機器映像](cli-ps-findimage.md)。

[az vm create](/cli/azure/vm#create) 命令有一些別名可用來快速存取更常見的散發版本及其最新版本。 使用別名通常比每次建立 VM 時指定發佈者、提供項目、SKU 和版本還要快速：

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
如果您需要特定的自訂，可以擷取該 VM，根據現有的 Azure VM 使用映像。 也可以上傳在內部部署建立的映像。 如需有關支援的散發版本以及如何使用自己的映像的詳細資訊，請參閱下列文件：

* [Azure 背書的散發套件](endorsed-distros.md)
* [非背書散發套件的資訊](create-upload-generic.md)
* [如何從現有的 Azure VM 建立映像](tutorial-custom-images.md)。
  
  * 可從現有 Azure VM 建立映像的快速入門範例命令︰
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>後續步驟
* 透過 [CLI](quick-create-cli.md)、[入口網站](quick-create-portal.md)或使用 [Azure Resource Manager 範本](../windows/cli-deploy-templates.md)建立 Linux VM。
* 建立 Linux VM 之後，[深入了解 Azure 磁碟與儲存體](tutorial-manage-disks.md)。
* [重設密碼或 SSH 金鑰及管理使用者](using-vmaccess-extension.md)的快速步驟。

