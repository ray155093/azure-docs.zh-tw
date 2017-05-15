---
title: "修改區域網路閘道 IP 位址前置詞和 VPN 閘道 IP 位址| Azure| CLI| Microsoft Docs"
description: "本文逐步解說如何使用 Azure CLI 將區域網路閘道的 IP 位址首碼進行變更"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 81c1a6e2357c8d336449224fac841be8dcc2a3cd
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017


---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>使用 Azure CLI 修改區域網路閘道設定

有時候區域網路閘道 AddressPrefix 或 GatewayIPAddress 的設定會變更。 本文將說明如何修改區域網路閘道設定。 您也可以在 Azure 入口網站或 PowerShell 中修改下列設定。

## <a name="before-you-begin"></a>開始之前

安裝 CLI 命令的最新版本 (2.0 版或更新版本)。 如需關於安裝 CLI 命令的資訊，請參閱[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a>修改 IP 位址首碼

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a>修改閘道 IP 位址

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>後續步驟

您可以驗證閘道連線。 請參閱 [驗證閘道連線](vpn-gateway-verify-connection-resource-manager.md)。


