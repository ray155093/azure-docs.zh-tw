---
title: "變更 Azure RemoteApp 計費 | Microsoft Docs"
description: "了解如何停止支付 Azure RemoteApp。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 8f94da9a-7848-4ddc-b7b7-d9c280ccf4d7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 32fc673eeef01e80c73375bf264206beea8cfbe5
ms.contentlocale: zh-tw
ms.lasthandoff: 07/18/2017

---
# <a name="migrate-from-azure-remoteapp-to-mycloudit"></a>從 Azure RemoteApp 移轉至 MyCloudIT 

**您目前使用 Microsoft Azure RemoteApp 嗎？**：MyCloudIT 建置了一個自動化工具來將您的 Azure RemoteApp (ARA) 集合移轉至 MyCloudIT 管理平台，同時還能在 Microsoft Azure 上繼續執行。

**利用 Azure Resource Manager 入口網站**：已完成移轉至 MyCloudIT 平台，可讓您立即存取 Azure 的新 Azure Resource Manager 入口網站。 此入口網站包含 Microsoft Azure 提供的所有新功能和創新，包括存取虛擬機器大小，以確保建置的部署可支援您的商務需求。

**以平行方式進行測試，以確保正確的解決方案符合您的需求**：MyCloudIT 移轉工具已建置來以平行方式初始移轉程序並進行測試，同時您目前的 ARA 使用者還能繼續使用 ARA。  在您的移轉和測試皆已完成之前，您的使用者將保留於 ARA 中。  移轉工具已建置來處理一般 ARA 集合。  如果您認為您具有獨特且非標準的案例，請與我們連絡 ([sales@conexlink.com](mailto:sales@conexlink.com))，讓我們可以提供量身訂做的計劃來協助您移轉。

**桌面即服務功能**：請注意，MyCloudIT 不只提供您慣用的 RemoteApp 功能，我們也以每月相同的成本提供完整的「桌面即服務」功能，而且沒有任何基本的使用者需求。

## <a name="what-we-will-do-for-you"></a>我們將為您執行哪些動作

MyCloudIT 會利用我們的自動化移轉工具，自動將您的 Azure RemoteApp 範本從訂用帳戶的 Azure 傳統入口網站，移轉至訂用帳戶的 Azure Resource Manager 入口網站。  

> [!NOTE]
> Azure RemoteApp 範本必須保留在與您原始 Azure RemoteApp 部署相同的 Azure 區域中。  如果您需要在移轉期間變更 Azure 區域或 Azure 訂用帳戶，請與我們連絡 ([sales@conexlink.com](mailto:sales@conexlink.com))，以取得其他指引。

如需使用 MyCloudIT 移轉工具之自動化移轉程序的詳細資訊，請參閱下列內容：

1. 移轉工具會掃描您目前的訂用帳戶，以取得所有現有的 ARA 部署。  
2. 一次選取一個要移轉的 ARA 集合。  如果您有多個集合，請多次執行我們的工具。
3. 您可以選擇將使用者設定檔磁碟 (UPD) 複製到新部署，如此就能擷取舊版資料，或手動將您的 UPD 對應到新部署。 如果您選擇複製 UPD，我們將儲存 UPD，並包含一個文字檔，以將 UPD 名稱對應至每位使用者的名稱。  UPD 將會複製到 RDSMGMT 伺服器 `F:\Shares\LegacyUPD` 上的共用，且將透過共用 `\\RDSmgmt\LegacyUPD` 來公開。 
4. 針對您目前的 ARA 部署，您的移轉不需要停機。  但是，如果在複製之後對 UPD 進行了任何變更 (從 ARA)，這些變更將不會反映於儲存在 Azure Resource Manager 入口網站的 UPD 中。 
5. 如果您在傳統 Azure 虛擬網路中具有類似網域控制站和檔案伺服器的其他 VM，我們將在新的 Azure Resource Manager 入口網站中，於您現有的傳統 Azure 虛擬網路以及我們為您建立的新虛擬網路之間建立 VNet 對等。
6. 如果您現有的 ARA 部署是混合式部署，我們的自動化解決方案只會在您現有的傳統 Azure 虛擬網路和新的虛擬網路之間建立 VNet 對等；這表示，您會在現有的傳統虛擬網路中，使用 Windows Server Active Directory 網域控制站進行驗證。 如果我們未針對您的集合建立 VNet 對等，但您需要 VNet 對等，請與我們連絡 ([sales@conexlink.com](mailto:sales@conexlink.com))，我們會很樂意設定 VNet 對等且不需任何額外成本。
7. 我們的自動化解決方案將可確保您的 Azure DNS 設定會以新的虛擬網路設定來更新，以確定您的新部署可在傳統 VNet 中連線到您現有的網域控制站。
8. 我們的自動化解決方案將確保沒有任何 IP 位址衝突，因為我們會建立這個新的虛擬網路，並針對包含現有 Windows Server Active Directory 伺服器的部署建立 VNet 對等。
9. 如果您只使用 Azure AD 進行驗證，MyCloudIT 將建立新的 Windows Server Active Directory 網域，並使用 Azure AD Connect 來同步處理現有 Azure AD 執行個體和 MyCloudIT 所建立之新 Windows Server Active Directory 網域間的使用者。
10. 如果您使用 Windows Server Active Directory 網域來驗證 ARA 使用者，我們的自動化解決方案就會透過 VNet 對等，將您的新 MyCloudIT 部署連線到您現有的 Windows Server Active Directory 網域控制站。
11. 如果您使用 Azure Active Directory Domain Services 進行驗證，我們就能將您移轉，但請與我們連絡，讓我們可為您建立自訂的移轉計劃。  請將電子郵件傳送至 [sales@conexlink.com](mailto:sales@conexlink.com)。 
12. 一旦確認要移轉的集合之後，在我們的自動化解決方案將您的 ARA 集合和使用者設定檔磁碟 (選擇性) 移轉至新 MyCloudIT 驅動的遠端應用程式解決方案時，請坐下來放輕鬆。
13. 當部署完成之後，我們將重新發佈與 ARA 中所發佈相同的應用程式，而在部署後，您將能發佈其他應用程式。

