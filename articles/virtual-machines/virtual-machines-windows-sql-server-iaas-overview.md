<properties
	pageTitle="在 Azure 虛擬機器上開始使用 SQL Server | Microsoft Azure"
	description="將您的內部部署 SQL Server 資料庫工作負載移至雲端與 Azure 虛擬機器。即刻開始使用預先設定的 SQL VM 映像。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="06/03/2016"
	ms.author="jroth"/>

# 在 Azure 虛擬機器上開始使用 SQL Server

本主題描述在 Azure 虛擬機器上執行 SQL Server 的選項，並提供可讓您快速入門的指引和資源。

您可能是想要將內部部署 SQL Server 工作負載移到雲端的資料庫系統管理員。或者，可能是針對 Azure 應用程式考慮 SQL Server 關聯式資料庫功能的開發人員。在 Azure 虛擬機器中執行 SQL Server 工作負載的優點為何？ 以下概觀影片討論這些優點並提供技術概觀。

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## 評估優點

開始之前，先評估在 Azure VM 上使用 SQL Server 的收穫。

當您將其他工作負載移至 Azure，例如企業應用程式，也必須將任何相依的 SQL Server 資料庫移至 Azure，以改善效能。不過，在 Azure VM 中裝載 SQL Server 可提供其他優點。例如，您會自動取得多個資料中心的存取權，以便獲得全域支援和災害復原。如需完整的案例和優點清單，請參閱 [Azure VM 產品頁面上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)。

> [AZURE.NOTE] 當您在評估 Azure VM 上的 SQL Server 時，也要檢閱 Azure 上的其他儲存體和 SQL 選項，例如 [SQL Database](../sql-database/sql-database-technical-overview.md)、[SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 和 [SQL Server Stretch Database](../sql -server-stretch-database/sql-server-stretch-database-overview.md)。如需詳細的比較，請參閱[選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)。

您決定要在 Azure VM 上執行 SQL Server 之後，您的第一個決策之一為是否要使用包含 SQL Server 授權成本的 VM 映像。另一個選擇是自備授權 (BYOL)，而只支付 VM 本身。下面兩節會說明這些選項。

## 選項 1︰ 部署的 SQL VM (每分鐘授權)
下表提供虛擬機器資源庫中的可用 SQL Server 映像比較。按一下任何連結，即可開始建立採用您指定的版本和作業系統的新 SQL VM。所有映像都包含 [SQL Server 授權成本](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)。

如需逐步指引，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。此外，檢閱 [SQL Server VM 的效能最佳作法](virtual-machines-windows-sql-performance.md)，該文說明如何選取適當的機器大小和佈建期間其他可用的功能。

|SQL Server 版本|作業系統|SQL Server 版本|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL Server 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## 選項 2︰ 部署的 SQL VM (BYOL)
另一個選項是自備授權 (BYOL)。在此案例中，您只需支付 VM 費用，而 SQL Server 授權不需任何額外的費用。若要使用自己的授權，請使用 SQL Server 版本及下列作業系統的矩陣。在入口網站中，映像名稱前面會加上 **{BYOL}**。

> [AZURE.IMPORTANT] 若要使用 BYOL VM 映像，您必須具備 Enterprise 合約和 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。您也需要您要使用的 SQL Server 版本的有效授權。您必須在佈建您的 VM 的 **10** 天內[提供必要的 BYOL 資訊給 Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf)。

適用[佈建教學課程](virtual-machines-windows-portal-sql-server-provision.md)中的指引，但您必須使用下列其中一個 **BYOL** 映像選項。此外，檢閱 [SQL Server VM 的效能最佳作法](virtual-machines-windows-sql-performance.md)，該文說明如何選取適當的機器大小和佈建期間其他可用的功能。

|SQL Server 版本|作業系統|SQL Server 版本|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

## 管理您的 SQL VM
佈建之後您的 SQL Server VM 之後，有幾個選用的管理工作。在某些方面，您可以設定和管理 SQL Server，如同您處理內部部署一樣。但是某些是 Azure 特有的工作。下列各節強調上述某些領域並提供詳細資訊的連結。

### 遷移資料

如果您有現有的資料庫，您會想要將該資料庫移到新佈建的 SQL VM。如需移轉選項的清單和指引，請參閱[將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)。

### 設定高可用性

如果您需要高可用性，請考慮設定 SQL Server 可用性群組。這牽涉到虛擬網路中多個 Azure VM。Azure 入口網站有一個範本，己經有您需要的設定。如需詳細資訊，請參閱[在 Azure Resource Manager 虛擬機器中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)。如果您想要手動設定「可用性群組」及關聯的接聽程式，請參閱[在 Azure VM 中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

如需其他高可用性注意事項，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)。

### 備份您的資料
直接在 Azure 中使用 Blob 儲存體來儲存資料庫備份。如需詳細資訊，請參閱[使用 Azure 儲存體進行 SQL Server 備份與還原](../sql-database/storage-use-storage-sql-server-backup-restore.md)。雖然這很適合用於 SQL VM，但也適用於內部部署 SQL Server 資料庫。如需備份與還原選項的概觀，請參閱 [Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-windows-sql-backup-recovery.md)。

Azure VM 也可以利用 SQL Server 專用的[自動備份](virtual-machines-windows-sql-automated-backup.md)和[自動化修補](virtual-machines-windows-sql-automated-patching.md)功能。

### 客戶經驗改進計畫 (CEIP)
預設會啟用「客戶經驗改進計畫」(CEIP)。這不是管理工作，除非您想要在佈建之後停用 CEIP。您可以透過遠端桌面連接到 VM，以自訂或停用 CEIP。然後執行 [SQL Server 錯誤和使用情況報告] 公用程式。請遵循指示來停用報告功能。

## 後續步驟
[探索學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)：Azure 虛擬機器上的 SQL Server。

其他問題？ 請先參閱 [Azure 虛擬機器上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)。也請將您的問題或意見加入任何 SQL VM 主題的底部來與 Microsoft 和社群互動。

<!---HONumber=AcomDC_0608_2016-->