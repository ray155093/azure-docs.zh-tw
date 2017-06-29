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
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 34726377836d00d484ca01edb098f6c7cbfa9dbf
ms.contentlocale: zh-tw
ms.lasthandoff: 06/17/2017


---
# <a name="what-is-azure-analysis-services"></a>什麼是 Azure Analysis Services？
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services 利用經實證的 Microsoft SQL Server Analysis Services 中之分析引擎所建置，其可提供企業級的雲端資料模型。 

觀看此影片以了解 Azure Analysis Services 如何配合 Microsoft 的整體 BI 功能，以及您如何才能受惠於將資料模型置入雲端。


>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


## <a name="built-on-sql-server-analysis-services"></a>建置在 SQL Server Analysis Services 上
Azure Analysis Services 與您已知的同一個 SQL Server Analysis Services Enterprise Edition 相容。 Azure Analysis Services 支援 1200 或更新相容性層級的表格式模型。 DirectQuery、分割區、資料列層級安全性、雙向關聯性和轉譯都有支援。

## <a name="use-the-tools-you-already-know"></a>使用您已知的工具
![BI 開發人員工具](./media/analysis-services-overview/aas-overview-dev-tools.png)

建立 Azure Analysis Services 的資料模型時，使用的工具與建立 SQL Server Analysis Services 時的相同。 使用 [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) 來撰寫和開發模型。 使用 [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 來管理伺服器及建立資料庫模型。 並且，使用 [Powershell](analysis-services-powershell.md) 和 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 範例自動執行工作 

## <a name="supports-the-latest-features"></a>支援最新的功能
Azure Analysis Services 支援 1200 和 1400 預覽相容性層級的表格式模型。

**表格式 1200** - 首度包含在 SQL Server 2016 Analysis Services 中，1200 引進了表格式物件模型 (TOM) 來說明模型物件，例如資料表、資料行和關聯性。 表格式物件模型會經由 [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) 命名空間透過[表格式模型指令碼語言 (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)和 AMO 資料定義語言以 JSON 公開。

**表格式 1400 預覽** - 引進詳細資料列支援、物件層級安全性、不完全階層支援、用於資料連線的新式「取得資料」經驗，以及其他許多增強功能。 若要利用所有最新功能，您需要使用最新版的 [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)。 因為表格式 1400 仍在預覽中，所有很快就會有變化。 若要取得最新版本，請參閱我們的[部落格文章](https://azure.microsoft.com/blog/1400-models-in-azure-as/)。

## <a name="data-sources"></a>資料來源
部署到 Azure 中的伺服器的資料模型支援連接到您組織或雲端中的內部部署資料來源。 結合內部部署和雲端資料來源的資料，可成就混和式商務智慧解決方案。

因為您的伺服器在雲端中，因此能無縫連接到雲端資料來源。 連線至內部部署資料來源時，[內部部署資料閘道](analysis-services-gateway.md)可確保和雲端中的伺服器建立快速且安全的連線。 若要深入了解所支援的內部部署資料來源，請參閱 [Azure Analysis Services 中所支援的資料來源](analysis-services-datasource.md)。


## <a name="explore-your-data-from-anywhere"></a>從任何地方瀏覽資料
從幾乎任何地方連接到伺服器並從中取得資料。 Azure Analysis Services 支援從 Power BI Desktop、Excel、自訂應用程式和以瀏覽器為基礎的工具連接。

![資料視覺效果](./media/analysis-services-overview/aas-overview-visualization.png)


## <a name="secure"></a>安全
#### <a name="user-authentication"></a>使用者驗證
Azure Analysis 服務的使用者驗證由 [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) 處理。 嘗試登入 Azure Analysis Services 資料庫時，使用者會使用具有他們嘗試存取之資料庫的存取權的組織帳戶身分識別。 這些使用者身分識別必須是 Azure Analysis Services 伺服器所在之預設 Azure Active Directory 目錄的訂用帳戶成員。 AAD 與內部部署 Active Directory 之間的[目錄整合](https://technet.microsoft.com/library/jj573653.aspx)是讓使用者存取 Azure Analysis Services 資料庫的絕佳方法，但並不適用所有案例。

使用者使用由其帳戶和密碼組成的使用者主體名稱 (UPN) 登入。 與內部部署 Active Directory 同步時，使用者的 UPN 通常是其組織的電子郵件地址。

管理 Azure Analysis Services 伺服器資源的權限，是透過將使用者指派到您 Azure 訂用帳戶中的角色來授予。 根據預設，訂用帳戶管理員具有 Azure 中之伺服器資源的擁有者權限。 使用 Azure Resource Manager 可以新增其他使用者。

#### <a name="data-security"></a>資料安全性
Azure Analysis Services 利用 Azure Blob 儲存體來保留 Analysis Services 資料庫的儲存體和中繼資料。 Blob 內的資料檔案是使用 Azure Blob 伺服器端加密 (SSE) 來加密。 使用直接查詢模式時，只會儲存中繼資料。 查詢時會從資料來源存取實際資料。

#### <a name="on-premises-data-sources"></a>內部部署資料來源
透過安裝和設定[內部部署資料閘道](analysis-services-gateway.md)，即可安全地存取您組織中的內部部署資料。 閘道提供直接查詢和記憶體內部模式的資料存取。 當 Azure Analysis Services 模型連接至內部部署資料來源時，會建立一個查詢，以及內部部署資料來源的加密認證。 閘道雲端服務會分析該查詢，並將要求推送至 Azure 服務匯流排。 內部部署閘道會輪詢 Azure 服務匯流排是否有待處理的要求。 閘道接著收到查詢、解密認證，並連接至資料來源來執行。 結果會從資料來源傳送回閘道，然後再到 Azure Analysis Services 資料庫。

Azure Analysis Services 受 [Microsoft Online Services 條款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和 [Microsoft Online Services 隱私權聲明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)。
若要深入了解「Azure 資訊安全」，請參閱 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/Security/AzureSecurity)。

## <a name="get-help"></a>取得說明

### <a name="documentation"></a>文件
設定和管理 Azure Analysis Services 非常容易。 您可以在此找到建立和管理伺服器需要的所有資訊。 建立要部署到您的伺服器的資料模型時，就像是要建立您部署到內部部署伺服器的資料模型一樣。 [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) 有一個內容廣泛的程式庫，包含概念、程序、教學課程及參考文件。

### <a name="videos"></a>影片
觀看 [Channel 9 上有關 Azure Analysis Services](https://channel9.msdn.com/series/Azure-Analysis-Services) 的實用影片。

### <a name="blogs"></a>部落格
事情變化得很快。 您隨時可以在 [Azure Analysis Services 團隊部落格](https://blogs.msdn.microsoft.com/analysisservices/)和 [Azure 部落格](https://azure.microsoft.com/blog/)上找到最新資訊。

### <a name="community"></a>社群
Analysis Services 有活躍的使用者社群。 在 [Azure Analysis Services 論壇](https://aka.ms/azureanalysisservicesforum)的對話。

## <a name="feedback"></a>意見反應
您有任何建議或功能要求嗎？ 請務必在 [Azure Analysis Services 意見反應](https://aka.ms/azureanalysisservicesfeedback)留下您的意見。

您對文件有何建議嗎？ 您可以使用每篇文章下方的 Livefyre 新增留言。

## <a name="next-steps"></a>後續步驟
在深入了解後，您現在可以開始使用 Azure Analysis Services。 了解如何在 Azure 中[建立伺服器](analysis-services-create-server.md)，並在其中[部署表格式模型](analysis-services-deploy.md)。

