---
title: "公用和私人 DC/OS 代理程式集區 ACS | Microsoft Docs"
description: "公用和私用代理程式集區如何與 Azure Container Service 叢集搭配運作。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker、容器、微服務、Mesos、Azure"
ms.assetid: 36d657c9-8845-4bf7-bed2-088323b67406
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a6d9ab6d95de936e4f1d28d47d4e1d74c080bdae


---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Azure Container Service 的 DC/OS 代理程式集區
DC/OS Azure Container Service 會將代理程式分割成公用或私用集區。 您可以對任一集區進行部署，這會影響容器服務的電腦之間的存取性。 電腦可以公開至網際網路 (公用) 或保留在內部 (私用)。 本文簡短概述為什麼會有公用和私用集區。

### <a name="private-agents"></a>私人 Agent
私用代理程式節點會透過非可路由網路來執行。 只有從系統管理區域或透過公用區域邊緣路由器才可存取此網路。 根據預設，DC/OS 會在私用代理程式節點上啟動應用程式。 請參閱 [DC/OS 文件](https://dcos.io/docs/1.7/administration/securing-your-cluster/) 以取得網路安全性的詳細資訊。

### <a name="public-agents"></a>公用代理程式
公用代理程式節點會透過可公開存取的網路執行 DC/OS 應用程式和服務。 請參閱 [DC/OS 文件](https://dcos.io/docs/1.7/administration/securing-your-cluster/) 以取得網路安全性的詳細資訊。

## <a name="using-agent-pools"></a>使用代理程式集區
根據預設，**Marathon** 會將任何新的應用程式部署至*私用*代理程式節點。 您必須在建立應用程式期間明確地將應用程式部署至「公用」  節點。 選取 [選用] 索引標籤，然後輸入 **slave_public** 做為 [接受的資源角色] 值。 此程序已詳載於[這裡](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)和 [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) 文件中。

## <a name="next-steps"></a>後續步驟
深入了解 [管理 DC/OS 容器](container-service-mesos-marathon-ui.md)。

了解如何 [開啟 Azure 所提供的防火牆](container-service-enable-public-access.md) 以允許對 DC/OS 容器進行公用存取。




<!--HONumber=Nov16_HO3-->


