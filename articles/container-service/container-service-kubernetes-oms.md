---
title: "監視 Azure Kubernetes 叢集 - Operations Management | Microsoft Docs"
description: "使用 Microsoft Operations Management Suite 監視 Azure Container Service 中的 Kubernetes 叢集"
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
ms.sourcegitcommit: 4e4a4f4e299dc2747eb48bbd2e064cd80783211c
ms.openlocfilehash: 46240f3dc99a8c8a103a1e7919ad4f5e7a8ea62a
ms.lasthandoff: 04/04/2017


---

# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>使用 Microsoft Operations Management Suite (OMS) 監視 Azure Container Service 叢集

## <a name="prerequisites"></a>必要條件
本逐步解說假設您已[使用 Azure Container Service 建立 Kubernetes 叢集](container-service-kubernetes-walkthrough.md)。

同時也假設您已經安裝 `az` Azure cli 和 `kubectl` 工具。

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

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>使用 Operations Management Suite (OMS) 監視容器

Microsoft Operations Management (OMS) 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。 容器解決方案是 OMS Log Analytics 中的解決方案，可協助您在單一位置檢視容器詳細目錄、效能和記錄檔。 您可以在集中式位置檢視記錄檔以稽核、對容器進行疑難排解，並尋找壟斷及佔用過量主機資源的容器。

![](media/container-service-monitoring-oms/image1.png)

如需容器解決方案的詳細資訊，請參閱[容器解決方案 Log Analytics](../log-analytics/log-analytics-containers.md)。

## <a name="installing-oms-on-kubernetes"></a>在 Kubernetes 上安裝 OMS

### <a name="obtain-your-workspace-id-and-key"></a>取得您的工作區識別碼和金鑰
為了使 OMS 代理程式能與服務溝通，必須使用工作區識別碼和工作區金鑰來設定它。 若要取得工作區識別碼和金鑰，您必須在 <https://mms.microsoft.com> 建立一個 OMS 帳戶。
請依步驟指示建立帳戶。 建立帳戶之後，您必須依序按一下 [設定]、[連接的來源]、[Linux 伺服器]，以取得您的識別碼和金鑰，如下所示。

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>使用 DaemonSet 安裝 OMS 代理程式
DaemonSet 是 Kubernetes 用來在叢集中每個主機上執行容器的單一執行個體。
它們非常適合用來執行監視代理程式。

這是 [DaemonSet YAML 檔案](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)。 將它儲存到名為 `oms-daemonset.yaml` 的檔案，並將檔案中的 `WSID` 和 `KEY` 的預留位置值取代為您的工作區識別碼和金鑰。

當您將工作區識別碼和金鑰新增至 DaemonSet 組態之後，就可以使用 `kubectl` 命令列工具在您的叢集上安裝 OMS：

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="conclusion"></a>結論
就這麼簡單！ 幾分鐘後，您應該可以看到資料流向您的 OMS 儀表板。

