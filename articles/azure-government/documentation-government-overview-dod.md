---
title: "Azure Governmnet DoD 概觀 | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 71e8742d71ce6ebab08988751a886df4df000f8a
ms.openlocfilehash: 4e815c5fce563155f7a747262a3695a5180706e3
ms.lasthandoff: 01/18/2017


---
# <a name="department-of-defense-dod-in-azure-government"></a>Azure Government 中的美國國防部 (DoD)
## <a name="overview"></a>概觀
美國國防部 (DoD) 所屬各機關將 Azure Government 用於部署一系列的工作負載和解決方案，包括<a href="http://iasecontent.disa.mil/cloud/SRG/index.html">《美國國防部雲端運算安全性需求指南第 1 版發行 2》</a>中，影響等級 4 (L4) 和影響等級 5 (L5) 所涵蓋的工作負載。

Azure Government 是唯一榮獲國防資訊系統局資訊影響等級 5 DoD 佈建授權的第一款超大規模商業雲端服務。 此外，專供美國國防部客戶工作負載使用的 Azure Government 區域現已正式推出。

推動 DoD 轉向雲端的關鍵因素，是可以讓組織專注於自身的任務，並將建置和管理內部 IT 解決方案的干擾因素減到最低。

Azure Government 為主的雲端架構，可讓 DoD 人員專注於任務目標，並管理 IT 商品服務 (如 SharePoint) 以及其他應用程式的工作負載。  這可讓關鍵 IT 資源得以重新調配，集中在應用程式開發、分析和網路安全性上。

Azure 所提供的彈性和靈活度，對於 DoD 客戶有極大的好處。 在內部或在 DoD 資料中心內工作時，在雲端擴大工作負載的規模，比起透過傳統的硬體和服務採購過程，更簡單、更快速、更符合成本效益。 例如，購買新的多伺服器硬體，即使是用於測試環境，也可能會需要數個月，且需要批准重大資本支出。 相較之下，使用 Azure 時，現有工作負載的測試移轉，可以設定在數週或甚至數天後再進行，並可使用符合成本效益的方式 (測試結束時，可以拆除環境而不會有持續費用)。

這種彈性非常重要。 轉移至 Azure，DoD 客戶不只是節省金錢，雲端也提供新的機會。 例如，可以輕易讓測試環境運轉，以便深入了解新技術；您可以在正式開始生產環境部署之前，先將應用程式移轉到 Azure 中並進行測試。 任務的擁有者，可以更輕鬆地探索更多符合成本效益的選項，而不會帶來風險。

安全性是另一個關鍵領域，雖然任何雲端部署都需要適當的規劃，才能確保安全且可靠的服務提供，但實際上在 Azure Government 中，經過最適當設定的雲端式工作負載 (包含 L4 及以下等級的工作負載)，會比在 DoD 地點和資料中心內的許多傳統部署更安全。 這是因為，雖然在保護所有資產的實體安全方面，國防機關具有經驗和專業知識；然而，IT 介面區卻會面臨不同的挑戰。 網路安全是變化迅速的領域，需要專家的技術，而且要能夠快速開發並部署所需的應變措施。 Azure 平台 (商業和政府) 現在支援成千上萬個客戶，這種規模可讓 Microsoft 快速偵測不斷演變的攻擊途徑，並將其資源導向快速開發和實作適當的防禦措施。

## <a name="dod-region-qa"></a>DoD 區域問答集

### <a name="what-are-the-azure-government-dod-regions"></a>Azure Government DoD 區域有哪些？ 
美國 DoD 東部和美國 DoD 中部區域是實際分開的 Microsoft Azure 區域，其架構是為了符合美國國防部 (DoD) 針對雲端運算的安全性需求，特別適用於根據 DoD 雲端運算安全性需求指南 (SRG) 指定為 DoD 影響等級 5 的資料。   

### <a name="what-is-the-difference-between-azure-government-and-the-azure-government-dod-regions"></a>Azure Government 和 Azure Government DoD 區域有何差異？ 
Azure Government 是美國政府社群的雲端，會為聯邦政府、州政府和地區政府客戶、部落、受 ITAR 所規範的實體，和代其執行工作的解決方案提供者來提供服務。 所有 Azure Government 區域的架構和操作，都會符合 DoD 影響等級 5 資料和 FedRAMP High 標準的安全性需求。

