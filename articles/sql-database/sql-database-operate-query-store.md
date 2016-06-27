<properties
   pageTitle="操作 Azure SQL Database 中的查詢存放區"
   description="了解如何操作 Azure SQL Database 中的查詢存放區"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="05/25/2016"
   ms.author="carlrab"/>

# 操作 Azure SQL Database 中的查詢存放區 

Azure 中的查詢存放區是完全受管理的資料庫功能，可持續收集及呈現有關所有查詢的詳細歷程記錄資訊。您可以將查詢存放區視為類似於飛航資料記錄器，可大幅簡化雲端選項與內部部署客戶的查詢效能疑難排解。這篇文章說明在 Azure 中操作查詢存放區的特定層面。使用此預先收集的查詢資料，使用者可以快速地診斷並解決效能問題，因此花更多時間專注於業務上。

查詢存放區已自 2015 年 11 月在 Azure SQL Database 中[通用版本上市](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/)。查詢存放區是效能分析及調整功能的基礎，例如 [SQL Database 建議程式和效能儀表板](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)。在本文發行時，查詢存放區正於 Azure 中執行 200,000 個以上的使用者資料庫，不間斷地收集數個月的查詢相關資訊。

> [AZURE.IMPORTANT] Microsoft 正在為所有 Azure SQL Database (現有和新的) 啟用「查詢存放區」。此啟動程序目前包括只有單一資料庫，但在不久的將來會擴大為彈性集區資料庫。使用彈性集區時，僅對小部分的資料庫啟用查詢存放區。對彈性集區中的所有資料庫啟用查詢存放區，可能會導致過多的資源使用，並可能導致讓您的系統沒有回應。

## 查詢存放區作為 Azure SQL Database 中的受管理功能

「查詢存放區」在 Azure SQL Database 中是根據兩個基本原則來運作：

- 對客戶工作負載不會造成任何可見的影響
- 除非客戶工作負載受影響，否則會持續監視查詢

對客戶工作負載的影響有兩個維度：

- ***可用性***：當「查詢存放區」正在執行時，不會降低 [SQL Database 的 SLA](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/)。
- ***效能***：「查詢存放區」導致的平均額外負荷通常是在 1-2% 的範圍。

Azure 中查詢存放區的運作會使用有限的資源 (CPU、記憶體、磁碟 I/O、磁碟大小等)。為了將對一般工作負載的影響降到最低，它會遵守各種系統限制。

- ***靜態限制：***指定服務層 (基本、標準、進階、彈性集區) 的資源容量所加諸的限制。
- ***動態限制：***目前的工作負載耗用 (即可用資源) 所加諸的限制。

為了確保連續且可靠的作業，Azure SQL Database 已對會收集來自每個資料庫重要作業資料的查詢存放區，建置了永久監視基礎結構。因此，正不斷地監視數個技術 KPI 以確保可靠性：

- 例外狀況和自動風險降低的數目
- READ\_ONLY 狀態的資料庫數目和 READ\_ONLY 狀態的持續時間
- 「查詢存放區」記憶體耗用量超過限制的前幾名資料庫。
- 依據自動清除頻率和持續期間排名的前幾名資料庫
- 依據將資料載入記憶體 (「查詢存放區」初始化) 的持續期間排名的前幾名資料庫
- 依據將資料排清到磁碟的持續期間排名的前幾名資料庫

Azure SQL Database 會使用收集到的資料，以便：

- ***了解大量資料庫的使用模式，並藉此改善功能的可靠性及品質：***「查詢存放區」會隨著 Azure SQL Database 的每次更新獲得改善。 
- ***解決或減輕查詢存放區所造成的問題：***Azure SQL Database 可以偵測並減輕對客戶工作負載具有實質影響的問題，而不會造成太多延遲 (少於一小時)。問題的最常見處理方式是將「查詢存放區」暫時設定為 ***OFF***。

有時候，查詢存放區更新會造成套用至內部和極少外部 (面對客戶) 組態預設值的變更。因此，客戶對 Azure SQL Database 上「查詢存放區」的體驗可能會因 Azure 平台自動執行的動作而與內部部署環境上的體驗有所不同：

