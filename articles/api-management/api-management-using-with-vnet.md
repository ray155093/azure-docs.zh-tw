---
title: "如何將 Azure API 管理與虛擬網路搭配使用"
description: "了解如何在「Azure API 管理」中設定虛擬網路連線，然後透過它存取 Web 服務。"
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7dfbf0e460dc8de13a4a71c1f925c93f6238df5d


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>如何將 Azure API 管理與虛擬網路搭配使用
「Azure 虛擬網路」(VNET) 可讓您將任何 Azure 資源，放在您控制存取權的非網際網路可路由網路中。 然後，可以使用各種 VPN 技術，將這些網路連接到您的內部部署網路。 若要深入了解「Azure 虛擬網路」，請從以下資訊著手：[Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

「Azure API 管理」可以連接到「虛擬網路」(VNET)，因此它可以存取該網路內的後端服務，也因此開發人員入口網站和 API 閘道在該網站內可供存取。

## <a name="enable-vpn"> </a>啟用 VNET 連線
> VNET 連線僅適用於「進階」和「開發人員」層。 若要在不同的層之間切換，請在「Azure 入口網站」中開啟您的「API 管理」服務，然後開啟 [級別與價格] 索引標籤。 在 [定價層] 區段下選取 [進階] 層，然後按一下 [儲存]。
> 
> 

若要啟用 VNET 連線，請在「Azure 入口網站」中開啟您的「API 管理」服務，然後開啟 [虛擬網路] 頁面。

![API 管理的虛擬網路功能表][api-management-using-vnet-menu]

選取所需的存取類型：

* **外部**：可透過外部負載平衡器，從公用網際網路存取「API 管理」閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

![公用對等互連][api-management-vnet-public]

* **內部**：只能透過內部負載平衡器，從虛擬網路內存取「API 管理」閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

![私人對等互連][api-management-vnet-private]

您現在會看見佈建 API 管理服務所在的所有區域的清單。 為每個區域選取 VNET 和子網路。 此清單中會同時填入傳統和 ARM 虛擬網路，這些您要設定之區域中所設定 Azure 訂用帳戶可用的虛擬網路。

![選取 VPN][api-management-setup-vpn-select]

按一下畫面頂端的 [儲存]。 

> 當「API 管理」服務正在進行更新時，您將無法在該服務上執行管理作業。 「API 管理」閘道和開發人員入口網站會保持可用。
> 請注意，「API 管理」執行個體的 VIP 位址可能在每次啟用或停用 VNET 時都會變更。
> 
> 

## <a name="enable-vnet-powershell"> </a>使用 PowerShell Commandlet 來啟用 VNET 連線
您也可以使用 PowerShell Commandlet [Set-AzureRmApiManagementVirtualNetworks](https://msdn.microsoft.com/library/mt619277.aspx) 來啟用 VNET 連線。

## <a name="connect-vnet"> </a>連接到裝載於虛擬網路內的 Web 服務
在您的「API 管理」服務連接到 VNET 之後，存取 VNET 內的後端服務與存取公用服務便沒有差別。 只要在建立新 API 或編輯現有 API 時，於 [Web 服務 URL] 欄位中輸入您 Web 服務的本機 IP 位址或主機名稱 (如果為 VNET 設定了 DNS 伺服器) 即可。

![透過 VPN 加入 API][api-management-setup-vpn-add-api]

## <a name="custom-dns"> </a>自訂 DNS 伺服器設定
API 管理取決於多項 Azure 服務。 當「API 管理」是裝載於具有自訂 DNS 伺服器的 VNET 中時，它必須要能夠解析這些 Azure 服務的主機名稱。 請遵循 [這份](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 有關自訂 DNS 設定的指引。 如需參考資料，請參閱下面的圖表和連接埠表格。

## <a name="ports-required"> </a>API 管理所需的連接埠
> 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。 搭配 VNET 使用 API 管理時，封鎖這其中一或多個連接埠是最常見的錯誤組態問題。
> 
> 

當 API 管理服務執行個體裝載於 VNET 時，會使用下表中的連接埠。

| 連接埠 | 方向 | 傳輸通訊協定 | 目的 | 來源/目的地 | 存取類型 |
| --- | --- | --- | --- | --- | --- |
| 80、443 |輸入 |TCP |與 API 管理的用戶端通訊 |INTERNET / VIRTUAL_NETWORK |外部 |
| 3443 |輸入 |TCP |管理端點 |INTERNET / VIRTUAL_NETWORK |外部和內部 |
| 80、443 |輸出 |TCP |與「Azure 儲存體」和「Azure 服務匯流排」的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| 1433 |輸出 |TCP |與 Azure SQL 的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| 9350, 9351, 9352, 9353, 9354 |輸出 |TCP |與「服務匯流排」的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| 5671 |輸出 |AMQP |「記錄到事件中樞」原則的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| 6381, 6382, 6383 |輸入/輸出 |UDP |與「Redis 快取」的相依性 |VIRTUAL_NETWORK / VIRTUAL_NETWORK |外部和內部 |
| 445 |輸出 |TCP |與「適用於 GIT 的 Azure 檔案共用」的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |

## <a name="limitations"> </a>限制
* 包含「API 管理」執行個體的子網路無法包含任何其他 Azure 資源類型。
* 子網路和「API 管理」服務必須位於同一個訂用帳戶中。
* 包含「API 管理」執行個體的子網路無法跨訂用帳戶移動。
* 使用內部虛擬網路時，只有 [RFC 1918](https://tools.ietf.org/html/rfc1918) 中所述範圍的內部 IP 位址可用，無法提供公用 IP 位址。
* 針對已設定內部虛擬網路的多區域「API 管理」部署，使用者需負責管理自己的負載平衡，因為他們是 DNS 的擁有者。

## <a name="related-content"> </a>相關內容
* [使用 Azure 入口網站建立具有站對站 VPN 連線的虛擬網路][使用 Azure 入口網站建立具有站對站 VPN 連線的虛擬網路]
* [如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫][如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫]

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[啟用 VPN 連線]: #enable-vpn
[連接至 VPN 的 Web 服務]: #connect-vpn
[相關內容]: #related-content


[使用 Azure 入口網站建立具有站對站 VPN 連線的虛擬網路]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫]: api-management-howto-api-inspector.md



<!--HONumber=Nov16_HO3-->


