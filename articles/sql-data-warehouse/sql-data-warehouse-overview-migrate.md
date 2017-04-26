---
title: "將您的解決方案移轉至 SQL 資料倉儲 | Microsoft Docs"
description: "將您的解決方案帶入 Azure SQL 資料倉儲平台的移轉指導。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e5937f8472492cd1dd77c82ed518a665718623a1
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-solution-to-sql-data-warehouse"></a>將您的解決方案移轉至 SQL 資料倉儲
SQL 資料倉儲是一個能夠彈性調整以符合您需求的分散式資料庫系統。 為了維護效能與延展性，並非所有 SQL Server 內的功能都會在「SQL 資料倉儲」內實作。 下列移轉主題會介紹一些將解決方案移轉到「SQL 資料倉儲」的重要因素。 由於針對延展性設計資料倉儲帶來了不同的設計模式，因此傳統的方法不一定最適合。 您可能會因而發現到，調整現有的解決方案可確保您能夠充分利用「SQL 資料倉儲」所提供的分散式平台。

另外也請務必記得，SQL 資料倉儲是以 Microsoft Azure 為基礎的平台。 因此，移轉過程很有可能需將您的資料傳輸至雲端。 資料傳輸應另行討論且必須謹慎考量，尤其是在磁碟區增加時。 資料傳輸和資料載入是個別的主題。

## <a name="migration-guidance"></a>移轉指導
開始移轉之前，請確定您已將這些文章都看過一遍，以確保您了解一些產品差異和基本概念。

* [移轉結構描述][Migrate your schema]
* [移轉資料][Migrate your data]
* [移轉程式碼][Migrate your code]

## <a name="next-steps"></a>後續步驟
CAT (客戶諮詢小組) 也會透過部落格發佈一些實用的 SQL 資料倉儲指引。  請瀏覽他們的[將資料移轉到 Azure SQL 資料倉儲的實際作法][Migrating data to Azure SQL Data Warehouse in practice]一文，以取得關於移轉的額外指導方針。

<!--Image references-->

<!--Article references-->
[Migrate your schema]: sql-data-warehouse-migrate-schema.md
[Migrate your data]: sql-data-warehouse-migrate-data.md
[Migrate your code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

