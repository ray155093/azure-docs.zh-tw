---
title: "雲端商務持續性 - 還原已刪除的資料庫- SQL Database | Microsoft Docs"
description: "了解還原時間點，其可讓您復原 Azure SQL Database 到先前的時間 (最多 35 天)。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: fd1d334d-a035-4a55-9446-d1cf750d9cf7
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/11/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 14ba9a0d5a373dc46fcb38ad157e97496311c0d3
ms.openlocfilehash: 9db1ce94e4cfdf6caadb22111ffe05afb96220d1


---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>使用自動資料庫備份復原 Azure SQL Database
SQL Database 提供三個選項，可使用 [SQL Database 自動備份](sql-database-automated-backups.md)進行資料庫復原。 您可以在 [保留期限](sql-database-service-tiers.md) 內從服務起始的備份還原資料庫至下列資料庫：

* 同一部邏輯伺服器上的新資料庫，復原到保留期限內指定的時間點。 
* 同一部邏輯伺服器上的資料庫，復原到已刪除資料庫的刪除時間。
* 任何區域中任意邏輯伺服器上的新資料庫，復原到異地複寫 Blob 儲存體 (RA-GRS) 中最新的每日備份。

您也可以使用 [SQL Database 自動備份](sql-database-automated-backups.md)，在任何區域中任意邏輯伺服器上建立[資料庫複本](sql-database-copy.md)，其交易會與目前的 SQL Database 一致。 您可以使用資料庫複本並 [匯出到 BACPAC](sql-database-export.md) ，以針對超過您保存期限的長期儲存體封存資料庫中交易一致的複本，或者將資料庫複本傳送到 SQL Server 的內部部署或 Azure VM 執行個體。

## <a name="recovery-time"></a>復原時間
使用自動資料庫備份還原資料庫的復原時間會受到一些因素所影響： 

* 資料庫的大小
* 資料庫的效能層級
* 相關的交易記錄檔數目
* 需要重新執行以復原到還原點的活動數目
* 還原到不同區域的網路頻寬 
* 要在目標區域中處理的並行還原要求數目。 
  
  針對非常大型及/或作用中的資料庫，還原可能需要數小時。 如果某區域中的中斷延長，其他區域可能要處理大量的異地還原要求。 如果有許多要求，可能會增加該區域中的資料庫復原時間。 大多數資料庫的還原會在 12 小時內完成。
  
  沒有可執行大量還原的內建功能。 [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) 指令碼是其中一種完成這項工作的範例。

> [!IMPORTANT]
> 若要使用自動備份復原，您必須是訂用帳戶中 SQL Server 參與者角色的成員或訂用帳戶擁有者。 您可以使用 Azure 入口網站、PowerShell 或 REST API 復原。 您無法使用 Transact-SQL。 
> 
> 

## <a name="point-in-time-restore"></a>還原時間點
還原時間點可讓您使用 [SQL Database 自動備份](sql-database-automated-backups.md)，將現有資料庫當成新資料庫還原到同一部邏輯伺服器上較早的時間點。 您無法覆寫現有的資料庫。 您可以使用 [Azure 入口網站](sql-database-point-in-time-restore-portal.md)、[PowerShell](sql-database-point-in-time-restore-powershell.md) 或 [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) 還原到較早的時間點。


資料庫可以還原到任何效能層級或彈性集區。 您必須確定您在邏輯伺服器或彈性集區上有足夠的 DTU 配額。 請記住，還原會建立新的資料庫，以及還原資料庫的服務層和效能層級可能會與實際資料庫的目前狀態不同。 完成之後，還原的資料庫通常是可在線上完全存取的資料庫，根據其服務層和效能層級依正常費率計費。 在完成資料庫還原之前，不會產生任何費用。

基於復原目的，您通常會將資料庫還原到較早的時間點。 當您執行此動作時，可以將還原的資料庫視為原始資料庫的替代品，或用它來從原始資料庫擷取資料並將其更新。 

* ***資料庫取代：***如果要使用還原資料庫做為原始資料庫的替代品，您應該確認效能層級及/或服務層適當，並在必要時調整資料庫大小。 您可以重新命名原始資料庫，然後使用 T-SQL 中的 ALTER DATABASE 命令提供原始名稱給還原的資料庫。 
* ***資料復原︰***如果您打算從還原的資料庫中擷取資料以便從使用者或應用程式錯誤中復原，您就必須個別撰寫並執行從還原資料庫中擷取資料到原始資料庫所需的資料復原指令碼。 雖然還原作業可能要花很長的時間才能完成，但是還原中的資料庫在整個過程中將會顯示於資料庫清單上。 如果您在還原期間刪除該資料庫，即會取消作業，而且不會針對未完成還原的資料庫向您收費。 

