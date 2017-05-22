Azure 端點運作的方法與傳統和 Resource Manager 部署模型之間有些微不同。 在 Resource Manager 部署模型中，您現在可以彈性地建立網路篩選器，來控制進出您 VM 的流量。 除了傳統部署模型中的簡單端點，這些篩選器可讓您建立複雜的網路環境。 這篇文章提供網路安全性群組的概觀，以及它們與使用傳統端點、建立篩選規則和範例部署案例有何不同。

## <a name="overview-of-resource-manager-deployments"></a>Resource Manager 部署的概觀
傳統部署模型中的端點由網路安全性群組和存取控制清單 (ACL) 規則所取代。 實作網路安全性群組 ACL 規則的快速步驟如下︰

* 建立網路安全性群組。
* 若要允許或拒絕流量，請定義您的網路安全性群組 ACL 規則。
* 將您的網路安全性群組指派給網路介面或虛擬網路子網路。

如果您想要執行連接埠轉送，您需要將負載平衡器放在您的 VM 前方，並使用 NAT 規則。 實作負載平衡器和 NAT 規則的快速步驟應如下所示︰

* 建立負載平衡器。
* 建立後端集區，並將 VM 新增至集區。
* 定義必要的連接埠轉送的 NAT 規則。
* 將 NAT 規則指派給您的 VM。

## <a name="network-security-group-overview"></a>網路安全性群組概觀
網路安全性群組可提供一層安全性，允許特定連接埠及子網路存取您的 VM。 您通常都有網路安全性群組，提供您的 VM 與外界之間的安全性層級。 網路安全性群組可以套用至虛擬網路子網路或 VM 的特定網路介面。 不是建立端點 ACL 規則，您現在會建立網路安全性群組 ACL 規則。 這些 ACL 規則提供比僅建立端點以轉送指定的連接埠更多的控制權。 如需詳細資訊，請參閱[什麼是網路安全性群組？](../articles/virtual-network/virtual-networks-nsg.md)