Azure Government DoD 區域的架構則會支援影響等級 5 資料的實體分隔需求，其方法是藉由提供專用的計算和儲存體基礎結構，以專供 DoD 客戶使用。  

#### <a name="what-is-the-difference-between-impact-level-4-and-impact-level-5-data"></a>影響等級 4 和影響等級 5 的資料有何差異？  
影響等級 4 資料是受控管的非保密資訊 (CUI)，可能包括受到匯出控管的資料、具有隱私權資訊保護的健康資訊，以及其他需要明確 CUI 指定 (例如僅供官方使用、具有法律強制執行敏感性、機密安全性資訊) 的資料。

影響等級 5 資料所包括的受控管非保密資訊 (CUI)，則需要資訊擁有者、公法或政府法規認為有其必要的較高等級保護。  影響等級 5 資料包括非保密的國家安全系統。  如需 SRG 影響等級、其特殊需求和特性的詳細資訊，請參閱《美國國防部雲端運算安全性需求指南》的第 3 節。  

### <a name="what-data-is-categorized-as-impact-level-5"></a>哪些資料會歸類為影響等級 5？ 
等級 5 容納受控管非保密資訊 (CUI)，需要資訊擁有者、公法或其他政府法規認為有其必要、高於等級 4 所提供的較高等級保護。 等級 5 也支援非保密的國家安全系統 (NSS)。  此等級會根據 CNSSI-1253 (最高可至中度機密性和中度完整性 (M-M-x)) 容納 NSS 和 CUI 資訊分類。

### <a name="what-is-microsoft-doing-differently-to-support-impact-level-5-data"></a>為了支援影響等級 5 資料，Microsoft 做了哪些改變？ 
根據定義，影響等級 5 資料只能在專用的基礎結構中進行處理，以確保 DoD 客戶會與非聯邦政府租用戶實際分開。  在提供美國 DoD 東部和美國 DoD 中部區域時，Microsoft 會為 DoD 客戶提供專屬服務，以符合比 DoD 所提需求更高的標準，並超過任何其他超大規模商業雲端解決方案所提供的保護與功能等級。

### <a name="do-these-regions-support-classified-data-requirements"></a>這些區域支援保密資料需求嗎？ 
這些 Azure Government DoD 區域只支援上至影響等級 5 (含) 的非保密資料。  影響等級 6 資料則會定義為上至機密的保密資訊。

### <a name="what-organizations-in-the-dod-can-use-the-azure-government-dod-regions"></a>哪些 DoD 組織可以使用 Azure Government DoD 區域？ 
美國 DoD 東部和美國 DoD 中部區域是為了支援美國國防部客戶群所建置。  其中包括：
* 國防部長辦公室
* 參謀首長聯席會議
* 聯合參謀部
* 國防局
* 國防領域工作室部門
* 陸軍部門
* 海軍部門 (包括美國海軍陸戰隊)
* 空軍部門
* 美國海岸警備隊
* 統一作戰司令部
* 其他受到上述任何核准實體控制或監督的辦公室、機構、工作室和司令部

### <a name="are-the-dod-regions-more-secure"></a>DoD 區域會更加安全嗎？ 
Microsoft 負責營運其所有 Azure 資料中心和支援基礎結構，以符合當地和國際的安全性和合規性標準，在合規性投資和成果上位居所有商業雲端平台之冠。  這些新的 DoD 區域會提供特定的保證和承諾，以符合 DoD SRG 中所定義的雲端運算需求。

### <a name="why-are-there-multiple-dod-regions"></a>為何有多個 DoD 區域？ 
擁有多個 DoD 區域，Microsoft 就能讓客戶將解決方案架構設計為跨區域形式來因應災害復原案例，以確保業務持續性並滿足系統認證需求。  此外，客戶也可以將解決方案部署在最接近其實際位置的地理位置內，以獲得最好的效能。

### <a name="are-these-dod-regions-connected-to-the-niprnet"></a>這些 DoD 區域是否有連線到 NIPRNet？ 
DoD 規定用於 CUI 的商業雲端服務必須透過雲端存取點 (CAP) 與客戶連線。  因此，Azure DoD 區域會透過連往多個地理位置分散 CAP 的備援連線來連線到 NIPRNet。  DoD CAP 是網路疆界保護和監視裝置系統，可為 DoD 資訊系統網路和服務提供保護。

