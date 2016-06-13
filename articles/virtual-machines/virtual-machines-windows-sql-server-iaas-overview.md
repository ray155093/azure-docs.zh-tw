<properties
	pageTitle="虛擬機器上的 SQL Server 概觀 | Microsoft Azure"
	description="開始使用雲端中 Azure 虛擬機器上執行的 SQL Server 資料庫。此基礎結構即服務 (IaaS) 模型可讓您在 Azure 中執行 SQL Server 工作負載。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/25/2016"
	ms.author="jroth"/>

# Azure 虛擬機器上的 SQL Server 概觀

[Azure 虛擬機器上執行的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 可讓您將 SQL Server 資料庫架設於雲端。例如，您可以將內部部署資料庫移轉至預先設定好 Windows Server 2012 R2 和 SQL Server 2014 Enterprise 版的 Azure VM。但是，有許多其他可能案例，例如支援高可用性或混合式架構並可連線到內部部署網路的多電腦組態。

如需絕佳的概觀，請觀看以下影片︰

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## SQL 供應項目

在 Azure VM 上執行 SQL Server，是用來將關聯式資料儲存到 Azure 的選項之一。下表摘要說明各種供應項目。

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| SQL 供應項目 | 說明 |
|---:|---|---|
|![Azure 虛擬機器上的 SQL Server](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)|在 Azure 虛擬機器上執行 SQL Server。在零售版 SQL Server 上直接管理虛擬機器，並執行您的資料庫。 |
|![SQL Database](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[SQL Database](https://azure.microsoft.com/services/sql-database/)|使用 SQL Database 服務來存取及調整您的資料庫，而無需管理基礎結構。|
|![SQL 資料倉儲](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[SQL 資料倉儲](https://azure.microsoft.com/zh-TW/services/sql-data-warehouse/)|使用 Azure SQL 資料倉儲來處理大量的關聯式與非關聯式資料。提供可調整的資料倉儲功能，做為一項服務。|
|![SQL Server Stretch Database](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[SQL Server Stretch Database](https://azure.microsoft.com/zh-TW/services/sql-server-stretch-database/)|以動態方式將內部部署交易資料從 Microsoft SQL Server 2016 延展到 Azure。|

>[AZURE.NOTE] 如需 SQL VM 和 SQL Database 的完整比較，請參閱[選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)。

## 部署 SQL Server VM

若要在 Azure 中建立 SQL Server 虛擬機器，必須先取得 Azure 平台訂用帳戶。您可以在 [[購買選項](https://azure.microsoft.com/pricing/purchase-options/)] 中購買 Azure 訂用帳戶。若要免費試用，請造訪 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

在註冊訂用帳戶之後，若要在 Azure 中部署 SQL Server 虛擬機器，最簡單的方式就是[在 Azure 入口網站中佈建 SQL Server 機器資源庫映像](virtual-machines-windows-portal-sql-server-provision.md)。這些映像包含的 SQL Server 授權，是以 VM 為計價單位。

請務必注意，建立及管理 Azure 虛擬機器的模型有兩種：傳統及 Resource Manager。Microsoft 建議讓大部分的新部署使用 Resource Manager 模式。如需詳細資訊，請參閱[了解資源管理員部署和傳統部署](../resource-manager-deployment-model.md)。每個主題應該清楚描述其目標模型，除非它和本文一樣同時適用於傳統和 Resource Manager 模型。

## 選擇 SQL VM 映像
下表提供虛擬機器資源庫中的可用 SQL Server 映像比較。按一下版本與作業系統表格中任何一個連結。然後按一下 Marketplace 頁面上的 [建立虛擬機器] 按鈕。

|SQL Server 版本|作業系統|SQL Server 版本|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rtmenterprisewindowsserver2012r2/)、[Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rtmstandardwindowsserver2012r2/)、[Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rtmwebwindowsserver2012r2/)、[Express](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rtmexpresswindowsserver2012r2/)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014sp1enterprisewindowsserver2012r2/)、[Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014sp1standardwindowsserver2012r2/)、[Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014sp1webwindowsserver2012r2/)、[Express](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014sp1expresswindowsserver2012r2/)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/)、[Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/)、[Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012r2/)、[Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012r2/)、[Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/)、[Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/)、[Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/)、[Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2expresswindowsserver2012/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2008r2sp3enterprisewindowsserver2008r2/)、[Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2008r2sp3standardwindowsserver2008r2/)、[Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2008r2sp3webwindowsserver2008r2/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2008r2sp3expresswindowsserver2012/)|

>[AZURE.NOTE] 預設會啟用「客戶經驗改進計畫」(CEIP)。如有必要，佈建虛擬機器之後可以自訂或停用 CEIP。以遠端桌面連接至 VM，並執行 **SQL Server 錯誤和使用方式報表**公用程式。

需要建立新 SQL VM 的詳細說明嗎？ 請參閱[佈建教學課程](virtual-machines-windows-portal-sql-server-provision.md)中的詳細逐步解說。

除了這些預先設定的映像之外，您也可以在不預先安裝 SQL Server 的狀況下，[建立 Azure 虛擬機器](virtual-machines-windows-hero-tutorial.md)。您可以安裝任何您擁有授權的 SQL Server 執行個體。將您的授權移轉至 Azure，以使用 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)，在 Azure 虛擬機器中執行 SQL Server。在此案例中，您只需支付與虛擬機器相關聯的 Azure 計算和儲存體[成本](https://azure.microsoft.com/pricing/details/virtual-machines/)。

為了判斷 SQL Server 映像的最佳虛擬機器組態設定，請檢閱 [SQL Server VM 的效能最佳做法](virtual-machines-windows-sql-performance.md)。針對生產環境工作負載，為 SQL Server Enterprise 版本建議的虛擬機器大小至少是 **DS3**。針對 Standard 版本上的生產環境工作負載，建議的虛擬機器大小至少是 **DS2**。

## 遷移資料

在 SQL Server 虛擬機器啟動並執行之後，您可能想要將現有的資料庫移轉至機器。如需移轉選項的清單和指引，請參閱[將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)。

## 高可用性

如果您需要高可用性，請考慮設定 SQL Server 可用性群組。這牽涉到虛擬網路中多個 Azure VM。Azure 入口網站有一個範本，己經有您需要的設定。如需詳細資訊，請參閱[在 Azure Resource Manager 虛擬機器中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)。

如果您想要手動設定「可用性群組」及關聯的接聽程式，請參閱[在 Azure VM 中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

如需其他高可用性注意事項，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)。

## 備份與還原
至於內部部署資料庫，Azure 可以充當次要資料中心，負責儲存 SQL Server 備份檔案。如需備份與還原選項的概觀，請參閱 [Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-windows-sql-backup-recovery.md)。

[SQL Server 備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx) 會將 Azure 的備份檔案儲存至 Azure Blob 儲存體。[SQL Server 託管備份](https://msdn.microsoft.com/library/dn449496.aspx)可以讓您在 Azure 中安排和保留備份。這些服務可以搭配內部部署 SQL Server 執行個體或 Azure VM 上執行的 SQL Server。Azure VM 也可以利用 SQL Server 專用的[自動備份](virtual-machines-windows-classic-sql-automated-backup.md)和[自動化修補](virtual-machines-windows-classic-sql-automated-patching.md)功能。

## 後續步驟

首先，[在 Azure 入口網站中建立您自己的 SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md)。

然後，當您考慮將您的 SQL Server 工作負載移至 Azure VM 時，請檢閱[效能最佳做法](virtual-machines-windows-sql-performance.md)和[移轉技術](virtual-machines-windows-migrate-sql.md)。

您對 Azure 虛擬機器的 SQL Server 還有很多疑問嗎？ 請先參閱 [Azure 虛擬機器上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)。也請將您的問題或意見加入任何 SQL VM 主題的底部來與 Microsoft 和社群互動。

<!---HONumber=AcomDC_0601_2016-->