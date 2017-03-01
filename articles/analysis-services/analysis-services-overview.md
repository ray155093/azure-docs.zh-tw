---
title: "什麼是 Azure Analysis Services | Microsoft Docs"
description: "了解 Azure 中的 Analysis Services 全貌。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 653c9a04016c0157b2107710e170ba63b3a499f7
ms.openlocfilehash: 38f68d6df19d24492a234933e10d5d5cf34d1b23
ms.lasthandoff: 01/05/2017


---
# <a name="what-is-azure-analysis-services"></a>什麼是 Azure Analysis Services？
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services 利用經實證的 Microsoft SQL Server Analysis Services 中之分析引擎所建置，其可提供企業級的雲端資料模型。 

觀看此影片以深入了解 Azure Analysis Services 如何配合 Microsoft 的整體 BI 功能，以及您如何才能受惠於將語意模型置入雲端。

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesGettingStarted/player]
>
>

> [!IMPORTANT]
> Azure Analysis Services 目前為**預覽版**。 有一些功能尚無法運作。 請務必查看本文後面的[預覽版期望](#preview-expectations)。 也請務必留意我們的 [Azure Analysis Services 部落格](https://go.microsoft.com/fwlink/?linkid=830920)以取得最新資訊。
> 
> 

## <a name="built-on-sql-server-analysis-services"></a>建置在 SQL Server Analysis Services 上
Azure Analysis Services 與您已知的同一個 SQL Server 2016 Analysis Services Enterprise Edition 相容。 Azure Analysis Services 支援 1200 種相容性層級的表格式模型。 DirectQuery、分割區、資料列層級安全性、雙向關聯性和轉譯都有支援。

## <a name="use-the-tools-you-already-know"></a>使用您已知的工具
![BI 開發人員工具](./media/analysis-services-overview/aas-overview-dev-tools.png)

建立 Azure Analysis Services 的資料模型時，使用的工具與建立 SQL Server Analysis Services 時的相同。 使用最新版的 [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) 或使用 [Azure Powershell](/powershell/azureps-cmdlets-docs) 和 [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 中的 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 範本製作和部署表格式模型。

## <a name="connect-to-data-sources"></a>連接到資料來源
部署到 Azure 中的伺服器的資料模型支援連接到您組織或雲端中的內部部署資料來源。 結合內部部署和雲端資料來源的資料，可成就混和式商務智慧解決方案。

![資料來源](./media/analysis-services-overview/aas-overview-data-sources.png)

因為您的伺服器在雲端中，因此能無縫連接到雲端資料來源。 連接到內部部署資料來源時，[內部部署資料閘道](analysis-services-gateway.md)可確保和雲端中的 Analysis Services 伺服器建立快速且安全的連線。  

 \*預覽版目前不支援某些資料來源。 若要深入了解，請參閱本文後面的[預覽版期望](#preview-expectations)。

## <a name="explore-your-data-from-anywhere"></a>從任何地方瀏覽資料
從任何地方連接，並從您的伺服器[取得資料](analysis-services-connect.md)。 Azure Analysis Services 支援從 Power BI Desktop、Excel、自訂應用程式和以瀏覽器為基礎的工具連接。

![資料視覺效果](./media/analysis-services-overview/aas-overview-visualization.png)

 \*預覽版目前不支援 Power BI Embedded。

## <a name="secure"></a>安全
#### <a name="user-authentication"></a>使用者驗證
Azure Analysis 服務的使用者驗證由 [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) 處理。 嘗試登入 Azure Analysis Services 資料庫時，使用者會使用具有他們嘗試存取之資料庫的存取權的組織帳戶身分識別。 這些使用者身分識別必須是 Azure Analysis Services 伺服器所在之預設 Azure Active Directory 目錄的訂用帳戶成員。 AAD 與內部部署 Active Directory 之間的[目錄整合](https://technet.microsoft.com/library/jj573653.aspx)，是讓您的內部部署使用者存取 Azure Analysis Services 資料庫的最佳方法，但並不適用所有案例。

使用者使用由其帳戶和密碼組成的使用者主體名稱 (UPN) 登入。 與內部部署 Active Directory 同步時，使用者的 UPN 通常是其組織的電子郵件地址。

管理 Azure Analysis Services 伺服器資源的權限，是透過將使用者指派到您 Azure 訂用帳戶中的角色來授予。 根據預設，訂用帳戶管理員具有 Azure 中之伺服器資源的擁有者權限。 使用 Azure Resource Manager 可以新增其他使用者。

#### <a name="data-security"></a>資料安全性
Azure Analysis Services 利用 Azure Blob 儲存體來保留 Analysis Services 資料庫的儲存體和中繼資料。 Blob 內的資料檔案是使用 Azure Blob 伺服器端加密 (SSE) 來加密。 使用直接查詢模式時，只會儲存中繼資料；查詢時會從資料來源存取實際資料。

#### <a name="on-premises-data-sources"></a>內部部署資料來源
透過安裝和設定[內部部署資料閘道](analysis-services-gateway.md)，即可安全地存取您組織中的內部部署資料。 閘道提供直接查詢和記憶體內部模式的資料存取。 當 Azure Analysis Services 模型連接至內部部署資料來源時，會建立一個查詢，以及內部部署資料來源的加密認證。 閘道雲端服務會分析該查詢，並將要求推送至 Azure 服務匯流排。 內部部署閘道會輪詢 Azure 服務匯流排是否有待處理的要求。 閘道接著收到查詢、解密認證，並連接至資料來源來執行。 結果會從資料來源傳送回閘道，然後再到 Azure Analysis Services 資料庫。

Azure Analysis Services 受 [Microsoft Online Services 條款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和 [Microsoft Online Services 隱私權聲明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)。
若要深入了解「Azure 資訊安全」，請參閱 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/Security/AzureSecurity)。

## <a name="get-help"></a>取得說明
設定和管理 Azure Analysis Services 非常容易。 您可以在此找到建立和管理伺服器需要的所有資訊。 建立要部署到您的伺服器的資料模型時，就像是要建立您部署到內部部署伺服器的資料模型一樣。 [MSDN 上的 Analysis Services](https://msdn.microsoft.com/library/bb522607.aspx) 有一個內容廣泛的程式庫，包含概念、程序、教學課程及參考文件。

[Channel 9 上的 Azure Analysis Services](https://channel9.msdn.com/series/Azure-Analysis-Services)一些實用的影片。

事情變化得很快。 您隨時可以在 [Azure Analysis Services 部落格](https://go.microsoft.com/fwlink/?linkid=830920)上找到最新資訊。

## <a name="community"></a>社群
Analysis Services 有活躍的使用者社群。 在 [Azure Analysis Services 論壇](https://aka.ms/azureanalysisservicesforum)的對話。

## <a name="feedback"></a>意見反應
您有任何建議或功能要求嗎？ 請務必在 [Azure Analysis Services 意見反應](https://aka.ms/azureanalysisservicesfeedback)留下您的意見。

您對文件有何建議嗎？ 您可以使用每篇文章下方的 Disqus 新增留言。

## <a name="preview-expectations"></a>預覽版期望
Azure Analysis Services 目前為預覽版。 您必須留意一些事項：

##### <a name="server-modes"></a>伺服器模式
Azure Analysis Services 目前支援 1200 種相容性層級的表格式模型。 不支援多維度和資料採礦模式，以及 Power Pivot for SharePoint 模式。

##### <a name="data-sources"></a>資料來源
在預覽版本中，部署到 Azure Analysis Services 伺服器的 1200 種表格式模型支援下列資料來源。

| **雲端** | **內部部署** |
| --- | --- |
| SQL Database |SQL Server |
| SQL 資料倉儲 |APS |
| Oracle | Oracle |
| Teradata | |

### <a name="data-source-providers"></a>資料來源提供者
Azure Analysis Services 中的資料模型連接到資料來源需要的資料提供者，可能不同於 SQL Server Analysis Services 中的資料模型。 資料提供者需求取決於資料來源是在雲端或內部部署，以及資料模型的類型：記憶體中或直接查詢。 若要深入了解，請參閱[資料來源連接](analysis-services-datasource.md)。

### <a name="client-connections"></a>用戶端連接
預覽版目前不支援 Power BI Embedded。

不支援與 Azure Analysis Services 伺服器即時連接和儲存在 OneDrive 或 SharePoint Online 的 Excel 活頁簿。

## <a name="next-steps"></a>後續步驟
在深入了解後，您現在可以開始使用 Azure Analysis Services。 了解如何在 Azure 中[建立伺服器](analysis-services-create-server.md)，並在其中[部署表格式模型](analysis-services-deploy.md)。