> [!TIP]
> 您可以將網路安全性群組指派給多個子網路或網路介面。 沒有 1:1 對應。 您可以使用一組常用的 ACL 規則來建立網路安全性群組，並套用到多個子網路或網路介面。 此外，網路安全性群組可以套用至資源您訂用帳戶中的資源 (根據 [角色型存取控制](../articles/active-directory/role-based-access-control-what-is.md)。

## <a name="load-balancers-overview"></a>負載平衡器概觀
在傳統部署模型中，Azure 會在雲端服務上為您執行所有的網路位址轉譯 (NAT) 和連接埠轉送。 在建立端點時，您可以指定外部連接埠與流量導向的內部連接埠一起公開。 網路安全性群組本身不會執行這個相同的 NAT 和連接埠轉送。 

若要讓您為這類連接埠轉送建立 NAT 規則，請在您的資源群組中建立 Azure Load Balancer。 同樣地，負載平衡器十分細微，可依視需要只套用至特定 VM。 Azure Load Balancer NAT 規則與網路安全性群組 ACL 規則搭配使用，提供比使用雲端服務端點可以達成的更多彈性和控制。 如需詳細資訊，請參閱[負載平衡器概觀](../articles/load-balancer/load-balancer-overview.md)。

## <a name="network-security-group-acl-rules"></a>網路安全性群組 ACL 規則
ACL 規則可讓您根據特定的連接埠、連接埠範圍或通訊協定，定義可以進出您 VM 的流量。 將這些規則指派給個別 VM 或子網路 下列螢幕擷取畫面是常見的 Web 伺服器上的 ACL 規則的範例︰

![網路安全性群組 ACL 規則的清單](./media/virtual-machines-common-endpoints-in-resource-manager/example-acl-rules.png)

ACL 規則會根據您指定的優先順序度量套用 - 值越高，優先順序越低。 每個網路安全性群組具有三個預設規則，設計來處理 Azure 網路流量，具有明確的 `DenyAllInbound` 做為最終的規則。 預設的 ACL 規則的優先順序很低，因此不會影響您建立的規則。

## <a name="assigning-network-security-groups"></a>指派網路安全性群組
您將網路安全性群組指派給子網路或網路介面。 這種方法可讓您在僅將 ACL 規則套用至特定 VM 時夠細膩，或確保一組常用的 ACL 規則會套用至子網路的所有 VM 組件︰

![將 NSG 套用至網路介面或子網路](./media/virtual-machines-common-endpoints-in-resource-manager/apply-nsg-to-resources.png)

網路安全性群組的行為不會根據指派給子網路或網路介面而變更。 常見的部署案例將網路安全性群組指派給子網路，以確保符合連接到該子網路的所有 VM。 如需詳細資訊，請參閱[將網路安全性群組套用至資源](../articles/virtual-network/virtual-networks-nsg.md#associating-nsgs)。

## <a name="default-behavior-of-network-security-groups"></a>網路安全性群組的預設行為
根據您建立網路安全性群組的方式和時機，建議您為 Windows VM 建立預設規則以允許 TCP 連接埠 3389 上的 RDP 存取。 Linux VM 的預設規則會在 TCP 連接埠 22 上允許 SSH 存取。 在下列情況下，會建立這些自動 ACL 規則︰

* 如果您透過入口網站建立 Windows VM，並接受預設動作來建立網路安全性群組，就會建立 ACL 規則以允許 TCP 連接埠 3389 (RDP)。
* 如果您透過入口網站建立 Linux VM，並接受預設動作來建立網路安全性群組，就會建立 ACL 規則以允許 TCP 連接埠 22 (SSH)。

在所有其他情況下，不會建立這些預設 ACL 規則。 若未建立適當的 ACL 規則，您會無法連接到 VM。 這些情況包括下列常見的動作：

* 透過入口網站建立網路安全性群組做為個別的動作來建立 VM。
* 透過 PowerShell、Azure CLI、Rest API 等以程式設計方式建立網路安全性群組。
* 建立 VM，並將它指派給現有的網路安全性群組，該群組尚未定義適當的 ACL 規則。

在所有上述案例中，您必須為您的 VM 建立 ACL 規則，以允許適當的遠端管理連接。

## <a name="default-behavior-of-a-vm-without-a-network-security-group"></a>沒有網路安全性群組的 VM 的預設行為
您可以建立 VM，而不建立網路安全性群組。 在這些情況下，您可以使用 RDP 或 SSH 連接至 VM，而不建立任何 ACL 規則。 同樣地，如果您在連接埠 80 上安裝 Web 服務，該服務會自動可從遠端存取。 VM 讓所有連接埠開啟。

> [!NOTE]
> 您仍然必須將公用 IP 位址指派至 VM，以便進行任何遠端連接。 沒有子網路或網路介面的網路安全性群組，就不會將 VM 公開至任何外部流量。 透過入口網站建立 VM 時的預設動作是建立新的公用 IP。 對於建立 VM 的所有其他形式，例如 PowerShell、Azure CLI 或 Resource Manager 範本，除非明確要求，否則不會自動建立公用 IP。 透過入口網站的預設動作也是建立網路安全性群組。 預設動作表示您不應該在沒有網路篩選器就緒的公開 VM 情況下結束。

## <a name="understanding-load-balancers-and-nat-rules"></a>了解負載平衡器及 NAT 規則
在傳統部署模型中，您可以建立也會執行連接埠轉送的端點。 當您在傳統部署模型中建立 VM 時，RDP 或 SSH 的 ACL 規則就會自動建立。 這些規則不會分別對外界公開 TCP 連接埠 3389 或 TCP 連接埠 22。 相反地，高值的 TCP 連接埠會公開，對應至適當的內部連接埠。 您也可以以類似的方式建立自己的 ACL 規則，例如在 TCP 連接埠 4280 上將 Web 伺服器向外界公開。 您可以在傳統入口網站的下列螢幕擷取畫面中看到這些 ACL 規則和連接埠對應：

![使用傳統端點的連接埠轉送](./media/virtual-machines-common-endpoints-in-resource-manager/classic-endpoints-port-forwarding.png)

利用網路安全性群組，該連接埠轉送功能是由負載平衡器處理。 如需詳細資訊，請參閱 [Azure 中的負載平衡器](../articles/load-balancer/load-balancer-overview.md)。 下列範例顯示具有 NAT 規則以進行 TCP 連接埠 4222 至 VM 的內部 TCP 連接埠 22 的連接埠轉送的負載平衡器：

![連接埠轉送的負載平衡器 NAT 規則](./media/virtual-machines-common-endpoints-in-resource-manager/load-balancer-nat-rules.png)

> [!NOTE]
> 當您實作負載平衡器時，通常不會對 VM 本身指派公用 IP 位址。 相反地，負載平衡器會有公用 IP 位址指派給它。 您還是需要建立您的網路安全性群組和 ACL 規則，以定義進出您的 VM 的流量。 負載平衡器 NAT 規則只會定義哪些連接埠允許透過負載平衡器，以及它們如何跨後端 VM 分散。 因此，您需要建立 NAT 規則，讓流量流經負載平衡器。 若要允許流量觸達 VM，請建立網路安全性群組 ACL 規則。
