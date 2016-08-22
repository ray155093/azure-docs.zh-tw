<properties
   pageTitle="修改區域網路閘道 IP 位址首碼和閘道 IP | Microsoft Azure"
   description="本文逐步解說如何變更區域網路閘道的 IP 位址首碼。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# 使用 PowerShell 修改區域網路閘道設定

有時候區域網路閘道 AddressPrefix 或 GatewayIPAddress 的設定會變更。下方指示將協助您修改區域網路閘道設定。您也可以在 Azure 入口網站中修改下列設定。

## 開始之前
	
您必須安裝最新版的 Azure 資源管理員 PowerShell Cmdlet。如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## 修改 IP 位址首碼

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## 修改閘道 IP 位址

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## 後續步驟

您可以驗證閘道連線。請參閱[驗證閘道連線](vpn-gateway-verify-connection-resource-manager.md)。

<!---HONumber=AcomDC_0810_2016-->