---
title: "Azure Analysis Services 用戶端連線所需的資料提供者 | Microsoft Docs"
description: "描述用戶端連接 Azure Analysis Services 所需的資料提供者 | Microsoft Docs"
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
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 197e32058908533bc46b2ea2e38ff521a4a1fa3f
ms.lasthandoff: 04/07/2017


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>可供連接到 Azure Analysis Services 的資料提供者

資料提供者 (也稱為用戶端程式庫) 是用戶端應用程式連接到 Analysis Services 伺服器的必備條件。 

Analysis Services 會利用三個資料提供者。 ADOMD.NET 和 Analysis Services 管理物件 (AMO) 是受管理的資料提供者。 Analysis Services OLE DB 提供者 (MSOLAP DLL) 是原生資料提供者。 通常會同時安裝上述所有三個提供者。 Azure Analysis Services 需要最新版本的資料提供者。 

Microsoft 用戶端應用程式 (如 Power BI Desktop 和 Excel) 會安裝所有三個資料提供者。 不過，視 Excel 的版本而定，或者是否會每月更新較新版本的 Excel 和 Power BI Desktop，已安裝的資料提供者可能不會更新為 Azure Analysis Service 所需的最新版本。 同樣適用於自訂應用程式或其他介面，例如 AsCmd、TOM、ADOMD.NET。 這些應用程式需要手動安裝提供者。 手動安裝的資料提供者會以可散發套件形式包含在 SQL Server 功能套件中；不過，這些會繫結至 SQL Server 版本，而且可能不是最新版本。  

用戶端連線的資料提供者不同於從 Azure Analysis Services 伺服器連接到資料來源所需的資料提供者。 若要深入了解資料來源連接，請參閱[資料來源連接](analysis-services-datasource.md)。

## <a name="download-the-latest-preview-data-providers"></a>下載最新的**預覽**資料提供者  
請使用下列資料提供者來取得最新的錯誤修正和更新。 連接到「Azure Analysis Services 預覽」或「SQL Server vNext 分析服務」時，建議使用下列資料提供者。

[MSOLAP (amd64) 預覽](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[MSOLAP (x86) 預覽](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[AMO 預覽](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[ADOMD 預覽](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-data-providers"></a>下載最新的 **RTM** 資料提供者  
如果您處於生產環境並需要完整發行且受支援的版本，請使用下列資料提供者。

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>後續步驟
安裝最新的資料提供者後，用戶端應用程式即可連接到伺服器。 若要深入了解從用戶端連接，請參閱[從 Azure Analysis Services 取得資料](analysis-services-connect.md)。

