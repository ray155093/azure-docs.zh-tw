<properties
   pageTitle="使用 Resource Manager 和 PowerShell 將 VNet 閘道加入至 ExpressRoute 的虛擬網路 | Microsoft Azure"
   description="本文會逐步引導您完成將 VNet 閘道加入至已經建立的 ExpressRoute 的 Resource Manager VNet"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/22/2016"
   ms.author="charwen"/>

# 使用 Resource Manager 和 PowerShell 設定 ExpressRoute 的虛擬網路閘道


> [AZURE.SELECTOR]
- [PowerShell - 資源管理員](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - 傳統](expressroute-howto-add-gateway-classic.md)


本文將逐步引導您完成既存 VNet 的虛擬網路 (VNet) 閘道加入、重新調整和移除步驟。此組態的步驟是使用**Resource Manager 部署模型**建立的 VNet 專用，且將用於 ExpressRoute 組態中。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 開始之前

確認您已安裝此組態所需的 Azure PowerShell Cmdlet (1.0.2 或更新版本)。如果您尚未安裝 Cmdlet，您必須先安裝，然後才能開始進行組態步驟。如需安裝 Azure PowerShell 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

	
## 後續步驟

建立 VNet 閘道之後，您可以將 VNet 連結至 ExpressRoute 循環。請參閱[將虛擬網路連結到 ExpressRoute 循環](expressroute-howto-linkvnet-arm.md)。

<!---HONumber=AcomDC_0928_2016-->