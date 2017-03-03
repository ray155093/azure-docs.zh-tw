---
title: "使用 PowerShell 為 Azure SQL Database 設定作用中異地複寫 | Microsoft Docs"
description: "使用 PowerShell 為 Azure SQL Database 設定作用中異地複寫"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: bc5e50e4-bbb2-4ce1-9ee5-9a632de6fa06
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/14/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: 85efdb2a5b9571d76338aeb0871b729693b63dcb
ms.lasthandoff: 02/16/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-powershell"></a>使用 PowerShell 為 Azure SQL Database 設定作用中異地複寫

本文說明如何使用 PowerShell 為 SQL Database 設定作用中異地複寫。

若要使用 PowerShell 起始容錯移轉，請參閱 [使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉](sql-database-geo-replication-failover-powershell.md)。

> [!NOTE]
> 主動式異地複寫 (可讀取次要複本) 現在可供所有服務層中的所有資料庫使用。 2017 年 4 月將淘汰不可讀取的次要類型，而現有的不可讀取資料庫將自動升級為可讀取的次要複本。
> 
> 

若要使用 PowerShell 設定作用中異地複寫，您需要下列項目：

* Azure 訂用帳戶。 
* Azure SQL Database - 您想要複寫的主要資料庫。
* Azure PowerShell 1.0 或更新版本。 依照 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)，即可以下載並安裝 Azure PowerShell 模組。

## <a name="configure-your-credentials-and-select-your-subscription"></a>設定您的認證並選取您的訂用帳戶
您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。 在登入畫面中，請輸入與登入 Azure 入口網站相同的電子郵件和密碼。

    Login-AzureRmAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。

### <a name="select-your-azure-subscription"></a>選取您的 Azure 訂用帳戶
若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID。 您可以複製上一個步驟中顯示資訊的訂用帳戶 ID，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureRmSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊，以取得詳細資訊。 下列 Cmdlet 使用訂用帳戶 ID 來設定目前的訂用帳戶：

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行 **Select-AzureRmSubscription** 之後，您會返回 PowerShell 提示字元。

## <a name="add-secondary-database"></a>加入次要資料庫
下列步驟會在「異地複寫」合作關係中建立新的次要資料庫。  

若要啟用次要複本，您必須是訂用帳戶擁有者或共同擁有者。 

您可以使用 **New-AzureRmSqlDatabaseSecondary** Cmdlet，將夥伴伺服器上的次要資料庫加入到您所連接的伺服器上的本機資料庫 (主要資料庫)。 

這個 Cmdlet 會將 **Start-AzureSqlDatabaseCopy** 取代為 **-IsContinuous** 參數。  它會輸出可供其他 Cmdlet 用來清楚地識別特定複寫連結的 **AzureRmSqlDatabaseSecondary** 物件。 建立次要資料庫並將其完全植入時，這個 Cmdlet 會傳回。 視資料庫的大小而定，可能需要從數分鐘到數小時的時間。

次要伺服器上的複寫資料庫會具備與主要伺服器上的資料庫相同的名稱，並且預設具有相同的服務層級。 次要資料庫可以是可讀取或不可讀取，並且可以是獨立資料庫或在彈性集區中。 如需詳細資訊，請參閱 [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689\(v=azure.300\).aspx) 和[服務層](sql-database-service-tiers.md)。
建立並植入次要複本之後，就會開始從主要資料庫將資料複寫到新的次要資料庫。 下列步驟說明如何使用 PowerShell 以獨立資料庫的形式或在彈性集區中建立不可讀取和可讀取的次要複本，來完成這項工作。

如果夥伴資料庫已經存在 (例如，因終止先前的「異地複寫」關聯性所導致)，命令將會失敗。

### <a name="add-a-non-readable-secondary-standalone-database"></a>新增不可讀取的次要複本 (獨立資料庫)
下列命令會在資源群組 "rg2" 伺服器 "srv2" 中建立資料庫 "mydb" 的不可讀取次要複本：

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-standalone-database"></a>新增可讀取的次要複本 (獨立資料庫)
下列命令會在資源群組 "rg2" 伺服器 "srv2" 中建立資料庫 "mydb" 的可讀取次要複本：

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-pool"></a>新增不可讀取次要複本 (彈性集區)
下列命令會在資源群組 "rg2" 之伺服器 "srv2" 中名為 "ElasticPool1" 的彈性集區內建立資料庫 "mydb" 的不可讀取次要複本：

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-pool"></a>新增可讀取次要複本 (彈性集區)
下列命令會在資源群組 "rg2" 之伺服器 "srv2" 中名為 "ElasticPool1" 的彈性集區內建立資料庫 "mydb" 的可讀取次要複本：

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>移除次要資料庫
使用 **Remove-AzureRmSqlDatabaseSecondary** Cmdlet 以永久終止次要資料庫與其主要資料庫之間的複寫關係。 關聯性終止之後，次要資料庫會成為讀寫資料庫。 如果與次要資料庫的連線中斷，命令將會成功，但次要資料庫必須等到連線恢復後才會變成可讀寫。 如需詳細資訊，請參閱 [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457\(v=azure.300\).aspx) 和[服務層級](sql-database-service-tiers.md)。

這個 Cmdlet 會取代用於複寫的 Stop-AzureSqlDatabaseCopy。 

這項移除作業相當於強制終止，會移除複寫連結並將先前的次要資料庫保留做為終止之前未完全複寫的獨立資料庫。 將會清除先前的主要和先前的次要資料庫上連結的所有資料 (若有)。 移除複寫連結時這個 Cmdlet 會傳回。 

為了移除次要資料庫，根據 RBAC，使用者應該具備主要和次要資料庫的寫入權限。 如需詳細資料，請參閱角色型存取控制。

以下會移除資源群組 "rg2" 的伺服器 "srv2" 名為 "mydb" 的資料庫複寫連結。 

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink -SecondaryResourceGroup "rg2" -PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>監視異地複寫組態和健康狀態
監視工作包括異地複寫組態的監視和監視資料複寫健康狀態。  

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330\(v=azure.300\).aspx) 可用來擷取在 sys.geo_replication_links 目錄檢視中顯示的正向複寫連結的相關資訊。

下列命令會擷取主要資料庫 "mydb" 與資源群組 "rg2" 上伺服器 "srv2" 上的次要複本之間複寫連結的狀態。

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink -PartnerResourceGroup "rg2” -PartnerServerName "srv2”


## <a name="next-steps"></a>後續步驟
* 若要深入了解作用中異地複寫，請參閱[作用中異地複寫](sql-database-geo-replication-overview.md)
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)


