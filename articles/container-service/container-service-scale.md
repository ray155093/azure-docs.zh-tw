---
title: "調整 Azure Container Service 叢集 | Microsoft Docs"
description: "如何使用 Azure CLI 或 Azure 入口網站來調整 Azure Container Service 叢集。"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Docker、容器、微服務、Mesos、Azure"
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: 773eba80715cb990b23ecec548a18c112ba73a42
ms.lasthandoff: 02/22/2017


---
# <a name="scale-an-azure-container-service-cluster"></a>調整 Azure Container Service 叢集
在[部署 Azure Container Service 叢集](container-service-deployment.md)之後，您可能需要變更代理程式節點的數目。 例如，您可能需要更多代理程式，以便可以執行更多的容器應用程式或執行個體。 

您可以使用 Azure 入口網站或 Azure CLI 2.0 來變更 DC/OS、Docker Swarm 或 Kubernetes 叢集中的代理程式節點數目。 Azure CLI 2.0 是適用於 Resource Manager 部署模型的[新一代 CLI](/cli/azure/old-and-new-clis)。

## <a name="scale-with-the-azure-portal"></a>使用 Azure 入口網站進行調整

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽**容器服務**，然後按一下您想要修改的容器服務。
2. 在 [容器服務] 刀鋒視窗中，按一下 [代理程式]。
3. 在 [VM 計數] 中，輸入所需的代理程式節點數目。

    ![在入口網站中調整集區](./media/container-service-scale/container-service-scale-portal.png)

4. 若要儲存組態時，請按一下 [儲存]。



## <a name="scale-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 進行調整

請確定您[已安裝](/cli/azure/install-az-cli2)最新的 Azure CLI 2.0 並登入 Azure 帳戶 (`az login`)。


### <a name="see-the-current-agent-count"></a>查看目前的代理程式計數
若要查看叢集中目前擁有的代理程式數目，請執行 `az acs show` 命令。 這會顯示叢集組態。 例如，下列命令會顯示資源群組 `myResourceGroup` 中名為 `containerservice-myACSName` 之容器服務的組態：

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

此命令會在 `AgentPoolProfiles` 底下的 `Count` 值中傳回代理程式數目。


### <a name="use-the-az-acs-scale-command"></a>使用 az acs scale 命令
若要變更代理程式節點數目，請執行 `az acs scale` 命令，並提供**資源群組**、**容器服務名稱**及所需的**新代理程式計數**。 藉由使用較少或更高的數字，即可分別相應減少或相應增加。

例如，若要將先前叢集中的代理程式數目變更為 10，請輸入下列命令︰

```azurecli
azure acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

Azure CLI 2.0 會傳回 JSON 字串，這個字串代表容器服務的新組態，其中包括新的代理程式計數。

如需更多的命令選項，請執行 `az acs scale --help`。


## <a name="scaling-considerations"></a>調整考量


* 代理程式節點的數目必須介於 1 到 100 (含) 之間。 

* 核心配額可以限制叢集中的代理程式節點數目。

* 代理程式節點調整作業會套用至包含代理程式集區的 Azure 虛擬機器擴展集。 在 DC/OS 叢集中，只有私人集區中的代理程式節點會受到本文所示作業的調整。

* 您可以根據您在叢集中部署的 Orchestrator，個別地調整在叢集上執行之容器的執行個體數目。 例如，在 DC/OS 叢集中，使用 [Marathon UI](container-service-mesos-marathon-ui.md) 來變更容器應用程式的執行個體數目。

* 目前並不支援在容器服務叢集中自動調整代理程式節點。





## <a name="next-steps"></a>後續步驟
* 請參閱[更多範例](container-service-create-acs-cluster-cli.md)，以了解如何搭配使用 Azure CLI 2.0 命令與 Azure Container Service。
* 深入了解 Azure Container Service 中的 [DC/OS 代理程式集區](container-service-dcos-agents.md)。


