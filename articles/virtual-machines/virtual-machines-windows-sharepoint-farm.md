<properties
	pageTitle="建立 SharePoint 伺服器陣列 | Microsoft Azure"
	description="在 Azure 中快速建立新的 SharePoint 2013 或 SharePoint 2016 伺服器陣列。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="josephd"/>

# 建立 SharePoint 伺服器陣列

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統模型。

## SharePoint 2013 伺服器陣列

透過 Microsoft Azure 入口網站的 Marketplace，您可以快速建立預先設定的 SharePoint Server 2013 伺服器陣列。當您在開發/測試環境中需要基本或高可用性 SharePoint 伺服器陣列時，或是您要評估將 SharePoint Server 2013 做為組織的共同作業方案時，這將可為您省下許多時間。

> [AZURE.NOTE] Azure 入口網站的 Azure Marketplace 中的 **SharePoint 伺服器陣列**項目已移除。已由「SharePoint 2013 非 HA 伺服器陣列」和「SharePoint 2013 HA 伺服器陣列」項目將其取代。

基本的 SharePoint 伺服器陣列由這個組態中的三部虛擬機器所組成。

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

此伺服器陣列組態可用於 SharePoint 應用程式開發的簡化設定，或用於您第一次的 SharePoint 2013 評估。

若要建立基本 (三部伺服器) 的 SharePoint 伺服器陣列：

1. 按一下 [[這裡](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)]。
2. 按一下 [**部署**]。
3. 在 [**SharePoint 2013 非 HA 伺服器陣列**] 窗格中，按一下 [**建立**]。
4. 在 [建立 SharePoint 2013 非 HA 伺服器陣列] 窗格中，指定步驟的相關設定，然後按一下 [建立]。

高可用性 SharePoint 伺服器陣列由下列組態中的九個虛擬機器組成。

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

此伺服器陣列組態可用來測試較高的用戶端負載、外部 SharePoint 網站的高可用性，以及 SharePoint 伺服器陣列的 SQL Server AlwaysOn 可用性群組。此組態也可用於高可用性環境中的 SharePoint 應用程式開發。

若要建立高可用性 (九部伺服器) 的 SharePoint 伺服器陣列：

1. 按一下 [[這裡](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)]。
2. 按一下 [**部署**]。
3. 在 [**SharePoint 2013 HA 伺服器陣列**] 窗格中，按一下 [**建立**]。
4. 在 [建立 SharePoint 2013 HA 伺服器陣列] 窗格的七個步驟中指定設定，然後按一下 [建立]。

> [AZURE.NOTE] 您無法使用「Azure 免費試用版」來建立「SharePoint 2013 非 HA 伺服器陣列」或「SharePoint 2013 HA 伺服器陣列」。

Azure 入口網站會在具有網際網路對向網站空間的純雲端虛擬網路中，同時建立這兩種伺服器陣列。沒有任何站對站 VPN 或 ExpressRoute 連線會連回您的組織網路。

## SharePoint 2016 伺服器陣列

如需如何建置下列單一伺服器 SharePoint 2016 伺服器陣列的相關指示，請參閱[這個主題](https://technet.microsoft.com/library/mt723354.aspx)。

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## 管理 SharePoint 伺服器陣列

您可以透過遠端桌面連接管理這些伺服器陣列的伺服器。如需詳細資訊，請參閱[登入虛擬機器](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine)。

在「管理中心 SharePoint」網站中，您可以設定「我的網站」、SharePoint 應用程式和其他功能。如需詳細資訊，請參閱[設定 SharePoint](http://technet.microsoft.com/library/ee836142.aspx)。

## 後續步驟

- 探索 Azure 基礎結構服務中的其他 [SharePoint 組態](https://technet.microsoft.com/library/dn635309.aspx)。

<!---HONumber=AcomDC_0810_2016------>