如需使用還原時間點來從使用者與應用程式錯誤中復原的詳細資訊，請參閱 [還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>還原已刪除的資料庫
還原已刪除的資料庫可讓您使用 [SQL Database 自動備份](sql-database-automated-backups.md)，將已刪除的資料庫還原到同一部邏輯伺服器上已刪除資料庫的刪除時間。 

> [!IMPORTANT]
> 如果您刪除 Azure SQL Database 伺服器執行個體，其所有資料庫也會一併刪除且無法加以復原。 目前不支援還原已刪除的伺服器。
> 
> 

您可以針對已還原資料庫使用相同或新的資料庫名稱。 您可以使用 [Azure 入口網站](sql-database-restore-deleted-database-portal.md)、[PowerShell](sql-database-restore-deleted-database-powershell.md) 或 [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)。 


## <a name="geo-restore"></a>異地還原
異地還原可讓您從最新異地複寫的 [每日自動備份](sql-database-automated-backups.md)，在任何 Azure 區域中的任何伺服器上還原 SQL Database。 異地還原使用異地備援備份做為其來源，即使因為中斷而無法存取資料庫或資料中心，也能用來復原資料庫。 您可以使用 [Azure 入口網站](sql-database-geo-restore-portal.md)、[PowerShell](sql-database-geo-restore-powershell.md) 或 [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 


當您的資料庫因為裝載資料庫區域中的事件而無法使用時，異地還原就是預設的復原選項。 如果區域中的大規模意外導致您無法使用資料庫應用程式，可以使用異地還原，將資料庫從最近的備份還原到任何其他區域中的伺服器。 所有備份都會進行異地複寫，而且可以在採用備份與將其異地複寫至不同區域中的 Azure blob 之間具有延遲。 此延遲可能長達一小時，因此當災害事件發生時，最長可能有 1 小時的資料遺失，亦即，最多 1 小時的 RPO。 以下顯示從上次每日備份起的資料庫還原。

![異地還原](./media/sql-database-geo-restore/geo-restore-2.png)

如需使用異地還原來從中斷復原的詳細資訊，請參閱 [從中斷復原](sql-database-disaster-recovery.md)

> [!IMPORTANT]
> 異地還原可供所有服務層使用，而且它是 SQL Database 中最基本的災害復原解決方案，具備最長的 RPO 和預估復原時間 (ERT)。 對於大小上限為 2 GB 的「基本」資料庫，異地還原提供 ERT 為 12 小時的合理 DR 解決方案。 對於較大的「標準」或「高階」資料庫，如果需要大幅縮短復原時間，或為了降低資料遺失的可能性，您應該考慮使用「主動式異地複寫」。 「主動式異地複寫」提供明顯較低的 RPO 和 ERT，因為它只需要您起始對連續複寫次要資料庫的容錯移轉。 如需詳細資料，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)。
> 
> 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>使用自動備份以程式設計方式執行復原
如上所述，除了 Azure 入口網站，還可使用 Azure PowerShell 和 REST API，以程式設計方式執行資料庫復原。 下表描述可用的命令集。

### <a name="powershell"></a>PowerShell
| Cmdlet | 說明 |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx) |取得一或多個資料庫。 |
| [Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx) |取得可還原的已刪除資料庫。 |
| [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) |取得資料庫的異地備援備份。 |
| [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) |還原 SQL Database。 |
|  | |

### <a name="rest-api"></a>REST API
| API | 說明 |
| --- | --- |
| [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |還原資料庫 |
| [取得建立或更新資料庫狀態](https://msdn.microsoft.com/library/azure/mt643934.aspx) |在還原作業期間傳回狀態 |
|  | |

## <a name="summary"></a>摘要
自動備份可在發生使用者和應用程式錯誤、意外刪除資料庫和長時間中斷時保護您的資料庫。 此零成本免管理解決方案可供所有 SQL 資料庫使用。 

## <a name="next-steps"></a>後續步驟
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要了解更快速的復原選項，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自動備份進行封存，請參閱 [資料庫複製](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


