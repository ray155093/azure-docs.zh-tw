---
title: "Azure 虛擬網路對等互連 | Microsoft Docs"
description: "了解 Azure 中的虛擬網路對等互連。"
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6fbcdcf77f46a3c643e8fedc1d112588cbd7befc
ms.lasthandoff: 04/03/2017


---
# <a name="virtual-network-peering"></a>虛擬網路對等互連
虛擬網路 (VNet) 對等互連可讓您透過 Azure 骨幹網路來連接同一區域中的兩個 VNet。 一旦對等互連，對連線用作而言，兩個 VNet 看起來就像一個。 這兩個 VNet 仍做為不同的資源進行管理，但對等互連的 VNet 中的虛擬機器 (VM) 可以使用私人 IP 位址彼此直接通訊。

在對等互連的 VNet 中，VM 之間的流量會透過 Azure 基礎結構路由傳送，如同在同一 VNet 中 VM 之間路由傳送流量。 使用 VNet 對等互連的一些優點包括︰

* 不同 VNet 資源之間具有低延遲、高頻寬連線。
* 可以在已對等互連的 VNet 中將網路應用裝置和 VPN 閘道等資源當做傳輸點。
* 能夠對等互連透過 Azure Resource Manager 部署模型建立的兩個 VNet，或將透過 Resource Manager 建立的 VNet 對等互連至透過傳統部署模型建立的 VNet。 若要深入了解兩個 Azure 部署模型之間的差異，請閱讀[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md)一文。

VNet 對等互連的需求和重要層面︰

* 對等互連的 VNet 必須存在於同一個 Azure 區域中。
* 對等互連的 VNet 必須有非重疊的 IP 位址空間。
* VNet 對等互連是介於兩個 VNet 之間，但沒有衍生跨對等項目的可轉移關聯性。 例如，如果 VNetA 與 VNetB 對等互連，而 VNetB 與 VNetC 對等互連，則 VNetA 「不會」對等互連至 VNetC。
* 只要兩個訂用帳戶之間有一個具有權限的使用者授權對等互連，您就可以對等互連存在於兩個不同訂用帳戶中的 VNet，而訂用帳戶會與相同的 Azure Active Directory 租用戶產生關聯。
* 如果兩個 VNet 都是透過 Resource Manager 部署模型建立，或如果其中一個是透過 Resource Manager 部署模型建立，而另一個是透過傳統部署模型建立，即可對等互連 VNet。 然而，透過傳統部署模型建立的兩個 VNet 無法彼此對等互連。 對等互連透過不同部署模型建立的 VNet 時，Vnet 必須同時存在於「相同」的訂用帳戶中。 能夠對等互連透過存在於「不同」訂用帳戶中的部署模型建立的 VNet，而該功能屬於**預覽**版本。 如需進一步的詳細資訊，請讀取[使用 Powershell 建立虛擬網路對等互連](virtual-networks-create-vnetpeering-arm-ps.md)一文。
* 雖然已對等互連 VNet 中的 VM 之間的通訊沒有其他頻寬限制，但仍然會有基於 VM 大小的網路頻寬上限。 若要深入了解不同 VM 大小的網路頻寬上限，請閱讀 [Windows](../virtual-machines/windows/sizes.md) 或 [Linux](../virtual-machines/linux/sizes.md) VM 大小文章。

