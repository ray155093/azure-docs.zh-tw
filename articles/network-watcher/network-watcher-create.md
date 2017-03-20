---
title: "建立 Azure 網路監看員執行個體 | Microsoft Docs"
description: "本頁提供使用入口網站和 Azure REST API 建立網路監看員執行個體的步驟"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: bdaf5afff8466a189afe96517135b1b347b85550
ms.lasthandoff: 03/04/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>建立 Azure 網路監看員執行個體

網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 案例層級監視可讓您在端對端網路層級檢視診斷問題。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="register-the-preview-capability"></a>註冊預覽功能

「網路監看員」目前為預覽版，若要使用「網路監看員」的功能，必須註冊該功能。 若要這麼做，請執行下列 PowerShell 範例：

```powershell
Register-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

若要確認註冊是否成功，請執行下列 Powershell 範例︰

```powershell
Get-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace  Microsoft.Network
```

如果已正確註冊該功能，輸出應該會與以下相符︰

```
FeatureName         ProviderName      RegistrationState
-----------         ------------      -----------------
AllowNetworkWatcher Microsoft.Network Registered
```

### <a name="instructions-for-cli"></a>適用於 CLI 的指示

進行註冊

```CLI
azure feature register  Microsoft.Network AllowNetworkWatcher
azure provider register Microsoft.Network
```

若要確認註冊是否成功，請執行下列 CLI 命令︰

```CLI
azure feature show Microsoft.Network AllowNetworkWatcher
```

如果已正確註冊該功能，輸出應該會與以下相符︰

```CLI
info:    Executing command feature show
data:    Feature Name:       AllowNetworkWatcher
data:    Provider Name:      Microsoft.Network
data:    Registration State: Registered
info:    feature show command OK
```

## <a name="create-a-network-watcher-in-the-portal"></a>在入口網站中建立網路監看員

瀏覽至 [更多服務] > [網路] > [網路監看員]。 您可以選取要啟用網路監看員的所有訂用帳戶。 此動作會在每個可用區域建立網路監看員。

![建立網路監看員][1]

## <a name="create-a-network-watcher-with-powershell"></a>使用 PowerShell 建立網路監看員

若要建立網路監看員執行個體，請執行下列範例︰

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>使用 REST API 建立網路監看員

使用 ARMclient 透過 PowerShell 呼叫 REST API。 您可以在 chocolatey 的 [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient) 上找到 ARMClient

### <a name="log-in-with-armclient"></a>使用 ARMClient 登入

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>建立網路監看員

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>後續步驟

您已擁有網路監看員執行個體，接下來您可以了解可用的功能︰

* [拓撲](network-watcher-topology-overview.md)
* [封包擷取](network-watcher-packet-capture-overview.md)
* [IP 流量驗證](network-watcher-ip-flow-verify-overview.md)
* [下一個躍點](network-watcher-next-hop-overview.md)
* [安全性群組檢視](network-watcher-security-group-view-overview.md)
* [NSG 流量記錄](network-watcher-nsg-flow-logging-overview.md)
* [虛擬網路閘道疑難排解](network-watcher-troubleshoot-overview.md)

建立「網路監看員」執行個體之後，即可依照[建立由警示觸發的封包擷取 (英文)](network-watcher-alert-triggered-packet-capture.md) 一文來設定封包擷取

[1]: ./media/network-watcher-create/figure1.png












