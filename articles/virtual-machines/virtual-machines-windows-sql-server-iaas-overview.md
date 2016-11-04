---
title: Azure 虛擬機器上的 SQL Server 概觀 | Microsoft Docs
description: 深入了解如何在 Azure 虛擬機器上執行完整的 SQL Server 版本。 取得所有 SQL Server VM 映像和相關內容的直接連結。
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: jhubbard
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: jroth

---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SQL Server 概觀
本主題說明在 Azure 虛擬機器 (VM) 上執行 SQL Server 的選項，以及提供[入口網站映像的連結](#option-1-create-a-sql-vm-with-per-minute-licensing)和[常見工作](#manage-your-sql-vm)的概觀。

> [!NOTE]
> 如果您已熟悉 SQL Server，只想要了解如何部署 SQL Server VM，請參閱[ Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。
> 
> 

## <a name="overview"></a>Overview
如果您是資料庫管理員或開發人員，Azure VM 提供您將內部部署 SQL Server 工作負載和應用程式移動到雲端的方式。 下列影片提供 SQL Server Azure VM 的技術概觀。

> [!VIDEO https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016/player]
> 
> 

影片涵蓋下列領域︰

| 時間 | 領域 |
| --- | --- |
| 00:21 |何謂 Azure VM？ |
| 01:45 |安全性 |
| 02:50 |連線能力 |
| 03:30 |儲存體可靠性和效能 |
| 05:20 |VM 大小 |
| 05:54 |高可用性和 SLA |
| 07:30 |組態支援 |
| 08:00 |監視 |
| 08:32 |示範︰建立 SQL Server 2016 VM |

> [!NOTE]
> 影片的重點在於 SQL Server 2016，但 Azure 會提供許多 SQL Server 版本 (包括 2008、2012、2014 和 2016) 的 VM 映像。 
> 
> 

## <a name="understand-your-options"></a>了解您的選項
可能會有許多原因讓您選擇主控 Azure 中的資料。 如果您的應用程式移至 Azure，則也會改善移動資料的效能。 但是還有其他好處。 您會自動取得多個資料中心的存取權，以獲得全域支援和災害復原。 資料也是高度安全且具有持久性。

在 Azure VM 上執行的 SQL Server，是用來將關聯式資料儲存到 Azure 的選項之一。 下表提供 Azure 上的 SQL 產品簡短摘要。

| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | SQL 供應項目 | 說明 |
| ---:| --- | --- |
| ![Azure 虛擬機器上的 SQL Server](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png) |[Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) |在 Azure 虛擬機器上執行 SQL Server (本主題之重點)。 在零售版 SQL Server 上直接管理虛擬機器，並執行您的資料庫。 |
| ![SQL Database](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png) |[SQL Database](https://azure.microsoft.com/services/sql-database/) |使用 SQL Database 服務來存取及調整您的資料庫，而無需管理基礎結構。 |
| ![SQL 資料倉儲](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png) |[SQL 資料倉儲](https://azure.microsoft.com/en-us/services/sql-data-warehouse/) |使用 Azure SQL 資料倉儲來處理大量的關聯式與非關聯式資料。 提供可調整的資料倉儲功能，做為一項服務。 |
| ![SQL Server Stretch Database](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png) |[SQL Server Stretch Database](https://azure.microsoft.com/en-us/services/sql-server-stretch-database/) |以動態方式將內部部署交易資料從 Microsoft SQL Server 2016 延展到 Azure。 |

有了這些不同的選項，在 Azure VM 上執行的 SQL Server 對於幾個案例而言會是很好的選擇。 例如，您可能要盡可能以類似方法將 Azure VM 設定為內部部署 SQL Server 的電腦。 或者，您可能要在相同的資料庫伺服器上執行其他應用程式和服務。 有兩個資源可協助您仔細思考更多的決策因素︰

* [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 提供在 Azure VM 上使用 SQL Server 的最佳案例概觀。 
* [選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) 可提供 SQL Database 和在 VM 上執行之 SQL Server 的完整比較。

## <a name="create-a-new-sql-vm"></a>建立新的 SQL VM
下列各節提供 Azure 入口網站中 SQL Server 虛擬機器資源庫映像的直接連結。 視您選取的映像而定，您可以按分鐘支付 SQL Server 授權成本，或者可以自備授權 (BYOL)。

在[在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)教學課程中尋找此程序的逐步指引。 此外，檢閱 [SQL Server VM 的效能最佳作法](virtual-machines-windows-sql-performance.md)，該文說明如何選取適當的機器大小和佈建期間其他可用的功能。

## <a name="option-1:-create-a-sql-vm-with-per-minute-licensing"></a>選項 1︰利用每分鐘授權建立 SQL VM
下表提供虛擬機器資源庫中的可用 SQL Server 映像比較。 按一下任何連結，即可開始建立採用您指定的版本和作業系統的新 SQL VM。

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL 2016** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2)[Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2) |
| **SQL 2014 SP1** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2) |
| **SQL 2014** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2) |
| **SQL 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012) |
| **SQL 2008 R2 SP3** |Windows Server 2008 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2) |
| **SQL 2008 R2 SP3** |Windows Server 2012 |[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012) |

## <a name="option-2:-create-a-sql-vm-with-an-existing-license"></a>選項 2︰利用現有授權建立 SQL VM
您也可以自備授權 (BYOL)。 在此案例中，您只需支付 VM 費用，而 SQL Server 授權不需任何額外的費用。 若要使用自己的授權，請使用 SQL Server 版本及下列作業系統的矩陣。 在入口網站中，這些映像名稱前面會加上 **{BYOL}**。

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL Server 2016** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2) |
| **SQL Server 2014 SP1** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

