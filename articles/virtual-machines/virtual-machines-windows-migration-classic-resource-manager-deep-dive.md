<properties
	pageTitle="平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討 | Microsoft Azure"
	description="這篇文章提供平台支援之資源移轉 (從傳統移轉至 Azure Resource Manager) 的技術深入探討"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="mahthi"/>

# 平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討
讓我們深入探討如何從 Azure 傳統部署模型移轉至 Azure Resource Manager 部署模型。我們會就資源和功能層級來探討資源，以協助您了解 Azure 平台如何在這兩種部署模型之間移轉資源。如需詳細資訊，請閱讀服務公告文章：[平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager)](virtual-machines-windows-migration-classic-resource-manager.md)。

## 詳細的移轉指導方針

下表提供傳統和 Resource Manager 的資源表示法。目前不支援其他功能和資源。

| 傳統表示法 | Resource Manager 表示法 | 詳細資訊 | | |
|--------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 雲端服務名稱 | DNS 名稱 | 在移轉期間，會以命名樣式 `<cloudservicename>-migrated` 為每個雲端服務建立新的資源群組。此資源群組包含您的所有資源。雲端服務名稱會成為與公用 IP 位址關聯的 DNS 名稱。 | | |
| 虛擬機器 | 虛擬機器 | VM 特定屬性會原封不動地移轉過去。有些 osProfile 資訊 (例如電腦名稱) 不會儲存在傳統部署模型中，因此移轉後會保留空白。 | | |
| 連接至 VM 的磁碟資源 | 連接至 VM 的隱含磁碟 | 在 Resource Manager 部署模型中，並未將磁碟塑造成最上層資源。這些磁碟是被當作 VM 底下的隱含磁碟來移轉。目前只支援連接至 VM 的磁碟。Resource Manager VM 現在可以使用傳統儲存體帳戶，這可讓您不須進行任何更新，即可輕鬆移轉磁碟。 | | |
| VM 擴充功能 | VM 擴充功能 | 所有資源擴充功能 (XML 擴充功能除外) 都會從傳統部署模型移轉出來。 | | |
| 虛擬機器憑證 | Azure 金鑰保存庫中的憑證 | 如果雲端服務包含服務憑證，系統就會為每個雲端服務建立一個新的 Azure 金鑰保存庫，然後將憑證移至該金鑰保存庫。VM 將會更新為參照該金鑰保存庫中的憑證。 | | |
| WinRM 組態 | osProfile 下的 WinRM 組態 | 「Windows 遠端管理」組態在移轉過程中會原封不動地移過去。 | | |
| 可用性設定組屬性 | 可用性設定組資源 | 可用性設定組規格是傳統部署模型中 VM 上的屬性。在移轉過程中，可用性設定組會變成最上層資源。不支援下列組態：每個雲端服務有多個可用性設定組，或是一或多個可用性設定組帶有不屬於雲端服務中任何可用性設定組的 VM。 | | |
| VM 上的網路組態 | 主要網路介面 | 移轉之後，VM 上的網路組態就會顯示為主要網路介面資源。VM 如果不在虛擬網路中，其內部 IP 位址在移轉期間將會變更。 | | |
| VM 上的多個網路介面 | 網路介面 | 如果 VM 有多個關聯的網路介面，則在移轉至 Resource Manager 部署模型的過程中，每個網路介面以及所有的屬性都會成為最上層資源。 | | |
| 負載平衡的端點集 | 負載平衡器 | 在傳統部署模型中，平台已為每個雲端服務指派一個隱含的負載平衡器。在移轉期間，會建立新的負載平衡器資源，而負載平衡端點集則會成為負載平衡器規則。 | | |
| 傳入的 NAT 規則 | 傳入的 NAT 規則 | 在移轉期間，VM 上定義的輸入端點會轉換成負載平衡器下的輸入網路位址轉譯規則。 | | |
| VIP 位址 | 具 DNS 名稱的公用 IP 位址 | 虛擬 IP 位址會變成公用 IP 位址並與負載平衡器產生關聯。 | | |
| 虛擬網路 | 虛擬網路 | 虛擬網路會與其所有屬性一起移轉至 Resource Manager 部署模型。將會建立名為 `-migrated` 的新資源群組。有[不支援的組態](virtual-machines-windows-migration-classic-resource-manager.md)。 | | |
| 保留的 IP | 搭配靜態配置方法的公用 IP 位址 | 與負載平衡器關聯的保留 IP 會隨著雲端服務或虛擬機器的移轉一併移轉。目前不支援移轉未關聯的保留 IP。 | | |
| 每一個 VM 的公用 IP 位址 | 搭配動態配置方法的公用 IP 位址 | 與 VM 關聯的公用 IP 位址會轉換成公用 IP 位址資源，且配置方法會設定為靜態。 | | |
| NSG | NSG | 在移轉至 Resource Manager 部署模型的過程中，會複製與子網路關聯的網路安全性群組。在移轉期間不會移除傳統部署模型中的 NSG。不過，在移轉進行時，會封鎖 NSG 的管理平面作業。 | | |
| DNS 伺服器 | DNS 伺服器 | 與虛擬網路或 VM 關聯的 DNS 伺服器，會在對應的資源移轉過程中，與其所有屬性一起移轉。 | | |
| UDR | UDR | 在移轉至 Resource Manager 部署模型的過程中，會複製與子網路關聯的使用者定義路由。在移轉期間不會移除傳統部署模型中的 UDR。不過，移轉進行時，會封鎖 UDR 的管理平面作業。 | | |
| VM 網路組態上的 IP 轉送屬性 | NIC 上的 IP 轉送屬性 | 在移轉期間，VM 上的 IP 轉送屬性會轉換成網路介面上的屬性。 | | |
| 具有多個 IP 的負載平衡器 | 具有多個公用 IP 資源的負載平衡器 | 與負載平衡器關聯的每個公用 IP 在移轉後都會轉換成公用 IP 資源，並與負載平衡器產生關聯。 | | |
| VM 上的內部 DNS 名稱 | NIC 上的內部 DNS 名稱 | 在移轉期間，VM 的內部 DNS 尾碼會移轉至 NIC 上名為 "InternalDomainNameSuffix" 的唯讀屬性。尾碼在移轉後保持不變，VM 解析應該會像之前一樣繼續運作。 | | |

## 簡單的移轉逐步解說圖解

下列螢幕擷取畫面顯示在完成準備階段後帶有一部 VM (不在虛擬網路中) 的現有雲端服務：

![傳統模型在準備就緒後的樣子](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)

在移轉程序完成之後，下列螢幕擷取畫面顯示新資源已在新的資源群組中建立︰![Resource Manager 模型在準備就緒後的樣子](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## 後續步驟

既然您已了解如何將傳統 IaaS 資源移轉至 Resource Manager，您便可以開始移轉資源。

- [使用 PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [使用 CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager)](virtual-machines-windows-migration-classic-resource-manager.md)
- [使用社群 PowerShell 指令碼將傳統虛擬機器複製到 Azure Resource Manager](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0824_2016-->