## <a name="post-migration-benefits"></a>移轉後的優點

1. 我們提供管理主控台，可讓您管理遠端應用程式部署的完整生命週期。
2. 您將能從我們的入口網站管理您的虛擬機器。  視需要啟動 / 停止及調整個別的 VM。
3. 管理主控台讓您能夠從我們的管理入口網站建立及管理使用者 / 群組。
4. 管理主控台讓您能夠將使用者與 Office 365 同步以建立相同的登入體驗。
5. 管理主控台不需重複的使用者費用或最低使用者需求，就能讓您建立其他遠端應用程式和桌面集合。 
6. 管理主控台讓您能夠發佈新的「遠端應用程式」應用程式。
7. 如果您只有在特定時間需要解決方案，管理主控台讓您能夠排程遠端應用程式部署的啟動與關閉。
8. 管理主控台讓您能夠自動化 Azure 備份代理程式的安裝與設定，此代理程式可為您的客戶資料提供文件保留歷程記錄。
9. 管理主控台提供存取您部署之效能計量的權限。  這讓您不需安裝其他效能管理工具，就能識別出任何潛在的效能瓶頸。
10. 如果您有多個工作階段主機，便能夠啟用自動調整規模，如此就只有需要執行的工作階段才會執行。
11. MyCloudIT 透過 MyCloudIT 網域名稱提供 RDWeb 閘道伺服器的存取權。  我們也會提供針對使用者的品牌將 URL 重新對應至自訂 URL 的能力。

## <a name="prerequisites-for-migration"></a>移轉的必要條件

1. 您必須具有裝載您目前 Azure RemoteApp 解決方案之 Azure 訂用帳戶的存取權。
2. 您必須在您的訂用帳戶內為我們的入口網站授與權限來移轉您的範本，以及建立 / 修改您新的 MyCloudIT 部署。
3. 請注意，由於 Azure 遠端應用程式中的限制，每個集合只能移轉三次。  如果您需要移轉集合三次以上，可以在 Azure 中提出票證以提高您的匯出計數，或與我們連絡，而我們將協助 ARA 要求來提高匯出計數。

## <a name="mycloudit-billing"></a>MyCloudIT 計費

請參閱 [RemoteApp 解決方案的 MyCloudIT 定價](https://mcitdocuments.blob.core.windows.net/terms/MyCloudIT_Pricing_Overview.pdf) \(英文\) (PDF)，以取得如何預測及管理您整體 Azure 成本的相關資訊。

如果您仍有問題，請與我們連絡 ([sales@conexlink.com](mailto:sales@conexlink.com))，或者觀賞完整的示範影片 [Azure RemoteApp 移轉工具：MyCloudIT](https://www.youtube.com/watch?v=YQ_1F-JeeLM&t=482s) \(英文\)。 


