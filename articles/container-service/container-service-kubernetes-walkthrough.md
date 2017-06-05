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
ms.date: 05/08/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 0604a85192ed632b621113b98cc44172c584ea01
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>在 Container Service 中開始使用 Kubernetes 叢集


本逐步解說會示範如何使用 Azure CLI 2.0 命令在 Azure Container Service 中建立 Kubernetes 叢集。 然後，您可以使用 `kubectl` 命令列工具來開始使用叢集中的容器。

下圖顯示容器服務叢集搭配一個 Linux 主要節點及兩個 Linux 代理程式的架構。 主要節點供 Kubernetes REST API 使用。 代理程式節點則是以群組方式存在於 Azure 可用性設定組並執行您的容器。 所有 VM 都位於相同的私人虛擬網路，可完全存取彼此。

![azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-walkthrough/kubernetes.png)

如需詳細背景，請參閱 [Azure Container Service 簡介](container-service-intro.md)和 [Kubernetes 文件](https://kubernetes.io/docs/home/)。

## <a name="prerequisites"></a>必要條件
若要使用 Azure CLI 2.0 建立 Azure Container Service 叢集，您必須︰
* 有一個 Azure 帳戶 ([取得免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/))
* 已安裝及設定 [Azure CLI 2.0](/cli/azure/install-az-cli2)

此外，您需要 (或您可以在叢集部署期間使用 Azure CLI 自動產生)︰

* **SSH RSA 公開金鑰**：如果您想要預先建立安全殼層 (SSH) RSA 金鑰，請參閱 [macOS 和 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 或 [Windows](../virtual-machines/linux/ssh-from-windows.md) 指引。 

* **服務主體用戶端識別碼和祕密**：如需建立 Azure Active Directory 服務主體的步驟和其他資訊，請參閱[關於 Kubernetes 叢集的服務主體](container-service-kubernetes-service-principal.md)。

 本文中的命令範例會自動產生 SSH 金鑰和服務主體。

## <a name="create-your-kubernetes-cluster"></a>建立 Kubernetes 叢集

以下是使用 Azure CLI 2.0 來建立叢集的簡短 Bash 殼層命令。 

### <a name="create-a-resource-group"></a>建立資源群組
若要建立叢集，您必須先在[可取得](https://azure.microsoft.com/regions/services/) Azure Container Service 的位置建立資源群組。 執行類似下列的命令：

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>建立叢集
使用 `az acs create` 命令搭配 `--orchestrator-type=kubernetes`，在資源群組中建立 Kubernetes 叢集。 如需命令語法，請參閱`az acs create`[說明](/cli/azure/acs#create)。

這個版本的命令會自動產生 Kubernetes 叢集的 SSH RSA 金鑰和服務主體。



```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```

幾分鐘後，命令完成，您應有作用中的 Kubernetes 叢集。

> [!IMPORTANT]
> 如果帳戶沒有用以建立 Azure AD 服務主體的權限，則命令會產生錯誤，此錯誤的內容類似**權限不足，無法完成作業**。 如需詳細資訊，請參閱[關於 Kubernetes 叢集的服務主體](container-service-kubernetes-service-principal.md)。
> 



### <a name="connect-to-the-cluster"></a>連接到叢集

若要從用戶端電腦連線到 Kubernetes 叢集，您要使用 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)，Kubernetes 命令列用戶端。 

如果您尚未安裝 `kubectl`，可以使用 `az acs kubernetes install-cli` 安裝它。 (您也可以從 [Kubernetes 網站](https://kubernetes.io/docs/tasks/kubectl/install/)進行下載。)

```azurecli
sudo az acs kubernetes install-cli
```

> [!TIP]
> 根據預設，此命令會安裝 `kubectl` 二進位檔至 Linux 或 macOS 系統上的 `/usr/local/bin/kubectl`，或 Windows 上的 `C:\Program Files (x86)\kubectl.exe`。 若要指定不同的安裝路徑，請使用 `--install-location` 參數。
>
> 安裝 `kubectl`之後，請確認其為您系統路徑中的目錄，或將它新增至路徑。 
>


然後，執行下列命令會將主要 Kubernetes 叢集組態下載到 `~/.kube/config` 檔案中：

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

如需更多安裝和設定 `kubectl` 的選項，請參閱[連線至 Azure Container Service 叢集](container-service-connect.md)。

此時您應該已準備好從您的電腦存取叢集。 請嘗試執行：

```bash
kubectl get nodes
```

確認您可以看到叢集中的電腦清單。

## <a name="create-your-first-kubernetes-service"></a>建立第一個 Kubernetes 服務

完成此逐步解說之後，您將了解如何︰
* 部署 Docker 應用程式並對外公開
* 使用 `kubectl exec` 在容器中執行命令 
* 存取 Kubernetes 儀表板

### <a name="start-a-container"></a>啟動容器
您可以執行下列命令來執行容器 (在此案例中為 Nginx Web 伺服器)：

```bash
kubectl run nginx --image nginx
```

這會在其中一個節點上的 Pod 中啟動 Nginx Docker 容器。

查看執行中的容器，請執行：

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>對外公開服務
若要對外公開服務，請建立類型 `LoadBalancer` 的 Kubernetes `Service`：

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

此命令會使 Kubernetes 以公用 IP 位址建立 Azure Load Balancer 規則。 變更需要數分鐘的時間才能傳遍負載平衡器。 如需詳細資訊，請參閱[在 Azure Container Service 中針對 Kubernetes 叢集內的容器進行負載平衡](container-service-kubernetes-load-balancing.md)。

執行下列命令，以監看服務從 `pending` 變更為顯示外部 IP 位址︰

```bash
watch 'kubectl get svc'
```

  ![監看從 pending 轉換為外部 IP 位址的影像](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

一旦看到外部 IP 位址後，您就可以在瀏覽器中瀏覽它︰

  ![瀏覽至 Nginx 的影像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>瀏覽 Kubernetes UI
若要查看 Kubernetes Web 介面，您可以使用：

```bash
kubectl proxy
```
此命令會在 localhost 上執行經過驗證的 Proxy，可讓您用來檢視在 [http://localhost:8001/ui](http://localhost:8001/ui) 上執行的 Kubernetes Web UI。 如需詳細資訊，請參閱[搭配 Azure Container Service 使用 Kubernetes Web UI](container-service-kubernetes-ui.md)。

![Kubernetes 儀表板的影像](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>容器中的遠端工作階段
Kubernetes 可讓您在叢集中執行的遠端 Docker 容器中執行命令。

```bash
# Get the name of your nginx pods
kubectl get pods
```

使用您的 pod 名稱，您可以在 pod 上執行遠端命令。 例如：

```bash
kubectl exec <pod name> date
```

您也可以使用 `-it` 旗標取得完整的互動式工作階段：

```bash
kubectl exec <pod name> -it bash
```

![容器內的遠端工作階段](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>後續步驟

若要使用 Kubernetes 叢集執行更多作業，請參閱下列資源︰

* [Kubernetes 訓練營](https://katacoda.com/embed/kubernetes-bootcamp/1/) - 說明如何部署、調整、更新和偵錯容器化應用程式。
* [Kubernetes 使用者指南](http://kubernetes.io/docs/user-guide/) - 提供在現有 Kubernetes 叢集中執行程式的相關資訊。
* [Kubernetes 範例](https://github.com/kubernetes/kubernetes/tree/master/examples) - 提供如何使用 Kubernetes 執行實際應用程式的範例。

