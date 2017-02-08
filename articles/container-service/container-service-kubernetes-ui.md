---
title: "在 Azure Container Service 中使用 Kubernetes Web UI | Microsoft Docs"
description: "在 Azure Container Service 中使用 Kubernetes Web UI"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: bcc2d3468c8a560105aa2c2feb0d969ec3cccdcb
ms.openlocfilehash: 9ae6e606bf215d9f53f725899b8ac7c466b781f1


---

# <a name="microsoft-azure-container-service-engine---using-the-kubernetes-web-ui"></a>Microsoft Azure Container Service 引擎 - 使用 Kubernetes Web UI

## <a name="prerequisites"></a>必要條件
本逐步解說假設您已[使用 Azure Container Service 建立 Kubernetes 叢集](container-service-kubernetes-walkthrough.md)。

> [!NOTE]
> Azure Container Service 中的 Kubernetes 支援目前為預覽狀態。
>

同時也假設您已經安裝 azure cli 和 kubectl 工具。

您可以藉由執行下列操作來測試是否已安裝 `az` 工具：

```console
$ az --version
```

如果您尚未安裝 `az` 工具，[這裡](https://github.com/azure/azure-cli#installation)有指示。

您可以藉由執行下列操作來測試是否已安裝 `kubectl` 工具：

```console
$ kubectl version
```

如果您尚未安裝 `kubectl`，可以執行︰

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>概觀

### <a name="connect-to-the-web-ui"></a>連接至 Web UI
您可以藉由執行下列操作啟動 Kubernetes Web UI：

```console
$ az acs kubernetes browse
```

這應該會開啟網頁瀏覽器，設定與安全 proxy (將您的本機電腦連接至 Kubernetes Web UI) 通訊。

### <a name="create-and-expose-a-service"></a>建立和公開服務
在 Kubernetes Web UI 中，您應該會在右上方視窗看到 [建立] 按鈕。

![Kubernetes Create UI](media/k8s/create.png)

這應該會開啟對話方塊，您可以在其中開始建立應用程式。
請為其指定 `hello-nginx` 名稱。 使用 [`nginx`Docker 容器](https://hub.docker.com/_/nginx/)及部署此 Web 服務的三個複本。

![Kubernetes Pod 建立對話方塊](media/k8s/nginx.png)

繼續建立「外部」Kubernetes 服務，將流量負載平衡至我們的三個複本。  選取「外部」並輸入連接埠 80。

![Kubernetes 服務建立對話方塊](media/k8s/service.png)

最後，按下 [部署] 按鈕以部署這些容器和服務。

![Kubernetes 部署](media/k8s/deploy.png)

### <a name="view-your-containers"></a>檢視您的容器
按 [部署] 之後，UI 會在您的服務部署時顯示檢視︰

![Kubernetes 狀態](media/k8s/status.png)

您可以在 UI 左手邊的圓圈中看到每個 Kubernetes 物件的狀態。 如果是部分完整的圓圈，則物件仍然在部署中。 完整部署物件時，它會顯示綠色的核取記號︰

![Kubernetes 已部署](media/k8s/deployed.png)

一旦所有項目都在執行，您可以按一下其中一個 Pod，以查看執行中相關的 Web 服務詳細資料

![Kubernetes Pods](media/k8s/pods.png)

在 Pod 特定檢視中，您可以看到 Pod 中容器的相關資訊，以及這些容器所使用的 CPU 和記憶體資源︰

![Kubernetes 資源](media/k8s/resources.png)

如果看不到資源，您可能需要等候幾分鐘讓監視資料傳播。

您也可以按一下 [記錄檔] 連結，以查看容器的記錄檔︰

![Kubernetes 記錄檔](media/k8s/logs.png)

### <a name="viewing-your-service"></a>檢視您的服務
除了執行您的容器，Kubernetes UI 建立外部 `Service`，佈建負載平衡器將流量帶向叢集中的容器。

您可以在左側導覽窗格中按一下 [服務] 以檢視所有服務 (目前應該只有一個)

![Kubernetes 服務](media/k8s/service-deployed.png)

在該檢視中，您應該能夠看到已經配置給您的服務外部 IP 位址。
如果您按一下該 IP 位址，您應該會看到在負載平衡器後方執行的 nginx 容器。

![nginx 檢視](media/k8s/nginx-page.png)

### <a name="resizing-your-service"></a>調整您的服務大小
除了在 UI 中檢視您的物件，您也可以編輯和更新 Kubernetes API 物件。

首先，請移至服務的 `Deployment`，按一下左側導覽窗格中的 [部署]。

一旦您在該檢視中，按一下 ReplicaSet，然後再按一下上方導覽列中的 [編輯] 按鈕︰

![Kubernetes 編輯](media/k8s/edit.png)

將 `spec.replicas` 欄位編輯為 '2'，然後按 [更新]。

藉由刪除您的其中一個 Pod，讓複本的數目卸除為兩個。

 




<!--HONumber=Dec16_HO3-->


