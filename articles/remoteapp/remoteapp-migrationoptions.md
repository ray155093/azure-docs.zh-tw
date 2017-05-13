---
title: "移出 Azure RemoteApp 的選項 | Microsoft Docs"
description: "了解移出 Azure RemoteApp 的選項。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c4e0e5bc-5c13-4487-b1b6-ebf2a5edc1f0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 11254a9bd82885701b457ba3b4ec0ef0aad710e6
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017


---
# <a name="options-for-migrating-out-of-azure-remoteapp"></a>移出 Azure RemoteApp 的選項
> [!IMPORTANT]
> Azure RemoteApp 即將於 2017 年 8 月 31 日停止服務。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。


如果您因為[停用公告](https://go.microsoft.com/fwlink/?linkid=821148)或因為您已完成評估而停止使用 Azure RemoteApp，您需要從 Azure RemoteApp 移轉至另一個應用程式服務。 移轉有兩種不同的方法︰自我管理 (通常稱為基礎結構即服務 [IaaS]) 部署，或完全受管理 (通常稱為平台即服務或軟體即服務 [PaaS/SaaS]) 供應項目。 

自助服務的 IaaS 是由您管理、操作及擁有的自己動手部署，可直接部署在虛擬機器 (VM) 或實體系統上。 另一方面，完全受管理的 PaaS/SaaS 供應項目比較像 Azure RemoteApp - 合作夥伴會在遠端解決方案之上提供一個可處理操作和服務事宜的服務層，而身為客戶的您，只要進行一些映像和應用程式管理。

[ Azure RemoteApp 網路研討會](https://social.msdn.microsoft.com/Forums/azure/40557aaa-3e9f-403c-b221-ad3eac10dc56/migration-option-webinar-recordings?forum=AzureRemoteApp)，或閱讀更多詳細資訊 (包括不同裝載選項的範例)。

## <a name="self-managed-iaas-solutions"></a>自我管理的 (IaaS) 解決方案
### <a name="rds-on-iaas"></a>**IaaS 上的 RDS**
您可以使用 RemoteApp 或內部部署桌上型電腦或在託管環境 (如 Azure Vm 上) 中，部署以工作階段為基礎的原生遠端桌面服務 (在 Windows Server 中) 部署。 IaaS 部署上的 RDS 最適合於已經熟悉 RDS 部署且具備其現有技術專業知識的客戶。 

> [!NOTE]
> 您需要 RDS 用戶端存取授權的大量授權與軟體保證 (SA)，才能使用此部署選項。

在 Azure VM 上部署 RDS 比以往您使用部署和修補範本時還要容易 (請讀取[概觀](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/)，然後 [加以取得](https://aka.ms/rdautomation))。 您可以使用[自動調整指令碼](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76)，在 Azure RemoteApp 內取得與 Azure 傳統部署模型資源 (而非 Azure 資源模型資源) 相同的彈性調整功能，不過有更多的自訂和設定功能。 當您在 Azure VM 上部署 RDS 時，會透過 [Azure 支援](https://azure.microsoft.com/support/plans/) (為您提供 Azure RemoteApp 支援的相同支援專業人員) 提供支援。 您可以連絡 [Azure 支援](https://azure.microsoft.com/support/plans/)，也可以使用即將發行的成本計算機自行執行計算，以取得以現有使用量為基礎的成本估計。  此外，在 N 系列 VM (目前處於私人預覽狀態) 中，您可以新增 vGPU - 深入了解如何新增 vGPU，以及如何在我們的 Ignite 工作階段中[控管 Windows Server 2016 中的 RDS 改進](https://myignite.microsoft.com/videos/2794)。   

我們提供 [Windows Server 2012 R2](http://aka.ms/rdsonazure) 和 [Windows Server 2016](http://aka.ms/rdsonazure2016) 的逐步部署指南，協助您進行部署。 查看[遠端桌面部落格](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services)中的最新消息。

### <a name="citrix-on-iaas"></a>**IaaS 上的 Citrix**
以工作階段為基礎之 XenApp 或 XenDesktop 的原生 Citrix 部署，可以部署於內部部署或託管環境中 (例如在 Azure VM 上)。 

如需詳細資訊，請參閱逐步部署指南 ([Azure 上的 Citrix XA 7.6](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx))。 深入了解 [Azure 上的 Citrix](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx)，包括價格計算機。 您也可以找到 [Citrix 連絡人](http://citrix.com/English/contact/index.asp)一起討論您的選項。

## <a name="fully-managed-paassaas-offerings"></a>完全受管理的 (PaaS/SaaS) 供應項目

### <a name="awingu"></a>Awingu
Awingu 提供執行傳統應用程式的簡易線上工作區解決方案、SaaS 和 html5 瀏覽器的文件。 這麼一來，即可在任何類型的裝置上安全地使用任何應用程式， 而 SaaS 服務也可使用多種單一登入選項。 此外，多樣化 (雲端) 檔案系統可深入整合至您的工作區。 除了完整的行動力之外，Awingu 豐富的線上工作區也會提供最佳的安全性及細微控制 (如下載/上傳)、完整的使用稽核、多重要素驗證 (如 Azure MFA)、工作階段記錄等等。 現成的 Awingu 可讓您分享文件和應用程式工作階段，以達到最佳化和安全的工作作業。
Awingu 的解決方案是多租用戶、多 AD 和開放式 API。 小型和大型企業、雲端服務提供者和 [Isv](http://www.isv2saas.com) 使用此解決方案。 這些客戶特別喜愛使用簡單、安裝簡單和低 TCO 等特點。

Awingu All-in-One 於 [Azure Marketplace 上推出](https://azuremarketplace.microsoft.com/marketplace/apps/awingu.awingu-arm)，具有 2 個內建的並行使用者。 其他授權可透過 [廣大的經銷商和轉售商](http://www.awingu.com/reseller) 取得。

深入了解 [做為 Azure RemoteApp 替代方案的 Awingu](http://alternative-for-azure-remoteapp.awingu.com/)。


> 主要地點︰比利時
> 
> 作業區域：EMEA、北美洲和巴西
> 
> 提供以工作階段為基礎的 RemoteApp 和桌面解決方案︰是，兩者 
> 
> 全域：
> 
> Arnaud Marlière，CMO
> 
> 電子郵件：[arnaud@awingu.com](mailto:arnaud@awingu.com)
> 
> 電話：+1 646 583 3025
> 
> **比利時總部**：
> 
> Ottergemsesteenweg-Zuid 808 B44
> 
> 9000 Gent
> 
> 電子郵件：[info@awingu.com](mailto:info@awingu.com) 
> 
> 電話：+32 9 296 40 11
> 
> 美國：
> 
> 7th floor, 1177 Ave of the Americas,
> 
> New York, NY 10036
> 
> 電子郵件：[info.us@awingu.com](mailto:info.us@awingu.com)

### <a name="citrix-xenapp-essentials-released-april-2017"></a>Citrix XenApp Essentials (2017 年 4 月發行)
Citrix XenApp Essentials 現在已在 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Citrix.XenAppEssentials) 上推出，是新的應用程式虛擬化服務，將 Citrix Cloud 平台的優勢和彈性與 Microsoft Azure RemoteApp 簡單、規範性和便於取用的願景結合。  

現有 Azure RemoteApp 客戶可以 [註冊免費試用](https://www.citrix.com/global-partners/microsoft/remote-app.html)。  注意：只有 Citrix 使用者服務費免費，仍須收取 Azure 計算和儲存體費用

深入了解 [Citrix XenApp Essentials](https://www.citrix.com/global-partners/microsoft/remote-app.html)。

### <a name="citrix-cloud-xenapp-service-and-xendesktop-service"></a>Citrix Cloud XenApp 服務和 XenDesktop 服務 

[Citrix Cloud XenApp 服務和 XenDesktop 服務](https://www.citrix.com/products/citrix-cloud/services.html)是傳遞應用程式和桌面，以及進階管理和監視功能的最佳解決方案。 

### <a name="citrix-service-provider-program"></a>Citrix 服務提供者方案
「Citrix 服務提供者方案」讓服務提供者能輕鬆提供簡單的虛擬雲端運算功能給中小企業，並以簡單、隨收隨付的模式提供他們想要的服務。 Citrix 服務提供者可利用任何裝置、隨處存取、最廣泛的應用程式支援、豐富的經驗、提升的安全性和延展性，使其 Microsoft SPLA 業務成長茁壯，並擴充其 RDS 平台投資。 接著，Citrix 服務提供者可吸引更多訂閱者、提高客戶滿意度並降低其營運成本。 [深入了解](http://www.citrix.com/products/service-providers.html)或[尋找合作夥伴](https://www.citrix.com/buy/partnerlocator.html)。

### <a name="frame"></a>Frame

企業和政府中的 IT 組織、受管理的服務提供者和主要軟體供應商都選擇 Frame 來建立和管理其雲端中的安全、軟體定義程式的工作區。 從小型到大型組織，Frame 讓使用者可以非常輕鬆地從任何裝置及任何瀏覽器上存取 Windows 應用程式。 Frame 平台包含系統管理員需要的所有項目以從雲端部署應用程式，包括 Azure 基礎結構和 RDS 授權 (可選擇性使用自己的 Azure 帳戶和授權)。 

深入了解 [Azure 上的 Frame](https://www.fra.me/ara)。 

> 主要位置︰美國加州的聖馬刁
>
> 營運區域︰全球
>
> Microsoft 合作夥伴：是
> 
> 電話：1-480-269-4668

### <a name="microsoft-hosted-service-provider"></a>Microsoft 託管服務提供者
託管協力廠商通常會依照協力廠商與 Microsoft 和其他軟體提供者簽訂的授權合約，以及允許轉售訂戶存取授權 (SAL) 的服務提供者授權合約，提供完全受管理的託管 Windows 桌面和應用程式服務，可能包括管理 Azure 資源、作業系統、應用程式和技術服務。 下列資訊針對某些專門協助客戶處理其 Azure RemoteApp 移轉的主機服務提供者，提供詳細資料和連絡資訊。 查看目前已完成 RDS on IaaS 學習路徑和評估的[託管服務提供者清單](http://aka.ms/rdsonazurecertified)。  

#### <a name="aspex"></a>ASPEX
[ASPEX](http://www.aspex.be/en) 專精於從 ISV 轉換至雲端，而 ISV 想要將其目前的雲端設定最佳化。 ASPEX 會提供廣泛的受管理服務、開發和諮詢服務。  

> 主要地點︰比利時安特衛普
> 
> 營運區域︰西歐
> 
> 合作夥伴狀態︰[銀級](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)
> 
> Microsoft Cloud 服務提供者：是
> 
> 提供以工作階段為基礎的 RemoteApp 和桌面解決方案︰是，兩者
> 
> Azure RemoteApp 移轉解決方案︰是，[深入了解](https://www.aspex.be/en/azure-remote-apps)
> 
> 電話︰+3232202198
> 
> 電子郵件︰[info@aspex.be](mailto:info@aspex.be)
> 
> Web：[http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>Conexlink (平台名稱︰MyCloudIT)
[MyCloudIT](https://mycloudit.com) 是一個自動化平台，可供 IT 公司簡化、最佳化和調整移轉，以及在 Microsoft Azure 雲端中提供遠端桌面、遠端應用程式和基礎結構。 

MyCloudIT 平台可減少 95% 的部署時間、30% 的 Azure 成本，而且只要按幾個按鍵，即可將其用戶端的整個 IT 基礎結構移到雲端。 協力廠商現在可以從通用儀表板管理客戶、以前所未有的方式服務世界各地的使用者，並且讓營收成長，但不會增加額外的負荷或廣泛 Azure 訓練。  

> 主要地點︰美國德州達拉斯
> 
> 營運區域︰全球
> 
> 合作夥伴狀態︰[金級](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)
> 
> Microsoft Cloud 服務提供者：是
> 
> 提供以工作階段為基礎的 RemoteApp 和桌面解決方案︰是，兩者
> 
> Azure RemoteApp 移轉解決方案︰是，[深入了解](https://mycloudit.com/remote-app-microsoft/)
> 
> Brian Garoutte, VP of Business Development
> 
> 電話：972-218-0741
>   
> 電子郵件：[brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>Acuutech
[Acuutech](http://www.acuutech.com) 專門提供託管桌面解決方案，並從 Azure 與其自己的資料中心將以 Microsoft 技術為基礎的完整桌面和 ISV 應用程式體驗提供給全球客戶群。

> 主要地點︰英國倫敦、新加坡、休士頓、德州
> 
> 營運區域︰全球
> 
> 合作夥伴狀態︰金級
> 
> Microsoft Cloud 服務提供者：是
> 
> 提供以工作階段為基礎的 RemoteApp 和桌面解決方案︰是，兩者
> 
> Azure RemoteApp 移轉解決方案︰是，[深入了解](http://www.acuutech.com/ara-migration/)
> 
> **英國**：
>   
> 5/6 York House, Langston Road,
>   
> Loughton, Essex IG10 3TQ
>   
> 電話：+44 (0) 20 8502 2155
> 
> **新加坡**：
>   
> 100 Cecil Street, #09-02, 
>   
> The Globe, Singapore 069532
> 
> 電話：+65 6709 4933
>   
> **北美洲**：
>   
> 3601 S. Sandman St.
>   
> Suite 200, Houston, TX 77098
>   
> 電話：+1 713 691 0800

#### <a name="saasplaza"></a>**SaaSplaza**
[SaaSplaza](http://www.saasplaza.com/) 提供完整的 Microsoft Dynamics 組合 (NAV、AX、GP、SL、CRM) 私人和公用雲端 (Azure)。

> 主要位置︰荷蘭
> 
> 營運區域︰全球
> 
> 合作夥伴狀態︰[金級](https://partnercenter.microsoft.com/pcv/solution-providers/saasplaza_4295495801/791011_2?k=saasplaza)
> 
> Microsoft Cloud 服務提供者：是
> 
> 提供以工作階段為基礎的 RemoteApp 和桌面解決方案︰是，兩者
> 
> **歐洲、中東與非洲**：
> 
> Prins Mauritslaan 29-35
> 
> 71 LP Badhoevedorp
> 
> The Netherlands
> 
> 電話：+31 20 547 8060 
> 
>  **美洲**：
> 
> 171 Saxony Road, Suite 105
> 
> Encinitas, CA 92024
> 
> San Diego
> 
> 美國
> 
> 電話：+1 858 385 8900 
> 
> **亞太地區**：
> 
> 105 Cecil Street
>    
> \#11-08, The Octagon
> 
> Singapore 069534
> 
> 新加坡
>   
> 電話 - 新加坡：+65 6222 6591
> 
> 電話 - 澳大利亞：+61 2 8310 5568 
>    
> 電話 - 紐西蘭：+64 4 488 0321
> 
## <a name="need-more-help"></a>需要其他協助？
仍然需要協助進行選擇，或有進一步的問題？ 使用下列其中一種方法來取得協助。 

1. 請透過下列電子郵件地址連絡我們：[arainfo@microsoft.com](mailto:arainfo@microsoft.com)。
2. 連絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 從開啟 [Azure 支援案例](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)著手。
3. 致電給我們。 [尋找當地的銷售代表電話](https://azure.microsoft.com/overview/sales-number/)。


