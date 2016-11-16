建立虛擬網路閘道時，您必須指定想要使用的閘道 SKU。 當您選取較高的閘道 SKU 時，會配置更多的 CPU 和網路頻寬給閘道，如此一來，閘道即可支援對虛擬網路的更高網路輸送量。

VPN 閘道可以使用下列 SKU：

* 基本
* 標準
* 高效能

VPN 閘道不會使用 UltraPerformance 閘道 SKU。 如需 UltraPerformance SKU 的相關資訊，請參閱 [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) 文件。

選取 SKU 時，請考慮下列：

* 如果您想要使用原則式 VPN 類型，您必須使用基本 SKU。 其他所有 SKU 均不支援原則式 VPN (先前稱為靜態路由)。
* 基本 SKU 不支援 BGP。
* 基本 SKU 不支援 ExpressRoute-VPN 閘道共存組態。
* 僅可以在高效能 SKU 上設定主動-主動 S2S VPN 閘道連線。



<!--HONumber=Nov16_HO2-->