> [!IMPORTANT]
> 若要使用 BYOL VM 映像，您必須具備 Enterprise 合約和 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。 您也需要您要使用的 SQL Server 版本的有效授權。 您必須在佈建您的 VM 的 [10](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) 天內 **提供必要的 BYOL 資訊給 Microsoft** 。
> 
> 

## <a name="manage-your-sql-vm"></a>管理您的 SQL VM
佈建之後您的 SQL Server VM 之後，有幾個選用的管理工作。 在許多方面，您設定和管理 SQL Server 的方式，完全如同您管理內部部署 SQL Server 執行個體。 不過，有些是 Azure 特有的工作。 下列各節強調上述某些領域並提供詳細資訊的連結。

### <a name="connect-to-the-vm"></a>連接至 VM
最基本的管理步驟之一，是透過工具連接到您的 SQL Server VM，例如 SQL Server Management Studio (SSMS)。 如需如何連接至新 SQL Server VM 的指示，請參閱[連接 Azure 上的 SQL Server 虛擬機器](virtual-machines-windows-sql-connect.md)。

### <a name="migrate-your-data"></a>遷移資料
如果您有現有的資料庫，您會想要將該資料庫移到新佈建的 SQL VM。 如需移轉選項的清單和指引，請參閱 [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)。

### <a name="configure-high-availability"></a>設定高可用性
如果您需要高可用性，請考慮設定 SQL Server 可用性群組。 這牽涉到虛擬網路中多個 Azure VM。 Azure 入口網站有一個範本，己經有您需要的設定。 如需詳細資訊，請參閱 [在 Azure Resource Manager 虛擬機器中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)。 如果您想要手動設定「可用性群組」及關聯的接聽程式，請參閱 [在 Azure VM 中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

如需其他高可用性注意事項，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)。

### <a name="back-up-your-data"></a>備份您的資料
Azure VM 可以利用 [自動備份](virtual-machines-windows-sql-automated-backup.md)，以定期建立資料庫的備份至 Blob 儲存體。 您也可以手動使用此技術。 如需詳細資訊，請參閱 [使用 Azure 儲存體進行 SQL Server 備份與還原](virtual-machines-windows-use-storage-sql-server-backup-restore.md)。 如需所有備份與還原選項的概觀，請參閱 [Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-windows-sql-backup-recovery.md)。

### <a name="automate-updates"></a>自動更新
Azure VM 可以使用 [自動修補](virtual-machines-windows-sql-automated-patching.md) 來排程維護期間，以便自動安裝重要的 Windows 和 SQL Server 更新。

### <a name="customer-experience-improvement-program-(ceip)"></a>客戶經驗改進計畫 (CEIP)
預設會啟用「客戶經驗改進計畫」(CEIP)。 這會定期將報表傳送至 Microsoft 以協助改進 SQL Server。 CEIP 不需要管理工作，除非您要在佈建之後將它停用。 您可以透過遠端桌面連接到 VM，以自訂或停用 CEIP。 然後執行 [SQL Server 錯誤和使用情況報告]  公用程式。 請遵循指示來停用報告功能。 

如需詳細資訊，請參閱[接受授權條款](https://msdn.microsoft.com/library/ms143343.aspx) CEIP 一節。 

## <a name="next-steps"></a>後續步驟
[探索學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) ：Azure 虛擬機器上的 SQL Server。

其他問題？ 請先參閱 [Azure 虛擬機器上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)。 也請將您的問題或意見加入任何 SQL VM 主題的底部來與 Microsoft 和社群互動。

<!--HONumber=Oct16_HO2-->


