---
title: "開始使用 Service Fabric 和 Azure CLI 2.0"
description: "如何在 Azure CLI 2.0 版中使用 Service Fabric 命令模組，包括連線至叢集和管理應用程式"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: zh-tw
ms.lasthandoff: 07/01/2017

---
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

# Service Fabric 和 Azure CLI 2.0

新的 Azure CLI 2.0 現在包含用來管理 Service Fabric 叢集的命令。 本文件包含開始使用 Azure CLI 的步驟。

<a id="install-azure-cli-20" class="xliff"></a>

## 安裝 Azure CLI 2.0

Azure CLI 現在包含用來管理以及與 Service Fabric 叢集互動的命令。 您可以依照[標準安裝流程](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)取得最新的 Azure CLI。

如需詳細資訊，請查看 [Azure CLI 2.0 文件](https://docs.microsoft.com/en-us/cli/azure/overview)

<a id="cli-syntax" class="xliff"></a>

## CLI 語法

在 Azure CLI 中，所有的 Azure Service Fabric 命令前面都會加上 `az sf`。 有關可用命令的詳細資訊，您可以執行 `az sf -h` 以取得一般資訊。 或者，您可以執行 `az sf <command> -h` 以取得單一命令的詳細說明。

CLI 中的 Azure Service Fabric 命令會遵循命名模式

```azurecli
az sf <object> <action>
```

在這裡，`<object>` 是 `<action>` 的目標。

<a id="selecting-a-cluster" class="xliff"></a>

## 選取叢集

在您開始執行任何作業之前，必須選取要連線的叢集。 例如，請參閱下列程式碼片段以連線至不安全的叢集。

> [!WARNING]
> 請勿於生產環境中使用不安全的 Service Fabric 叢集

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

叢集端點必須前置 `http` 或 `https`，且應包含 HTTP 閘道的連接埠。 此連接埠和位址等同於 Service Fabric Explorer 的 URL。

針對以憑證保護的叢集，支援未加密的 `pem`，或 `crt` 和 `key` 檔案。

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

如需詳細資訊，請參閱[連線至安全叢集的詳細文件](service-fabric-connect-to-secure-cluster.md)。

> [!NOTE]
> 選取的命令不會在回傳之前執行任何要求。 若要確認是否已正確指定叢集，請執行命令例如 `az sf cluster health`，並檢查該命令未傳回任何錯誤。

<a id="performing-basic-operations" class="xliff"></a>

## 執行基本作業

叢集連線資訊會跨不同的 Azure CLI 工作階段保存。 選取 Service Fabric 叢集後，就可以執行任何 Service Fabric 命令。

例如，若要取得 Service Fabric 叢集的健康情況狀態，請執行下列命令

```azurecli
az sf cluster health
```

命令會產生下列輸出，假設 Azure CLI 組態中已指定 JSON 輸出

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

<a id="tips-and-faq" class="xliff"></a>

## 秘訣和常見問題集

以下提供一些實用資訊，以解決在 Azure CLI 中使用 Service Fabric 命令遇到的問題

<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

### 將憑證從 PFX 轉換為 PEM

Azure CLI 支援以 PEM (副檔名 `.pem`) 檔案作為用戶端憑證。 如果使用 Windows 的 PFX 檔案，必須將這些憑證轉換為 PEM 格式。 若要從 PFX 檔案轉換為 PEM 檔案，請使用下列命令︰

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

請參閱[OpenSSL 文件](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html)以取得詳細資訊。

<a id="connection-issues" class="xliff"></a>

### 連接問題

執行作業時，您可能會遇到下列錯誤：

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

在此情況下，請仔細檢查指定的叢集端點是否可以連線且正在接聽。 另外請確認 Service Fabric Explorer 的 UI 在該主機和連接埠上可以連線。 使用 `az sf cluster select` 更新端點。

<a id="getting-detailed-logs" class="xliff"></a>

### 取得詳細記錄

偵錯或報告問題時，包含詳細記錄會很有幫助。 Azure CLI 包含全域 `--debug` 旗標，可增加記錄的詳細資訊。

<a id="command-help-and-syntax" class="xliff"></a>

### 命令的說明和語法

Service Fabric 命令與 Azure CLI 遵循相同的慣例。 指定 `-h` 旗標以取得有關特定命令或一群命令的說明。 例如：

```azurecli
az sf application -h
```

或

```azurecli
az sf application create -h
```

