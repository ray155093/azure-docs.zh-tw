---
title: "使用 Resource Manager 和 PowerShell 將 VNet 閘道加入至 ExpressRoute 的虛擬網路 | Microsoft Docs"
description: "本文會逐步引導您完成將 VNet 閘道加入至已經建立的 ExpressRoute 的 Resource Manager VNet"
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 2a0db85dc5699b362aa6a920cc26c40bdfdfc28d
ms.openlocfilehash: 80ffdd66a02f84b844b660e6c88586d4e99ff9e7
ms.lasthandoff: 12/14/2016


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>使用 Resource Manager 和 PowerShell 設定 ExpressRoute 的虛擬網路閘道
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [傳統 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [視訊 - Azure 入口網站](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

本文將逐步引導您完成既存 VNet 的虛擬網路 (VNet) 閘道加入、重新調整和移除步驟。 此組態的步驟是使用 **Resource Manager 部署模型** 建立的 VNet 專用，且將用於 ExpressRoute 組態中。 

**關於 Azure 部署模型**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>開始之前
確認您已安裝此組態所需的 Azure PowerShell Cmdlet (1.0.2 或更新版本)。 如果您尚未安裝 Cmdlet，您必須先安裝，然後才能開始進行組態步驟。 如需安裝 Azure PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>後續步驟
建立 VNet 閘道之後，您可以將 VNet 連結至 ExpressRoute 循環。 請參閱 [將虛擬網路連結到 ExpressRoute 循環](expressroute-howto-linkvnet-arm.md)。


