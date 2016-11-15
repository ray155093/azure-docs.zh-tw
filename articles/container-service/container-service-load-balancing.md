---
title: "Azure Container Service 叢集中容器的負載平衡 | Microsoft Docs"
description: "Azure Container Service 叢集中多個容器的負載平衡。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "容器, 微服務, DC/OS, Azure"
ms.assetid: f0ab5645-2636-42de-b23b-4c3a7e3aa8bb
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cf255856302ad5bdb1f6022d231833610acbcac5


---
# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Azure Container Service 叢集中容器的負載平衡
在本文中，我們將探討如何在 DC/OS 管理的 Azure Container Service 中使用 Marathon-LB 建立內部負載平衡器。 這可讓您以水平方式調整應用程式。 也可讓您在將負載平衡器放在公用叢集上，並將應用程式容器放在私用叢集上，藉此利用公用和私用代理程式叢集。

## <a name="prerequisites"></a>必要條件
[部署 Azure Container Service 的執行個體](container-service-deployment.md) (其 Orchestrator 類型為 DCOS)，並[確保您的用戶端可以連線至您的叢集](container-service-connect.md)。 

## <a name="load-balancing"></a>負載平衡
我們將在 Container Service 叢集中建立兩個負載平衡層︰ 

1. Azure Load Balancer 會提供公用進入點 (一般使用者會抵達的點)。 Azure Container Service 會自動提供此進入點，而且會預設為公開連接埠 80、443 和 8080。
2. Marathon 負載平衡器 (marathon-lb) 會將輸入要求傳送至為這些要求提供服務的容器執行個體。 當我們調整提供 Web 服務的容器時，marathon-lb 會跟著動態調整。 根據預設，您的 Container Service 中不會提供此負載平衡器，但非常容易進行安裝。

## <a name="marathon-load-balancer"></a>Marathon Load Balancer
Marathon Load Balancer 會根據您所部署的容器以動態方式重新設定本身。 它也比較容易從遺失容器或代理程式中復原 - 如果發生這種情況，Apache Mesos 只會重新啟動別處的容器且 marathon-lb 會調整。

若要安裝 Marathon 負載平衡器，您可以使用 DC/OS Web UI 或命令列。

### <a name="install-marathonlb-using-dcos-web-ui"></a>使用 DC/OS Web UI 安裝 Marathon-LB
1. 按一下 'Universe'
2. 搜尋 'Marathon-LB'
3. 按一下 'Install'

![透過 DC/OS Web 介面安裝 marathon-lb](./media/dcos/marathon-lb-install.png)

### <a name="install-marathonlb-using-the-dcos-cli"></a>使用 DC/OS CLI 安裝 Marathon-LB
安裝 DC/OS CLI 並確保您可以連接至叢集之後，請從用戶端電腦執行下列命令︰

```bash
dcos package install marathon-lb
```

此命令會在公用代理程式叢集上自動安裝負載平衡器。

## <a name="deploy-a-load-balanced-web-application"></a>部署負載平衡 Web 應用程式
我們現在有 marathon-lb 套件，可以部署希望平衡負載的應用程式容器。 在此範例中，我們將使用下列組態來部署簡單的 Web 伺服器：

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

* 將 `HAProxy_0_VHOST` 的值設為您代理程式負載平衡器的 FQDN。 其格式為 `<acsName>agents.<region>.cloudapp.azure.com`。 例如，如果您在 `West US` 區域中使用名稱 `myacs` 建立 Container Service 叢集，FQDN 會是 `myacsagents.westus.cloudapp.azure.com`。 在 [Azure 入口網站](https://portal.azure.com)中瀏覽您為 Container Service 在資源群組中建立的資源時，尋找名稱中有 "agent" 的負載平衡器，也可以找到此 FQDN。
* 將 servicePort 設定為連接埠 > = 10,000。 這可識別正在此容器中執行的服務 -- marathon-lb 會以此識別它應該平衡負載的服務。
* 將 `HAPROXY_GROUP` 標籤設定為 [外部]。
* 將 `hostPort` 設定為 0。 這意味著 Marathon 將任意配置可用的通訊埠。
* 將 `instances` 設定為您想要建立的執行個體數目。 您稍後一律可以相應增加和相應減少這些數目。

值得注意的是，Marathon 預設會部署到私人叢集，這表示只能透過您的負載平衡器存取上述部署，而這通常是我們想進行的行為。

### <a name="deploy-using-the-dcos-web-ui"></a>使用 DC/OS Web UI 進行部署
1. 請造訪位於 http://localhost/marathon 的 Marathon 頁面 (設定您的 [SSH 通道](container-service-connect.md)之後) 並按一下 `Create Appliction`
2. 在 `New Application` 對話方塊中，按一下右上角的 `JSON Mode`
3. 將上述 JSON 貼到編輯器中
4. 按一下 `Create Appliction`

### <a name="deploy-using-the-dcos-cli"></a>使用 DC/OS CLI 進行部署
若要使用 DC/OS CLI 部署此應用程式，只要將上述 JSON 複製到名為 `hello-web.json` 的檔案，並執行︰

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Azure Load Balancer
根據預設，Azure Load Balancer 會公開連接埠 80、8080 和 443。 如果您使用上述其中一個連接埠 (如我們在上述範例中所為)，則不需要執行任何作業。 您應該可以叫用代理程式負載平衡器的 FQDN，而每次重新整理，您就會以循環配置方式叫用三個 Web 伺服器之一。 不過，如果您使用不同的連接埠，您需要在負載平衡器上針對您所用的連接埠新增循環備置規則和探查。 您可以從 [Azure CLI](../xplat-cli-azure-resource-manager.md) 中使用 `azure network lb rule create` 和 `azure network lb probe create` 命令來執行此動作。 您也可以使用 Azure 入口網站來執行這項工作。

## <a name="additional-scenarios"></a>其他案例
您可以使用不同的網域來公開不同的服務。 例如：

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

若要達到此目的，請參閱 [虛擬主機](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)，其可供建立網域與特定 marathon-lb 路徑的關聯。

或者，您可以公開不同的通訊埠並將其重新對應至 marathon-lb 背後的正確服務。例如：

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432

## <a name="next-steps"></a>後續步驟
如需 [marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/)的詳細資訊，請參閱 DC/OS 文件。




<!--HONumber=Nov16_HO2-->


