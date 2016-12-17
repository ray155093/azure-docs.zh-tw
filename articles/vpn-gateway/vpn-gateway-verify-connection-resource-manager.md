---
title: "驗證閘道連線 | Microsoft Docs"
description: "本文說明如何在 Resource Manager 部署模型中驗證虛擬連線"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 420b193d558d00f574d24c87ab9db6e9e757a952


---
# <a name="verify-a-gateway-connection"></a>驗證閘道連線
您可以用數種不同的方式驗證閘道連線。 本文將說明如何使用 Azure 入口網站和 PowerShell 來驗證 Resource Manager 閘道連線的狀態。

## <a name="verify-using-powershell"></a>使用 PowerShell 進行確認
您必須安裝最新版的 Azure 資源管理員 PowerShell Cmdlet。 如需有關安裝 PowerShell Cmdlet 的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。 如需使用 Resource Manager Cmdlet 的詳細資訊，請參閱 [搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。

### <a name="step-1-log-in-to-your-azure-account"></a>步驟 1：登入您的 Azure 帳戶
1. 以提高的權限開啟 PowerShell 主控台並連接到您的帳戶。
   
        Login-AzureRmAccount
2. 檢查帳戶的訂用帳戶。
   
        Get-AzureRmSubscription 
3. 指定您要使用的訂用帳戶。
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>步驟 2：確認您的連線
[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal"></a>使用 Azure 入口網站進行確認
[!INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>後續步驟
* 您可以將虛擬機器加入您的虛擬網路。 請參閱 [建立網站的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 以取得相關步驟。




<!--HONumber=Nov16_HO3-->


