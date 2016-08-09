<properties
   pageTitle="Azure 虛擬網路對等互連 | Microsoft Azure"
   description="深入了解 Azure 中的 VNet 對等互連。"
   services="virtual-network"
   documentationCenter="na"
   authors="narayanannamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="narayan" />

# VNet 對等互連

VNet 對等互連是透過 Azure 骨幹網路來連接同一區域中兩個虛擬網路的機制。一旦對等互連，針對用作連線而言兩個虛擬網路看起來就像一個。它們仍做為不同的資源進行管理，但這些 Vnet 中的虛擬機器可以使用私人 IP 位址彼此直接通訊。在已對等互連 Vnet 中虛擬機器之間的流量會透過 Azure 基礎結構路由傳送，如同在同一 VNet 中 VM 之間的流量。使用 VNet 對等互連的一些優點︰

- 不同 Vnet 資源之間具有低延遲、高頻寬連線。
- 可以在已對等互連的 VNet (傳輸) 中使用例如網路虛擬設備和 VPN 閘道等資源。
- 將 Resource Manager VNet 連接到傳統 VNet，並啟用這些 Vnet 中資源之間的完整連線

VNet 對等互連的需求和重要層面︰

- 已對等互連的兩個虛擬網路應位於相同的 Azure 區域
- 已對等互連的 Vnet 應該要有非重疊的 IP 位址空間
- VNet 對等互連只在兩個虛擬網路之間，並沒有衍生可轉移關聯性，也就是如果 VNet A 與 VNet B 對等互連，同時 VNet B 與 VNet C 對等互連，不代表 VNet A 與 VNet C 對等互連
- 只要個別訂用帳戶中一個具有權限的使用者授權對等互連，就可以建立兩個不同訂用帳戶虛擬網路之間的對等互連
- Resource Manager VNet 可以與另一個 Resource Manager VNet 或傳統 VNet 對等互連，但兩個傳統 Vnet 無法彼此對等互連
- 雖然在已對等互連 Vnet 中虛擬機器之間的通訊沒有其他頻寬限制，但仍然會有基於 VM 大小的頻寬上限。


![VNet 對等互連基本](./media/virtual-networks-peering-overview/figure01.png)

## 連線能力 
兩個 Vnet 已對等互連後，Vnet 中的虛擬機器 (Web 角色/背景工作角色) 可以直接與已對等互連 VNet 中的其他虛擬機器連線。它們會具有完整的 IP 層級連線能力。在已對等互連 Vnet 中兩部虛擬機器之間來回傳送的網路延遲時間會與在本機 VNet 中相同。網路輸送量會根據虛擬機器大小具有一定比例的允許頻寬，對允許的頻寬不會有其他限制。已對等互連 Vnet 中虛擬機器之間的流量會透過 Azure 的後端基礎結構直接路由傳送，而不是透過閘道。

在 Vnet 中的虛擬機器都能夠存取對等互連 Vnet 中的內部負載平衡端點 (ILB)。網路安全性群組可以適用於 Vnet 或子網路 (如有需要) 以封鎖其他 Vnet 的存取。使用者在設定對等互連時，可選擇開啟或關閉 Vnet 之間的網路安全性群組規則。如果使用者選擇開啟對等互連 Vnet (預設選項) 之間完整的連線，則使用者接著可以在特定子網路或虛擬機器上使用 NSG 以封鎖或拒絕特定的存取。

Azure 提供的虛擬機器內部 DNS 名稱解析無法在對等互連 Vnet 中運作。虛擬機器必須具有只在本機虛擬網路內可解析的內部 DNS 名稱。不過，使用者可以將在對等互連 Vnet 中執行的虛擬機器設定為虛擬網路的 DNS 伺服器。

## 服務鏈結
使用者可以將指向對等互連 VNet 中虛擬機器的使用者定義路徑資料表設定為下一個躍點 (如下圖所示)。這可讓使用者達到服務鏈結，他們可以透過使用者定義路徑資料表將流量從一個 VNet 導向在對等互連 VNet 中執行的虛擬設備。使用者也可以有效建立中樞和支點類型環境，中樞可在其中裝載基礎結構元件，例如網路虛擬設備，且所有支點 Vnet 可以與其對等互連並將流量子集導向在中樞 VNet 中執行的設備。簡單來說，VNet 對等互連可讓在「使用者定義路徑資料表」上的下一個躍點 IP 位址等於對等互連 VNet 中虛擬機器的位址。

## 閘道及內部部署連線能力
不論每個虛擬網路是否與其他 Vnet 對等互連，每個虛擬網路仍可以擁有自己的閘道並使用它來連線至內部部署。即使 Vnet 已對等互連，使用者也可以使用閘道來設定 VNet 對 VNet 連線 (提供連結)。當針對 VNet 內部連線的兩個選項已設定時，Vnet 之間的流量將會透過對等互連設定流動 (也就是透過 Azure 骨幹)。

當 Vnet 已對等互連時，使用者也可以設定使用對等互連 Vnet 中的閘道做為內部部署傳輸點。在此情況下，使用遠端閘道的 VNet 無法擁有自己的閘道，簡單來說一個 VNet 只可以有一個閘道，可以是本機閘道或 (在對等互連 Vnet 中的) 遠端閘道，如下圖所示。在 Resource Manager 和傳統 Vnet 之間不支援閘道傳輸，有對等互連關係的兩個 Vnet 必須是 Resource Manager Vnet，閘道傳輸才得以運作。當共用單一 ER 循環的 Vnet 已對等互連時，兩者之間的流量會經過對等關聯性 (也就是透過 Azure 骨幹網路)。使用者仍可使用每個 Vnet 中的本機閘道來連接到內部部署循環，或使用共用閘道並設定內部部署連線傳輸。

![VNet 對等互連傳輸](./media/virtual-networks-peering-overview/figure02.png)

## 佈建
VNet 對等互連是需要權限的作業。它是虛擬網路命名空間下的個別功能。使用者可以取得特定權限以授權對等互連。在 VNet 上具有讀寫存取的使用者會自動繼承權限。身為系統管理員或具有對等互連功能權限的使用者可以啟動對另一個 VNet 的對等互連作業。如果另一端有對等互連的相符要求而且其他需求也符合，就會建立對等互連。

請參閱操作說明文章，進一步了解如何建立兩個虛擬網路之間的 VNet 對等互連。

## 限制
單一虛擬網路中允許的對等互連數目有受到限制，如需詳細資訊，請參閱 [Azure 網路限制](../azure-subscription-service-limits.md#networking-limits)。

## 價格
VNet 對等互連在審查期間不需支付費用。發行上市之後，針對使用對等互連的輸入和輸出流量會收取少許費用。如需詳細資訊，請參閱 [價格頁面](https://azure.microsoft.com/pricing/details/virtual-network)


## 後續步驟
- [設定虛擬網路之間的對等互連](virtual-networks-create-vnetpeering-arm-portal.md)。
- 了解 [NSG](virtual-networks-nsg.md)。
- 了解[使用者定義的路由和 IP 轉送](virtual-networks-udr-overview.md)。

<!---HONumber=AcomDC_0803_2016-->