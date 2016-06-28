<properties
   pageTitle="Azure 容器服務叢集負載平衡 | Microsoft Azure"
   description="Azure 容器服務叢集負載平衡。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="容器, 微服務, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Azure 容器服務叢集負載平衡

在本文中，我們將設定可以相應增加的 Web 前端，以提供 Azure Load Balancer 背後的服務。

## 必要條件

[部署 Azure 容器服務的執行個體](container-service-deployment.md) (其 Orchestrator 類型為 DCOS)，並[確保您的用戶端可以連線至您的叢集](container-service-connect.md)。此外，請執行下列步驟。[AZURE.INCLUDE [安裝 DC/OS 命令列介面 (CLI)](../../includes/container-service-install-dcos-cli-include.md)]


## 負載平衡

容器服務叢集中有兩個負載平衡層︰適用於公用進入點 (使用者會叫用的點) 的 Azure Load Balancer，以及可將輸入要求路由傳送至提供要求服務之容器執行個體的基礎 Marathon Load Balancer (marathon-lb)。當我們調整提供服務的容器時，marathon-lb 會跟著動態調整。

## Marathon Load Balancer

Marathon Load Balancer 會根據您所部署的容器以動態方式重新設定本身。它也比較容易從遺失容器或代理程式中復原 -- 如果發生這種情況，Mesos 只會重新啟動別處的容器並重新設定 Marathon Load Balancer。

若要安裝 Marathon Load Balancer，請從您的用戶端電腦執行下列命令︰

```bash
dcos package install marathon-lb
```

我們現在有 marathon-lb 封裝時，可以使用下列組態部署簡單的 Web 伺服器︰


```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "tutum/hello-world",
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

其主要部分：
  * 將 HAProxy\_0\_VHOST 的值設為您代理程式的負載平衡器的 FQDN。其格式為 `<acsName>agents.<region>.cloudapp.azure.com`。例如，如果您在 `West US` 區域中使用名稱 `myacs` 建立容器服務叢集，FQDN 會是 `myacsagents.westus.cloudapp.azure.com`。在 [Azure 入口網站](https://portal.azure.com)中瀏覽資源群組中您為容器服務建立的資源時，尋找名稱中有 "agent" 的負載平衡器，也可以找到此 FQDN。
  * 將 servicePort 設定為連接埠 > = 10,000。這可識別正在此容器中執行的服務 -- marathon-lb 會以此識別它應該平衡負載的服務。
  * 將 HAPROXY\_GROUP 標籤設定為 [外部]。
  * 將 hostPort 設定為 0。這意味著 Marathon 將任意配置可用的通訊埠。

將此 JSON 複製到名為 `hello-web.json` 的檔案，並用它來部署容器︰

```bash
dcos marathon app add hello-web.json
```

## Azure Load Balancer

根據預設，Azure Load Balancer 會公開連接埠 80、8080 和 443。如果您使用上述其中一個連接埠 (如我們在上述範例中所為)，則不需要執行任何作業。您應該可以叫用代理程式負載平衡器的 FQDN，而每次重新整理，您就會以循環配置方式叫用三個 Web 伺服器之一。不過，如果您使用不同的連接埠，您需要在負載平衡器上針對您所用的連接埠新增循環備置規則和探查。您可以從 [Azure CLI](../xplat-cli-azure-resource-manager.md) 中使用 `azure lb rule create` 和 `azure lb probe create` 命令來執行此動作。


## 其他案例

您可以使用不同的網域來公開不同的服務。例如：

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292 mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

若要達到此目的，請參閱[虛擬主機](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)，其可供建立網域與特定 marathon-lb 路徑的關聯。

或者，您可以公開不同的通訊埠並將其重新對應至 marathon-lb 背後的正確服務。例如：

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423 Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## 後續步驟

如需 Marathon Load Balancer 的詳細資訊，請參閱[負載平衡部落格文章](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)。

<!---HONumber=AcomDC_0622_2016-->