- 可以將「查詢存放區」狀態變更為 ***OFF*** 以減輕問題，然後在問題解決後，再變更回 ***ON***。
- 可以變更查詢存放區組態，以確保可靠的工作。執行方式可以是：
    - 可解決不穩定或不可靠問題的個別資料庫變更。
    - 全域推出最佳組態變更可為使用查詢存放區的所有資料庫提供優點。

將「查詢存放區」設定為 ***OFF*** 是範圍限於個別資料庫的自動動作。它會發生在有產品行為會對使用者資料庫造成負面影響，而偵測機制會引發警示時。針對該特定資料庫，「查詢存放區」會保持 ***OFF***，直到有含已改進之「查詢存放區」實作的新版本可供使用為止。當發生轉換至 ***OFF*** 狀態的情況時，會透過電子郵件對客戶發出通知並建議客戶避免重新啟用「查詢存放區」，直到推出新版本為止。在推出新版本之後，Azure SQL Database 基礎結構會針對「查詢存放區」被設定為 ***OFF*** 的所有資料庫自動啟用「查詢存放區」。

有時，Azure SQL Database 可能會對所有使用者資料庫強制執行新的組態預設值，針對可靠的工作與持續收集資料最佳化。

## 最佳查詢存放區組態

本節描述最佳的組態預設值，其設計目的是要確保「查詢存放區」以及相依功能 (例如 [SQL Database 建議程式和效能儀表板](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)) 能夠可靠地運作。預設組態已針對持續收集資料最佳化，也就是在 OFF/READ\_ONLY 狀態花費最少的時間。

| 組態 | 說明 | 預設值 | 註解 |
| ------------- | ----------- | ------- | ------- |
| MAX\_STORAGE\_SIZE\_MB | 指定查詢存放區會在客戶資料庫內佔用的資料空間的限制 | 100 | 對新資料庫強制執行 |
| INTERVAL\_LENGTH\_MINUTES | 定義彙總和保存查詢計畫所收集到的執行階段統計資料的時段大小。對於此組態定義的一段時間，每個使用中的查詢計畫將會有最多一個資料列 | 60 | 對新資料庫強制執行 |
| STALE\_QUERY\_THRESHOLD\_DAYS | 以時間為基礎的清理原則，可控制保存執行階段統計資料和非使用中查詢的保留期限 | 30 | 對新資料庫和具有先前的預設值 (367) 的資料庫強制執行 |
| SIZE\_BASED\_CLEANUP\_MODE | 指定當查詢存放區資料大小接近限制時，是否進行自動資料清理 | AUTO | 對所有資料庫強制執行 |
| QUERY\_CAPTURE\_MODE | 指定是否會追蹤所有查詢或只有查詢的子集 | AUTO | 對所有資料庫強制執行 |
| FLUSH\_INTERVAL\_SECONDS | 指定擷取的執行階段統計資料在排清到磁碟之前，會保留在記憶體中的最大期間 | 900 | 對新資料庫強制執行 |
||||||

> [AZURE.IMPORTANT] 在所有 Azure SQL Database 中的最後「查詢存放區」啟用階段會自動套用這些預設值 (請參閱上面的重要附註)。在這次推出之後，Azure SQL Database 不會變更客戶所設定的組態值，除非該組態值會對主要工作負載或查詢存放區的可靠操作造成負面影響。

如果您想要繼續使用您的自訂設定，請使用 [ALTER DATABASE 搭配「查詢存放區」選項](https://msdn.microsoft.com/library/bb522682.aspx)，以將組態還原到先前的狀態。請查看[使用查詢存放區的最佳做法](https://msdn.microsoft.com/library/mt604821.aspx)，以了解如何選擇最佳的組態參數。

## 後續步驟

[SQL Database 效能深入解析](sql-database-performance.md)

## 其他資源

如需詳細資訊，請參閱下列文章：

- [您的資料庫的航班資料錄製器](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [使用查詢存放區來監視效能](https://msdn.microsoft.com/library/dn817826.aspx)

- [查詢存放區使用案例](https://msdn.microsoft.com/library/mt614796.aspx)

- [使用查詢存放區來監視效能](https://msdn.microsoft.com/library/dn817826.aspx)

<!---HONumber=AcomDC_0615_2016-->