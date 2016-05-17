<properties
	pageTitle="作用中異地複寫的安全性組態"
	description="本主題說明管理 SQL Database 之主動式異地複寫案例的安全性考量。"
	services="sql-database"
	documentationCenter="na"
	authors="carlrabeler"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="04/27/2016"
	ms.author="carlrab" />

# 異地複寫的安全性設定

>[AZURE.NOTE] [Active Geo-Replication](sql-database-geo-replication-overview.md) 現在可供所有服務層中的所有資料庫使用。

## 作用中異地複寫驗證需求的概觀
本主題描述設定和控制[主動式異地複寫](sql-database-geo-replication-overview.md)的驗證需求，以及設定次要資料庫之使用者存取所需的步驟。如需使用異地覆寫的詳細資訊，請參閱[從中斷復原 Azure SQL Database](sql-database-disaster-recovery.md)。

## 對自主使用者使用作用中異地複寫
有了 [V12 版的 Azure SQL Database](sql-database-v12-whats-new.md)，SQL Database 現在支援自主使用者。不同於傳統的使用者必須對應到 master 資料庫中的登入，自主使用者完全由資料庫本身管理。這樣有兩個優點。在異地覆寫案例中，使用者可以繼續連接到次要資料庫，而不需任何額外的組態，因為資料庫可管理使用者。從登入的觀點來看，這個組態也有潛在的延展性和效能優勢。如需詳細資訊，請參閱[自主資料庫使用者 - 讓資料庫具有可攜性](https://msdn.microsoft.com/library/ff929188.aspx)。

當您有使用相同登入的多個資料庫時，維護在多個資料庫中使用自主使用者的認證可能不利於自主使用者。例如，當密碼變更時，將必須在每個資料庫中分別為自主使用者進行變更，而不是在變更伺服器層級一次性變更登入的密碼。基於這個理由，如果您有使用相同使用者名稱和密碼的多個資料庫，則不建議使用自主使用者。

## 對作用中異地複寫使用登入與使用者
如果您使用登入和使用者 (而非自主使用者)，您必須採取額外步驟以確保相同的登入存在於次要資料庫伺服器上。下列各節概述涉及的步驟和其他考量。

### 設定次要資料庫的使用者存取
為了讓次要資料庫可供使用做為唯讀次要資料庫，或是在容錯移轉之後可行的主要資料庫，次要資料庫必須具有適當的安全性組態。

伺服器管理員或具有適當權限的使用者可以完成本主題中所述的設定步驟。本主題稍後說明每個步驟的特定權限。

隨時都可以準備主動式異地複寫線上次要資料庫的使用者存取。它包含如下所述的三個步驟：

1. 決定具備主要資料庫存取權的登入。
2. 在來源伺服器上尋找這些登入的 SID。
3. 利用來自來源伺服器的相符 SID，在目標伺服器上建立登入。

>[AZURE.NOTE] 如果目標伺服器上的登入未正確與次要資料庫中的使用者對應，在容錯移轉後以唯讀資料庫對其存取或存取新主要複本僅限於伺服器管理員。

#### 1\.決定具備主要資料庫存取權的登入：
程序的第一個步驟是判斷目標伺服器上必須複製哪些登入。這是以一組 SELECT 陳述式完成，一個在來源伺服器上的邏輯 master 資料庫中，一個在主要資料庫本身。

只有伺服器系統管理員或 **LoginManager** 伺服器角色的成員可以利用下列 SELECT 陳述式判斷來源伺服器上的登入。

	SELECT [name], [sid] 
	FROM [sys].[sql_logins] 
	WHERE [type_desc] = 'SQL_Login'

只有 db\_owner 資料庫角色的成員、dbo 使用者或伺服器系統管理員可以判斷主要資料庫中的所有資料庫使用者主體。

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

#### 2\.尋找在步驟 1 中識別的登入 SID：
藉由比較上一節查詢的輸出並比對 SID，您可以將伺服器登入對應到資料庫使用者。具有資料庫使用者及相符 SID 的登入也會有該資料庫的使用者存取權做為資料庫使用者主體。

下列查詢可用來查看資料庫中的所有使用者主體及其 SID。只有 db\_owner 資料庫角色的成員或伺服器系統管理員才能執行此查詢。

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE] **INFORMATION\_SCHEMA** 和 **sys** 使用者擁有 *NULL* SID，而**客體** SID 是 **0x00**。如果資料庫建立者是伺服器系統管理員，而不是 **DbManager** 的成員，**dbo** SID 可能會以 *0x01060000000001648000000000048454* 開頭。

#### 3\.在目標伺服器上建立登入：
最後一個步驟是移至目標伺服器或伺服器，並以適當的 SID 產生登入。基本語法如下所示。

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE] 如果您想要授與使用者次要資料庫的存取權，而不是主要資料庫的，您可以藉由使用下列語法在主要伺服器上改變使用者登入，即可完成。
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE 不會變更密碼，因此必要時可以永遠啟用。

## 後續步驟
如需作用中異地複寫的詳細資訊，請參閱[作用中異地複寫](sql-database-geo-replication-overview.md)。


## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [SQL Database BCDR 常見問題集](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0504_2016-->