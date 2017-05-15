---
title: "適用於 Windows 的 Azure Kubernetes 叢集 | Microsoft Docs"
description: "在 Azure Container Service 中部署並開始使用適用於 Windows 的 Kubernetes 叢集"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>在 Container Service 中開始使用 Kubernetes 和 Windows 容器


本文說明如何在包含 Windows 節點的 Azure Container Service 中建立 Kubernetes 叢集。以執行 Windows 容器。 開始使用 `az acs`Azure CLI 2.0 命令以在 Azure Container Service 中建立 Kubernetes 叢集。 然後，使用 Kubernetes `kubectl` 命令列工具以開始使用從 Docker 映像建置的 Windows 容器。 

> [!NOTE]
> 支援 Windows 容器使用 Azure Container Service 中的 Kubernetes 處於預覽階段。 
>



下圖顯示 Azure Container Service 中 Kubernetes 叢集的架構，搭配使用一個 Linux 主要節點與兩個 Windows 代理程式節點。 

![azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* Linux 主要節點提供 Kubernetes REST API，並可由連接埠 22 上的 SSH 或連接埠 443 上的 `kubectl` 存取。 
* Windows 代理程式節點則是以群組方式存在於 Azure 可用性設定組並執行您的容器。 Windows 節點可以透過主要節點經由 RDP SSH 通道來存取。 Azure Load Balancer 規則會根據公開的服務而動態地新增至叢集。



所有 VM 都位於相同的私人虛擬網路，可完全存取彼此。 所有 VM 都會執行 kubelet、Docker 和 Proxy。

如需詳細背景，請參閱 [Azure Container Service 簡介](container-service-intro.md)和 [Kubernetes 文件](https://kubernetes.io/docs/home/)。

## <a name="prerequisites"></a>必要條件
若要使用 Azure CLI 2.0 建立 Azure Container Service 叢集，您必須︰
* 有一個 Azure 帳戶 ([取得免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/))
* 已安裝及登入 [Azure CLI 2.0](/cli/azure/install-az-cli2)

您的 Kubernetes 叢集還需要下列項目。 您可以事先準備這些項目，或使用 `az acs create` 命令選項在叢集部署期間自動產生。 

* **SSH RSA 公開金鑰**：如果您想要建立安全殼層 (SSH) RSA 金鑰，請參閱 [macOS 和 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 和 [Windows](../virtual-machines/linux/ssh-from-windows.md) 指引。 

* **服務主體用戶端識別碼和祕密**：如需建立 Azure Active Directory 服務主體的步驟和其他資訊，請參閱[關於 Kubernetes 叢集的服務主體](container-service-kubernetes-service-principal.md)。

本文中的命令範例會自動產生 SSH 金鑰和服務主體。
  
## <a name="create-your-kubernetes-cluster"></a>建立 Kubernetes 叢集

以下是用來建立叢集的 Azure CLI 2.0 命令。 

### <a name="create-a-resource-group"></a>建立資源群組
在[可取得](https://azure.microsoft.com/regions/services/) Azure Container Service 的位置建立資源群組。 下列命令會在 westus 位置建立名為 myKubernetesResourceGroup 的資源群組：

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>使用 Windows 代理程式節點建立 Kubernetes 叢集

使用 `az acs create` 命令搭配 `--orchestrator-type=kubernetes` 和 `--windows` 代理程式選項，在資源群組中建立 Kubernetes 叢集。 如需命令語法，請參閱`az acs create`[說明](/cli/azure/acs#create)。

下列命令會建立名為 myKubernetesClusterName 的 Container Service 叢集，其管理節點的 DNS 前置詞為 myPrefix，並具有指定的認證可觸達 Windows 節點。 這個版本的命令會自動產生 Kubernetes 叢集的 SSH RSA 金鑰和服務主體。


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

幾分鐘後，命令完成，您應有作用中的 Kubernetes 叢集。

> [!IMPORTANT]
> 如果您的帳戶沒有建立 Azure AD 服務主體的權限，則命令會產生類似 `Insufficient privileges to complete the operation.` 的錯誤。如需詳細資訊，請參閱[關於 Kubernetes 叢集的服務主體](container-service-kubernetes-service-principal.md)。 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>使用 kubectl 連線到叢集

若要從用戶端電腦連線到 Kubernetes 叢集，請使用 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) (Kubernetes 命令列用戶端)。 

如果您未在本機安裝 `kubectl`，可以使用 `az acs kubernetes install-cli` 安裝它。 (您也可以從 [Kubernetes 網站](https://kubernetes.io/docs/tasks/kubectl/install/)進行下載。)

**Linux 或 macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> 根據預設，此命令會將 `kubectl` 二進位檔安裝至 Linux 或 macOS 系統上的 `/usr/local/bin/kubectl`，或 Windows 上的 `C:\Program Files (x86)\kubectl.exe`。 若要指定不同的安裝路徑，請使用 `--install-location` 參數。
>
> 安裝 `kubectl` 之後，請確認其目錄在您的系統路徑中，或將它新增至該路徑。 


然後，執行下列命令，將主要 Kubernetes 叢集組態下載到本機 `~/.kube/config` 檔案：

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

此時，您已準備好從您的電腦存取叢集。 請嘗試執行：

```bash
kubectl get nodes
```

確認您可以看到叢集中的電腦清單。

![在 Kubernetes 叢集中執行的節點](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>建立第一個 Kubernetes 服務

建立叢集並與 `kubectl` 連線之後，嘗試從 Docker 容器啟動 Windows 應用程式並對網際網路公開。 此基本範例使用 JSON 檔案來指定 Microsoft Internet Information Server (IIS) 容器，然後使用 `kubctl apply` 建立加以。 

1. 建立名為 `iis.json` 的本機檔案，並複製下列內容。 此檔案會告訴 Kubernetes 使用來自 [Docker 中樞](https://hub.docker.com/r/microsoft/iis/)的公用映像，在 Windows Server 2016 Server Core 上執行 IIS。 該容器會使用連接埠 80，但一開始只能在叢集網路中存取。

  ```JSON
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "iis",
      "labels": {
        "name": "iis"
      }
    },
    "spec": {
      "containers": [
        {
          "name": "iis",
          "image": "microsoft/iis",
          "ports": [
            {
            "containerPort": 80
            }
          ]
        }
      ],
      "nodeSelector": {
        "beta.kubernetes.io/os": "windows"
      }
    }
  }
  ```
2. 若要啟動應用程式，請輸入：  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. 若要追蹤容器的部署，請輸入︰  
  ```bash
  kubectl get pods
  ```
  部署容器時，狀態會是 `ContainerCreating`。 

  ![處於 ContainerCreating 狀態的 IIS 容器](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  由於 IIS 映像的大小，容器可能需要幾分鐘的時間才會進入 `Running` 狀態。

  ![處於執行中狀態的 IIS 容器](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. 若要將容器公諸於世，請輸入下列命令︰

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  使用此命令，Kubernetes 會以公用 IP 位址建立 Azure Load Balancer 規則。 變更需要數分鐘的時間才能傳遍負載平衡器。 如需詳細資訊，請參閱[在 Azure Container Service 中針對 Kubernetes 叢集內的容器進行負載平衡](container-service-kubernetes-load-balancing.md)。

5. 執行下列命令來查看服務的狀態。

  ```bash
  kubectl get svc
  ```

  IP 位址一開始會顯示為 `pending`：

  ![擱置的外部 IP 位址](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  幾分鐘之後，會設定好 IP 位址：
  
  ![適用於 IIS 的外部 IP 位址](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. 外部 IP 位址可用後，您就可以在瀏覽器中瀏覽它︰

  ![瀏覽至 IIS 的影像](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. 若要刪除 IIS Pod，請輸入︰

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>後續步驟

* 若要使用 Kubernetes UI，請執行 `kubectl proxy` 命令。 然後，瀏覽至 http://localhost:8001/ui。

* 如需建置自訂 IIS 網站並在 Windows 容器中執行它的步驟，請參閱位於 [Docker 中樞](https://hub.docker.com/r/microsoft/iis/)的指引。

* 若要使用 PuTTy 透過主機的 RDP SSH 通道存取 Windows 節點，請參閱 [ACS 引擎文件](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master)。 

