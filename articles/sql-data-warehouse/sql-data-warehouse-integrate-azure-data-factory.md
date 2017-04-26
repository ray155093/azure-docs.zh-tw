---
title: "搭配使用 Azure Data Factory 與 SQL 資料倉儲 | Microsoft Docs"
description: "搭配使用 Azure Data Factory (ADF) 與 SQL 資料倉儲以便開發解決方案的秘訣。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 12743b3594a0a62b0b974fb6dd1c39972c1bd3fa
ms.lasthandoff: 04/03/2017


---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>搭配使用 Azure Data Factory 與 SQL 資料倉儲
Azure Data Factory 提供完全受管理的方法，可供協調資料的傳輸和 SQL 資料倉儲上預存程序的執行。  這可讓您更輕鬆地設定及排程 SQL 資料倉儲的複雜擷取、轉換和載入 (ETL) 程序。 如需 Azure Data Factory 的更完整概觀，請參閱 [Azure Data Factory 文件][Azure Data Factory documentation]。

## <a name="data-movement"></a>資料移動
Azure Data Factory 可讓資料在內部部署來源與不同的 Azure 服務之間移動。  整體而言，目前與 Azure Data Factory 的整合可支援下列位置的雙向資料移動：

* Azure Blob
* Azure SQL Database
* 內部部署 SQL Server
* IaaS 上的 SQL Server

如需有關如何設定資料複製活動的資訊，請參閱[使用 Azure Data Factory 複製資料][Copy data with Azure Data Factory]

## <a name="stored-procedures"></a>預存程序
 如同可用來排程資料傳輸，Azure Data Factory 也可以用來協調預存程序的執行。  這允許建立更多複雜的管線並擴充 Azure Data Factory 的功能，以利用 SQL 資料倉儲的運算能力。

## <a name="next-steps"></a>後續步驟
如需整合概觀，請參閱 [SQL 資料倉儲整合概觀][SQL Data Warehouse integration overview]。
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][SQL Data Warehouse development overview]。

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/data-factory-data-movement-activities.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/


