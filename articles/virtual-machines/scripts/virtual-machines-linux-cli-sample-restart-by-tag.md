---
title: "Azure CLI 指令碼範例 - 重新啟動 VM | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 依標記和識別碼重新啟動 VM"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b292a02770fa74812e74fa38f870e47ed7473b63
ms.lasthandoff: 03/04/2017

---

# <a name="restart-vms-by-tag"></a>依標記重新啟動 VM

此範例會使用指定的標記在多個資源群組中建立虛擬機器。
建立這些虛擬機器時，會使用 `--no-wait` 以平行方式建立，然後等候它們集體完成。

建立這些虛擬機器之後，會使用兩個不同的查詢機制來重新啟動它們。

第一個機制會使用在等候其非同步建立作業時所用的相同查詢來重新啟動 VM。
```bash
az vm restart --ids $(az vm list --query "join(' ', ${GROUP_QUERY}] | [].id)" \
    -o tsv) $1>/dev/null
```

第二個機制會使用一般資源列表和查詢來依標記截取其識別碼。
```bash
az vm restart --ids $(az resource list --tag ${TAG} \
    --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv) $1>/dev/null
```

這個範例適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart-by-tag.sh "依標記重新啟動 VM")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、VM 和所有相關資源。

```azurecli
az group delete -n GROUP1 --no-wait --yes && \ 
az group delete -n GROUP2 --no-wait --yes && \
az group delete -n GROUP3 --no-wait --yes
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、虛擬機器、可用性設定組、負載平衡器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | 建立虛擬機器。  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | 與 `--query` 搭配使用以確保在重新啟動 VM 之前先佈建 VM。 |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#list) | 重新啟動 VM。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Linux VM 文件](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 CLI 指令碼範例。

