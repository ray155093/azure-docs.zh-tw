---
title: "開始使用 Azure Service Fabric 和 Azure CLI 2.0"
description: "了解如何在 Azure CLI 2.0 版中使用 Azure Service Fabric 命令模組。 了解如何連線到叢集，以及如何管理應用程式。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: zh-tw
ms.lasthandoff: 07/14/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Azure Service Fabric 和 Azure CLI 2.0

Azure 命令列工具 (Azure CLI) 2.0 版包含可協助您管理 Azure Service Fabric 叢集的命令。 了解如何開始使用 Azure Service Fabric 和 Service Fabric。

## <a name="install-azure-cli-20"></a>安裝 Azure CLI 2.0

您可以使用 Azure CLI 2.0 命令來與 Service Fabric 叢集互動和進行管理。 若要取得最新版的 Azure CLI，請遵循 [Azure CLI 2.0 標準安裝程序](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)。

如需詳細資訊，請參閱 [Azure CLI 2.0 概觀](https://docs.microsoft.com/en-us/cli/azure/overview)。

## <a name="azure-cli-syntax"></a>Azure CLI 語法

在 Azure CLI 中，所有 Service Fabric 命令前面都會加上 `az sf`。 如需有關可用命令的一般資訊，請使用 `az sf -h`。 如需單一命令的說明，請使用 `az sf <command> -h`。

Azure CLI 中的 Service Fabric 命令會遵循此命名模式：

```azurecli
az sf <object> <action>
```

`<object>` 是 `<action>` 的目標。

## <a name="select-a-cluster"></a>選取叢集

在您執行任何作業之前，必須選取要連線的叢集。 如需範例，請參閱下列程式碼。 此程式碼會連線至不安全的叢集。

> [!WARNING]
> 請勿於生產環境中使用不安全的 Service Fabric 叢集。

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

叢集端點前面必須加上 `http` 或 `https`。 它必須包含 HTTP 閘道的連接埠。 此連接埠和位址等同於 Service Fabric Explorer URL。

對於使用憑證保護的叢集，您可以使用未加密的 .pem 檔案或 .crt 和 .key 檔案。 例如：

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

如需詳細資訊，請參閱[連線到 Azure Service Fabric 叢集](service-fabric-connect-to-secure-cluster.md)。

> [!NOTE]
> `select` 命令不會在要求傳回前採取動作。 若要確認您已正確指定叢集，請使用 `az sf cluster health` 之類的命令。 確認此命令不會傳回任何錯誤。

## <a name="basic-operations"></a>基本作業

叢集連線資訊會跨多個 Azure CLI 工作階段保存。 選取 Service Fabric 叢集後，您可以在此叢集上執行任何 Service Fabric 命令。

例如，若要取得 Service Fabric 叢集健康情況，請使用下列命令：

```azurecli
az sf cluster health
```

此命令會產生下列輸出 (假設已在 Azure CLI 組態中指定 JSON 輸出)：

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

## <a name="tips-and-troubleshooting"></a>秘訣與疑難排解

如果您在 Azure CLI 中使用 Service Fabric 命令時遇到問題，您會發現下列資訊很有幫助。

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>將憑證從 PFX 轉換為 PEM 格式

Azure CLI 支援以 PEM (.pem 副檔名) 檔案作為用戶端憑證。 如果您從 Windows 使用 PFX 檔案，則必須將這些憑證轉換成 PEM 格式。 若要將 PFX 檔案轉換為 PEM 檔案，請使用下列命令︰

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

如需詳細資訊，請參閱 [OpenSSL 文件](https://www.openssl.org/docs/)。

### <a name="connection-issues"></a>連接問題

某些作業可能會產生下列訊息：

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

確認指定的叢集端點可以使用且正在接聽。 另外確認 Service Fabric Explorer 的 UI 可在該主機和連接埠上使用。 若要更新端點，請使用 `az sf cluster select`。

### <a name="detailed-logs"></a>詳細記錄

當您偵錯或回報問題時，詳細記錄通常很有幫助。 Azure CLI 提供全域 `--debug` 旗標，以增加記錄檔的詳細程度。

### <a name="command-help-and-syntax"></a>命令的說明和語法

Service Fabric 命令與 Azure CLI 遵循相同的慣例。 如需特定命令或一組命令的說明，請使用 `-h` 旗標：

```azurecli
az sf application -h
```

以下是另一個範例︰

```azurecli
az sf application create -h
```

