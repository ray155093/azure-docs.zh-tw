---
title: "資料來源連線 | Microsoft Docs"
description: "說明 Azure Analysis Services 中資料模型的資料來源連線。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 1b439e2b48cc009e727a49c271318cfd4ead9ef2


---
# <a name="datasource-connections"></a>資料來源連線
Azure Analysis Services 中的資料模型連線至某些資料來源時，可能需要不同的資料提供者。 在某些情況下，使用如 SQL Server Native Client (SQLNCLI11) 等原生提供者連線至資料來源的表格式模型可能會傳回錯誤。

例如，當您有 In-Memory 或 DirectQuery 資料模型與雲端資料來源 (如 Azure SQL Database) 連線時，如果您使用 SQLOLEDB 以外的原生提供者，可能會看見以下的錯誤訊息：**“The provider 'SQLNCLI11.1' is not registered”** (尚未註冊提供者 'SQLNCLI11.1')。

或者，當您有 DirectQuery 模型與內部部署資料來源連線時，如果您使用原生提供者，可能會看見錯誤訊息：**“Error creating OLE DB row set.Incorrect syntax near 'LIMIT'”** (建立 OLE DB 列集時發生錯誤.。'LIMIT' 附近的語法錯誤)。

## <a name="data-source-providers"></a>資料來源提供者
當與內部部署或雲端資料來源連線時，In-Memory 或 DirectQuery 資料模型支援下列資料來源提供者：

|  | **資料來源** | **In-memory** | **DirectQuery** |
| --- | --- | --- | --- |
| **雲端** |Azure SQL 資料倉儲 |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server |
| Azure SQL Database |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server | |
| **內部部署** (透過閘道) |SQL Server |SQL Server Native Client 11.0 |.NET Framework Data Provider for SQL Server |
| SQL Server |Microsoft OLE DB Provider for SQL Server |.NET Framework Data Provider for SQL Server | |
| SQL Server |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server | |
| Oracle |Microsoft OLE DB Provider for Oracle |Oracle Data Provider for .NET | |
| Oracle |Oracle Data Provider for .NET |Oracle Data Provider for .NET | |
| Teradata |Teradata 的 OLE DB 提供者 |Teradata Data Provider for .NET | |
| Teradata |Teradata Data Provider for .NET |Teradata Data Provider for .NET | |
| 分析平台系統 |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server | |

> [!NOTE]
> 請確定使用內部部署閘道時，系統會安裝 64 位元提供者。
> 
> 

將內部部署 SQL Server Analysis Services 表格式模型移轉至 Azure Analysis Services 時，可能需要變更提供者。

**提供資料來源提供者**

1. 在 SSDT > **Tabular Model Explorer** (表格式模型總管)  >  [資料來源] 中，以滑鼠右鍵按一下資料來源連線，然後按一下 [編輯資料來源]。
2. 在 [編輯連線] 中按一下 [進階]，以開啟 [進階屬性] 視窗。
3. 在[設定進階屬性]  >  [提供者] 中，然後選取適當的提供者。

## <a name="impersonation"></a>模擬
在某些情況下，可能需要指定不同的模擬帳戶。 模擬帳戶可以在 SSDT 或 SSMS 中指定。

對於內部部署資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。
* 如果使用 Windows 驗證，請設定 Windows 使用者/密碼。 對於 SQL Server，In-Memory 資料模型僅支援具有特定模擬帳戶的 Windows 驗證。

對於雲端資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。

## <a name="next-steps"></a>後續步驟
如果您有內部部署資料來源，請務必安裝 [內部部署閘道](analysis-services-gateway.md)。 若要深入了解在 SSDT 或 SSMS 中管理您伺服器的相關資訊，請參閱[管理您的伺服器](analysis-services-manage.md)。




<!--HONumber=Nov16_HO3-->


