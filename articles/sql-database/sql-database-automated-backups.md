---
title: "SQL Database 備份 - 自動、異地備援 | Microsoft Docs"
description: "SQL Database 每隔幾鐘會自動建立一個本機資料庫備份，並使用 Azure 讀取權限異地備援儲存體來進行異地備援。"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2016
ms.author: sashan;carlrab;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: f6bb6e1c81cafe5f0e5c43c99ab15a0483742868


---
# <a name="learn-about-sql-database-backups"></a>了解 SQL Database 備份
<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.

Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    � Definition of the feature terminology.  i.e., What is a database backup?
    � Characteristics and capabilities of the feature. (How the feature works)
    � Common uses with links to overview topics that recommend when to use the feature.
    � Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    � Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    � How to steps for using the feature (Tasks)
    � How to solve business problems that incorporate the feature (Overviews)

GUIDELINES for the H1 

    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  

    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "elastic pools", use a synonym. For example:    "Learn about elastic pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

GUIDELINES for introduction

    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

SQL Database 會自動建立一個資料庫備份，並使用 Azure 讀取權限異地備援儲存體 (RA-GRS) 來提供異地備援。 這些備份是自動建立的，且不需額外付費。 您不需要執行任何動作來建立備份。 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 如果您想要在自己的儲存體容器中保留備份，您可以設定長期的備份保留原則。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.

    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>什麼是 SQL Database 備份？
<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->


<!----------------- 
    Explains first component of the backup feature
------------------>

SQL Database 使用 SQL Server 技術來建立[完整](https://msdn.microsoft.com/library/ms186289.aspx)、[差異](https://msdn.microsoft.com/library/ms175526.aspx)，及[交易記錄](https://msdn.microsoft.com/library/ms191429.aspx)備份。 交易記錄備份根據效能層級和資料庫活動量的頻率通常每隔 5-10 分鐘會進行一次。 具有完整和差異備份的交易記錄備份可讓您將資料庫還原到特定的時間點，至裝載資料庫相同的伺服器。 在您還原資料庫時，服務會判斷需要還原的完整、差異及交易記錄備份。

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

您可以使用這些備份︰

* 將資料庫還原至保留期限內的某一個時間點。 這項作業將會在與原始資料庫相同的伺服器中建立新的資料庫。
* 將已刪除的資料庫還原至其被刪除的時間，或保留期限內的任何時間。 已刪除的資料庫只能在當中已建立原始資料庫的相同伺服器中還原。
* 將資料庫還原到另一個地理區域。 這可讓您在無法存取您的伺服器和資料庫時，從地理災害中復原。 在世界各地任何現有的伺服器中建立新的資料庫。 
* 從 Azure 復原服務保存庫中儲存的特定備份中還原資料庫。 這可讓您還原舊版的資料庫來符合規範要求，或執行舊版的應用程式。 請參閱[長期保留](sql-database-long-term-retention.md)。
* 若要執行還原，請參閱[從備份還原資料庫](sql-database-recovery-using-backups.md)。

> [!TIP]
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery.md)
>

<!----------------- 
    Explains first component of the backup feature
------------------>

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞指的是將檔案從某個位置複製到另一個位置。 SQL 的「資料庫複寫」  指的是保持多個次要資料庫與主要資料庫同步。 
> 
> 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>在免費情況下包含多少備份儲存體？
SQL Database 提供高達 200% 的最大可佈建資料庫儲存體作為備份儲存體，且不須支付額外費用。 例如，如果您擁有可佈建 DB 大小為 250 GB 的標準 DB 執行個體，您就能免費獲得 500 GB 的備份儲存體。 如果您的資料庫超過所提供的備份儲存體，您可以連絡 Azure 支援人員來選擇縮短保留期限。 另一個選項是付費購買額外的備份儲存體，按讀取權限異地備援儲存體 (RA-GRS) 標準費率計費。 

## <a name="how-often-do-backups-happen"></a>備份頻率是？
每週進行一次完整備份，通常每幾個小時進行一次差異資料庫備份，以及通常每隔 5 - 10 分鐘進行一次交易記錄備份。 建立資料庫之後，會立即排程第一次完整備份。 通常會在 30 分鐘內完成，但如果資料庫很大，則時間可能更久。 比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。 第一次完整備份之後，將會自動排程進一步的備份，並在背景中以無訊息方式管理。 資料庫備份的確切時間，依 SQL Database 服務整體系統工作負載維持平衡而決定。 

備份儲存體異地複寫是根據 Azure 儲存體複寫排程進行。

## <a name="how-long-do-you-keep-my-backups"></a>您會保留我的備份多久？
每個 SQL Database 備份都有一個保留期限，該期限是以資料庫的[服務層](sql-database-service-tiers.md)為依據。 資料庫保留期限如下：

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

* 基本服務層為 7 天。
* 標準服務層為 35 天。
* 高階服務層為 35 天。

如果您將資料庫從「標準」或「高階」服務層降級到「基本」服務層，備份就會保存 7 天。 所有超過 7 天的現有備份都無法再使用。 

如果您將資料庫從「基本」服務層升級到「標準」或「高階」服務層，SQL Database 就會將現有備份自其建立日期算起保存 35 天。 它也會將新的備份自其建立日期算起保存 35 天。

如果您刪除資料庫，SQL Database 會以和保存線上資料庫備份的相同方式保存備份。 例如，假設您刪除保存期限為 7 天的「基本」資料庫。 已保存 4 天的備份會再保存 3 天。

> [!IMPORTANT]
> 如果您刪除裝載 SQL Database 的 Azure SQL Server，所有屬於該伺服器的資料庫也會一併刪除且無法復原。 您無法還原已刪除的伺服器。
> 
> 

## <a name="how-to-extend-the-backup-retention-period"></a>如何延長備份保留期限？
如果您的應用程式需要較長時間可進行備份，您可以延長內建的保留期限，方法為設定個別資料庫的長期備份保留原則 (LTR 原則)。 這可讓您將保留期限延長 35 天至多達 10 年。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

一旦您使用 Azure 入口網站或 API 將 LTR 原則新增至資料庫後，每週的完整資料庫備份將會自動複製到您自己的 Azure 備份服務保存庫。 如果您的資料庫是使用 TDE 加密，則備份會自動加密靜止。  服務保存庫將自動刪除您過期的備份，根據其時間戳記和 LTR 原則。  因此您不需要管理備份排程，或擔心清除舊的檔案。 還原 API 支援保存庫中儲存的備份，只要保存庫在與您的 SQL Database 相同的訂用帳戶中。 您可以使用 Azure 入口網站或 PowerShell 存取這些備份。

> [!TIP]
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery.md)
>

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>後續步驟

- 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要深入了解其他 Azure SQL Database 業務持續性解決方案，請參閱[業務持續性概觀](sql-database-business-continuity.md)。
- 若要還原至某個點時間，請參閱[將資料庫還原至時間點](sql-database-point-in-time-restore.md)。
- 若要從服務產生的資料庫備份檢視最早的還原點，請參閱[檢視最早的還原點](sql-database-view-oldest-restore-point.md)
- 若要在 Azure 復原服務保存庫中設定自動備份的長期保留，請參閱[設定長期備份保留](sql-database-configure-long-term-retention.md)



<!--HONumber=Dec16_HO2-->


