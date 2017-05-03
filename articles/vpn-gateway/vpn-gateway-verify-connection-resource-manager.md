---
title: "確認 VPN 閘道連線 | Microsoft Docs"
description: "本文說明如何確認虛擬網路「VPN 閘道」連線。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 0e9fa1b1397c60985de9d2e60b3f01146036801f
ms.lasthandoff: 04/26/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>確認 VPN 閘道連線

本文說明如何確認 Resource Manager 和傳統部署模型的 VPN 閘道連線。

## <a name="verify-using-the-azure-portal"></a>使用 Azure 入口網站進行確認

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>使用 PowerShell 進行確認

若要使用 PowerShell 進行確認，請安裝最新版的 Azure Resource Manager PowerShell Cmdlet。 如需有關安裝 PowerShell Cmdlet 的資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 如需使用 Resource Manager Cmdlet 的詳細資訊，請參閱 [搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。

### <a name="log-in-to-your-azure-account"></a>登入您的 Azure 帳戶
1. 以提高的權限開啟 PowerShell 主控台並連接到您的帳戶。

  ```powershell
  Login-AzureRmAccount
  ```
2. 檢查帳戶的訂用帳戶。

  ```powershell
  Get-AzureRmSubscription
  ``` 
3. 指定您要使用的訂用帳戶。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

### <a name="verify-your-connection"></a>確認您的連接

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-cli"></a>使用 Azure CLI 進行確認

若要使用 Azure CLI 進行確認，請安裝最新版的 CLI 命令 (2.0 或更新版本)。 如需關於安裝 CLI 命令的資訊，請參閱[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="log-in-to-your-azure-account"></a>登入您的 Azure 帳戶

1. 使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

  ```azurecli
  az login
  ```
2. 如果您有多個 Azure 訂用帳戶，請列出帳戶的所有訂用帳戶。

  ```azurecli
  Az account list --all
  ```
3. 指定您要使用的訂用帳戶。

  ```azurecli
  Az account set --subscription
  <replace_with_your_subscription_id>
  ```

### <a name="verify-your-connection"></a>確認您的連接

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>使用 Azure 入口網站進行確認 (傳統)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>使用 PowerShell 進行確認 (傳統)
若要使用 PowerShell 進行確認，請安裝最新版的 Azure PowerShell Cmdlet。 請務必將 Resource Manager 和「服務管理」(SM) 版本都下載並安裝。 如需有關安裝 PowerShell Cmdlet 的資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 

### <a name="log-in-to-your-azure-account"></a>登入您的 Azure 帳戶
1. 以提高的權限開啟 PowerShell 主控台並連接到您的帳戶。

  ```powershell
  Login-AzureRmAccount
  ```
2. 檢查帳戶的訂用帳戶。

  ```powershell
  Get-AzureRmSubscription 
  ```
3. 指定您要使用的訂用帳戶。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. 登入以使用適用於傳統部署模型的「服務管理」Cmdlet。

  ```powershell
  Add-AzureAccount
  ```

### <a name="verify-your-connection"></a>確認您的連接
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>後續步驟
* 您可以將虛擬機器加入您的虛擬網路。 請參閱 [建立網站的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 以取得相關步驟。


