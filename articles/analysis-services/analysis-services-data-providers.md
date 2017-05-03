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
ms.date: 04/14/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: c29a6627f712b9d89ac65e845f3ccb4fb87bf8fb
ms.lasthandoff: 04/20/2017


---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>可供連接到 Azure Analysis Services 的用戶端程式庫

用戶端程式庫是用戶端應用程式和工具連接到 Analysis Services 伺服器的必備條件。 

Analysis Services 會利用三個用戶端程式庫。 ADOMD.NET 和「Analysis Services 管理物件」(AMO) 是受管理的用戶端程式庫。 Analysis Services OLE DB 提供者 (MSOLAP DLL) 是原生用戶端程式庫。 通常會同時安裝這所有三個程式庫。 Azure Analysis Services 需要的是最新版本。 

Microsoft 用戶端應用程式 (例如 Power BI Desktop 和 Excel) 會安裝這所有三個用戶端程式庫。 不過，視 Excel 的版本或是否會每月更新較新版本的 Excel 和 Power BI Desktop 而定，已安裝的用戶端程式庫可能不會更新至 Azure Analysis Service 所需的最新版本。 同樣適用於自訂應用程式或其他介面，例如 AsCmd、TOM、ADOMD.NET。 這些應用程式需要手動安裝程式庫。 手動安裝的用戶端程式庫會以可散發的套件形式包含在 SQL Server 功能套件中；不過，這些會與 SQL Server 版本繫結，而可能不是最新版本。  

用於用戶端連線的用戶端程式庫，與從 Azure Analysis Services 伺服器連接到資料來源所需的資料提供者不同。 若要深入了解資料來源連接，請參閱[資料來源連接](analysis-services-datasource.md)。

## <a name="download-the-latest-preview-client-libraries"></a>下載最新的**預覽**用戶端程式庫  
請使用下列用戶端程式庫來取得最新的錯誤修正和更新。 連線到 Azure Analysis Services 或 SQL Server vNext Analysis Services 時，建議使用下列用戶端程式庫。

[MSOLAP (amd64) 預覽](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[MSOLAP (x86) 預覽](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[AMO 預覽](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[ADOMD 預覽](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-client-libraries"></a>下載最新的 **RTM** 用戶端程式庫  
如果您處於生產環境並需要完整發行且受支援的版本，請使用下列用戶端程式庫。

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>後續步驟
[連接到 Azure Analysis Services 伺服器](analysis-services-connect.md)。

