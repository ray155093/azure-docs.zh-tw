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
ms.date: 04/05/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a26cbe2172dc27c152246d70b7f6b504ec4a08a5
ms.lasthandoff: 04/12/2017


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>使用 PowerShell 修改區域網路閘道設定
有時候區域網路閘道 AddressPrefix 或 GatewayIPAddress 的設定會變更。 下方指示將協助您修改區域網路閘道設定。 您也可以在 Azure 入口網站中修改下列設定。

## <a name="before-you-begin"></a>開始之前
您必須安裝最新版的 Azure 資源管理員 PowerShell Cmdlet。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。

## <a name="to-modify-ip-address-prefixes"></a>修改 IP 位址首碼
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>修改閘道 IP 位址
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>後續步驟
您可以驗證閘道連線。 請參閱 [驗證閘道連線](vpn-gateway-verify-connection-resource-manager.md)。