### <a name="what-does-general-availability-mean"></a>正式運作是什麼意思？ 
正式運作意指 Azure Government 中的 DoD 區域可能用於支援生產工作負載、對該區域中部署的所有服務提供有財務做後盾的 SLA，而且也會支援全面的可用性。

### <a name="how-does-a-dod-customer-acquire-azure-government-dod-services"></a>DoD 客戶要如何取得 Azure Government DoD 服務？ 
符合資格的實體可透過和 Azure Government 相同的轉售商通路購買 Azure Government DoD 服務。  為了遵守 Microsoft 要簡化雲端服務購買計劃和成本估計的承諾，在正式運作時，Azure 價格計算機中會納入 Azure Government DoD 區域的價格。  Azure Government DoD 服務可以相應增加或減少以符合需求，所以用多少就付多少。
已經在使用 Azure Government 的 Enterprise 合約客戶不必修改合約。  

### <a name="how-are-the-dod-regions-priced"></a>DoD 區域如何定價？ 
DoD 區域使用以區域為基礎的價格。  這表示已通過驗證之 DoD 客戶的服務成本，會以工作負載執行所在的 Azure Government 區域為準。  如需特定價格的詳細資訊，請向您的 Microsoft 客戶經理洽詢。  DoD 區域的價格會在日後透過 Azure.com 計算機提供。

### <a name="how-does-a-dod-organization-get-validated-for-the-azure-government-dod-regions"></a>DoD 組織要如何取得 Azure Government DoD 區域的驗證？ 
若要存取 Azure DoD 區域，客戶必須完成資格預審程序，以確認組織身分並確定其想要使用 Azure DoD 環境。  成功完成資格預審程序後，Microsoft 會為該組織的申請者提供進一步指示，讓其了解如何建立訂用帳戶、存取環境，以及將角色型存取控制提供給組織內的其他成員。

### <a name="can-independent-software-vendors-and-solution-providers-building-on-azure-deploy-solutions-in-the-azure-government-dod-regions"></a>採用 Azure 來進行建置的獨立軟體廠商和解決方案提供者可以在 Azure Government DoD 區域部署解決方案嗎？ 
解決方案提供者若採用 Azure 來建置其雲端服務供應項目，可在 Azure Government DoD 區域運作 DoD 專用的單一租用戶和多租用戶解決方案。  這些提供者必須先證明其符合資格，方法是提供與已核准 DoD 實體所簽訂之合約的書面證據，或出示已核准 DoD 實體所交付的擔保文件。  在 Azure Government DoD 區域提供服務的提供者，必須在其供應項目中包含電腦網路防禦、事件報告以及經過篩選來負責操作處理影響等級 5 資訊之解決方案的人員。  其他關於解決方案提供者的指引，可能可以在《DoD 雲端運算安全性需求指南》中找到。

### <a name="will-office-365-or-microsoft-dynamics-365-be-a-part-of-this-offering"></a>Office 365 或 Microsoft Dynamics 365 是否會是此供應項目的一員？ 
Microsoft 會連同此供應項目一起提供適用於 DoD 的影響等級 5 Office 365 服務。  Dynamics 365 則預計會在日後從 Azure DoD 區域提供影響等級 5 服務。

### <a name="how-do-i-connect-to-the-dod-regions-once-i-have-a-subscription"></a>在擁有訂用帳戶後，該如何連線到 DoD 區域？ 
Azure Government DoD 區域可透過 Azure Government 管理入口網站來使用。  核准使用的 DoD 客戶在部署可用服務時，會看到這些區域列為可用的選項。  如需管理 Azure Government 訂用帳戶的一般指引，請參閱我們的文件。

### <a name="what-services-are-part-of-your-impact-level-5-accreditation-scope"></a>哪些服務屬於影響等級 5 認證範圍？ 
Azure 是長青服務，每週都會新增服務和功能，其範圍內的服務數目不斷擴增。  如需最新資訊，請造訪我們的 <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA">Microsoft 信任中心。


## <a name="next-steps"></a>後續步驟：
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA">Microsoft 信任中心 - 美國國防部網頁</a>

<a href="http://iasecontent.disa.mil/cloud/SRG/index.html">《美國國防部雲端運算安全性需求指南第 1 版發行 2》</a>

<a href="https://azure.microsoft.com/en-us/offers/azure-government/">Azure Government 轉售商通路

<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>


