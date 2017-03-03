---
title: "使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉 | Microsoft Docs"
description: "使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 5849b600-89cb-4995-ae9f-0188a17b4e1b
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: ac575284544819c6bed7ef84669b2793085a3dc6
ms.lasthandoff: 02/16/2017


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉

本文將說明如何使用 PowerShell 為 SQL Database 起始計劃性或非計劃性容錯移轉。 若要設定「異地複寫」，請參閱 [為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-powershell.md)。

## <a name="initiate-a-planned-failover"></a>起始規劃的容錯移轉
使用 **Set-AzureRmSqlDatabaseSecondary** Cmdlet 搭配 **-Failover** 參數來升級次要資料庫，使它成為新主要資料庫，將現有主要資料庫降級成為次要資料庫。 這項功能是為了規劃的容錯移轉 (例如災害復原鑽研期間) 設計，並且需要主要資料庫可供使用。

此命令會執行下列工作流程：

1. 暫時將複寫切換到同步模式。 這會導致將所有未處理的交易排清至次要資料庫。
2. 切換「異地複寫」合作關係中兩個資料庫的角色。  

此順序可保證在角色切換之前兩個資料庫經過同步處理，因此不會發生資料遺失。 切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。 在正常情況下，完成整個作業所需的時間應該少於一分鐘。 如需詳細資訊，請參閱 [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393\(v=azure.300\).aspx)。

將次要資料庫切換為主要程序完成時，這個 Cmdlet 將傳回。

下列命令會將資源群組 "rg2" 下伺服器 "srv2" 上名為 "mydb" 的資料庫角色切換為主要資料庫。 "db2" 所連線的原始主要資料庫，在兩個資料庫完全同步處理之後會切換為次要資料庫。

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg2” -ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [!NOTE]
> 在少數情況下，作業會無法完成並且可能會顯得沒有回應。 在此情況下，使用者可以呼叫強制容錯移轉命令 (未規劃的容錯移轉) 並接受資料遺失。
> 
> 

## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>起始從主要資料庫到次要資料庫的未規劃的容錯移轉
您可以使用 **Set-AzureRmSqlDatabaseSecondary** Cmdlet 搭配 **-Failover** 和 **-AllowDataLoss** 參數來升級次要資料庫，使它成為非計劃性方式的新主要資料庫，強制讓現有主要資料庫在不再可供使用時降級成次要複本。

這項功能是針對還原資料庫的可用性非常重要而且部分資料遺失是可接受時的災害復原所設計。 叫用強制容錯移轉時，指定的次要資料庫立即成為主要資料庫，並開始接受寫入交易。 在強制容錯移轉作業後，原始主要資料庫能夠與這個新的主要資料庫重新連線時，會在原始主要資料庫執行增量備份，而舊的主要資料庫會變成新主要資料庫的次要資料庫；之後就只是新的主要資料庫的複本。

但因為還原時間點在次要資料庫不受支援，如果您想要復原已認可到舊主要資料庫但尚未被複寫到新主要資料庫的資料，您應該接洽 CSS 來將資料庫還原至已知的記錄備份。

> [!NOTE]
> 如果在主要資料庫和次要資料庫上線時發出此命令，舊的主要會立即變成新的次要資料庫，而不會進行資料同步處理。 發出命令時，如果主要複本正在認可交易，可能會發生部分資料遺失。
> 
> 

如果主要資料庫中有多個次要複本，命令將只有部分成功。 執行命令的次要複本會變成主要複本。 不過舊的主要複本將仍為主要複本，亦即，兩個主要複本最終會處於不一致狀態並透過擱置的複寫連結連接。 使用者必須在主要資料庫上使用「移除次要複本」API 手動修復此組態。

下列命令在主要複本無法使用時，將名為 "mydb" 的資料庫角色切換為主要複本。 "mydb" 所連線的原始主要複本，將在回到線上之後切換為次要複本。 在該時間點，同步處理可能會導致資料遺失。

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg2” -ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover -AllowDataLoss




## <a name="next-steps"></a>後續步驟
* 容錯移轉之後，請確認已在新的主要資料庫上設定伺服器和資料庫的驗證需求。 如需詳細資訊，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。
* 若要了解如何使用主動式異地複寫在災害之後進行復原，包括復原前和復原後步驟，以及執行災害復原演練，請參閱 [災害復原演練](sql-database-disaster-recovery.md)
* 如需 Sasha Nosov 有關主動式異地複寫的部落格文章，請參閱 [新異地複寫功能要點](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* 如需如何設計雲端應用程式使用主動式異地複寫的相關資訊，請參閱 [使用異地複寫設計商務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* 如需使用主動式異地複寫與彈性集區的相關資訊，請參閱[彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
* 如需商務持續性的概觀，請參閱 [商務持續性概觀](sql-database-business-continuity.md)


