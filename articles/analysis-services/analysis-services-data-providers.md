---
title: "連接到 Azure Analysis Services 所需的用戶端程式庫 | Microsoft Docs"
description: "說明用戶端應用程式和工具連接到 Azure Analysis Services 所需的用戶端程式庫"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/14/2016
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: 949f34158f4fc8ee6d86cf3dd408c3c903234109
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>可供連接到 Azure Analysis Services 的用戶端程式庫

用戶端程式庫是用戶端應用程式和工具連接到 Analysis Services 伺服器的必備條件。 

Analysis Services 會利用三個用戶端程式庫。 ADOMD.NET 和「Analysis Services 管理物件」(AMO) 是受管理的用戶端程式庫。 Analysis Services OLE DB 提供者 (MSOLAP DLL) 是原生用戶端程式庫。 通常會同時安裝這所有三個程式庫。 Azure Analysis Services 需要的是最新版本。 

Microsoft 用戶端應用程式 (例如 Power BI Desktop 和 Excel) 會安裝這所有三個用戶端程式庫。 不過，用戶端程式庫可能不是 Azure Analysis Services 所需的最新版本，需依更新的版本或頻率而定。 同樣適用於自訂應用程式或其他介面，例如 AsCmd、TOM、ADOMD.NET。 這些應用程式需要手動安裝程式庫。 手動安裝的用戶端程式庫會以可散發的套件形式包含在 SQL Server 功能套件中。 不過，這些會用戶端程式庫與 SQL Server 版本繫結，而可能不是最新版本。  

用於用戶端連線的用戶端程式庫，與從 Azure Analysis Services 伺服器連接到資料來源所需的資料提供者不同。 若要深入了解資料來源連接，請參閱[資料來源連接](analysis-services-datasource.md)。

## <a name="download-the-latest-client-libraries"></a>下載最新的用戶端程式庫  
如果您處於生產環境並需要完整發行且受支援的版本，請使用下列用戶端程式庫。

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>後續步驟
[使用 Excel 進行連接](analysis-services-connect-excel.md)    
[與 Power BI 連線](analysis-services-connect-pbi.md)

