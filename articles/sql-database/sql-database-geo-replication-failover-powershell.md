<properties 
    pageTitle="使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉 | Microsoft Azure" 
    description="使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# 使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉



> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


本文將說明如何使用 PowerShell 為 SQL Database 起始計劃性或非計劃性容錯移轉。若要設定「異地複寫」，請參閱[為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-powershell.md)。



## 起始規劃的容錯移轉

使用 **Set-AzureRmSqlDatabaseSecondary** Cmdlet 搭配 **-Failover** 參數，來升級次要資料庫，使它成為新主要資料庫，將現有主要資料庫降級成為次要資料庫。這項功能是為了規劃的容錯移轉 (例如災害復原鑽研期間) 設計，並且需要主要資料庫可供使用。

此命令會執行下列工作流程：

1. 暫時將複寫切換到同步模式。這會導致將所有未處理的交易排清至次要資料庫。

2. 切換「異地複寫」合作關係中兩個資料庫的角色。

此順序可保證在角色切換之前兩個資料庫經過同步處理，因此不會發生資料遺失。切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。在正常情況下，完成整個作業所需的時間應該少於一分鐘。如需詳細資訊，請參閱 [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx)。




將次要資料庫切換為主要程序完成時，這個 Cmdlet 將傳回。

下列命令會將資源群組 "rg2" 下伺服器 "srv2" 上名為 "mydb" 的資料庫角色切換為主要資料庫。"db2" 所連線的原始主要資料庫，在兩個資料庫完全同步處理之後會切換為次要資料庫。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] 在少數情況下，作業會無法完成並且可能會顯得沒有回應。在此情況下，使用者可以呼叫強制容錯移轉命令 (未規劃的容錯移轉) 並接受資料遺失。


## 起始從主要資料庫到次要資料庫的未規劃的容錯移轉


您可以使用 **Set-AzureRmSqlDatabaseSecondary** Cmdlet 搭配 **-Failover** 和 **-AllowDataLoss** 參數來升級次要資料庫，使它成為未規劃的方式中的新主要資料庫，每當主要資料庫無法使用時，強制將現有主要複本降級成為次要複本。

這項功能是針對還原資料庫的可用性非常重要而且部分資料遺失是可接受時的災害復原所設計。叫用強制容錯移轉時，指定的次要資料庫立即成為主要資料庫，並開始接受寫入交易。在強制容錯移轉作業後，原始主要資料庫能夠與這個新的主要資料庫重新連線時，會在原始主要資料庫執行增量備份，而舊的主要資料庫會變成新主要資料庫的次要資料庫；之後就只是新的主要資料庫的複本。

但因為還原時間點在次要資料庫不受支援，如果您想要復原已認可到舊主要資料庫但尚未被複寫到新主要資料庫的資料，您應該接洽 CSS 來將資料庫還原至已知的記錄備份。

> [AZURE.NOTE] 如果在主要資料庫和次要資料庫上線時發出此命令，舊的主要會立即變成新的次要資料庫，而不會進行資料同步處理。發出命令時，如果主要複本正在認可交易，可能會發生部分資料遺失。


如果主要資料庫中有多個次要複本，命令將只有部分成功。執行命令的次要複本會變成主要複本。不過舊的主要複本將仍為主要複本，亦即，兩個主要複本最終會處於不一致狀態並透過擱置的複寫連結連接。使用者必須在主要資料庫上使用「移除次要複本」API 手動修復此組態。


下列命令在主要複本無法使用時，將名為 "mydb" 的資料庫角色切換為主要複本。"mydb" 所連線的原始主要複本，將在回到線上之後切換為次要複本。在該時間點，同步處理可能會導致資料遺失。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## 其他資源

- [新異地複寫功能要點](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [使用異地複寫設計業務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)

<!---HONumber=AcomDC_0615_2016-->