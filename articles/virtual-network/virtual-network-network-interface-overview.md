<properties 
   pageTitle="網路介面 | Microsoft Azure"
   description="了解 Azure Resource Manager 中的 Azure 網路介面。"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />


# <a name="network-interfaces"></a>網路介面

網路介面 (NIC) 是虛擬機器 (VM) 與基礎軟體網路之間互相連線的橋樑。 本文說明什麼是網路介面，以及在 Azure Resource Manager 部署模型中如何加以使用。

Microsoft 建議您使用 Resource Manager 部署模型來部署新的資源，但您也可以在 [傳統](virtual-network-ip-addresses-overview-classic.md) 部署模型中部署有網路連線的 VM。 如果您熟悉傳統模式，在 VM 網路與在 Resource Manager 部署模型中有很重要的差異。 請閱讀 [虛擬機器網路功能 - 傳統](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) 文章深入了解差異。

在 Azure 中，網路介面：

1. 是一種可以建立、刪除的資源，本身也有可供調整的設定。
2. 在建立時必須連線到一個 Azure 虛擬網路 (VNet) 中的一個子網路。 如果您不熟悉 VNet，請閱讀 [虛擬網路概觀](virtual-networks-overview.md) 文章以深入了解。 NIC 只能連線到與 NIC 位於相同 Azure [位置](https://azure.microsoft.com/regions)和[訂用帳戶](../azure-glossary-cloud-terminology.md#subscription)中的 VNet。 NIC 建立之後，您可以變更它所連線的子網路，但無法變更它所連線的 VNet。
3. 有一個指派的名稱，在 NIC 建立之後就無法變更。 名稱在 Azure [資源群組](../resource-group-overview.md#resource-groups)中中必須是獨一無二的，但在訂用帳戶、建立所在的 Azure 位置，或所連線到的 VNet 內不必是唯一的。 通常會在 Azure 訂用帳戶中建立幾個 NIC。 建議您可以設計一種命名慣例，比使用預設名稱還更容易管理多個 NIC。 請參閱 [Azure 資源的建議命名慣例](../guidance/guidance-naming-conventions.md) 文章了解一些建議。
4. 可以附加至 VM，但只能附加至與 NIC 位於相同位置的單一 VM。
5. 有 MAC 位址，只要 NIC 與 VM 維持連線，MAC 位址就會和 NIC 一起保留。 無論 VM (從作業系統內) 重新啟動或停止 (取消配置) 並使用 Azure 入口網站、Azure PowerShell 或 Azure 命令列介面啟動，MAC 位址都會保留下來。 如果 NIC 已從 VM 卸離又附加至不同的 VM，就會收到不同的 MAC 位址。 如果刪除 NIC，MAC 位址就會指派至其他 NIC。
6. 必須指派一個主要的 **私人** *IPv4* 靜態或動態 IP 位址。
8. 可以有一個關聯的公用 IP 位址資源。
9. 執行特定 Microsoft Windows Server 作業系統版本的特定 VM 大小，支援有 Single-Root I/O Virtualization (SR-IOV) 的加速網路功能。 若要深入了解這項「預覽版」功能，請閱讀 [虛擬機器的加速網路](virtual-network-accelerated-networking-powershell.md) 文章。
10. 可以接收不是要傳送到被指派之私人 IP 位址的流量 (如果 NIC 已啟用 IP 轉送)。 例如，如果 VM 正在執行防火牆軟體，則會路由不是要傳送它自己 IP 位址的封包。 VM 仍然必須執行能夠路由或轉送流量的軟體，但是如果要達到這個目的，NIC 必須啟用 IP 轉送。
11. 通常會在與附加的 VM 位於相同的資源群組或與所連線的 VNet 中建立，但不是必要。

如果 VM 大小支援，可以將多個 NIC 連線至同一個 VM。 若要深入了解哪些 VM 大小支援多個 NIC，請閱讀 [Windows Server VM 大小](../virtual-machines/virtual-machines-windows-sizes.md)或 [Linux VM 大小](../virtual-machines/virtual-machines-linux-sizes.md)文章。

## <a name="next-steps"></a>後續步驟

- 閱讀 [建立 VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 文章了解如何建立只有單一 NIC 的 VM。
- 閱讀 [部署具有多個 NIC 的 VM](virtual-network-deploy-multinic-arm-ps.md) 文章了解如何建立具有多個 NIC 的 VM。
- 閱讀 [Azure 虛擬機器的多個 IP 位址](virtual-network-multiple-ip-addresses-powershell.md) 文章了解如何建立具有多個 IP 組態的 NIC。



<!--HONumber=Oct16_HO2-->


