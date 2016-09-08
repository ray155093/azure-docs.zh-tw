<properties
   pageTitle="公用和私用代理程式集區 ACS | Microsoft Azure"
   description="公用和私用代理程式集區如何與 Azure Container Service 叢集搭配運作。"
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、容器、微服務、Mesos、Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="adegeo"/>

# 代理程式集區

Azure Container Service 會將代理程式分割成多個集區。集區中的每個虛擬機器都相同，而每個集區都有自己的子網路。因此，每個集區都可以標示為公用或私用，這會影響容器服務的電腦之間的存取能力。

### 私人 Agent

私用代理程式節點會透過非可路由網路來執行。只有從系統管理區域或透過公用區域邊緣路由器才可存取此網路。根據預設，DC/OS 會在私用代理程式節點上啟動應用程式。請參閱 [DC/OS 文件](https://dcos.io/docs/1.7/administration/securing-your-cluster/)以取得網路安全性的詳細資訊。

### 公用代理程式

公用代理程式節點會透過可公開存取的網路執行 DC/OS 應用程式和服務。請參閱 [DC/OS 文件](https://dcos.io/docs/1.7/administration/securing-your-cluster/)以取得網路安全性的詳細資訊。

## 使用代理程式集區

根據預設，**Marathon** 會將任何新的應用程式部署至「私用」代理程式節點。您必須明確地將應用程式部署至「公用」節點。不幸的是，部署至公用節點不像勾選方塊這麼簡單。在建立新的應用程式期間，請移至 [選擇性] 索引標籤，並在 [接受的資源角色] 中輸入 **slave\_public**。此程序已詳載於[這裡](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)和 [DC\\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) 文件中。

## 後續步驟

深入了解[管理 DC/OS 容器](container-service-mesos-marathon-ui.md)。

<!---HONumber=AcomDC_0824_2016-->