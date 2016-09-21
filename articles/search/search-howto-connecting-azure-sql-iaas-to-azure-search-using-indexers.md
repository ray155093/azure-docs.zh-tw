<properties 
	pageTitle="在 Azure 虛擬機器上設定從 Azure 搜尋服務索引子到 SQL Server 的連線 | Microsoft Azure | 索引子" 
	description="啟用加密的連線並設定防火牆，以允許在 Azure 虛擬機器 (VM) 上從 Azure 搜尋服務的索引子連接到 SQL Server。" 
	services="search" 
	documentationCenter="" 
	authors="jack4it" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/06/2016" 
	ms.author="jackma"/>

# 在 Azure VM 上設定從 Azure 搜尋服務索引子到 SQL Server 的連線

如[使用索引子將 Azure SQL Database 連接至 Azure 搜尋服務](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions)中所述，Azure 搜尋服務支援針對 **Azure VM 上的 SQL Server** (或簡稱 **SQL Azure VM**) 建立索引子，但必須先處理兩個安全性相關的必要條件。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 啟用加密的連線

Azure 搜尋服務需要安全連線才能從資料庫讀取資料。這表示您必須設定 SSL 憑證，以在 SQL Azure VM 上啟用加密的連線。

對 SQL Server 啟用加密連線的步驟記錄在[啟用 Database Engine 的加密連線](https://msdn.microsoft.com/library/ms191192.aspx)文件中，但對於像是從 Azure 搜尋服務連接到 SQL Azure VM 的公用網際網路連線來說，需要符合幾項額外需求才能完成這項工作。

### 指定 SSL 憑證中的 FQDN

SSL 憑證的主體名稱必須是 SQL Azure VM 的完整網域名稱 (或 **FQDN**)。它和您在搜尋服務中建立資料來源時，於資料庫連接字串中指定的 FQDN 相同。**Resource Manager** VM 的 FQDN 的格式為 `<your-VM-name>.<region>.cloudapp.azure.com`。如果您仍在使用「傳統」VM，其格式則為 `<your-cloud-service-name.cloudapp.net>`。您可以在 [Azure 入口網站](https://portal.azure.com/)中找到 SQL Azure VM 的 FQDN 為 DNS 名稱/標籤。

### 使用 REGEDIT 設定 SSL 憑證

SQL Server 組態管理員無法在 [憑證] 下拉式清單中顯示 FQDN SSL 憑證，如文件中所述。因應措施是藉由編輯以下登錄機碼來設定 SSL 憑證︰**HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server[MSSQL13.MSSQLSERVER]\\MSSQLServer\\SuperSocketNetLib\\Certificate**。*[MSSQL13.MSSQLSERVER]* 部分會依據您的 SQL Server 版本和執行個體名稱不同而改變。您需要利用在 SQL Azure VM 上所安裝 SSL 憑證的「指紋」來更新這個機碼。

### 授與權限給服務帳戶

務必要將 SSL 憑證私密金鑰的適當權限授與 SQL Server 服務帳戶。如果您忽略此步驟，SQL Server 將會無法啟動。

## 設定防火牆以允許從 Azure 搜尋服務連線

設定防火牆與相對應的 Azure 端點或存取控制清單 (ACL)，讓他人能夠存取您的 Azure VM。您很可能已完成此設定，允許自己的應用程式邏輯連接至您的 SQL Azure VM。這和 Azure 搜尋服務連線至 SQL Azure VM 並無不同。如果您尚未完成這部分，請記住下列幾個安全性作法。

如果您是使用 **Resource Manager** VM，請參閱[使用 Resource Manager 連線到 Azure 上的 SQL Server 虛擬機器](../virtual-machines/virtual-machines-windows-sql-connect.md)。如果您仍在使用「傳統」VM，請參閱[連線到 Azure 傳統部署上的 SQL Server 虛擬機器](../virtual-machines/virtual-machines-windows-classic-sql-connect.md)。

### 限制對搜尋服務 IP 位址的存取

對於任一部署模型，設定要從 Azure 搜尋服務連線時，強烈建議您在 ACL 中限制對搜尋服務 IP 位址的存取，而不是讓 SQL Azure VM 對任何連接要求來者不拒。您可以 Ping 搜尋服務的 FQDN (例如，`<your-search-service-name>.search.windows.net`)，輕鬆地找出 IP 位址。

### 設定 IP 位址範圍

請注意，如果您的搜尋服務只有一個搜尋單位 (也就是有一個複本和一個分割區)，在例行服務重新啟動期間，IP 位址可能會變更。若要避免連線失敗，您應該針對搜尋服務佈建所在的 Azure 區域，指定 IP 位址範圍。將公用 IP 位址配置給 Azure 資源的 IP 範圍清單已發佈於 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

### 包含 Azure 搜尋服務入口網站 IP 位址

此外，如果您使用 Azure 入口網站建立索引子，Azure 搜尋服務入口網站邏輯也需要在建立期間存取您的 SQL Azure VM。Ping `stamp1.search.ext.azure.com` 和 `stamp2.search.ext.azure.com`，即可找到 Azure 搜尋服務入口網站 IP 位址。

## 後續步驟

解決上述組態需求之後，您現在就可以在 Azure VM 指定 SQL Server 作為 Azure 搜尋服務索引子的資料來源。如需詳細資訊，請參閱[使用索引子將 Azure SQL Database 連接至 Azure 搜尋服務](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)。

<!----HONumber=AcomDC_0907_2016-->