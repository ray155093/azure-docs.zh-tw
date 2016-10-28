<properties
	pageTitle="網路基礎結構指導方針 | Microsoft Azure"
	description="了解適合用來在 Azure 基礎結構服務中部署虛擬網路的關鍵設計和實作指導方針。"
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>

# 網路基礎結構指導方針

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

本文著重於了解 Azure 內虛擬網路的必要計畫步驟，以及現有內部部署環境之間的連線能力。


## 虛擬網路的實作指導方針

决策：

- 您需要使用何種類型的虛擬網路來裝載 IT 工作負載或基礎結構 (僅限雲端或跨單位部署)？
- 如果是跨單位的虛擬網路，您現在需要多少位址空間來裝載子網路和 VM，並且在未來進行合理範圍的擴充？
- 您要建立集中式的虛擬網路，或是針對每個資源群組建立個別的虛擬網路？

工作：

- 定義適要建立虛擬網路的位址空間。
- 定義子網路組合以及適用於每個子網路的位址空間。
- 針對跨單位的虛擬網路，定義適用於內部部署位置的區域網路位址空間組合，而虛擬網路中的 VM 需要連接這類位置。
- 與內部部署網路團隊合作，以確保在建立跨單位虛擬網路時設定適當的路由。
- 使用您的命名慣例來建立虛擬網路。


## 虛擬網路

需要虛擬網路以支援虛擬機器 (VM) 之間的通訊。和實體網路一樣，您可以定義子網路、自訂 IP 位址、DNS 設定、安全性篩選，以及負載平衡。藉由使用[站對站 VPN](../vpn-gateway/vpn-gateway-topology.md) 或 [Express Route 線路](../expressroute/expressroute-introduction.md)，您可以將 Azure 虛擬網路連接到內部部署網路。您可以深入了解[虛擬網路及其元件](../virtual-network/virtual-networks-overview.md)。

透過使用資源群組，您便能彈性地設計您的虛擬網路元件。VM 可以連接到其資源群組之外的虛擬網路。一個常見的設計方式，便是建立可由一般小組管理且包含核心網路基礎結構的集中式資源群組。將 VM 與其應用程式部署到其他的資源群組。這種方式可讓應用程式使用者存取包含其 VM 的資源群組，而不需開放存取更廣泛虛擬網路資源的設定。

## 站台連線能力

### 僅限雲端虛擬網路
如果內部部署的使用者和電腦不需持續連線到 Azure 虛擬網路中的 VM，則您的虛擬網路設計是簡單易懂的：

![基本僅雲端虛擬網路圖表](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

這種方法通常適用於網際網路對應的工作負載，例如，以網際網路為基礎的 Web 伺服器。您可以使用 SSH 或點對站 VPN 連線來管理這些 VM。

由於它們不會連線到您的內部部署網路，因此，僅限 Azure 的虛擬網路可以使用任何部分的私人 IP 位址空間。位址空間可以是內部部署使用中的相同私用空間。


### 跨單位虛擬網路
如果內部部署的使用者和電腦需要持續連線到 Azure 虛擬網路中的 VM，則可建立跨單位的虛擬網路。透過 ExpressRoute 或站對站 VPN 連線，將虛擬網路連接到您的內部部署網路。

![跨部署虛擬網路圖表](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

在這個設定中，Azure 虛擬網路本質上是您的內部部署網路中具備雲端功能的延伸模組。

由於他們會連線到您的內部部署網路，跨單位虛擬網路必須使用您組織所用位址空間的一部分，且該部分必須是唯一的。與為不同的公司位置指派特定 IP 子網路相同，Azure 會隨著您擴充網路而成為另一個位置。

若要允許封包從跨單位的虛擬網路傳輸到內部部署網路，您必須設定相關的內部部署位址首碼組合，來做為適用於虛擬網路之區域網路定義的一部分。根據虛擬網路的位址空間及相關的內部部署位置組合而定，區域網路中可以有多個位址首碼。

您可以將僅限雲端的虛擬網路轉換為跨單位的虛擬網路，但是，它很可能會要求您為虛擬網路位址空間和 Azure 資源重新指派 IP。因此，在指派 IP 子網路時，請仔細考慮虛擬網路是否會需要連接到您的內部部署網路。

## 子網路
子網路允許您組織相關的資源，可能是邏輯相關 (例如，針對所有與相同應用程式相關聯的 VM 提供一個子網路)，也可能是實體相關 (例如，針對每個資源群組提供一個子網路)。您也可以採用子網路隔離技術來提高安全性。

對於跨單位虛擬網路，您應該利用您針對內部部署資源所使用的相同慣例來設計子網路。**Azure 一律會針對每個子網路使用位址空間的前三個 IP 位址**。若要判斷子網路所需的位址數目，請開始計算您現在需要的 VM 數目。估計未來成長量，接著使用下表來決定子網路的大小。

所需的 VM 數目 | 所需的主機位元數 | 子網路的大小
--- | --- | ---
1–3 | 3 | /29
4–11 | 4 | /28
12–27 | 5 | /27
28–59 | 6 | /26
60–123 | 7 | /25

> [AZURE.NOTE] 針對一般的內部部署子網路，適用於含有 n 個主機位元之子網路的主機位址數目上限是 2<sup>n</sup> – 2。針對 Azure 子網路，適用於含有 n 個主機位元之子網路的主機位址數目上限是 2<sup>n</sup> – 5 (2 加 3 適用於 Azure 在每個子網路上使用的位址)。

如果您選擇的子網路大小太小，就必須在子網路中為 VM 重新指派 IP 並重新部署。


## 網路安全性群組
您可以使用網路安全性群組，將篩選規則套用到流經您虛擬網路的流量。您可以建置細微的篩選規則來保護虛擬網路環境、控制傳入和傳出流量、來源和目的地 IP 範圍、允許的連接埠等等。網路安全性群組可以套用到虛擬網路內的子網路，或直接套用到特定的虛擬網路介面。我們建議您在虛擬網路上擁有某種程度的網路安全性群組來篩選流量。您可以深入了解[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。


## 其他網路元件
和內部部署實體網路基礎結構一樣，Azure 虛擬網路除了子網路和 IP 位址之外，還可以包含更多其他元件。當您在設計應用程式基礎結構時，您應該納入某些下列的其他元件：

- [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md) - 將 Azure 虛擬網路連接至其他 Azure 虛擬網路，或透過站對站 VPN 連線連接到內部部署網路。實作 Express Route 連線以做為專用的安全連線。您也可以利用點對站 VPN 連線，來提供使用者直接存取。
- [負載平衡器](../load-balancer/load-balancer-overview.md) - 視需求針對外部和內部流量提供流量負載平衡。
- [應用程式閘道](../application-gateway/application-gateway-introduction.md) - 應用程式層上的 HTTP 負載平衡，除了部署 Azure Load Balancer 之外，還能為 Web 應用程式提供一些其他的好處。
- [流量管理員](../traffic-manager/traffic-manager-overview.md) - DNS 型流量散發以將終端使用者引導到最接近的可用應用程式端點，讓您可以在不同地區中於 Azure 資料中心之外裝載您的應用程式。


## 後續步驟

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->