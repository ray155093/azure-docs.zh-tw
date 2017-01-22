---
title: "連接到Azure Container Service 叢集 | Microsoft Docs"
description: "使用 SSH 通道連接到 Azure 容器服務叢集。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服務, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: bcc2d3468c8a560105aa2c2feb0d969ec3cccdcb
ms.openlocfilehash: 5296586b9266f432042f847f4dff9e6ff62ebc8b


---
# <a name="connect-to-an-azure-container-service-cluster"></a>連接到 Azure 容器服務叢集
部署了 Azure Container Service 的 DC/OS、Kubernetes 和 Docker Swarm 叢集都會公開 REST 端點。  針對 Kubernetes，此端點會在網際網路上安全地公開，而您可以從連線到網際網路的任何電腦直接存取該端點。 針對 DC/OS 和 Docker Swarm，您必須建立 SSH 通道，才能安全地連線到 REST 端點。 以下說明每一個連線。

> [!NOTE]
> Azure Container Service 中的 Kubernetes 支援目前為預覽狀態。
>

## <a name="connecting-to-a-kubernetes-cluster"></a>連線到 Kubernetes 叢集。
若要連線到 Kubernetes 叢集，您需要安裝 `kubectl` 命令列工具。  安裝此工具最簡單的方法是使用 Azure 2.0 `az` 命令列工具。

```console
az acs kubernetes install cli [--install-location=/some/directory]
```

或者，您可以直接從[發行頁面](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md#downloads-for-v146)下載用戶端

安裝 `kubectl` 之後，您必須將叢集認證複製到您的電腦。  要這樣做最簡單的方式是再次使用 `az` 命令列工具：

```console
az acs kubernetes get-credentials --dns-prefix=<some-prefix> --location=<some-location>
```

這會將叢集認證下載到 `$HOME/.kube/config`，這是 `kubectl` 預期會找到它的位置。

或者，您可以使用 `scp` 以安全地將檔案從主要 VM 上的 `$HOME/.kube/config` 複製到您的本機電腦。

```console
mkdir $HOME/.kube/config
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

如果您使用 Windows，您將需要使用 Windows 上 Ubuntu 的 Bash 或 Putty 'pscp' 工具。

設定 `kubectl` 之後，您可藉由列出叢集中的節點來進行測試：

```console
kubectl get nodes
```

最後，您可以檢視 Kubernetes 儀表板。 首先，請執行︰

```console
kubectl proxy
```

Kubernetes UI 現在已可供使用：http://localhost:8001/ui

如需進一步的指示，您可以參閱 [Kubernetes 快速入門](http://kubernetes.io/docs/user-guide/quick-start/) (英文)

## <a name="connecting-to-a-dcos-or-swarm-cluster"></a>連線到 DC/OS 或 Swarm 叢集

Azure 容器服務部署的 DC/OS 和 Docker Swarm 叢集公開了一些 REST 端點。 不過，這些端點並不開放給外界。 為了管理這些端點，您必須建立 安全殼層 (SSH) 通道。 建立 SSH 通道後，您可以對叢集端點執行命令，並透過您自己系統上的 UI 瀏覽器來檢視叢集。 本文會逐步引導您從 Linux、OSX 和 Windows 建立 SSH 通道。

> [!NOTE]
> 您可以建立與叢集管理系統的 SSH 工作階段。 但不建議這樣做。 直接使用管理系統可能會不小心變更組態。   
> 
> 

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>在 Linux 或 OSX 上建立 SSH 通道
在 Linux 或 OS X 上建立 SSH 通道時，您所做的第一件事就是找出負載平衡主機的公用 DNS 名稱。 若要這樣做，請展開資源群組以便顯示每個資源。 找出並選取主機的公用 IP 位址。 這會開啟一個刀鋒視窗，其中包含公用 IP 位址的相關資訊 (包含 DNS 名稱)。 儲存這個名稱供稍後使用。 <br />

![公用 DNS 名稱](media/pubdns.png)

現在開啟殼層並執行下列命令，其中：

**PORT** 是您想要公開之端點的連接埠。 以 Swarm 來說是 2375。 若為 DC/OS，則使用連接埠 80。  
**USERNAME** 是您部署叢集時提供的使用者名稱。  
**DNSPREFIX** 是您部署叢集時提供的 DNS 首碼。  
**REGION** 是資源群組所在的區域。  
**PATH_TO_PRIVATE_KEY** [選用] 是與您建立容器服務叢集時所提供的公開金鑰對應的私密金鑰之路徑。 搭配使用此選項與 -i 旗標。

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> SSH 連線連接埠是 2200 而非標準連接埠 22。
> 
> 

## <a name="dcos-tunnel"></a>DC/OS 通道
若要開啟 DC/OS 相關端點的通道，請執行類似下列的命令：

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

您現在可以在下列位址存取 DC/OS 相關端點：

* DC/OS： `http://localhost/`
* Marathon： `http://localhost/marathon`
* Mesos： `http://localhost/mesos`

同樣地，您可以透過此通道到達每個應用程式的 REST API。

## <a name="swarm-tunnel"></a>Swarm 通道
若要開啟 Swarm 端點的通道，請執行類似下列的命令：

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

現在您可以設定 DOCKER_HOST 環境變數，如下所示。 您可以繼續正常使用 Docker 命令列介面 (CLI)。

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>在 Windows 上建立 SSH 通道
在 Windows 上建立 SSH 通道有很多選項。 本文件將說明如何使用 PuTTY 來執行這項操作。

將 PuTTY 下載到 Windows 系統，並執行此應用程式。

輸入叢集中第一個主機的主機名稱，由叢集系統管理員使用者名稱和公用 DNS 名稱所組成。 [主機名稱] 看起來像這樣：`adminuser@PublicDNS`。 輸入 2200 作為 [連接埠] 。

![PuTTY 組態 1](media/putty1.png)

選取 [SSH] 和 [驗證]。 加入用於驗證的私密金鑰檔。

![PuTTY 組態 2](media/putty2.png)

選取 [通道]  並設定下列已轉送的連接埠：

* **來源連接埠：** 您的喜好設定--DC/OS 使用 80 或 Swarm 使用 2375。
* **目的地：** DC/OS 使用 localhost:80 或 Swarm 使用 localhost:2375。

下列範例是針對 DC/OS 而設定，但對於 Docker Swarm 而言也很類似。

> [!NOTE]
> 建立此通道時，連接埠 80 不得使用中。
> 
> 

![PuTTY 組態 3](media/putty3.png)

完成時，儲存連接設定，並連接 PuTTY 工作階段。 連接時，可以在 PuTTY 事件記錄檔中看到連接埠設定。

![PuTTY 事件記錄檔](media/putty4.png)

設定 DC/OS 的通道之後，您即可在下列位址存取相關的端點：

* DC/OS： `http://localhost/`
* Marathon： `http://localhost/marathon`
* Mesos： `http://localhost/mesos`

設定 Docker Swarm 的通道之後，您即可透過 Docker CLI 存取 Swarm 叢集。 您必須先使用值 ` :2375` 設定名稱為 `DOCKER_HOST` 的 Windows 環境變數。

## <a name="next-steps"></a>後續步驟
使用 DC/OS 或 Swarm 來部署及管理容器：

* [使用 Azure 容器服務和 DC/OS](container-service-mesos-marathon-rest.md)
* [使用 Azure 容器服務和 Docker Swarm](container-service-docker-swarm.md)




<!--HONumber=Dec16_HO3-->


