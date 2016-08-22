<properties
   pageTitle="驗證閘道連線 | Microsoft Azure"
   description="本文說明如何在 Resource Manager 部署模型中驗證虛擬連線"
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
   ms.date="08/03/2016"
   ms.author="cherylmc"/>

# 驗證閘道連線

您可以用數種不同的方式驗證閘道連線。本文將說明如何使用 Azure 入口網站和 PowerShell 來驗證 Resource Manager 閘道連線的狀態。


## 開始之前

如果您打算使用 PowerShell，您必須安裝最新版的 Azure Resource Manager PowerShell Cmdlet。如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。如需使用 Resource Manager Cmdlet 的詳細資訊，請參閱[搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。

1. 開啟 PowerShell 主控台並連接到您的帳戶。

		Login-AzureRmAccount

2. 檢查帳戶的訂用帳戶。

		Get-AzureRmSubscription 

3. 指定您要使用的訂用帳戶。

		Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 驗證您的連線


[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## 後續步驟

- 您可以將虛擬機器加入您的虛擬網路。請參閱[建立網站的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)以取得相關步驟。

<!---HONumber=AcomDC_0810_2016-->