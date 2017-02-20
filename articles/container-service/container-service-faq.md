---
title: "Azure Container Service - 常見問題集 | Microsoft Docs"
description: "解答有關 Azure Container Service 的常見問題，Azure Container Service 服務可簡化用以執行 Docker 容器應用程式之虛擬機器叢集的建立、設定和管理作業。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker、容器、微服務、Mesos、Azure、Kubernetes"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 3bb83f231d16819e5f5da7edbc9fc3f38baff011
ms.openlocfilehash: b0c5efa595b0377d7ae2d936d0394667356d18c9


---
# <a name="frequently-asked-questions-azure-container-service"></a>常見問題集：Azure Container Service


## <a name="orchestrators"></a>Orchestrator

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Azure Container Service 上支援哪些容器 Orchestrator？ 

支援開放原始碼的 DC/OS、Docker Swarm 和 Kubernetes。 對於 DC/OS 和 Docker Swarm 的支援已正式推出，但 Kubernetes 的支援目前仍屬預覽狀態。 如需詳細資訊，請參閱[概觀](container-service-intro.md)。
 
### <a name="do-you-support-swarm-mode"></a>是否支援 Swarm 模式？ 

目前不支援 Swarm 模式，但已排入服務的規劃藍圖之中。 

### <a name="does-azure-container-service-support-windows-containers"></a>Azure Container Service 是否支援 Windows 容器？  

目前支援 Linux 容器。 至於 DC/OS、Docker Swarm 和 Kubernetes Orchestrator 的 Windows 容器支援則已排入服務的規劃藍圖之中。 

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>是否建議在 Azure Container Service 中使用特定 Orchestrator？ 
我們一般不建議使用特定 Orchestrator。 如果您有使用其中一種受支援 Orchestrator 的經驗，則可以在 Azure Container Service 中套用該經驗。 不過，資料趨勢表明，DC/OS 已在生產環境中證明適用於巨量資料和 IoT 工作負載，Kubernetes 適合用於雲端原生的工作負載，而 Docker Swarm 則眾所皆知可與 Docker 工具整合且容易上手。

根據您的情況，您也可以使用其他 Azure 服務建置並管理自訂的容器解決方案。 這些服務包括[虛擬機器](../virtual-machines/virtual-machines-linux-azure-overview.md)、[Service Fabric](../service-fabric/service-fabric-overview.md)、[Web Apps](../app-service-web/app-service-web-overview.md) 和 [Batch](../batch/batch-technical-overview.md)。  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Azure Container Service 和 ACS 引擎有何不同？ 
Azure Container Service 是具有 SLA 保證的 Azure 服務，其功能可在 Azure 入口網站、Azure 命令列工具和 Azure API 中使用。 此服務可讓您快速實作和管理執行標準容器協調流程工具的叢集，但設定選擇相對較少。 

[ACS 引擎](http://github.com/Azure/acs-engine)是開放原始碼專案，可讓進階使用者自訂各種層級的叢集組態。 因為能改變基礎結構和軟體的組態，所以我們沒有為 ACS 引擎提供 SLA。 其支援的處理是透過 GitHub 上的開放原始碼專案，而非透過 Microsoft 官方管道。 

## <a name="cluster-management"></a>叢集管理

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>如何為叢集建立 SSH 金鑰？

您可以在作業系統上使用標準工具來建立 SSH RSA 公用和私用金鑰組，以針對叢集的 Linux 虛擬機器進行驗證。 如需相關步驟，請參閱 [OS X 及 Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) 或 [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) 指引。 

如果您使用 [Azure CLI 2.0 (預覽) 命令](container-service-create-acs-cluster-cli.md)部署容器服務叢集，系統會自動為叢集產生 SSH 金鑰。

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>如何為 Kubernetes 叢集建立服務主體？

另外需要 Azure Active Directory 服務主體識別碼和密碼，才能在 Azure Container Service 中建立 Kubernetes 叢集。 如需詳細資訊，請參閱[關於 Kubernetes 叢集的服務主體](container-service-kubernetes-service-principal.md)


如果您使用 [Azure CLI 2.0 (預覽) 命令](container-service-create-acs-cluster-cli.md)部署 Kubernetes 叢集，系統會自動為叢集產生服務主體認證。


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>如何在建立叢集後增加主要主機的數目？ 
叢集建立之後，主要主機的數目就已固定，且無法變更。 在建立叢集時，您最好選取三個或五個主要主機，以便享有高可用性。

> [!NOTE]
> 在預覽版中，Azure Container Service 的 Kubernetes 叢集只能有一個主要主機。
>

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>如何在建立叢集後增加代理程式的數目？ 
您可以使用 Azure 入口網站或命令列工具來調整叢集中的代理程式數目。 請參閱[調整 Azure Container Service 叢集](container-service-scale.md)。

> [!NOTE]
> 在預覽版中，Azure Container Service 的 Kubernetes 叢集有固定數目的代理程式。 
>

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>主要主機和代理程式的 URL 為何？ 
Azure Container Service 中的叢集資源 URL 是根據您所提供的 DNS 名稱前置詞和您為部署選擇的 Azure 區域名稱來產生。 例如，主要節點的完整網域名稱 (FQDN) 形式如下︰

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

您可以在 Azure 入口網站、Azure 資源總管或其他 Azure 工具中找到叢集的常用 URL。
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>哪裡可以找到叢集的 SSH 連接字串？

您可以在 Azure 入口網站或使用 Azure 命令列工具找到連接字串。 

1. 在入口網站中，瀏覽至叢集部署的資源群組。  

2. 按一下 [概觀]，然後按一下 [基本資訊] 下 [部署] 的連結。 

3. 在 [部署歷程記錄] 刀鋒視窗中，按一下名稱開頭為 **microsoft-acs** 且後接部署日期的部署。 範例︰microsoft-acs-201701310000。  

4. 在 [摘要] 頁面的 [輸出] 底下，<provided></provided> 了一些叢集連結。 **SSHMaster0** 會提供容器服務叢集中第一個主要主機的 SSH 連接字串。 

如先前所述，您也可以使用 Azure 工具來尋找主要主機的 FQDN。 請使用建立叢集時所指定的主要主機 FQDN 和使用者名稱，透過 SSH 連線到主要主機。 例如：

```bash
ssh userName@masterFQDN –A –p 22 
```

如需詳細資訊，請參閱[連接到 Azure Container Service 叢集](container-service-connect.md)。




## <a name="next-steps"></a>後續步驟

* [深入了解](container-service-intro.md) Azure Container Service。
* 使用[入口網站](container-service-deployment.md)或 [Azure CLI 2.0 (預覽)](container-service-create-acs-cluster-cli.md) 部署容器服務叢集。


<!--HONumber=Feb17_HO3-->


