---

title: "如何從 Azure RemoteApp 移轉到 Citrix XenApp Essentials | Microsoft Docs"
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
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: de47edc5ea4dc1b67bd69b74929acf249b0d43e6
ms.contentlocale: zh-tw
ms.lasthandoff: 05/25/2017


---

# <a name="how-to-migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>如何從 Azure RemoteApp 移轉到 Citrix XenApp Essentials

如果 Azure RemoteApp 客戶希望移轉到 Citrix XenApp Essentials，需要注意幾要必要條件。  我們建議您先閱讀 Citrix 的 [Citrix XenApp Essentials 技術部署逐步指南](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf)，以及他們的[線上技術文件庫](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html)。 

幸運的是，您可以重複利用對 Azure RemoteApp 的大部份投資，但仍然需要一些高階的必要條件才能部署 XenApp Essentials。

## <a name="prerequisites"></a>必要條件

1. 建立新的 VNET，或決定您會在 Azure Resource Manager 的哪個 Azure VNET 中部署 Citrix XenApp Essentials。 Azure RemoteApp 使用傳統的 Azure；而 Citrix XenApp Essentials 僅支援 Azure Resource Manager。  
2. 確保您選取的 VNET可透過網路存取您的網域控制器，因為 Citrix 僅支援混合式部署。 如果您使用 Azure RemoteApp 雲端部署，則需確保您的 VNET 可透過網路存取 Active Directory 網域控制器，否則我們建議您使用 Azure Active Directly 網域服務 (AAD-DS)。 
3. 請務必適當地設定 DNS，使其適用於 VNET，讓您在第一次嘗試時就能成功加入網域。 您可以在選取的 VNET 中建立一個虛擬機器，並執行手動網域加入以驗證 DNS 及網域加入如預期般運作。 如此會確保您在第一次就能成功部署 Citrix XenApp Essentials。 
4. 如果需要，且您搭配 Azure RemoteApp 使用的 Azure 傳統 VNET 和您的 Azure Resource Manager VNET 位於相同區域，請建立雙方的 VNET 對等互連；如果並非位於相同區域，請使用 S2S VPN 以便 VNET 能連上網路。 
5. 如果需要，請閱讀[如何將資料移轉至 Azure RemoteApp/如何將資料從 Azure RemoteApp 移轉出來](remoteapp-migrate.md)。 
6. 更新現有的 Azure RemoteApp 映像以包含 Citrix VDA 元件，以便參考 Citrix 文件中的指示。 
7. 前往 Azure Marketplace 並開始部署 Citrix XenApp Essentials。

祝您好運，並感謝您使用 Azure RemoteApp。 

## <a name="other-considerations"></a>其他考量：

- Citrix XenApp Essentials 僅支援混合式部署。 換句話說，它需要的網路才能存取網域控制站，並進一步執行網域加入。 如果您使用 Azure RemoteApp 雲端部署，則需要使用 AAD-DS，或確保您的 VNET 能夠存取 Active Directory 以執行網域加入。 
- 若要了解如何將使用者資料移至 CXE，請閱讀[如何將資料移轉至 Azure RemoteApp/如何將資料從 Azure RemoteApp 移轉出來](remoteapp-migrate.md)。 
- Citrix XenApp Essentials 僅支援 Active Directory 帳戶。 並未支援 Microsoft 帳戶 (@outlook.com、@msn.com、@hotmail.com 等)。 

## <a name="understanding-billing-for-citrix-xenapp-essentials"></a>了解 Citrix XenApp Essentials 計費 

閱讀[常見問題](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699)和 [Citrix 概觀文章](https://www.citrix.com/global-partners/microsoft/remote-app.html)以了解完整的價格詳細資料。 Citrix XenApp Essentials 共有三種計費元件，分別是：

1. 每位使用者每個月收取 Citrix 服務費 $12。 如同所有的 Azure Marketplace 購買方式，費用會以您 Azure 訂用帳戶的付款方式收取。 如果您是 EA 客戶，則無法使用 Azure 貨幣點數。 
2. RDS CAL。 您必須擁有自己的 RDS CAL (即將推出) 或購買與 Citrix XenApp Essentials 搭配的 Remote Access 費用 (RAF)，金額為 $6.25 美元。 如果您是 EA 客戶，可以使用 Azure 貨幣點數支付此筆費用。 如果您希望使用現有的 RDS CAL，請與我們連絡，[arainfo@microsoft.com](mailto:arainfo@microsoft.com，我們將它套用到您的帳單中。 
3. Azure 計算和儲存體。 這是 Azure 儲存體的費用，會計算 VM 耗用量。 在選取 VM 大小及使用者密度時，請注意價格。 如果您是 EA 客戶，可以使用 Azure 貨幣點數支付此筆費用

如果您還有疑問，請與我們連絡
1. 請透過下列電子郵件地址連絡我們：[arainfo@microsoft.com](mailto:arainfo@microsoft.com)。
2. [連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 您可以[立 Azure 支援案例](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，取得上述步驟 #1 - #5 的相關協助。 在 Citrix 管理入口網站中建立支援票證，以連絡 Citrix 了解如何進行步驟 #6-7。 

