---
title: "使用 Azure CLI 1.0 建立 Linux VM 的不同方式 | Microsoft Docs"
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
ms.topic: 
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 756141fda3148726af0030a010465ee14e8ce6b6
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# 在 Azure 中建立 Linux 虛擬機器的不同方式
<a id="different-ways-to-create-a-linux-virtual-machine-in-azure" class="xliff"></a>
您在 Azure 中可選擇使用您習慣的工具和工作流程來建立 Linux 虛擬機器 (VM)。 本文摘要說明這些差異以及建立 Linux VM 的範例。

## Azure CLI
<a id="azure-cli" class="xliff"></a>
您可以在 Azure 中使用下列其中一個 CLI 版本建立 VM︰

- Azure CLI 1.0 - 適用於傳統和資源管理部署模型 (本文) 的 CLI
- [Azure CLI 2.0](../windows/creation-choices.md) - 適用於資源管理部署模型的新一代 CLI

Azure CLI 1.0 可透過 npm 套件、散發版本提供的套件或 Docker 容器，跨平台適用。 您可以深入了解 [如何安裝和設定 Azure CLI](../../cli-install-nodejs.md)。 下列教學課程提供有關使用 Azure CLI 1.0 的範例。 如需以下 CLI 快速入門命令的詳細資訊，請閱讀每篇文章：

* [從 Azure CLI 建立用於開發和測試的 Linux VM](quick-create-cli-nodejs.md)
  
  * 下列範例使用名為 azure_id_rsa.pub 的公開金鑰建立 CoreOS VM：
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [使用 Azure 範本建立受保護的 Linux VM](create-ssh-secured-vm-from-template.md)
  
  * 下列範例使用 GitHub 上儲存的範本來建立 VM：
    
    ```azurecli
    azure group create --name myResourceGroup --location eastus 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [使用 Azure CLI 建立完整的 Linux 環境](create-cli-complete-nodejs.md)
  
  * 包含在可用性設定組中建立負載平衡器和多個 VM。
* [在 Linux VM 中新增磁碟](add-disk.md)
  
  * 下列範例會將 5 Gb 磁碟新增至名為 myVM 的現有 VM：
    
    ```azurecli
    azure vm disk attach-new \
        --resource-group myResourceGroup \
        --vm-name myVM \
        --size-in-GB 5
    ```

## Azure 入口網站
<a id="azure-portal" class="xliff"></a>
[Azure 入口網站](https://portal.azure.com) 可讓您快速建立 VM，因為您的系統不需安裝任何項目。 使用 Azure 入口網站來建立 VM：

* [使用 Azure 入口網站建立 Linux VM](quick-create-portal.md) 

## 作業系統和映像選項
<a id="operating-system-and-image-choices" class="xliff"></a>
建立 VM 時，根據您想要執行的作業系統來選擇映像。 Azure 與其合作夥伴提供許多映像，其中有些包括已預先安裝的應用程式和工具。 或者，上傳您自己的其中一個映像 (請參閱 [下一節](#use-your-own-image))。

### Azure 映像
<a id="azure-images" class="xliff"></a>
使用 `azure vm image` CLI 命令，查看可用的發佈者、散發版本和組建。

列出可用的發佈者，如下所示︰

```azurecli
azure vm image list-publishers --location eastus
```

列出特定發佈者的可用產品 (提供項目)，如下所示︰

```azurecli
azure vm image list-offers --location eastus --publisher Canonical
```

列出特定提供項目的可用 SKU (散發版本)，如下所示︰

```azurecli
azure vm image list-skus --location eastus --publisher Canonical --offer UbuntuServer
```

列出特定版本的所有可用映像，如下所示︰

```azurecli
azure vm image list --location eastus --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

如需有關瀏覽和使用可用映像的更多範例，請參閱 [使用 Azure CLI 瀏覽和選取 Azure 虛擬機器映像](cli-ps-findimage.md#use-azure-cli-10)。

`azure vm quick-create` 和 `azure vm create` 命令有一些別名，您可以用來快速存取更多常見的散發版本及其最新版本。 使用別名通常比每次建立 VM 時指定發佈者、提供項目、SKU 和版本還要快速：

| Alias | 發佈者 | 提供項目 | SKU | 版本 |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |最新 |
| CoreOS |CoreOS |CoreOS |Stable |最新 |
| Debian |credativ |Debian |8 |最新 |
| openSUSE |SUSE |openSUSE |13.2 |最新 |
| RHEL |Redhat |RHEL |7.2 |最新 |
| SLES |SLES |SLES |12-SP1 |最新 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |最新 |

### 使用您自己的映像
<a id="use-your-own-image" class="xliff"></a>
如果您需要特定自訂項目，可以「擷取」  現有的 Azure VM，使用以該 VM 為基礎的映像。 也可以上傳在內部部署建立的映像。 如需有關支援的散發版本以及如何使用自己的映像的詳細資訊，請參閱下列文件：

* [Azure 背書的散發套件](endorsed-distros.md)
* [非背書散發套件的資訊](create-upload-generic.md)
* [上傳自訂磁碟映像並從這個映像建立 Linux VM](upload-vhd.md)
* [如何擷取 Linux 虛擬機器以做為 Resource Manager 範本](capture-image.md)。
  
  * 用來擷取現有 VM 的快速入門範例命令：
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## 後續步驟
<a id="next-steps" class="xliff"></a>
* 透過[入口網站](quick-create-portal.md)、[CLI](quick-create-cli.md) 或 [Azure Resource Manager 範本](../windows/cli-deploy-templates.md)建立 Linux VM。
* 在建立 Linux VM 後， [新增資料磁碟](add-disk.md)。
* [重設密碼或 SSH 金鑰及管理使用者](using-vmaccess-extension.md)


