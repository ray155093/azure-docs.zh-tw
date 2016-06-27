<properties
   pageTitle="將您現有的 Azure SQL 資料倉儲移轉到進階儲存體 | Microsoft Azure"
   description="將現有「SQL 資料倉儲」移轉到進階儲存體的指示"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="nicw;barbkess;sonyama"/>

# 移轉至進階儲存體詳細資料
SQL 資料倉儲最新引進了[進階儲存體，以獲得更高的效能可預測性][]。現在，我們已準備將目前在標準儲存體上的現有資料倉儲移轉至進階儲存體。如果您想要控制發生停機的時間，請閱讀更多有關如何及何時會發生自動移轉，以及如何自行移轉的詳細資訊。

如果您有一個以上的資料倉儲，請使用以下的[自動移轉排程][]來判斷它也會移轉的時間。

## 自動移轉詳細資料
根據預設，在以下的[自動移轉排程][]期間，我們會在下午 6 點與上午 6 點之間 (您所在地區的當地時間) 的某個時間點為您移轉資料庫。在移轉期間，現有的資料倉儲將無法使用。我們預估每個資料倉儲每 TB 的儲存體需要約 1 小時的時間進行移轉。我們也會確保在移轉的任何部分中，您不需支付任何費用。

> [AZURE.NOTE] 您無法在移轉期間使用現有的資料倉儲。移轉完成後，您的資料倉儲就會重新上線。

以下詳細資料是 Microsoft 代表您完成移轉的步驟，您不需要採取任何動作。基於此範例的目的，假設您在標準儲存體上的現有 DW 目前名為 “MyDW”。

1.	Microsoft 會將 “MyDW” 重新命名為 “MyDW\_ DO\_NOT\_USE\_[時間戳記]”
2.	Microsoft 會暫停 “MyDW\_ DO\_NOT\_USE\_[時間戳記]”。在這段期間，Microsoft 將會進行備份。如果在過程中發生任何問題，您可能會看到多個暫停/繼續。
3.	Microsoft 將會從上述步驟 2 中的備份，在進階儲存體上建立名為 “MyDW” 的新 DW。直到還原完成後，“MyDW” 才會出現。
4.	還原完成後，“MyDW” 會回到相同層級的 DWU 和移轉之前的暫停或作用中狀態。
5.	移轉完成後，Microsoft 會刪除 “MyDW\_DO\_NOT\_USE\_[時間戳記]”
	
> [AZURE.NOTE] 這些設定不會在移轉過程中沿用︰
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the Database level will need to be re-added

### 自動移轉排程
在下面所列的中斷排程期間，自動移轉會發生於下午 6 點至上午 6 點 (該地區的當地時間) 之間的某個時間點。

| 區域 | 預估開始日期 | 預估結束日期 |
| :------------------ | :--------------------------- | :--------------------------- |
| 澳洲東部 | 尚未決定 | 尚未決定 |
| 澳洲東南部 | 尚未決定 | 尚未決定 |
| 加拿大中部 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 加拿大東部 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 美國中部 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 中國東部 | 尚未決定 | 尚未決定 |
| 東亞 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 美國東部 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 美國東部 2 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 印度中部 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 印度南部 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 日本東部 | 尚未決定 | 尚未決定 |
| 日本西部 | 尚未決定 | 尚未決定 |
| 美國中北部 | 尚未決定 | 尚未決定 |
| 北歐 | 尚未決定 | 尚未決定 |
| 美國中南部 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 東南亞 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 西歐 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |
| 美國西部 | 2016 年 6 月 23日 | 2016 年 7 月 1 日 |

## 自行移轉至進階儲存體
如果您想要控制發生停機的時間，您可以使用下列步驟，將標準儲存體上的現有資料倉儲移轉至進階儲存體。如果您選擇自行移轉，則必須在該區域的自動移轉開始前，先完成自行移轉，以避免任何會造成衝突的自動移轉風險 (請參閱[自動移轉排程][])。

> [AZURE.NOTE] 採用進階儲存體的 SQL 資料倉儲目前不會進行異地備援。這表示您的資料倉儲一旦移轉至進階儲存體，資料只會放在您目前的區域中。異地備份一旦可用，就會每隔 24 小時將您的資料倉儲複製到 [Azure 配對區域][]，讓您從異地備份還原到 Azure 中的任何區域。一旦異地備份功能可用於自行移轉，就會在我們的[主要文件網站][]上發佈。相反地，自動移轉不會有這項限制。

