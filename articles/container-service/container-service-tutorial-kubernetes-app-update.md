---
title: "Azure Container Service 教學課程 - 更新應用程式 | Microsoft Docs"
description: "Azure Container Service 教學課程 - 更新應用程式"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服務, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: ce4a88a7958116890ec17eb2405ba809487b9c0f
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017

---

# <a name="update-an-application-in-kubernetes"></a>在 Kubernetes 中更新應用程式

在 Kubernetes 中部署應用程式之後，您可以藉由指定新的容器映像或映像版本來進行更新。 當您更新應用程式時，更新會分段推出，所以只有一部分的部署會同時更新。 

此分段更新可讓應用程式在更新期間繼續執行，並且在發生部署失敗時提供復原機制。 

在本教學課程中，已更新範例 Azure Vote 應用程式。 您完成的工作包括：

> [!div class="checklist"]
> * 更新前端應用程式程式碼。
> * 建立已更新的容器映像。
> * 將容器映像推送至 Azure Container Registry。
> * 部署已更新的應用程式。

## <a name="before-you-begin"></a>開始之前

在上一個教學課程中，我們已將應用程式封裝成容器映像、將映像上傳至 Azure Container Registry，並已建立 Kubernetes 叢集。 我們會接著在 Kubernetes 叢集上執行此應用程式。 

如果您尚未採取這些步驟，而現在想要嘗試，請回到[教學課程 1 – 建立容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

本教學課程至少需要執行中應用程式的 Kubernetes 叢集。

## <a name="update-application"></a>更新應用程式

若要完成本教學課程中的步驟，您必須複製一份 Azure Vote 應用程式。 如有必要，請使用下列命令建立此複製的複本：

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

使用任何程式碼或文字編輯器開啟 `config_file.cfg` 檔案。 您可以在所複製存放庫的下列目錄下找到這個檔案。

```bash
 /azure-voting-app/azure-vote/azure-vote/config_file.cfg
```

變更 `VOTE1VALUE` 和 `VOTE2VALUE` 的值，然後儲存檔案。

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Half Full'
VOTE2VALUE = 'Half Empty'
SHOWHOST = 'false'
```

使用 `docker build` 來重新建立前端映像。

```bash
docker build --no-cache ./azure-voting-app/azure-vote -t azure-vote-front:v2
```

## <a name="test-application"></a>測試應用程式

建立 Docker 網路。 此網路用於容器之間的通訊。  

```bash
docker network create azure-vote
```

使用 `docker run` 命令執行後端容器映像的執行個體。

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

執行前端容器映像的執行個體。

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front:v2
```

移至 `http://localhost:8080` 以查看已更新的應用程式。 此應用程式需要幾秒鐘的時間初始化。 如果您收到錯誤，請再試一次。

![azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>標記和推送映像

以容器登錄的 loginServer 標記 *azure-vote-front* 映像。

如果您使用 Azure Container Registry，請使用 [az acr list](/cli/azure/acr#list) 命令取得登入伺服器名稱。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 來標記映像，且務必以您的 Azure Container Registry 登入伺服器或公用登錄主機名稱來更新命令。

```bash
docker tag azure-vote-front:v2 <acrLoginServer>/azure-vote-front:v2
```

將映像推送至您的登錄。 以您的 Azure Container Registry 登入伺服器名稱或公用登錄主機名稱取代 `<acrLoginServer>`。

```bash
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-to-kubernetes"></a>將更新部署至 Kubernetes

### <a name="verify-multiple-pod-replicas"></a>驗證多個 POD 複本

若要確保最大執行時間，則應用程式 pod 必須有多個執行個體正在執行中。 請使用 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令驗證此組態。

```bash
kubectl get pod
```

輸出：

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

如果您沒有執行 azure-vote-front 映像的多個 pod，請調整 *azure-vote-front* 部署。


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

### <a name="update-application"></a>更新應用程式

若要更新應用程式，請執行下列命令。 以容器登錄的登入伺服器或主機名稱來更新 `<acrLoginServer>`。

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

若要監視部署，請使用 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令。 部署已更新的應用程式後，您的 pod 會終止並以新的容器映像重建。

```bash
kubectl get pod
```

輸出：

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>測試已更新的應用程式

取得 *azure-vote-front* 服務的外部 IP 位址。

```bash
kubectl get service
```

移至此 IP 位址以查看已更新的應用程式。

![azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，我們已更新應用程式並將此更新推出至 Kubernetes 叢集。 我們已完成下列工作：  

> [!div class="checklist"]
> * 更新了前端應用程式程式碼。
> * 建立了已更新的容器映像。
> * 已將容器映像推送至 Azure Container Registry。
> * 部署了已更新的應用程式。

請前進到下一個教學課程，了解如何利用 Operations Management Suite 監視 Kubernetes。

> [!div class="nextstepaction"]
> [透過 OMS 監視 Kubernetes](./container-service-tutorial-kubernetes-monitor.md)
