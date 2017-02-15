---
title: "Azure Government 資料庫 | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c94e643565374f852633254a94c2138713cc290e
ms.openlocfilehash: 58d6295057c71dad488e04be25cc9a85d73bcd20


---
# <a name="azure-government-databases"></a>Azure Government 資料庫
## <a name="sql-database"></a>SQL Database
請參閱 <a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx">SQL Database 引擎的 Microsoft 資訊安全中心</a>和 [Azure SQL Database 公開文件](../sql-database/index.md)，以取得關於中繼資料可見性組態和保護最佳作法的其他指引。

### <a name="variations"></a>變化
SQL V12 Database 已在 Azure Government 中正式推出。

Azure Government 中 SQL Azure 伺服器的位址不同︰

| 服務類型 | Azure 公用 | Azure Government |
| --- | --- | --- |
| SQL Database |*.database.windows.net |*.database.usgovcloudapi.net |

### <a name="considerations"></a>考量
下列資訊可識別 Azure SQL 的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
| --- | --- |
| 所有在 Microsoft Azure SQL 中儲存及處理的資料都可以包含受 Azure Government 管制的資料。 您必須使用資料庫工具來傳輸受 Azure Government 管制的資料。 |Azure SQL 中繼資料不可以包含受到匯出管制的資料。 此中繼資料包括建立和維護儲存體產品時所輸入的所有組態資料。  請勿輸入受管制/受控制資料到下列欄位︰資料庫名稱、訂用帳戶名稱、資源群組、伺服器名稱、伺服器管理員登入、部署名稱、資源名稱、資源標籤 |

## <a name="azure-redis-cache"></a>Azure Redis 快取
如需這項服務和使用方式的詳細資料，請參閱 [Azure Redis 快取公開文件](../redis-cache/index.md)。

### <a name="variations"></a>變化
Azure Government 中用於存取和管理 Azure Redis 快取的 URL 不同：

| 服務類型 | Azure 公用 | Azure Government |
| --- | --- | --- |
| 快取端點 |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |
| Azure 入口網站 |https://portal.azure.com |https://portal.azure.us |

> [!NOTE]
> 所有指令碼和程式碼都必須說明適當的端點和環境。 如需詳細資訊，請參閱[如何連接至 Azure Government 雲端](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud)。
> 
> 

### <a name="considerations"></a>考量
下列資訊可識別 Azure Redis 快取的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
| --- | --- |
| 所有在 Azure Redis 快取中儲存和處理的資料都可以包含受 Azure Government 管制的資料。 |Azure Redis 快取中繼資料不可以包含受到匯出管制的資料。 請不要將受管制/受控制資料輸入至下列欄位︰快取名稱、VNET 名稱、訂用帳戶名稱、資源群組、資源標籤、Redis 屬性。 |

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格</a>。




<!--HONumber=Nov16_HO3-->


