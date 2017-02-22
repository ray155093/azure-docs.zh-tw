---
title: "在 Azure 中快速開始 Kubernetes 叢集 | Microsoft Docs"
description: "部署並開始使用 Azure Container Service 的 Kubernetes 叢集"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Microsoft Azure Container Service 引擎 - Kubernetes 逐步解說

## <a name="prerequisites"></a>必要條件
本逐步解說假設您已安裝 ['azure-cli' 命令列工具 (英文)](https://github.com/azure/azure-cli#installation) 並已在 `~/.ssh/id_rsa.pub` 建立 [SSH 公開金鑰](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)。

## <a name="overview"></a>概觀

下列指示會建立一個 Kubernetes 叢集搭配一個主要節點及兩個背景工作節點。
主要節點供 Kubernetes REST API 使用。  背景工作節點則是以群組方式存在於 Azure 可用性設定組並執行您的容器。 所有 VM 都位於相同的私人 VNET，可完全存取彼此。

> [!NOTE]
> Azure Container Service 中的 Kubernetes 支援目前為預覽狀態。
>

下圖顯示容器服務叢集搭配一個主要節點及兩個代理程式的架構：

![Image of Kubernetes cluster on azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>建立 Kubernetes 叢集

### <a name="create-a-resource-group"></a>建立資源群組
若要建立叢集，您必須先在特定位置中建立資源群組，您可以透過下列命令來完成此動作：
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>建立叢集
一旦有資源群組之後，您就可以使用下列命令在該群組中建立叢集：
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> azure-cli 會將 `~/.ssh/id_rsa.pub` 上傳到 Linux VM。
>

該命令完成之後，您應該會有正常運作的 Kubernetes 叢集。

### <a name="configure-kubectl"></a>設定 kubectl
`kubectl` 是 Kubernetes 命令列用戶端。  如果您尚未安裝它，可以使用下列命令安裝：

```console
az acs kubernetes install-cli
```

一旦安裝了 `kubectl`，執行下列命令會將主要 kubernetes 叢集組態下載到 ~/.kube/config 檔案中
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

此時您應該已準備好從您的電腦存取叢集，請嘗試執行：
```console
kubectl get nodes
```

並確定您可以在叢集中看到電腦。

## <a name="create-your-first-kubernetes-service"></a>建立第一個 Kubernetes 服務

完成此逐步解說之後，您將了解如何︰
 * 部署 Docker 應用程式並對外公開、
 * 使用 `kubectl exec` 在容器中執行命令， 
 * 以及存取 Kubernetes 儀表板。

### <a name="start-a-simple-container"></a>啟動簡單容器
您可以執行下列命令來執行簡單容器 (在此案例中為 `nginx` Web 伺服器)：

```console
kubectl run nginx --image nginx
```

這會在其中一個節點上的 pod 中啟動 nginx Docker 容器。

您可以執行
```console
kubectl get pods
```

來查看執行中的容器。

### <a name="expose-the-service-to-the-world"></a>對外公開服務
若要對外公開服務。  您可以建立 `LoadBalancer` 類型的 Kubernetes `Service`：

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

現在，這將會使 Kubernetes 以公用 IP 建立 Azure Load Balancer。 變更需要 2-3 分鐘才能傳遍負載平衡器。

若要監看服務從 "pending" 變更為外部 IP，請輸入：
```console
watch 'kubectl get svc'
```

  ![監看從 pending 轉換為外部 ip 的影像](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

一旦看到外部 IP 後，您就可以在瀏覽器中瀏覽它︰

  ![Image of browsing to nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>瀏覽 Kubernetes UI
若要查看 Kubernetes Web 介面，您可以使用：

```console
kubectl proxy
```
這會在 localhost 上執行經過簡單驗證的 Proxy，可讓您用來檢視 [Kubernetes UI](http://localhost:8001/ui)

![Kubernetes 儀表板的影像](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>容器中的遠端工作階段
Kubernetes 可讓您在叢集中執行的遠端 Docker 容器中執行命令。

```console
# Get the name of your nginx pod
kubectl get pods
```

使用您的 pod 名稱，您可以在 pod 上執行遠端命令。  例如：
```console
kubectl exec nginx-701339712-retbj date
```

您也可以使用 `-it` 旗標取得完整的互動式工作階段：

```console
kubectl exec nginx-701339712-retbj -it bash
```

![Image of curl to podIP](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>詳細資料
### <a name="installing-the-kubectl-configuration-file"></a>安裝 kubectl 組態檔
當您執行 `az acs kubernetes get-credentials` 之後，用於遠端存取的 kube 組態檔會儲存於主目錄 ~/.kube/config 下。

如果您需要直接下載它，您可以使用 `ssh` (Linux 或 OS X) 或是 `Putty` (Windows)：

#### <a name="windows"></a>Windows
若要從 [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 使用 pscp。  請確定您已透過 [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant) 公開憑證：
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X 或 Linux：
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>深入了解

### <a name="azure-container-service"></a>Azure Container Service

1. [Azure Container Service 文件](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Azure Container Service 開放原始碼引擎](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>Kubernetes 社群文件

1. [Kubernetes 訓練營](https://katacoda.com/embed/kubernetes-bootcamp/1/) - 說明如何部署、調整、更新和偵錯容器化應用程式。
2. [Kubernetes 使用者指南](http://kubernetes.io/docs/user-guide/) - 提供在現有 Kubernetes 叢集中執行程式的相關資訊。
3. [Kubernetes 範例](https://github.com/kubernetes/kubernetes/tree/master/examples) - 提供如何使用 Kubernetes 執行實際應用程式的一些範例。



<!--HONumber=Jan17_HO4-->