![基本 VNet 對等互連](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>連線能力
對等互連兩個 VNet 之後，VNet 中的 VM 或雲端服務角色可以與連接至對等互連 VNet 的其他資源直接。 這兩個 VNet 具有完整的 IP 層級連線能力。

在對等互連的 VNet 中，兩部 VM 之間的來回網路延遲與在單一 VNet 中的來回網路延遲相同。 網路輸送量是以 VM 大小按比例允許的頻寬為基礎。 對等互連內的頻寬沒有其他額外限制。

已對等互連 VNet 中 VM 之間的流量會透過 Azure 後端基礎結構直接路由傳送，而不是透過閘道傳送。

連接到 VNet 的 VM 可以存取已對等互連 VNet 中的內部負載平衡 (ILB) 端點。 網路安全性群組 (NSG) 可套用於任一 VNet，以封鎖其他 VNet 或子網路的存取 (如有需要)。

設定對等互連時，您可以開啟或關閉 VNet 之間的 NSG 規則。 如果您開啟對等互連 VNet 之間的完整連線 (預設選項)，則可以將 NSG 套用至特定子網路或 VM，以封鎖或拒絕特定存取。 若要深入了解 NSG，請閱讀[網路安全性群組](virtual-networks-nsg.md)一文。

Azure 為 VM 提供的內部 DNS 名稱解析無法作用於對等互連的 VNet。 VM 具有只在本機 VNet 內可解析的內部 DNS 名稱。 不過，您可以將連接至對等互連 VNet 的 VM 設定為 VNet 的 DNS 伺服器。 如需進一步詳細資訊，請讀取[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)一文。

## <a name="service-chaining"></a>服務鏈結
您可以設定使用者定義的路由 (UDR)，其指向對等互連 VNet 中的 VM 作為「下一個躍點」IP 位址，如本文稍後的圖表所示。 這可形成服務鏈結，其可讓您透過 UDR 將來自一個 VNet 的流量導向至在對等互連 VNet 中執行的虛擬設備。

您也可以有效地建立中樞和輪輻類型的環境，讓中樞得以裝載網路虛擬應用裝置等基礎結構元件。 所有輪輻 VNet 都能與其對等互連，也能與在中樞 VNet 中執行之應用裝置的流量子集對等互量。 簡單來說，VNet 對等互連可讓在 UDR 上的下一個躍點 IP 位址成為對等互連 VNet 中 VM 的 IP 位址。 如需 UDR 的詳細資訊，請讀取[使用者定義的路由](virtual-networks-udr-overview.md)一文。

## <a name="gateways-and-on-premises-connectivity"></a>閘道及內部部署連線能力
不論每個 VNet 是否與其他 VNet 對等互連，它們仍可以擁有自己的閘道並使用它來連接至內部部署網路。 即使 VNet 已對等互連，使用者也可以使用閘道來設定 [VNet 對 VNet 連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

若已設定 VNet 內部連線的兩個選項，VNet 之間的流量將會透過對等互連設定 (也就是透過 Azure 骨幹) 流動。

已對等互連 VNet 時，使用者也可以將對等互連 VNet 中的閘道設定為內部部署網路的傳輸點。 在此情況下，使用遠端閘道的 VNet 不能擁有自己的閘道。 VNet 只能擁有一個閘道。 閘道可以是本機或遠端閘道 (在已對等互連的 VNet 中)，如下圖所示：

![VNet 對等互連傳輸](./media/virtual-networks-peering-overview/figure02.png)

透過不同部署模型建立的 VNet 之間的對等互連關聯性中不支援閘道傳輸。 對等互連關聯性中的兩個 VNet 都必須透過 Resource Manager 建立，閘道傳輸才能運作。

當共用單一 Azure ExpressRoute 連線的 VNet 對等互連時，它們之間的流量會經過對等互連關聯性 (也就是透過 Azure 骨幹網路)。 您仍然可以在每個 VNet 中使用本機閘道來連接到內部部署線路。 此外，您也可以使用共用閘道並設定內部部署連線的傳輸。

## <a name="provisioning"></a>佈建
VNet 對等互連是需要權限的作業。 它是 VirtualNetworks 命名空間下的個別功能。 使用者可以取得特定權限以授權對等互連。 具有虛擬網路讀寫權限的使用者會自動繼承這些權限。

身為系統管理員或具有對等互連功能權限的使用者可以啟動對另一個 VNet 的對等互連作業。 如果另一端有對等互連的相符要求而且其他需求也符合，就會建立對等互連。

請參閱本文的[後續步驟](#next-steps)一節，了解如何建立兩個 VNet 之間的 VNet 對等互連。

## <a name="limits"></a>限制
單一虛擬網路允許的對等互連數目有其限制。 如需詳細資訊，請參閱 [Azure 網路限制](../azure-subscription-service-limits.md#networking-limits) 。

## <a name="pricing"></a>價格
我們會針對使用 VNet 對等互連的輸入和輸出流量收取少許費用。 如需詳細資訊，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/virtual-network)。

## <a name="next-steps"></a>接續步驟
了解如何使用下列項目建立 VNet 對等互連︰

* [Azure 入口網站](virtual-networks-create-vnetpeering-arm-portal.md)
* [Azure PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
* [Azure Resource Manager 範本](virtual-networks-create-vnetpeering-arm-template-click.md)

