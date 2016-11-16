---
title: "在 Azure 搜尋服務中上傳資料 | Microsoft Docs"
description: "了解如何將資料上傳至 Azure 搜尋服務中的索引"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2ebe71b3456420f29ffe06bc15471d3fd2121f8c


---
# <a name="upload-data-to-azure-search"></a>上傳資料至 Azure 搜尋服務
> [!div class="op_single_selector"]
> * [概觀](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

## <a name="data-upload-models-in-azure-search"></a>Azure 搜尋服務中的資料上傳模型
有兩種方法可以在 Azure 搜尋服務索引中填入資料。 第一種方法是使用 Azure 搜尋服務 [REST API](search-import-data-rest-api.md) 或 [.NET SDK](search-import-data-dotnet.md) 來以手動方式將資料推送到索引中。 第二種方法是 [將支援的資料來源指向](search-indexer-overview.md) Azure 搜尋服務索引，並讓 Azure 搜尋服務自動將資料提取至搜尋服務。

本指南只會說明資料上傳推送模型 (僅在 [REST API](search-import-data-rest-api.md) 和 [.NET SDK](search-import-data-dotnet.md) 中受到支援) 的使用指示，但是您仍可從下面的內容深入了解提取模型。

### <a name="push-data-to-an-index"></a>將資料推送到索引
本方法涉及以程式設計方式將資料傳送至 Azure 搜尋服務以便可供搜尋。 若應用程式需要極低的延遲 (例如，如果您需要讓搜尋作業與動態庫存資料庫保持同步)，推送模式是您的唯一選擇。

您可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn798930.aspx) 或 [.NET SDK](search-import-data-dotnet.md) 將資料推送到索引。 目前沒有工具可支援透過入口網站推送資料。

這種方法比提取模型更有彈性，因為您可以個別或批次上傳文件 (每個批次最多 1000 個或 16 MB，視何者先到達)。 推送模型也可讓您將文件上傳至 Azure 搜尋服務，無論資料是存放在哪裡。

### <a name="pull-data-into-an-index"></a>將資料提取到索引
提取模型會將支援的資料來源編目，並自動為您將資料上傳到 Azure 搜尋服務索引。 除了辨識新文件，索引子還會追蹤現有文件的變更和刪除，讓您不必主動管理索引中的資料。

在 Azure 搜尋服務中，這項功能是透過索引子來實作，其目前可供 [Blob 儲存體 (預覽)](search-howto-indexing-azure-blob-storage.md)、[DocumentDB](http://aka.ms/documentdb-search-indexer)、[Azure SQL Database 和 Azure VM 上的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 使用。

索引子功能會在 [Azure 入口網站](search-import-data-portal.md)以及 [REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx) 中出現。




<!--HONumber=Nov16_HO2-->


