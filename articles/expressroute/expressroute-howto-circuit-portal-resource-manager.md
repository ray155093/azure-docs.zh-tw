<properties
   pageTitle="使用 Resource Manager 和 Azure 入口網站建立和修改 ExpressRoute 循環 | Microsoft Azure"
   description="本文說明如何建立、佈建、驗證、更新、刪除和取消佈建 ExpressRoute 線路。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="cherylmc"/>

# 建立和修改 ExpressRoute 線路

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)

本文說明如何使用 Azure 入口網站和 Azure Resource Manager 部署模型建立 Azure ExpressRoute 循環。下列步驟也會示範如何檢查線路的狀態、對它進行更新，或是對它進行刪除及取消佈建。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## 開始之前


- 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)頁面和[工作流程](expressroute-workflows.md)頁面。
- 確定您可以存取 [Azure 入口網站](https://portal.azure.com)。
- 確定您具有建立新網路資源的權限。如果您沒有正確的權限，請連絡您的帳戶管理員。

## 建立和佈建 ExpressRoute 線路

### 1\.登入 Azure 入口網站

透過瀏覽器瀏覽至 [Azure 入口網站](http://portal.azure.com)，並使用您的 Azure 帳戶登入。

### 2\.建立新的 ExpressRoute 循環

>[AZURE.IMPORTANT] ExpressRoute 循環將會從發出服務金鑰時開始收費。請確定在連線提供者準備好佈建循環之後，再執行這項作業。

- **步驟 1.** 您可以選取建立新資源的選項來建立 ExpressRoute 循環。按一下 [新增] **>** [網路] **>** [ExpressRoute] \(如下圖所示)。 

	![](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)

- **步驟 2.** 按一下 [ExpressRoute] 之後，將會看到 [Create ExpressRoute circuit] \(建立 ExpressRoute 循環) 刀鋒視窗。填寫此刀鋒視窗上的值時，請確定您指定正確的 SKU 層和資料計量。

	- **層**會判斷是否啟用 ExpressRoute 標準或 ExpressRoute 進階附加元件。您可以指定 [標準] 取得標準 SKU，或針對進階附加元件指定 [進階]。

	- **資料計量**決定計費類型。您可以針對已計量資料傳輸方案指定 [已計量]，針對無限制資料方案選取 [無限制]。**附註︰**您可以將計費類型從 [已計量] 變更為 [無限制]，但無法將類型從 [無限制] 變更為 [已計量]。


		![](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)


### 3\.檢視循環和屬性

- **檢視循環** 
	
	您可以選取左側功能表上的 [所有資源] 來檢視您建立的所有循環。

	![](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

- **檢視屬性**
	
	選取循環，即可檢視該循環的屬性。在此刀鋒視窗上，記下循環的服務金鑰。您必須複製您循環的循環金鑰，並將其傳遞給服務提供者以完成佈建程序。循環金鑰為您循環所專用。

	![](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


### 4\.將服務金鑰傳送給連線提供者以進行佈建

在此刀鋒視窗上，[提供者狀態] 提供目前在服務提供者端的佈建狀態的相關資訊，[循環狀態] 提供 Microsoft 端的狀態。如需循環佈建狀態的詳細資訊，請參閱[工作流程](expressroute-workflows.md#expressroute-circuit-provisioning-states)文章。

當您建立新的 ExpressRoute 線路時，線路會是下列狀態：


提供者狀態︰未佈建<BR> 循環狀態：已啟用

![](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)


當連線提供者正在為您啟用線路時，線路會變更為下列狀態：

提供者狀態︰正在佈建<BR> 循環狀態：已啟用

若要能夠使用 ExpressRoute 線路，它必須處於下列狀態：

提供者狀態︰已佈建<BR> 循環狀態：已啟用


### 5\.定期檢查循環金鑰的情況和狀態

選取感興趣的循環，即可檢視該循環的屬性。檢查 [提供者狀態]，並確定其已移至 [已佈建]，再繼續進行。


![](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)


### 6\.建立路由組態

如需逐步指示，請參閱 [ExpressRoute 循環路由組態](expressroute-howto-routing-portal-resource-manager.md)以建立和修改循環對等。

>[AZURE.IMPORTANT] 這些指示只適用於由提供第 2 層連線服務的服務提供者所建立的循環。如果您使用的服務提供者是提供受管理的第 3 層服務 (通常是 IP VPN，如 MPLS)，您的連線提供者會為您設定和管理路由。

### 7\.將虛擬網路連結到 ExpressRoute 電路

接下來，將虛擬網路連結到 ExpressRoute 線路。當使用 Resource Manager 部署模型時，您可以使用[將虛擬網路連結到 ExpressRoute 循環](expressroute-howto-linkvnet-arm.md)文章。

## 取得 ExpressRoute 線路的狀態

選取循環，即可檢視該循環的狀態。

![](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


## 修改 ExpressRoute 線路

您可以修改 ExpressRoute 線路的某些屬性，而不會影響連線。目前，您無法使用 Azure 入口網站修改 ExpressRoute 循環屬性。不過，您可以使用 PowerShell 修改循環屬性。請參閱文件章節：[使用 PowerShell 修改 ExpressRoute 循環](expressroute-howto-circuit-arm.md#modify)。

您可以執行下列工作，而無需中途停機：

- 啟用或停用 ExpressRoute 線路的 ExpressRoute 進階附加元件。

- 增加 ExpressRoute 線路的頻寬。請注意，不支援將循環的頻寬降級。

- 將計量方案從 [已計量資料] 變更為 [無限制資料]。請注意，不支援將計量方案從 [無限制資料] 變更為 [已計量資料]。

-  您可以啟用和停用 [Allow Classic Operations] \(允許傳統作業)。

如需限制的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)頁面。


## 刪除和取消佈建 ExpressRoute 線路

您可以選取刪除圖示，刪除 ExpressRoute 循環。請注意：

- 您必須取消連結 ExpressRoute 循環的所有虛擬網路。如果此操作失敗，請檢查您是否有任何虛擬網路連結至線路。

- 如果已啟用 ExpressRoute 線路服務提供者佈建狀態，狀態會從已啟用狀態變成 [正在停用]。您必須與服務提供者一起合作，取消佈建他們那邊的線路。Microsoft 將繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。

- 若服務提供者在您執行上述 Cmdlet 之前已取消佈建線路 (服務提供者佈建狀態設定為 [未佈建])，Microsoft 將會取消佈建線路並停止向您收費。

## 後續步驟

建立好線路後，請務必執行下列作業：

- [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-portal-resource-manager.md)
- [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0427_2016-->