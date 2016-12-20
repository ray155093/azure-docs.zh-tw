---
title: "將您的結構描述移轉至 SQL 資料倉儲 | Microsoft Docs"
description: "將您的結構描述移轉至 Azure SQL 資料倉儲來開發解決方案的秘訣。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1ac4db6d9404567be0d3d12f91a96fa4e31ef3bf


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>將您的結構描述移轉至 SQL 資料倉儲
下列摘要可協助您了解 SQL Server 與「SQL 資料倉儲」之間的差異，以幫助您移轉資料庫。

## <a name="table-migration"></a>資料表移轉：
在移轉您的資料表時，您會想要熟悉 SQL 資料倉儲資料表的資料表功能。  [資料表概觀][資料表概觀]是很好的起點。  本文將介紹建立資料表時最重要的考量事項，例如資料表統計資料、散發、分割和編製索引。  它也包含一些[不支援的資料表功能][不支援的資料表功能]和因應措施。

SQL 資料倉儲支援常見的商務資料類型。  請參閱[資料類型][資料類型]一文，了解支援和[不支援的資料類型][不支援的資料類型]清單。  [資料類型][資料類型]文章也包含查詢以識別[不支援的資料類型][不支援的資料類型]。  轉換資料類型時，請務必查看[資料類型的最佳作法][資料類型的最佳作法]。

## <a name="next-steps"></a>後續步驟
將資料庫結構描述成功移轉到「SQL 資料倉儲」之後，您就可以繼續閱讀下列其中一篇文章：

* [移轉資料][移轉資料]
* [遷移程式碼][遷移程式碼]

若要深入了解 SQL 資料倉儲最佳做法，請參閱[最佳做法][最佳做法]一文。

<!--Image references-->

<!--Article references-->
[遷移程式碼]: ./sql-data-warehouse-migrate-code.md
[移轉資料]: ./sql-data-warehouse-migrate-data.md
[最佳做法]: ./sql-data-warehouse-best-practices.md
[資料表概觀]: ./sql-data-warehouse-tables-overview.md
[不支援的資料表功能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[資料類型]: ./sql-data-warehouse-tables-data-types.md
[不支援的資料類型]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[資料類型的最佳作法]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


