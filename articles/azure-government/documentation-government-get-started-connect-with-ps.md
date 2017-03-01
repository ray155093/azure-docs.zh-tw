---
title: "使用 PowerShell 連接 Azure Government | Microsoft Docs"
description: "使用 PowerShell 在 Azure Government 中管理訂用帳戶的相關資訊"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: 47e5e535-baa0-457e-8c41-f9fd65478b38
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 3a0594fa639a2ac34c53b2263dbf7dd2410cf16a
ms.lasthandoff: 02/21/2017


---



# <a name="connect-to-azure-government-with-powershell"></a>使用 PowerShell 連接到 Azure Government
若要使用 Azure CLI，您需要連接到 Azure Government，而不是 Azure Public。 Azure CLI 可透過指令碼用來管理大量訂用帳戶，或用來存取 Azure 入口網站中目前無法使用的功能。 如果您是在 Azure 公用中使用 PowerShell ，則幾乎相同。  Azure Government 中的差異如下︰

* 連接帳戶
* 區域名稱

> [!NOTE]
> 如果您尚未使用 PowerShell，請參閱 [Azure PowerShell 簡介](/powershell/azureps-cmdlets-docs)。
> 
> 

當您啟動 PowerShell 時，必須指定環境參數，以告訴 Azure PowerShell 連接至 Azure Government。  此參數確保 PowerShell 連接至正確的端點。  當您登入帳戶時，會決定端點集合。  不同的 API 需要不同版本的環境參數︰

| 連線類型 | 命令 |
| --- | --- |
| [Azure Active Directory (傳統部署模型)](https://msdn.microsoft.com/library/dn708504.aspx) 命令 |`Add-AzureAccount -Environment AzureUSGovernment` |
| [資源管理](https://msdn.microsoft.com/library/mt125356.aspx)命令 |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) 命令 |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Azure Active Directory (Resource Manager 部署模型)](https://msdn.microsoft.com/library/azure/mt757189.aspx) 命令 |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Azure CLI 命令列](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

如果使用 New-AzureStorageContext 連接至儲存體帳戶，並指定 AzureUSGovernment，則也可能會使用 Environment 參數。

### <a name="determining-region"></a>判斷區域
連接之後，還有一項差異 – 用來將目標設為服務的區域。  每個 Azure 雲端都有不同的區域。  服務可用性頁面上會列出它們。  您通常會使用命令之 Location 參數中的區域。

需要考量一個項目。  Azure Government 區域的格式化方式與它們的一般名稱不同：

| 一般名稱 | 命令 |
| --- | --- |
| 美國政府維吉尼亞州 |美國政府維吉尼亞州 |
| 美國政府愛荷華州 |美國政府愛荷華州 |

> [!NOTE]
> 使用位置參數時，US 與 Gov 之間沒有空格。
> 
> 

如果您想要驗證 Azure Government 中可用的區域，可以執行下列命令，然後列印目前清單︰

    Get-AzureLocation

如果您想要知道 Azure 的可用環境，可以執行︰

    Get-AzureEnvironment

