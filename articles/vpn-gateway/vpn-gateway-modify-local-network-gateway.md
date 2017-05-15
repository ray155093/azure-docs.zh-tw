---
title: "修改區域網路閘道 IP 位址前置詞和 VPN 閘道 IP 位址| Azure| PowerShell| Microsoft Docs"
description: "本文逐步解說如何變更區域網路閘道的 IP 位址首碼。"
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
ms.date: 04/26/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e64ec9c7382cc635be8f5a5d3b547115d61caa2b
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>使用 PowerShell 修改區域網路閘道設定

有時候區域網路閘道 AddressPrefix 或 GatewayIPAddress 的設定會變更。 本文將說明如何修改區域網路閘道設定。 您也可以在 Azure 入口網站中或使用 Azure CLI 修改下列設定。

## <a name="before-you-begin"></a>開始之前

安裝最新版的 Azure Resource Manager PowerShell Cmdlet。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。

## <a name="modify-ip-address-prefixes"></a>修改 IP 位址首碼

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a>修改閘道 IP 位址

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>後續步驟

您可以驗證閘道連線。 請參閱 [驗證閘道連線](vpn-gateway-verify-connection-resource-manager.md)。
