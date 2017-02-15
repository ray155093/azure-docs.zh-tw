---
title: "使用 Azure CLI 調整 ACS 叢集 | Microsoft Docs"
description: "如何使用 Azure CLI 來調整 Azure Container Service 叢集。"
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
ms.date: 10/03/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e8df2e68b1b7018d76da89ba9ab332b6ea216fb


---
# <a name="scale-an-azure-container-service"></a>調整 Azure Container Service
您可以使用 Azure CLI 工具相應放大 Azure Container Service (ACS) 所具有的節點數目。 當您使用 Azure CLI 進行調整時，工具會將代表套用至容器之變更的新組態檔傳回給您。

## <a name="about-the-command"></a>關於命令
Azure CLI 必須處於 Azure Resource Manager 模式，讓您與 Azure Container 互動。 您可以呼叫 `azure config mode arm`，以切換至 Resource Manager 模式。 `acs` 命令具有名為 `scale` 的子命令，這個子命令會執行容器服務的所有調整作業。 您可以執行 `azure acs scale --help` 來取得 scale 命令中所使用之各種參數的說明，其輸出與下面類似：

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>使用命令進行調整
若要調整容器服務，您需要先知道**資源群組**和 **Azure Container Service (ACS) 名稱**，同時指定新的代理程式計數。 使用更少或更多的數量，即可分別相應減少或相應增加。

您可能想要先知道容器服務的目前代理程式計數，再進行調整。 使用 `azure acs show <resource group> <ACS name>` 命令傳回 ACS 組態。 請注意 <mark>Count</mark> 結果。

#### <a name="see-current-count"></a>請參閱目前計數
```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>調整為新的計數
因為它可能已經是清楚易懂，所以您可以呼叫 `azure acs scale` 並提供**資源群組**、**ACS 名稱**和**代理程式計數**來調整容器服務。 調整容器服務時，Azure CLI 會傳回 JSON 字串，這個字串代表容器服務的新組態，其中包括新的代理程式計數。

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>後續步驟
* [部署叢集](container-service-deployment.md)




<!--HONumber=Nov16_HO3-->


