---

title: "從 Azure RemoteApp 移轉到 Citrix XenApp Essentials | Microsoft Docs"
description: "如何從 Azure RemoteApp 移轉到 Citrix XenApp Essentials"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 695a8165-3454-4855-8e21-f2eb2c61201b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: fcd96a466d1c0dad17d7012308281ef868463b19
ms.contentlocale: zh-tw
ms.lasthandoff: 06/02/2017


---

# <a name="migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>從 Azure RemoteApp 移轉到 Citrix XenApp Essentials

如果您使用 Azure RemoteApp，且想要移轉到 Citrix XenApp Essentials，請注意幾個必要條件。 首先，請閱讀 Citrix 的 [Citrix XenApp Essentials 技術部署逐步指南](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) \(英文\)，以及其[線上技術文件庫](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html) \(英文\)。 

## <a name="prerequisite-steps-for-migration"></a>移轉先決條件步驟

1. 建立新的虛擬網路，或決定您要在 Azure Resource Manager 的哪個 Azure 虛擬網路中部署 Citrix XenApp Essentials。 Azure RemoteApp 使用 Azure 傳統入口網站，而 Citrix XenApp Essentials 僅支援 Azure Resource Manager。  
2. 確保您選取的虛擬網路可透過網路存取您的網域控制站，因為 Citrix 僅支援混合式部署。 如果您使用 Azure RemoteApp 雲端部署，請確定您的虛擬網路可透過網路存取 Active Directory 網域控制站。 您也可以使用 Azure Active Directory Domain Services (Azure AD DS)。 
3. 確認虛擬網路的 DNS 已正確設定，以使您首次便能成功加入網域。 您可以在選取的虛擬網路中建立一個虛擬機器 (VM)，並執行手動網域加入以驗證 DNS 及網域加入會如預期般運作。 這可確保您首次便能成功部署 Citrix XenApp Essentials。 
4. 如有需要，請在搭配 Azure RemoteApp 使用的 Azure 傳統入口網站虛擬網路，和您的 Azure Resource Manager 虛擬網路之間，建立虛擬網路對等互連。 只有在這兩個網路位於相同區域時，對等互連程序才能運作。 如果沒有，請使用站對站 VPN 來連接虛擬網路以存取網路。 
5. 如果需要，請閱讀[如何將資料移轉至 Azure RemoteApp/如何將資料從 Azure RemoteApp 移轉出來](remoteapp-migrate.md)。 
6. 更新現有的 Azure RemoteApp 映像以包含 Citrix VDA 元件 (如需指示，請參閱 Citrix 文件)。 
7. 前往 Azure Marketplace 並開始部署 Citrix XenApp Essentials。

## <a name="other-considerations"></a>其他考量

當您移轉時，請注意下列其他考量：
- Citrix XenApp Essentials 僅支援混合式部署。 換句話說，它需要的網路才能存取網域控制站，並進一步執行網域加入。 如果您使用 Azure RemoteApp 雲端部署，請使用 Azure AD DS，或確保您的虛擬網路能夠存取 Active Directory 以執行網域加入。 
- 若要了解如何將使用者資料移至 Citrix XenApp Essentials，請參閱[如何將資料轉入或轉出 Azure RemoteApp](remoteapp-migrate.md)。 
- Citrix XenApp Essentials 僅支援 Active Directory 帳戶。 它並不支援 Microsoft 帳戶 (例如 outlook.com、msn.com 或 hotmail.com)。 

## <a name="citrix-xenapp-essentials-billing"></a>Citrix XenApp Essentials 計費

如需完整的價格詳細資料，請參閱[常見問題集](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) \(英文\) 和 [Citrix 概觀文章](https://www.citrix.com/global-partners/microsoft/remote-app.html) \(英文\)。 Citrix XenApp Essentials 有三種計費元件：

- Citrix 服務費，會針對每位使用者於每個月收取 $12 美元。 如同所有的 Azure Marketplace 購買方式，費用會以您 Azure 訂用帳戶相關聯的付款方式收取。 如果您是 Enterprise 合約 (EA) 客戶，則無法使用 Azure 貨幣點數。 
- 遠端資料服務 (RDS) 用戶端存取授權 (CAL)。 目前，您可以購買與 Citrix XenApp Essentials 款項搭售的遠端存取費用，其金額為 $6.25 美元。 如果您是 EA 客戶，可以使用 Azure 貨幣點數來支付此項目。 如果您想要使用現有的 RDS CAL，請透過 [arainfo@microsoft.com](mailto:arainfo@microsoft.com) 連絡我們，我們會將它套用到您的帳單中。 
- Azure 計算和儲存體。 這是 Azure 儲存體的費用，會計算 VM 耗用量。 在選取 VM 大小及使用者密度時，請注意價格。 如果您是 EA 客戶，可以使用 Azure 貨幣點數來支付此項目。

如果仍有問題，您可以：
- 請透過下列電子郵件地址連絡我們：[arainfo@microsoft.com](mailto:arainfo@microsoft.com)。
- [連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 您可以[開啟 Azure 支援案例](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，來取得先決條件步驟 1-5 的相關協助。 針對步驟 6-7，請在 Citrix 管理入口網站內開啟支援票證以連絡 Citrix。 

