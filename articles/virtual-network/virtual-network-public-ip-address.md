---
title: "Azure 公用 IP 位址 | Microsoft Docs"
description: "了解如何建立、修改及刪除公用 IP 位址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5274ffe641768e0725623c2acf38d432a97eea9f
ms.lasthandoff: 04/03/2017


---

# <a name="public-ip-addresses"></a>公用 IP 位址

了解公用 IP 位址，以及如何建立、變更和刪除它們。 公用 IP 位址是可加以設定的資源。 將公用 IP 位址指派給其他 Azure 資源，能夠：
- 對以下資源進行輸入網際網路連線：例如 Azure 虛擬機器 (VM)、Azure 虛擬機器擴展集、Azure VPN 閘道和網際網路面向的 Azure Load Balancer。
- 對網際網路進行未網路位址轉譯 (NAT) 的輸出連線。 例如，VM 不需有指派的公用 IP 位址，即可對網際網路進行輸出通訊，但其位址是由 Azure 網路位址轉譯。 若要深入了解 Azure 資源的輸出連線，請閱讀[了解輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

本文說明如何使用公用 IP 位址。 本文適用於透過 Azure Resource Manager 部署模型部署的資源。 雖然可以將公用 IP 位址指派給透過傳統部署模型部署的資源，但位址的套用方式與透過 Resource Manager 時的方式不同。 如果您不熟悉這兩種模型之間的差異，請閱讀[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

本文的其餘各節會列出完成所有公用 IP 位址相關工作的步驟。 每一節會列出︰
- 在 Azure 入口網站中完成工作的步驟。 若要完成這些步驟，您必須登入 [Azure 入口網站](http://portal.azure.com)。 如果您沒有帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 使用 Azure PowerShell 搭配命令參考連結來完成工作的命令。 完成[如何安裝及設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)一文中的步驟，以安裝和設定 PowerShell。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 使用 Azure 命令列介面 (CLI) 搭配命令參考連結來完成工作的命令。 完成[如何安裝及設定 Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟，以安裝 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> -h`。

公用 IP 位址需要少許費用。 若要檢視價格，請閱讀 [IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses)頁面。 您可以在訂用帳戶內使用的公用 IP 位址數目有一些限制。 若要檢視限制，請閱讀 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。 

完成下列各節中建立、變更或刪除公用 IP 位址資源的步驟︰

## <a name="create"></a>建立公用 IP 位址

若要建立公用 IP 位址，請完成下列步驟：
1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「公用 ip 位址」。 當「公用 IP 位址」出現於搜尋結果時，按一下它。
3. 在顯示的 [公用 IP 位址] 刀鋒視窗中按一下 [+ 新增]。
4. 在顯示的 [建立公用 IP 位址] 刀鋒視窗中輸入或選取下列設定的值，然後按一下 [建立]：

    |**設定**|必要？|**詳細資料**|
    |---|---|---|
    |**名稱**|是|名稱必須是您選取的資源群組中唯一的名稱。|
    |**IP 位址指派**|是|**動態︰**只有在公用 IP 位址與 VM 所連結的 NIC 相關聯且該 VM 第一次啟動之後，才會指派動態位址。 如果連結 NIC 的 VM 已停止 (已解除配置)，則可變更動態位址。 如果 VM 已重新啟動或停止 (但未解除配置)，則位址維持不變。 **靜態︰**建立公用 IP 位址時會指派靜態位址。 即使 VM 處於已停止 (已解除配置) 狀態，靜態位址也不會變更。 只有在刪除 NIC 後才會釋出位址。 您可以在建立 NIC 之後，變更指派方法。|
    |**閒置逾時 (分鐘)**|否|不需依賴用戶端傳送保持連線訊息，讓 TCP 或 HTTP 連線保持開啟的分鐘數。|
    |**DNS 名稱標籤**|否|在您建立名稱的 Azure 位置 (跨越所有訂用帳戶和所有位置) 中必須是唯一的。 Azure 公用 DNS 服務會自動登錄名稱和 IP 位址，以便您連線至具有此名稱的資源。 Azure 會將 *location.cloudapp.azure.com* (其中 location 是您選取的位置) 附加至您提供的名稱，以建立完整的 DNS 名稱。 |
    |**訂用帳戶**|是|所在的訂用帳戶必須與您想要與公用 IP 位址建立關聯的資源相同。|
    |**資源群組**|是|所在的資源群組可以與您想要與公用 IP 位址建立關聯的資源相同或不同。|
    |**位置**|是|所在的位置必須與您想要與公用 IP 位址建立關聯的資源相同。|

|**工具**|**命令**|
|---|---|
|**CLI**|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change"></a>變更或刪除公用 IP 位址的設定

若要變更或刪除公用 IP 位址，請完成下列步驟：

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「公用 ip 位址」。 當「公用 IP 位址」出現於搜尋結果時，按一下它。
3. 在顯示的 [公用 IP 位址] 刀鋒視窗中，按一下您要變更設定或刪除的公用 IP 位址名稱。
4. 在針對公用 IP 位址顯示的刀鋒視窗中，根據您要刪除或變更公用 IP 位址，完成下列其中一個選項。
    - **刪除：**若要刪除公用 IP 位址，請在刀鋒視窗的 [概觀] 區段中按一下 [刪除]。 如果位址目前與 IP 組態相關聯，則無法加以刪除。 如果位址目前與組態相關聯，請按一下 [解除關聯] 來解除位址與 IP 組態的關聯。
    - **變更︰**按一下 [組態]。 使用[建立公用 IP 位址](#create)一節中步驟 4 的資訊來變更設定。 若要將指派從靜態變更為動態，您必須先解除公用 IP 位址與相關聯 IP 組態的關聯。 您可以接著將指派方法變更為動態，然後按一下 [關聯] 讓 IP 位址與相同 IP 組態、不同組態建立關聯，您也可以讓它解除關聯。 若要解除公用 IP 位址的關聯，請在 [概觀] 區段中，按一下 [解除關聯]。

>[!WARNING]
>當您將指派方法從靜態變更為動態時，您會遺失已指派給公用 IP 位址的 IP 位址。 雖然 Azure 公用 DNS 伺服器會維護靜態或動態位址與任何 DNS 名稱標籤 (如果您定義一個位置) 之間的對應，但是動態 IP 位址可能會在 VM 處於停止 (解除配置) 狀態後啟動時變更。 若要防止位址變更，請指派靜態 IP 位址。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) 用以更新；[az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) 用以刪除|
|**PowerShell**|[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) 用以更新；[Remove-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) 用以刪除|

## <a name="next-steps"></a>接續步驟
請在建立下列 Azure 資源時，指派公用 IP 位址︰

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器
- [網際網路面向的 Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 應用程式閘道](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 Azure VPN 閘道的站對站連線](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
