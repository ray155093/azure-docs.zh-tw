---
title: "使用 PowerShell 管理 Azure Analysis Services | Microsoft Docs"
description: "使用 PowerShell 的 Azure Analysis Services 管理。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: c0dd85570f052c4a9651faecbaeb3eaa181f9017
ms.openlocfilehash: b00b2cf1d3a54bba716d7470643aa1a5e6a58633
ms.lasthandoff: 03/01/2017


---

# <a name="manage-azure-analysis-services-with-powershell"></a>使用 PowerShell 管理 Azure Analysis Services

本文說明用來執行 Azure Analysis Services 伺服器和資料庫管理工作的 PowerShell Cmdlet。 

伺服器管理工作，例如建立或刪除伺服器、暫停或繼續伺服器作業，或是變更服務層級 (層級)，會使用 Azure Resource Manager (AzureRM) Cmdlet。 其他資料庫管理工作，例如新增或移除角色成員、處理或資料分割，會使用在 [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) 模組中與 SQL Server Analysis Services 相同的 Cmdlet。

## <a name="permissions"></a>權限
大部分的 PowerShell 工作需要您在您管理的 Analysis Services 伺服器上具備系統管理員權限。 排定的 PowerShell 工作都是自動的作業。 執行排程器的帳戶必須具有 Analysis Services 伺服器上的管理員權限。 

針對使用 AzureRm Cmdlet 的伺服器作業，您的帳戶或執行排程器的帳戶也必須屬於 [Azure 角色型存取控制 (RBAC)](../active-directory/role-based-access-control-what-is.md) 中之資源的 Owner 角色。 

## <a name="server-operations"></a>伺服器作業 
Azure Analysis Services Cmdlet 包含在 [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) 元件模組中。 若要安裝 AzureRM Cmdlet 模組，請參閱 PowerShell 資源庫中的 [Azure Resource Manager Cmdlet (英文)](https://docs.microsoft.com/powershell/resourcemanager/)。

|Cmdlet|說明| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/get-azurermanalysisservicesserver)|取得伺服器執行個體的詳細資料。|  
|[New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/new-azurermanalysisservicesserver)|建立新的伺服器執行個體。|
|[Remove-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/remove-azurermanalysisservicesserver)|移除伺服器執行個體。|  
|[Suspend-AzureRmAnalysisServicesServe](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/suspend-azurermanalysisservicesserver)|暫停伺服器執行個體。| 
|[Resume-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/resume-azurermanalysisservicesserver)|繼續伺服器執行個體。|  
|[Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/set-azurermanalysisservicesserver)|修改伺服器執行個體。|   
|[Test-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/test-azurermanalysisservicesserver)|測試伺服器執行個體的存在。| 

## <a name="database-operations"></a>資料庫作業
Azure Analysis Services 資料庫作業使用與 SQL Server Analysis Services 相同的 [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) 模組。 不過，Azure Analysis Services 預覽並不支援所有的 Cmdlet。 

SQLASCMDLETS 模組提供特定工作的資料庫管理 Cmdlet，以及接受「表格式模型指令碼語言」(TMSL) 查詢或指令碼的一般用途 Invoke-ASCmd Cmdlet。 Azure Analysis Services 預覽支援在 SQLASCMDLETS 模組中的下列 Cmdlet。
  
|Cmdlet|說明|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|將成員新增到資料庫角色。| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|從資料庫角色移除成員。|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|執行 TMSL 指令碼。|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|處理資料庫。|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|處理資料分割。| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|處理資料表。|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|合併資料分割。|  
  

## <a name="related-information"></a>相關資訊
* [Analysis Services 中的 PowerShell 指令碼](https://msdn.microsoft.com/library/hh213141.aspx)。
* [相容性層級 1200 的表格式模型程式設計 (英文)](https://msdn.microsoft.com/library/mt712541.aspx)
