---
title: "連接到Azure Container Service 叢集 | Microsoft Docs"
description: "從遠端電腦連接到 Azure Container Service 中的 Kubernetes、DC/OS 或 Docker Swarm 叢集"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服務, Kubernetes, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3dfa2c56dd6d3e0fe7757995d284cebe172eabc4
ms.lasthandoff: 04/03/2017


---
# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>從遠端連線至 Kubernetes、DC/OS，或 Docker Swarm 叢集
建立 Azure Container Service 叢集之後，您需要連接到叢集，才能部署和管理工作負載。 本文說明如何從遠端電腦連接到叢集的主要 VM。 

Kubernetes、DC/OS 和 Docker Swarm 叢集都會在本機提供 HTTP 端點。 針對 Kubernetes，此端點會在網際網路上安全地公開，而您可以從連線到網際網路的任何電腦執行 `kubectl` 命令列工具以存取該端點。 

針對 DC/OS 和 Docker Swarm，您必須建立連接到內部系統的安全殼層 (SSH) 通道。 建立通道後，您可以執行使用 HTTP 端點的命令，並從您的本機系統檢視叢集的 web 介面。 


## <a name="prerequisites"></a>必要條件

* [在 Azure Container Service 中部署的](container-service-deployment.md) Kubernetes、DC/OS 或 Swarm 叢集
* SSH RSA 私密金鑰檔案，其對應至在部署期間新增至叢集的公開金鑰。 這些命令假設 SSH 私密金鑰在您電腦上的 `$HOME/.ssh/id_rsa` 中。 如需詳細資訊，請參閱 [OS X 及 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 或 [Windows](../virtual-machines/linux/ssh-from-windows.md) 的相關指示。 如果 SSH 連線無法運作，您可能需要[重設 SSH 金鑰](../virtual-machines/linux/troubleshoot-ssh-connection.md)。

## <a name="connect-to-a-kubernetes-cluster"></a>連接到 Kubernetes 叢集

請遵循下列步驟，在電腦上安裝和設定 `kubectl`。

> [!NOTE] 
> 在 Linux 或 OS X 上，您可能需要使用 `sudo` 在此區段中執行命令。
> 

### <a name="install-kubectl"></a>安裝 kubectl
安裝此工具的方法之一是使用 `az acs kubernetes install-cli` Azure CLI 2.0 命令。 若要執行此命令，請確定您[已安裝](/cli/azure/install-az-cli2)最新的 Azure CLI 2.0 並登入 Azure 帳戶 (`az login`)。

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

或者，您可以直接從 [Kubernetes 發行頁面](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md)下載最新的用戶端。 如需詳細資訊，請參閱[安裝和設定 kubectl](https://kubernetes.io/docs/user-guide/prereqs/)。

### <a name="download-cluster-credentials"></a>下載叢集認證
安裝 `kubectl` 之後，您必須將叢集認證複製到您的電腦。 取得認證的方法之一是使用 `az acs kubernetes get-credentials` 命令。 傳遞資源群組的名稱和容器服務資源的名稱︰


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

此命令會將叢集認證下載到 `$HOME/.kube/config`，這是 `kubectl` 預期會找到它的位置。

或者，您可以使用 `scp` 以安全地將檔案從主要 VM 上的 `$HOME/.kube/config` 複製到您的本機電腦。 例如：

```console
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

如果您使用 Windows，您需要使用 Windows 上 Ubuntu 的 Bash、PuTTy 安全檔案複製用戶端或類似的工具。



### <a name="use-kubectl"></a>使用 kubectl

設定 `kubectl` 之後，您可藉由列出叢集中的節點來測試連線：

```console
kubectl get nodes
```

您可以嘗試其他 `kubectl` 命令。 例如，您可以檢視 Kubernetes 儀表板。 首先，執行 Kubernetes API 伺服器的 Proxy：

```console
kubectl proxy
```

Kubernetes UI 現在已可供使用：`http://localhost:8001/ui`。

如需詳細資訊，請參閱 [Kubernetes 快速入門](http://kubernetes.io/docs/user-guide/quick-start/)。

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>連接到 DC/OS 或 Swarm 叢集

若要使用 Azure Container Service 所部署的 DC/OS 和 Docker Swarm 叢集，請遵循下列指示，以從本機 Linux、OS X 或 Windows 系統中建立安全殼層 (SSH) 通道。 

> [!NOTE]
> 這些是透過 SSH 進行 TCP 流量的指示。 您也可以使用其中一個內部叢集管理系統，啟動互動式的 SSH 工作階段，但我們不建議您這樣做。 直接使用內部系統可能會不小心變更設定。  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>在 Linux 或 OSX 上建立 SSH 通道
在 Linux 或 OS X 上建立 SSH 通道時，您所做的第一件事就是找出負載平衡主機的公用 DNS 名稱。 請遵循下列步驟：


1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至包含您的容器服務叢集的資源群組。 展開資源群組，以便顯示每個資源。 

2. 按一下容器服務資源，然後按一下 [概觀]。 叢集的**主要 FQDN** 會出現在**基本資訊**下方。 儲存這個名稱供稍後使用。 

    ![公用 DNS 名稱](media/pubdns.png)

    或者，對容器服務執行 `az acs show` 命令。 尋找命令輸出中的**主要設定檔︰fqdn** 屬性。

3. 現在開啟殼層並藉由指定下列值來執行 `ssh` 命令： 

    **LOCAL_PORT** 是要連接的通道服務端上的 TCP 連接埠。 若是 Swarm，將此值設為 2375。 若是 DC/OS，將此值設為 80。  
    **REMOTE_PORT** 是您想要公開的端點連接埠。 針對 Swarm，使用連接埠 2375。 若為 DC/OS，則使用連接埠 80。  
    **USERNAME** 是您部署叢集時提供的使用者名稱。  
    **DNSPREFIX** 是您部署叢集時提供的 DNS 首碼。  
    **REGION** 是資源群組所在的區域。  
    **PATH_TO_PRIVATE_KEY** [選用] 是與您建立叢集時所提供的公開金鑰對應的私密金鑰之路徑。 搭配使用此選項與 `-i` 旗標。

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com 
    ```
    > [!NOTE]
    > SSH 連線連接埠是 2200，而非標準連接埠 22。 在具有一個以上主要 VM 的叢集中，這是第一個主要 VM 的連接通訊埠。
    > 



請參閱下列幾節中的 DC/OS 和 Swarm 範例。    

### <a name="dcos-tunnel"></a>DC/OS 通道
若要開啟 DC/OS 端點的通道，請執行以下命令︰

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> 您可以指定連接埠 80 以外的本機連接埠 (例如連接埠 8888)。 不過，當您使用此連接埠時，某些網頁 UI 連結可能無法運作。

您現在可以透過下列 URL (假設本機連接埠 80) 從本機系統存取 DC/OS 端點︰

* DC/OS： `http://localhost:80/`
* Marathon： `http://localhost:80/marathon`
* Mesos： `http://localhost:80/mesos`

同樣地，您可以透過此通道到達每個應用程式的 REST API。

### <a name="swarm-tunnel"></a>Swarm 通道
若要開啟 Swarm 端點的通道，請執行以下命令：

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```

現在您可以設定 DOCKER_HOST 環境變數，如下所示。 您可以繼續正常使用 Docker 命令列介面 (CLI)。

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>在 Windows 上建立 SSH 通道
在 Windows 上建立 SSH 通道有很多選項。 本節說明如何使用 PuTTY 建立通道。

1. 將 [PuTTY 下載](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)到 Windows 系統。

2. 執行應用程式。

3. 輸入叢集中第一個主機的主機名稱，由叢集系統管理員使用者名稱和公用 DNS 名稱所組成。 [主機名稱] 看起來類似 `adminuser@PublicDNSName`。 輸入 2200 作為 [連接埠] 。

    ![PuTTY 組態 1](media/putty1.png)

4. 選取 [SSH] > [Auth]。 新增私密金鑰檔 (.ppk 格式) 的路徑以供驗證。 您可以使用 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 等工具，從用來建立叢集的 SSH 金鑰產生此檔案。

    ![PuTTY 組態 2](media/putty2.png)

5. 選取 [SSH] > [通道] 並設定下列已轉送的連接埠︰

    * **來源連接埠：**DC/OS 使用 80 或 Swarm 使用 2375。
    * **目的地：** DC/OS 使用 localhost:80 或 Swarm 使用 localhost:2375。

    下列範例是針對 DC/OS 而設定，但對於 Docker Swarm 而言也很類似。

    > [!NOTE]
    > 建立此通道時，連接埠 80 不得使用中。
    > 

    ![PuTTY 組態 3](media/putty3.png)

6. 完成時，按一下 [工作階段] > [儲存] 以儲存連線組態。

7. 若要連接到 PuTTY 工作階段，請按一下 [開啟]。 連接時，可以在 PuTTY 事件記錄檔中看到連接埠設定。

    ![PuTTY 事件記錄檔](media/putty4.png)

設定 DC/OS 的通道之後，您即可在下列位址存取相關的端點：

* DC/OS： `http://localhost/`
* Marathon： `http://localhost/marathon`
* Mesos： `http://localhost/mesos`

設定 Docker Swarm 的通道之後，開啟您的 Windows 設定來設定名為 `DOCKER_HOST` 的系統環境變數 (值為 `:2375`)。 然後，您即可透過 Docker CLI 存取 Swarm 叢集。

## <a name="next-steps"></a>後續步驟
部署及管理叢集中的容器：

* [使用 Azure Container Service 和 Kubernetes](container-service-kubernetes-ui.md)
* [使用 Azure 容器服務和 DC/OS](container-service-mesos-marathon-rest.md)
* [使用 Azure 容器服務和 Docker Swarm](container-service-docker-swarm.md)


