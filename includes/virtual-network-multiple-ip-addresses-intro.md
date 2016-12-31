> [!div class="op_single_selector"]
> * [入口網站](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
> * [範本](../articles/virtual-network/virtual-network-multiple-ip-addresses-template.md)
>

Azure 虛擬機器 (VM) 可連接一或多個網路介面 (NIC)。 任何 NIC 都可以有一或多個指派的靜態或動態公用及私人 IP 位址。 將多個 IP 位址指派給 VM 可啟用下列功能：

* 在單一伺服器上，以不同 IP 位址和 SSL 憑證裝載多個網站或服務。
* 做為網路虛擬設備，例如防火牆或負載平衡器。
* 能夠將任何 NIC 的任何私人 IP 位址新增到 Azure Load Balancer 後端集區。 在過去，只能將主要 NIC 的主要 IP 位址新增到後端集區。 若要深入了解如何進行負載平衡多個 IP 組態，請閱讀[負載平衡多個 IP 組態](../articles/load-balancer/load-balancer-multiple-ip.md)文章。

連接到 VM 的每個 NIC 皆有一或多個 IP 組態與其相關聯。 每個組態會指派一個靜態或動態私人 IP 位址。 每個組態可能也會有一個關聯的公用 IP 位址資源。 公用 IP 位址資源具有任一動態或靜態 IP 位址指派給它。 若要深入了解 Azure 中的 IP 位址，請閱讀 [Azure 中的 IP 位址](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)文章。


<!--HONumber=Dec16_HO2-->


