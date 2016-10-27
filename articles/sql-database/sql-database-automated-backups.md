<properties
   pageTitle="了解 SQL Database 備份 | Microsoft Azure" 
   description="深入了解可讓您將 Azure SQL Database 還原到先前時間點，或將資料庫複製到地理區域中新資料庫 (最多 35 天) 的 SQL Database 內建資料庫備份。"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="carlrab"/>




<!-- # H1 Title

 H1 title should answer the question "What is in this topic?" Write the title in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
-->

# <a name="learn-about-sql-database-backups"></a>了解 SQL Database 備份

<!-- Introduction

1. Sentence #1 begins with "Learn about ..." and gives the scope of what the article will cover.
2. Sentence #2 explains the key capability or selling point of the feature.
3. Sentence #3 begins with "Use this feature to ..." and gives a common use case.

-->

SQL Database 每五分鐘會建立一個本機資料庫備份，並使用 Azure 讀取權限異地備援儲存體 (RA-GRS) 來將部分資料庫備份複製到另一個地理區域。 這些備份會**自動建立且不需要額外付費**。 使用本機資料庫備份來於相同伺服器上[將資料庫還原至某個時間點](sql-database-point-in-time-restore-portal.md)。 使用異地備援備份來[將資料庫還原至不同的地理區域](sql-database-geo-restore-portal.md)。  

>[AZURE.NOTE] 本機和異地備援備份會自動啟動。 您不需要執行任何動作來啟動它們，而且不需額外付費。 

在以下圖表中，SQL Database 是在美國東部地區執行。 它每五分鐘會建立一個資料庫備份，並將備份儲存在本地的 Azure 讀取權限異地備援儲存體 (RA-GRS) 中。 Azure 會將資料庫備份複製到美國西部區域中配對的資料中心。

![異地還原](./media/sql-database-geo-restore/geo-restore-1.png)

<!--## What is <feature>?" -->

## <a name="what-is-a-sql-database-backup?"></a>什麼是 SQL Database 備份？  

<!-- 
First sentence begins with "The <feature> is ..." followed by a definition of the feature. Provide a 1-2 paragraph intro to explain what the feature is, how it works, and the importance of the feature for solving business problems.
-->
SQL Database 備份是一個檔案，其中儲存在特定時間點之下資料庫狀態的相關資訊。 SQL Database 使用 SQL Server 技術來建立本機 [完整](https://msdn.microsoft.com/library/ms186289.aspx)、[差異](https://msdn.microsoft.com/library/ms175526.aspx )，及 [交易記錄](https://msdn.microsoft.com/library/ms191429.aspx) 備份。 交易記錄備份每五分鐘會備份一次，可讓您對裝載資料庫的同一部伺服器建立還原時間點。 在您還原資料庫時，服務會判斷需要還原的完整、差異及交易記錄備份。

>[AZURE.NOTE] SQL Database 會同時自動建立本機資料庫備份和異地備援備份。 您不需要執行任何動作來建立備份。 不會額外收費。

可使用資料庫備份來：

- 將資料庫還原至保留期限內的某一個時間點。 資料庫備份可讓您將資料庫還原到某個時間點、將已刪除的資料庫還原到它被刪除時的狀態，或將資料庫還原至另一個地理區域。 若要執行還原，請參閱[從資料庫備份還原資料庫](sql-database-recovery-using-backups.md)。

- 將資料庫複製到相同或不同區域中的 SQL Server。 複本與目前的 SQL 資料庫會維持交易一致性。 若要執行複製，請參閱[資料庫複製](sql-database-copy.md)。

- 封存超出備份保留期限的資料庫備份。 若要執行封存，請[將 SQL Database 匯出到 BACPAC](sql-database-export.md) 檔案。 然後您就可以將 BACPAC 檔案封存到長期儲存體，就能將它保存到超過保留期限。 或者，使用 BACPAC 將資料庫的複本傳送到內部部署或 Azure 虛擬機器 (VM) 中的 SQL Server。

## <a name="backups-have-geographical-redundancy"></a>備份有異地備援

SQL Database 是使用 [Azure 儲存體複寫](../storage/storage-redundancy.md)將您的資料庫備份到不同的地理位置。 為提供異地備援儲存體，SQL Database 會將本機資料庫備份檔案儲存在[讀取權限異地備援儲存體 (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) 帳戶中。 Azure 會將備份檔案複寫到 [配對的資料中心](../best-practices-availability-paired-regions.md)。 萬一您無法從主要資料庫區域存取資料庫備份，此異地複寫可確保您能夠還原資料庫。 

>[AZURE.NOTE] 在 Azure 儲存體中，「複寫」一詞指的是將檔案從某個位置複製到另一個位置。 SQL 的「資料庫複寫」  指的是保持多個次要資料庫與主要資料庫同步。 


## <a name="how-much-backup-storage-is-included-at-no-cost?"></a>在免費情況下包含多少備份儲存體？

SQL Database 提供高達 200% 的最大可佈建資料庫儲存體作為備份儲存體，且不須支付額外費用。 例如，如果您擁有可佈建 DB 大小為 250 GB 的標準 DB 執行個體，您就能免費獲得 500 GB 的備份儲存體。 如果您的資料庫超過所提供的備份儲存體，您可以連絡 Azure 支援人員來選擇縮短保留期限。 另一個選項是付費購買額外的備份儲存體，按讀取權限異地備援儲存體 (RA-GRS) 標準費率計費。 

## <a name="how-often-do-backups-happen?"></a>備份頻率是？

完整備份每週進行一次，差異資料庫備份每小時進行一次、而交易記錄備份每隔五分鐘進行一次。 建立資料庫之後，會立即排程第一次完整備份。 通常會在 30 分鐘內完成，但如果資料庫很大，則時間可能更久。 比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。 第一次完整備份之後，將會自動排程進一步的備份，並在背景中以無訊息方式管理。 完整和 [差異](https://msdn.microsoft.com/library/ms175526.aspx) 資料庫備份的確切時間，依整體系統工作負載維持平衡而決定。 

## <a name="how-long-do-you-keep-my-backups?"></a>您會保留我的備份多久？

每個 SQL Database 備份都有一個保留期限，該期限是以資料庫的[服務層](sql-database-service-tiers.md)為依據。 資料庫保留期限如下：

- 基本服務層為 7 天。
- 標準服務層為 35 天。
- 高階服務層為 35 天。


如果您將資料庫從「標準」或「高階」服務層降級到「基本」服務層，備份就會保存 7 天。 所有超過 7 天的現有備份都無法再使用。 

如果您將資料庫從「基本」服務層升級到「標準」或「高階」服務層，SQL Database 就會將現有備份自其建立日期算起保存 35 天。 它也會將新的備份自其建立日期算起保存 35 天。
 
如果您刪除資料庫，SQL Database 會以和保存線上資料庫備份的相同方式保存備份。 例如，假設您刪除保存期限為 7 天的「基本」資料庫。 已保存 4 天的備份會再保存 3 天。

> [AZURE.IMPORTANT]如果您刪除裝載 SQL Database 的 Azure SQL Server，所有屬於該伺服器的資料庫也會一併刪除且無法復原。 您無法還原已刪除的伺服器。

## <a name="next-steps"></a>後續步驟

資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要查看資料庫備份和您商業策略的適配程度，請參閱[商務持續性概觀](sql-database-business-continuity.md)。





<!--HONumber=Oct16_HO2-->


