---
title: "Azure Container Service 教學課程 - 部署應用程式 | Microsoft Docs"
description: "Azure Container Service 教學課程 - 部署應用程式"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 94e1eacd6864a7fcaf753a11e855f6e69aee0f98
ms.contentlocale: zh-tw
ms.lasthandoff: 07/08/2017

---

# <a name="run-applications-in-kubernetes"></a>在 Kubernetes 中執行應用程式

在本教學課程中，已在 Kubernetes 叢集中部署一個應用程式範例。 完成的步驟包括：

> [!div class="checklist"]
> * Kubernetes 物件導入
> * 下載 Kubernetes 資訊清單檔
> * 在 Kubernetes 中執行應用程式
> * 測試應用程式

在後續的教學課程中，會向外延展、更新此應用程式，並監視 Kubernetes 叢集。

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已將應用程式封裝成容器映像、將這些映像上傳至 Azure Container Registry，並已建立 Kubernetes 叢集。 如果您尚未完成這些步驟，而想要跟著做，請回到[教學課程 1 – 建立容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

本教學課程至少需要一個 Kubernetes 叢集。

## <a name="kubernetes-objects"></a>Kubernetes 物件

將容器化應用程式部署至 Kubernetes 時，會建立許多不同的 Kubernetes 物件。 每個物件代表叢集的預期狀態。 例如，一個簡單的應用程式可能包含 Pod (密切相關容器的群組)、永久性磁碟區 (網路儲存體)，以及部署 (負責管理應用程式的狀態)。 

如需有關所有 Kubernetes 物件的詳細資料，請參閱 kubernetes.io 上的 [Kubernetes 概念](https://kubernetes.io/docs/concepts/) \(英文\)。

## <a name="get-manifest-files"></a>取得資訊清單檔

針對此教學課程，會使用 Kubernetes 資訊清單來部署 Kubernetes 物件。 Kubernetes 資訊清單是一個包含物件組態指示的 YAML 檔案。

「Azure 投票」應用程式存放庫中提供了本教學課程中每個物件的資訊清單檔，這是在上一個教學課程中複製的存放庫。 如果您尚未這麼做，請使用下列命令來複製存放庫： 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

在所複製之存放庫的下列目錄底下，即可找到資訊清單檔。

```bash
/azure-voting-app/kubernetes-manifests/
```

## <a name="run-application"></a>執行應用程式

### <a name="storage-objects"></a>儲存體物件

由於「Azure 投票」應用程式包含 MySQL 資料庫，因此您會想要將資料庫檔案儲存在可供 Pod 之間共用的磁碟區上。 在這個組態中，如果重新建立 MySQL Pod，並不會影響到資料庫檔案。

`storage-resources.yaml` 資訊清單檔會建立[儲存體類別物件](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#storageclasses)，此物件定義了永久性磁碟區的建立方式和位置。 有數個磁碟區外掛程式可供 Kubernetes 使用。 在此案例中，使用的是 [Azure 磁碟](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#azure-disk)外掛程式。 

此外，也會建立[永久性磁碟區宣告](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)，此宣告會設定一個儲存體 (使用儲存體類別)，然後將它指派給某個 Pod。

執行下列命令來建立儲存體物件。

```bash
kubectl create -f storage-resources.yaml
```

完成之後，就會建立虛擬磁碟並連結到產生的 Kubernetes Pod。 系統會自動在與 Kubernetes 叢集位於相同資源群組且組態與儲存體類別物件 (Standard_LRS) 相同的儲存體帳戶中，建立此虛擬磁碟。

### <a name="secure-sensitive-values"></a>保護敏感值

[Kubernetes 祕密](https://kubernetes.io/docs/concepts/configuration/secret/)可為敏感資訊提供安全的儲存體。 藉由使用 `pod-secrets.yaml` 檔案，會將「Azure 投票」資料庫認證儲存在祕密中。 

執行下列命令來建立祕密物件。

```bash
kubectl create -f pod-secrets.yaml
```

### <a name="create-deployments"></a>建立部署

[Kubernetes 部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)可管理 Kubernetes Pod 的狀態。 此管理包含確保預期的複本計數正在執行、已掛接磁碟區，以及使用適當的容器映像等。

`azure-vote-deployment.yaml` 資訊清單檔會為「Azure 投票」應用程式的前端和後端部分建立部署。

#### <a name="update-image-names"></a>更新映像名稱

如果使用 Azure Container Registry 來儲存映像，就必須在映像名稱前面加上 ACR 登入伺服器名稱。

請使用 [az acr list](/cli/azure/acr#list) 命令來取得 ACR 登入伺服器名稱。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

更新 `azure-vote-deployment.yaml` 檔案中的 *azure-vote-front* 和 *azure-vote-back* 容器映像名稱。

前端映像名稱範例：

```yaml
containers:
      - name: azure-vote-front
        image: <acrLoginServer>/azure-vote-front:v1
```

後端映像名稱範例：

```yaml
containers:
      - name: azure-vote-back
        image: <acrLoginServer>/azure-vote-front:v1
```

#### <a name="create-deployment-objects"></a>建立部署物件

執行 [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) 來啟動「Azure 投票」應用程式。

```bash
kubectl create -f azure-vote-deployment.yaml
```

### <a name="expose-application"></a>公開應用程式

[Kubernetes 服務](https://kubernetes.io/docs/concepts/services-networking/service/)會定義 Pod 的存取方式。 使用「Azure 投票」應用程式時，必須可透過部署名稱在內部存取後端部署。 必需可透過網際網路存取前端部署。 「Azure 投票」應用程式服務組態定義於 `services.yaml` 資訊清單檔中。

執行下列命令來建立服務。

```bash
kubectl create -f services.yaml
```

## <a name="test-application"></a>測試應用程式

建立完所有物件之後，便可透過 azure-vote-front 服務的外部 IP 位址存取此應用程式。 建立此服務可能需要花費幾分鐘的時間。 若要監視服務建立程序，請執行下列命令。 當 *azure-vote-front* 服務的 *EXTERNAL-IP* 值從 *pending* 切換成 IP 位址時，即表示此應用程式已準備就緒，透過該外部 IP 位址即可存取此應用程式。

```bash
kubectl get service -w
```

輸出：

```bash
NAME               CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-back    10.0.77.30    <none>          3306/TCP       4m
azure-vote-front   10.0.120.96   40.71.227.124   80:31482/TCP   4m
kubernetes         10.0.0.1      <none>          443/TCP        7m
```

在服務準備就緒之後，請執行 `CTRL-C` 來終止 kubectl 監看式。

請瀏覽至所傳回的外部 IP 位址來查看此應用程式。

![Azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-tutorials/vote-app-external.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，是將 Azure 投票應用程式部署到 Azure Container Service Kubernetes 叢集。 完成的工作包括：  

> [!div class="checklist"]
> * Kubernetes 物件導入
> * 下載 Kubernetes 資訊清單檔
> * 在 Kubernetes 中執行應用程式
> * 測試應用程式

請前往下一個教學課程，以了解如何調整 Kubernetes 應用程式和基礎 Kubernetes 基礎架構。 

> [!div class="nextstepaction"]
> [調整 Kubernetes 應用程式和基礎結構](./container-service-tutorial-kubernetes-scale.md)
