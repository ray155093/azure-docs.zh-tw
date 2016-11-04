---
title: Azure 容器服務簡介 | Microsoft Docs
description: Azure Container Service 提供簡化建立、設定及管理虛擬機器叢集的方法，這些虛擬機器預先設定為執行容器化應用程式。
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker、容器、微服務、Mesos、Azure

ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle

---
# Azure 容器服務簡介
Azure Container Service 可讓您輕鬆建立、設定及管理虛擬機器的叢集，這些虛擬機器預先設定為執行容器化應用程式。它使用受歡迎開放原始碼排程和協調流程工具的最佳化組態。這樣可讓您使用現有技能，或運用大量且不斷成長的社群專業知識，在 Microsoft Azure 上部署及管理容器應用程式。

![Azure Container Service 提供一個方法來在 Azure 的多部主機上管理容器化應用程式。](./media/acs-intro/acs-cluster.png)

Azure Container Service 會使用 Docker 容器格式，確保您的應用程式容器具有完全的可攜式特性。它也支援您對於 Marathon 和 DC/OS 或 Docker Swarm 的選擇，確保您可以將這些應用程式擴展為成千上萬個容器。

藉由使用 Azure Container Service，您可以充分利用 Azure 的企業級功能，同時仍可保有應用程式可攜性--包括協調流程層的可攜性在內。

## 使用 Azure 容器服務
我們對於 Azure Container Service 的目標，是要使用現今頗受客戶歡迎的開放原始碼工具和技術，提供容器主控環境。為了這個目的，我們會為您所選擇的 Orchestrator (DC/OS 或 Docker Swarm) 公開標準 API 端點。您可以使用這些端點來運用能夠與這些端點通訊的任何軟體。比方說，在 Docker Swarm 端點的案例中，您可能會選擇使用 Docker 命令列介面 (CLI)。若是 DC/OS，您可能會選擇使用 DCOS CLI。

## 使用 Azure Container Service 建立 Docker 叢集
若要開始使用 Azure Container Service，您必須透過入口網站 (搜尋 'Azure Container Service') 部署 Azure Container Service 叢集、使用 Azure Resource Manager 範本 ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm) 或 [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos))，或使用 [CLI](/documentation/articles/xplat-cli-install/)。提供的快速入門範本可以修改為包含其他或進階 Azure 組態。如需有關部署 Azure Container Service 叢集的詳細資訊，請參閱[部署 Azure Container Service 叢集](container-service-deployment.md)。

## 部署應用程式
Azure Container Service 提供協調流程的選擇：Docker Swarm 或 DC/OS。部署應用程式的方式取決於您所選擇的 Orchestrator。

### 使用 DC/OS
DC/OS 是以 Apache Mesos 分散式系統核心為基礎的分散式作業系統。Apache Mesos 存放在 Apache Software Foundation，並將一些 [IT 業界鼎鼎有名的人物](http://mesos.apache.org/documentation/latest/powered-by-mesos/)列為使用者與參與者。

![針對顯示代理程式與主機的 Swarm 設定的 Azure Container Service。](media/acs-intro/dcos.png)

DC/OS 和 Apache Mesos 包含令人印象深刻的功能集︰

* 經實證的延展性
* 使用 Apache ZooKeeper 進行主要和從屬容錯複寫
* 支援 Docker 格式的容器
* 在工作與 Linux 容器之間的原生隔離
* 多資源排程 (記憶體、CPU、磁碟和連接埠)
* 用於開發全新平行應用程式的 Java、Python 和 C++ API
* 用於檢視叢集狀態的 Web UI

根據預設，在 Azure Container Service 上執行的 DC/OS 會包含用來排程工作負載的 Marathon 協調流程平台。不過，ACS 的 DC/OS 部署中包含的是可加入您的服務的 Mesosphere Universe 服務，其中包括 Spark、Hadoop、Cassandra 及更多的服務。

![Azure Container Service 中的 DC/OS Universe](media/dcos/universe.png)

#### 使用 Marathon
在 cgroups 中，Marathon 是服務的全叢集初始化和控制系統--或者，若是 Azure Container Service，則為 Docker 格式的容器。Marathon 提供 Web UI，您可以用它來部署您的應用程式。您可以在 `http://DNS_PREFIX.REGION.cloudapp.azure.com` 這樣的 URL 存取此程式：其中 DNS\_PREFIX 及 REGION 兩者都在部署時定義。當然，您也可以提供您自己的 DNS 名稱。如需有關使用 Marathon Web UI 來執行容器的詳細資訊，請參閱[透過 Web UI 來管理容器](container-service-mesos-marathon-ui.md)。

![Marathon 應用程式清單](media/dcos/marathon-applications-list.png)

您也可以使用 REST API 來與 Marathon 通訊。有許多可用於每個工具的用戶端程式庫。這些程式庫涵蓋各種不同語言--當然，您可以使用任何語言的 HTTP 通訊協定。此外，許多受歡迎的 DevOps 工具都提供 Marathon 的支援。當您使用 Azure Container Service 叢集時，這為作業小組提供了最大的彈性。如需有關使用 Marathon REST API 來執行容器的詳細資訊，請參閱[透過 REST API 進行容器管理](container-service-mesos-marathon-rest.md)。

### 使用 Docker Swarm
Docker Swarm 為 Docker 提供原生叢集。由於 Docker Swarm 可作為標準 Docker API 使用，已與 Docker 精靈通訊的任何工具都可以使用 Swarm 無障礙地延伸到 Azure 容器服務上的多部主機。

![設定來使用 DC/OS 以顯示 jumpbox、代理程式與主機的 Azure 容器服務。](media/acs-intro/acs-swarm2.png)

在 Swarm 叢集上管理容器的支援工具包括但不限於下列程式：

* Dokku
* Docker CLI 與 Docker Compose
* Krane
* Jenkins

## 影片
開始使用 Azure Container Service (101)：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Container-Service-101/player]
> 
> 

使用 Azure Container Service 建置應用程式 (組建 2016)

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/B822/player]
> 
> 

<!---HONumber=AcomDC_0914_2016-->