---
title: "使用 Azure 範本建立 Linux VM | Microsoft Docs"
description: "使用 Azure Resource Manager 範本在 Azure 上建立 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2016
ms.author: v-livech
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c8b4c8cd7e6e6e07efbaae7ac024160e1e0a3568
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-create-a-linux-vm-using-an-azure-resourec-manager-template"></a>如何使用 Azure Resource Manager 範本建立 Linux VM
本文示範如何使用 Azure 範本，在 Azure 上快速部署 Linux 虛擬機器。  本文需要：

* 一個 Azure 帳戶 ([取得免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/))。
* 使用 `azure login` 登入的 [Azure CLI](../../cli-install-nodejs.md)。
* Azure CLI *必須處於* Azure Resource Manager 模式 `azure config mode arm`。

您也可以使用 [Azure 入口網站](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)來快速部署 Linux VM 範本。

## <a name="quick-command-summary"></a>快速命令摘要
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說
範本可讓您在 Azure 上使用要在啟動期間自訂的設定 (如使用者名稱和主機名稱等設定) 建立 VM。 在本文中，我們推出的 Azure 範本會利用 Ubuntu VM 以及連接埠 22 對 SSH 開放的網路安全性群組 (NSG)。

Azure Resource Manager 範本是 JSON 檔案，可用於進行簡單的一次性工作 (如本文中的啟動 Ubuntu VM)。  Azure 範本也可用來建構整個環境的複雜 Azure 組態，例如測試、開發或生產部署堆疊。

## <a name="create-the-linux-vm"></a>建立 Linux VM
下列程式碼範例示範如何呼叫 `azure group create` ，以使用 [此 Azure Resource Manager 範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)同時建立資源群組和部署受 SSH 保護的 Linux VM。 請記住，在您的範例中您必須使用環境中唯一的名稱。 此範例則以 `myResourceGroup` 做為資源群組名稱，並以 `myVM` 做為 VM 名稱。

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

輸出應該看起來像下列的輸出區塊：

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

該範例使用 `--template-uri` 參數部署 VM。  您也可以使用 `--template-file` 參數並以範本檔案路徑做為引數，在本機下載或建立範本並傳遞範本。 Azure CLI 會提示您輸入範本所需的參數。

## <a name="next-steps"></a>後續步驟
請搜尋 [範本庫](https://azure.microsoft.com/documentation/templates/) 以探索接下來要部署哪些應用程式架構。


