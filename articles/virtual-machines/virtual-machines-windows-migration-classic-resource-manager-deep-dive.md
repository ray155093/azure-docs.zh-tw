<properties
	pageTitle="深入技術探討支援從傳統移轉至 Azure Resource Manager 的平台"
	description="本文深入技術探討支援移轉服務功能將 Service Management 移轉至 Azure Resource Manager 的平台。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# 深入技術探討支援從傳統移轉至 Azure Resource Manager 的平台

我們在本文中深入的技術性討論資源和功能層級的移轉，協助您了解平台如何將資源從傳統移轉至 Resource Manager。請仔細閱讀服務公告文件[支援將 IaaS 資源從傳統移轉至 Azure Resource Manager 的平台](virtual-machines-windows-migration-classic-resource-manager.md)。

## 資源和功能層級移轉的詳細指引

您可以在下列資源找到傳統和 Resource Manager 的表示法。目前不支援此表中未列出的功能和資源。

| 傳統的表示法 | Resource Manager 的表示法 | 詳細資訊 | | |
|--------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 雲端服務名稱 | DNS 名稱 | 在移轉期間，我們將為每個雲端服務建立包含所有資源的新資源群組，並命名為 `<cloudservicename>-migrated`。雲端服務名稱會變成與公用 IP 位址相關聯的 DNS 名稱 | | |
| 虛擬機器 | 虛擬機器 | VM 專屬屬性將如現狀移轉。請注意，傳統部署模型不會儲存某些 osProfile 資訊 (例如電腦名稱)，因此移轉後會保留空白。 | | |
| 連接至 VM 的磁碟資源 | 連接至 VM 的隱含磁碟 | 在 Resource Manager 部署模型中，磁碟不是頂層資源。會被當做 VM 下的隱含磁碟來移轉。目前我們只支援連接至 VM 的磁碟。很快會支援移轉未關聯的磁碟。若要啟用移轉，傳統儲存體帳戶現在已經可供 Resource Manager VM 使用。因此，不用進行任何更新便可輕鬆將磁碟移轉至 Resource Manager 模型。 | | |
| VM 擴充功能 | VM 擴充功能 | 除了 XML 擴充的所有資源擴充功能都會從傳統部署模型移轉出來。 | | |
| 虛擬機器憑證 | Azure 金鑰保存庫中的憑證 | 如果雲端服務包含服務憑證，平台會建立每個雲端服務的新 Azure 金鑰保存庫，並將憑證移至金鑰保存庫中。VM 將更新為參照此金鑰保存庫中的憑證。 | | |
| WinRM 組態 | osProfile 下的 WinRM 組態 | 在移轉過程中，WinRM 組態將如現狀移轉。 | | |
| 可用性設定組屬性 | 可用性設定組資源 | 「可用性設定組」規格是傳統部署模型中 VM 上的屬性。在移轉過程中，可用性設定組將會變成頂層資源。請注意，我們並不支援下列組態：一個雲端服務多個可用性設定組；或是，不屬於雲端服務中任何可用性設定組的 VM 帶有一或多個可用性設定組。 | | |
| VM 上的網路組態 | 主要網路介面 | 現在，在移轉之後 VM 上的網路組態將表示為主要網路介面資源。若 VM 不在 VNET 中，內部 IP 位址在移轉期間將會變更。 | | |
| VM 上的多個網路介面 | 網路介面 | 如果 VM 有多個相關聯的網路介面，則在移轉至 Resource Manager 部署模型的過程中，每個網路介面以及所有的屬性會成為頂層資源。 | | |
| 負載平衡端點集 | 負載平衡器 | 在傳統部署模型中，會針對每個雲端服務隱含指派一個負載平衡器給平台。在移轉期間，我們將建立新的負載平衡器資源，負載平衡端點集將成為負載平衡器規則。 | | |
| 輸入 NAT 規則 | 輸入 NAT 規則 | 在移轉期間，VM 上定義的輸入端點將會轉換成負載平衡器下的「輸入 NAT 規則」。 | | |
| VIP 或虛擬 IP 位址 | 具 DNS 名稱的公用 IP 位址 | 虛擬 IP 位址或 VIP 現在將變成公用 IP 位址，並將與負載平衡器關聯。 | | |
| 虛擬網路 | 虛擬網路 | 虛擬網路將和其所有屬性一起移轉至 Resource Manager 部署模型。將會建立名為 `-migrated` 的新資源群組。請參閱[這裡](virtual-machines-windows-migration-classic-resource-manager.md)了解有哪些不支援的組態 | | |
| 保留的 IP | 帶靜態配置方法的公用 IP 位址 | 與負載平衡器相關聯的保留 IP 會隨著雲端服務或虛擬機器的移轉一併移轉。目前不支援移轉未關聯的保留 IP。 | | |
| 每一個 VM 的公用 IP 位址 | 帶動態配置方法的公用 IP 位址 | 與 VM 相關聯的公用 IP 位址會轉換為公用 IP 位址資源，且配置方法會設為靜態。 | | |
| 網路安全性群組 | 網路安全性群組 | 在移轉至 Resource Manager 部署模型的過程中，會複製與子網路相關聯的 NSG。請注意，在移轉期間不會移除傳統部署模型中的 NSG。不過，移轉正在進行時，會封鎖 NSG 的平面作業。 | | |
| DNS 伺服器 | DNS 伺服器 | 與 VNET 或 VM 相關聯的 DNS 伺服器，會在對應資源移轉的過程中和其所有屬性一起移轉。 | | |
| 使用者定義的路由 | 使用者定義的路由 | 在移轉至 Resource Manager 部署模型的過程中，會複製與子網路相關聯的 UDR。請注意，在移轉期間不會移除傳統部署模型中的 UDR。不過，移轉正在進行時，會封鎖 UDR 的平面作業。 | | |
| VM 網路組態上的 IP 轉送屬性 | NIC 上的 IP 轉送屬性 | VM 上的 IP 轉送屬性會在移轉期間轉換為網路介面上的屬性 | | |
| 帶多個 IP 的負載平衡器 | 帶多個公用 IP 資源的負載平衡器 | 與負載平衡器相關聯的每個公用 IP 將轉換成公用 IP 資源，並在移轉後與負載平衡器相關聯。 | | |
| VM 上的內部 DNS 名稱 | NIC 上的內部 DNS 名稱 | 在移轉期間，會更新內部的 DNS 的尾碼，以符合 Resource Manager 部署模型的尾碼。請注意，移轉之後名稱解析會繼續工作。不過，與 VM 相關聯的尾碼將變更。 | | |

## 逐步解說簡單移轉範例圖解

在以下的範例螢幕擷取畫面，您可以看到「準備」階段之後 (不在 VNET) 中的 VM 的雲端服務的表示。

![顯示「準備」階段之後傳統表示法的螢幕擷取畫面](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png) ![顯示「準備」階段之後 Resource Manager 表示法的螢幕擷取畫面](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## 後續步驟

現在您已了解傳統 IaaS 資源至 Resource Manager 的移轉，可以開始移轉資源。

- [使用 PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [使用 CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [支援將 IaaS 資源從傳統移轉至 Azure Resource Manager 的平台](virtual-machines-windows-migration-classic-resource-manager.md)
- [利用 Community PowerShell 指令碼複製傳統虛擬機器至 Azure Resource Manager](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0511_2016-->