### 決定儲存體類型
如果您在下列日期前建立 DW，則您目前是使用標準儲存體。

| 區域 | 在此日期前建立的 DW |
| :------------------ | :-------------------------------- |
| 澳洲東部 | 尚未提供進階儲存體 |
| 澳洲東南部 | 尚未提供進階儲存體 |
| 加拿大中部 | 2016 年 5 月 25 日 |
| 加拿大東部 | 2016 年 5 月 26 日 |
| 美國中部 | 2016 年 5 月 26 日 |
| 中國東部 | 尚未提供進階儲存體 |
| 東亞 | 2016 年 5 月 25 日 |
| 美國東部 | 2016 年 5 月 26 日 |
| 美國東部 2 | 2016 年 5 月 27 日 |
| 印度中部 | 2016 年 5 月 27 日 |
| 印度南部 | 2016 年 5 月 26 日 |
| 日本東部 | 尚未提供進階儲存體 |
| 日本西部 | 尚未提供進階儲存體 |
| 美國中北部 | 尚未提供進階儲存體 |
| 北歐 | 尚未提供進階儲存體 |
| 美國中南部 | 2016 年 5 月 27 日 |
| 東南亞 | 2016 年 5 月 24 日 |
| 西歐 | 2016 年 5 月 25 日 |
| 美國西部 | 2016 年 5 月 26 日 |


### 自行移轉指示
如果您想要控制停機時間，您可使用備份/還原自行移轉您的資料倉儲。每個 DW 每 TB 的儲存體預計需要約 1 小時的時間來進行移轉作業的還原部分。如果您要在移轉完成後保留相同的名稱，請遵循[重新命名因應措施][]以下的步驟。

1.	[暫停][]會進行自動備份的 DW
2.	從最新的快照集[還原][]
3.	刪除標準儲存體上的現有 DW。**如果您無法執行此步驟，您需支付這兩個 DW 的費用。**

> [AZURE.NOTE] 這些設定不會在移轉過程中沿用︰
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the Database level will need to be re-added

#### 選擇性︰重新命名因應措施 
相同邏輯伺服器上的兩個資料庫不能具有相同的名稱。SQL 資料倉儲目前不支援重新命名 DW 功能。以下指示可讓您在自行移轉期間解決這項遺漏功能 (附註︰自動移轉不會有這項限制)。

基於此範例的目的，假設您在標準儲存體上的現有 DW 目前名為 “MyDW”。

1.	[暫停][]會進行自動備份的 "MyDW"
2.	從最新的快照集 (具有不同名稱 (如 "MyDWTemp") 的新資料庫) [還原][]
3.	刪除 "MyDW"。**如果您無法執行此步驟，您需支付這兩個 DW 的費用。**
4.	由於 "MyDWTemp" 是新建立的 DW，備份將有一段時間無法用於還原。建議繼續在 "MyDWTemp" 上執行作業數小時，然後繼續進行步驟 5 和 6。
5.	[暫停][]會進行自動備份的 "MyDWTemp"。
6.	從最新的 "MyDWTemp" 快照集 (具有 "MyDW" 名稱的新資料庫) [還原][]
7.	刪除 "MyDWTemp"。**如果您無法執行此步驟，您需支付這兩個 DW 的費用。**

> [AZURE.NOTE] 這些設定不會在移轉過程中沿用︰
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the Database level will need to be re-added

## 後續步驟
如果您的資料倉儲遇到任何問題，請[建立支援票證][]和參考「移轉至進階儲存體」做為可能的原因。

<!--Image references-->

<!--Article references-->
[自動移轉排程]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[建立支援票證]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Azure 配對區域]: ./best-practices-availability-paired-regions.md
[主要文件網站]: ./services/sql-data-warehouse.md
[暫停]: ./sql-data-warehouse-manage-compute-portal.md/#pause-compute
[還原]: ./sql-data-warehouse-manage-database-restore-portal.md
[重新命名因應措施]: #optional-rename-workaround

<!--MSDN references-->


<!--Other Web references-->
[進階儲存體，以獲得更高的效能可預測性]: https://azure.microsoft.com/zh-TW/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/

<!---HONumber=AcomDC_0615_2016-->