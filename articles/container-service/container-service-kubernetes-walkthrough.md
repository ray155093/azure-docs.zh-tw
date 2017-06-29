---
title: "快速入門 - 適用於 Linux 的 Azure Kubernetes 叢集 | Microsoft Docs"
description: "快速了解如何在 Azure Container Service 中使用 Azure CLI 建立適用於 Linux 的 Kubernetes 叢集。"
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
ms.date: 05/31/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 25043f6bf5e5ab3def8563bd2c096b79706bfec1
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>部署適用於 Linux 容器的 Kubernetes 叢集

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本指南詳述使用 Azure CLI 在 [Azure Container Service](container-service-intro.md) 中部署 [Kubernetes](https://kubernetes.io/docs/home/) 叢集。 部署叢集之後，您要使用 Kubernetes`kubectl` 命令列工具與其連線，且要部署第一個 Linux 容器。

本教學課程需要 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-azure"></a>登入 Azure 

使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯群組。 

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>建立 Kubernetes 叢集
使用 [az acs create](/cli/azure/acs#create) 命令，在 Azure Container Service 中建立 Kubernetes 叢集。 

下列範例會建立名為 myK8sCluster 的叢集，使用一個 Linux 主要節點和兩個 Linux 代理程式節點。 如果它們尚未存在於預設位置中，此範例就會建立 SSH 金鑰。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。 將叢集名稱更新為適合您環境的值。 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys 
```

幾分鐘之後，此命令就會完成，且會顯示您部署的相關資訊。

## <a name="install-kubectl"></a>安裝 kubectl

若要從用戶端電腦連線到 Kubernetes 叢集，請使用 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) (Kubernetes 命令列用戶端)。 

如果您是使用 Azure CloudShell，就已安裝 `kubectl`。 如果您想要在本機進行安裝，可以使用 [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) 命令。

下列 Azure CLI 範例會將 `kubectl` 安裝到您的系統。 如果您是在 Mac OS 或 Lunix 上執行 Azure CLI，可能需要搭配 `sudo` 來執行命令。

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>使用 kubectl 連線

若要將 `kubectl` 設定為連線到 Kubernetes 叢集，請執行 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 命令。 下列範例會下載 Kubernetes 叢集的叢集設定。

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

若要確認從您電腦至叢集的連線，請嘗試執行：

```azurecli-interactive
kubectl get nodes
```

`kubectl` 會列出主要和代理程式節點。

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>容器 NGINX 容器

您可以在 Kubernetes Pod 內執行 Docker 容器，其中包含一個或多個容器。 

下列命令會在其中一個節點上的 Kubernetes Pod 中啟動 NGINX Docker 容器。 在此情況下，容器會在 [Docker Hub](https://hub.docker.com/_/nginx/) 中執行從映像提取的 NGINX 網頁伺服器。

```azurecli-interactive
kubectl run nginx --image nginx
```
若要查看容器是否正在執行，請執行：

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>檢視 NGINX 歡迎使用頁面
若要向全球公開 NGINX 伺服器的公用 IP 位址，請輸入下列命令：

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

使用此命令，Kubernetes 會以服務的公用 IP 位址來建立服務和 [Azure Load Balancer 規則](container-service-kubernetes-load-balancing.md)。 

執行下列命令來查看服務的狀態。

```azurecli-interactive
kubectl get svc
```

IP 位址一開始會顯示為 `pending`。 幾分鐘之後，服務的外部 IP 位址會完成設定：
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

若要在外部 IP 位址查看預設的 NGINX 歡迎使用頁面，您可以使用指定的網頁瀏覽器：

![瀏覽至 Nginx 的影像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>刪除叢集
若不再需要叢集，您可以使用 [az group delete](/cli/azure/group#delete) 命令來移除資源群組、容器服務和所有相關資源。

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>後續步驟

在本快速入門中，您部署了 Kubernetes 叢集、與 `kubectl` 連線，並使用 NGINX 容器部署了 Pod。 若要深入了解 Azure Container Service，請繼續進行 Kubernetes 叢集教學課程。

> [!div class="nextstepaction"]
> [管理 ACS Kubernetes 叢集](./container-service-tutorial-kubernetes-prepare-app.md)

