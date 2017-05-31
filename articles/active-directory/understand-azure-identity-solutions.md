---
title: "了解 Azure 身分識別 | Microsoft Docs"
description: "取得 Microsoft Azure 身分識別解決方案條款、概念和建議的基本了解，讓您為貴組織做出最佳的身分識別控管決策。"
keywords: 
author: jeffgilb
manager: femila
ms.date: 5/12/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 8d550a9c2a6d2f28687aefc3b9db97ca3c68eb82
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="understand-azure-identity-solutions"></a>了解 Azure 身分識別解決方案
Microsoft Azure Active Directory (Azure AD) 是全方位、高可用性的身分識別和存取管理的雲端解決方案，它結合了核心目錄服務、進階身分識別控管及應用程式存取管理。 Azure AD 會對 1,000 個 [Azure AD 應用程式資源庫](https://azure.microsoft.com/marketplace/active-directory/all/)中預先整合的商業和自訂應用程式快速[啟用單一登入 (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) - 包括您每日所使用的，例如 Office 365、Salesforce.com、Box、ServiceNow 和 Workday。

Azure 身分識別是以最簡單的形式、透過單一 Azure AD 目錄所提供，在建立時會自動與 Azure 訂用帳戶產生關聯。 作為 Azure 中的識別服務，Azure AD 接著會針對以雲端為基礎的資源 (例如使用者、應用程式與群組)，為個別租用戶 (組織) 提供所有身分識別管理和存取控制功能，如下圖所示︰

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure 可提供數種方式，運用包含不同複雜性等級的身分識別即服務 (IDaaS)，來符合個別組織的需求。 本文的其餘部分將協助您了解須知的術語和概念，以及可協助您從可用的 Azure 身分識別選項做出最佳選擇的建議。

## <a name="terms-to-know"></a>須知詞彙

您必須初步了解提及 Azure 識別服務時的常用詞彙之後，才能為貴組織做出 Azure 識別服務解決方案的決策。

|須知詞彙| 說明|
|-----|-----|
|Azure 訂閱 |訂用帳戶會用來支付 Azure 雲端服務，且通常會連結到一張信用卡。 您可以擁有數個訂用帳戶，但就很難在訂用帳戶之間共用資源。|
|Azure 租用戶 | Azure AD 租用戶是代表單一組織。 它是組織在註冊 Microsoft 雲端服務訂用帳戶 (例如 Azure、Intune 或 Office 365) 時，所收到和擁有的專屬、受信任 Azure AD 執行個體。 租用戶可以在任一個專用環境 (單一租用戶)，或與其他組織 (多租用戶) 共用環境中取得服務的存取權。|
|Azure AD 目錄 | 每個 Azure 租用戶都具有專用、受信任的 Azure AD 目錄，其中包含租用戶的使用者、群組和應用程式。 它可用來執行身分識別和存取租用戶資源的管理功能。 當您註冊諸如 Azure、Microsoft Intune 或 Office 365 等 Microsoft 雲端服務時，會自動佈建唯一的 Azure AD 目錄來代表貴組織，因此有時候您會看到租用戶、Azure AD 和 Azure AD directory這些詞彙在交換使用。 |
|自訂網域 | 當您第一次註冊 Microsoft 雲端服務訂用帳戶時，您的租用戶 (組織) 會建立 .onmicrosoft.com 網域名稱。 不過，大部分的組織會使用一個或多個網域名稱來執行業務，以及使用者用來存取公司資源。 您可以將自訂網域名稱新增至 Azure AD，讓您的使用者熟悉網域名稱，例如 *alice@contoso.com* 而不是 *alice@contoso.onmicrosoft.com*。 |
|Azure AD 帳戶 | 這些身分識別是使用 Azure AD 或其他 Microsoft 雲端服務 (例如 Office 365) 所建立的。 它們儲存在租用戶的 Azure AD，且組織的任何雲端服務訂用帳戶皆可存取。 |
|Azure 訂用帳戶管理員| 帳戶管理員就是註冊或購買 Azure 訂用帳戶的人員。 他們已獲得授權存取[帳戶中心](https://account.windowsazure.com/Home/Index)並可執行各種管理工作，像是建立訂用帳戶、取消訂用帳戶、變更訂用帳戶的計費方式，或變更服務管理員。 |
|Azure AD 全域系統管理員 | Azure AD 全域系統管理員可完整存取所有的 Azure AD 系統管理功能。 依預設，註冊 Microsoft 雲端服務訂用帳戶的人員會自動成為全域系統管理員。 您可以擁有一個以上的全域系統管理員，但只有全域系統管理員才能將任何[其他的系統管理員角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)指派給使用者。 |
|Microsoft 帳戶 | Microsoft 帳戶 (您為了個人用途所建立) 可提供存取所有消費者導向的 Microsoft 產品和雲端服務，例如 Outlook (Hotmail)、Messenger、OneDrive、MSN、Xbox LIVE 或 Office 365。 會在 Microsoft 所執行的 Microsoft 取用者身分識別帳戶系統中建立這些身分識別並加以儲存。|
|工作或學校帳戶 | 工作或學校帳戶 (系統管理員為了商業/學術用途所發出) 可提供存取所有企業商業層級 Microsoft 雲端服務，例如 Azure、Intune 或 Office 365。|


## <a name="concepts-to-understand"></a>須知概念

現在您知道了基本的身分識別詞彙，必須了解下列核心概念，以協助您做出 Azure 識別服務的明智決定。

|須知概念 |說明|
|-----|-----|
|[Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |每個 Azure 訂用帳戶都具有 Azure AD 目錄的信任關係，可驗證使用者、服務和裝置。 多個訂用帳戶可以信任相同的 Azure AD 目錄，但訂用帳戶一律只會信任單一 Azure AD 目錄。 這個信任關係不同於訂用帳戶與其他 Azure 資源 (網站、資料庫等) 之間的關係，後者比較像是訂用帳戶的子資源。 如果訂用帳戶已過期，則也會停止存取與 Azure AD 之外的訂用帳戶相關聯的資源。 不過，Azure AD 目錄會保留在 Azure 中，因此您可以將其他訂用帳戶與該目錄產生關聯，並繼續管理租用戶資源。|
|[Azure AD 授權的運作方式](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | 當您購買或啟用 Enterprise Mobility Suite、Azure AD Premium 或 Azure AD Basic 時，您的目錄會隨著訂用帳戶更新，包括其有效期間和預付授權。 一旦訂用帳戶啟用後，Azure AD 全域系統管理員可以管理服務功能，且授權的使用者可使用服務功能。 可在 Azure 入口網站中的 [Azure Active Directory] > [授權] 刀鋒視窗中取得您訂用帳戶的資訊 (包括已指派或可用的授權數目)。 這也是管理您的授權指派最佳的位置。|
|[Azure 入口網站中的角色型存取控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|Azure 角色型存取控制 (RBAC) 可協助提供 Azure 資源的更細緻存取權管理。 權限太多可能會使帳戶暴露在攻擊者的威脅下。 權限太少則會讓員工無法有效率地完成工作。 使用 RBAC 時，您可以套用至所有資源群組的三個基本角色︰擁有者、參與者、讀取器作為基礎，為員工提供他們需要的確切權限。 您也可以建立最多 2000 個您自己的[自訂 RBAC 角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)，來符合您特定的需求。 |
|[混合式身分識別](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|您可以使用 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)，將內部部署 Windows Server Active Directory (AD DS) 與 Azure AD 整合，來達成混合式身分識別。 這可讓您為與 Azure AD 整合之 Office 365、Azure 和內部部署應用程式或 SaaS 應用程式的使用者提供通用身分識別。 您可以利用混合式身分識別，有效地將內部部署環境擴充到雲端，以進行身分識別與存取。|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Windows Server AD DS 與 Azure AD 之間的差異
如果您已經熟悉內部部署 Windows Server Active Directory Domain Services (AD DS)，第一次引進了 Windows 2000 Server，那麼您應該了解識別服務的基本概念。 不過，也請務必了解 Azure AD 不只是雲端中的網域控制器；它是在 Azure 中提供身分識別即服務 (IDaaS) 的一個嶄新方式，需要全新的思維模式來充分發揮以雲端作為基礎的功能，並保護貴組織免於遭受最新的威脅攻擊。 多年來，IT 都是使用 AD DS 來保護其安全性周邊，但現代化、無周邊的企業基於為員工、客戶及合作夥伴提供識別身分需求的支援，而需要新的控制項平面。 Azure AD 就是這個身分識別控制平面。

安全性已超越公司防火牆而移至雲端，Azure AD 會透過為使用者提供一個通用身分識別 (在內部部署或雲端)，從而保護公司資源及存取。 這可為您的使用者提供彈性，幾乎在任何裝置上都能安全地存取完成工作所需的應用程式，可順暢地提供以風險作為基礎的資料保護控制，並有機器學習服務功能與深入報告的支援，這些都是 IT 在保護資料安全時所需仰賴的。

### <a name="extend-office-365-management-and-security-capabilities"></a>擴充 Office 365 管理與安全性功能
已經在使用 Office 365 了嗎？ 您可以透過使用 Azure AD 來擴充內建的 Office 365 管理與安全性功能，保護您存取所有資源時的安全，並使您整體人力發揮順暢、安全的產能，從而加速您數位的轉換。 當您使用 Azure AD 功能時，除了 Office 365 所提供的之外，您可以利用針對所有應用程式啟用單一登入的身分識別，將整個應用程式組合加以保護。 您不只能以裝置狀態，還能以使用者、位置、應用程式和風險作為基礎，來擴充條件式存取功能。 您在需要時，Multi-Factor Authentication (MFA) 功能提供更多的保護。 您將取得使用者權限的其他監督權，並提供隨選、及時的管理存取。 您的使用者將更具生產力，且建立更少的技術支援票證，一切都要歸功於 Azure AD 所提供諸如將忘記的密碼進行重設、應用程式存取要求，以及建立和管理群組等自助式功能。

> [!TIP]
> 想要進一步了解如何搭配 Office 365 使用 Azure AD 身分識別管理嗎？ [取得電子書](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html)。

## <a name="microsoft-identity-solutions"></a>Microsoft 身分識別解決方案

Microsoft Azure 提供數種方式可供您管理使用者的身分識別，不論是完全在內部部署、僅在雲端中，或甚至是兩者之間的任何位置。 這些選項包括︰Azure 中的自己動手做 (DIY) AD DS、Azure Active Directory (Azure AD)、混合式身分識別和 Azure AD Domain Services。

對於只需要很小雲端使用量的公司，就可以使用 Azure 中的**自己動手做 (DIY) AD DS**。 此選項所支援的許多 Windows Server AD DS 案例，非常適合在 Azure 上部署作為虛擬機器 (VM)。 例如，您可以建立 Azure VM，作為連線到遠端網路的遠處資料中心內執行的網域控制器。 從該處，VM 就能夠支援遠端使用者的驗證要求，並提高驗證效能。 此選項也非常適合作為昂貴災害復原網站的相對低成本替代產品，方法是在 Azure 上裝載少量的網域控制器和單一虛擬網路。 最後，建議您在 Azure 上部署 SharePoint 等應用程式，如此一來就需要 Windows Server AD DS，但是不相依於內部部署網路或公司 Windows Server Active Directory。 在此情況下，您可以在 Azure 上部署隔離的樹系，以符合 SharePoint 伺服器陣列的要求。 它也支援部署需要連線到內部部署網路與內部部署 Active Directory 的網路應用程式。

**Azure Active Directory (Azure AD)** 獨立是完全以雲端為基礎的身分識別與存取管理即服務 (IDaaS) 解決方案。 Azure AD 提供一組強固的功能，可管理使用者和群組。 對於內部部署與雲端應用程式 (例如 Office 365 等 Microsoft Web 服務，以及許多非 Microsoft 的軟體即服務 (SaaS) 應用程式) 也都可提供保護。 Azure AD 共有三種版本：免費版、基本版及進階版。 Azure AD 能提升組織效率，並可將周邊防火牆之外的安全性擴充至受 Azure 機器學習服務和其他進階安全性功能保護的新控制平面。

許多前瞻思考的 CIO 和企業捨棄了內部部署或以雲端為基礎的身分識別解決方案之間選擇，而是開始著眼於其公司的長遠方向，準備透過**混合式身分識別**解決方案，將他們的內部部署目錄擴充至雲端。 您可以透過混合式身分識別，取得真正的全域身分識別和存取管理解決方案，在現在與未來，都能為需要執行作業的應用程式使用者提供安全且有效率的存取。

> [!TIP]
> 若要深入了解 CIO 如何將 Azure Active Directory 作為其 IT 策略的重心，請下載 [Azure Active Directory 的 CIO 指南](https://aka.ms/AzureADCIOGuide)。

最後，**Azure AD Domain Services** 提供以雲端為基礎的選項，可將 AD DS 運用於極輕量型的 Azure VM 設定控制，以及符合網路應用程式開發與測試之內部部署身分識別需求的方式。 Azure AD Domain Services 並非旨在將內部部署 Azure AD 基礎結構隨即轉移至 Azure AD Domain Services 所管理的 Azure VM。 相反地，應該將受管理網域中的 Azure VM 用來支援開發、測試，以及將需要 AD DS 驗證方法的內部部署應用程式移動至雲端。

## <a name="common-scenarios-and-recommendations"></a>常見案例和建議

關於哪個是最適合的 Azure 身分識別選項，以下是一些常見的身分識別與存取案例和建議。

  |身分識別案例| 建議|
  |-----|-----|
  |我在 Azure 中需要支援少量的虛擬機器，但我的公司仍然大量投資於內部部署 Active Directory (AD DS)。|當您只需要支援極少的虛擬機器，卻大量投資於內部部署 AD DS 時，可透過 [DIY AD DS](https://msdn.microsoft.com/library/azure/jj156090.aspx) 來使用 Azure VM。 |
  |我的企業是在雲端中創立，因此並未投資於內部部署身分識別解決方案。| 對於只有雲端而無內部部署投資的企業，[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 是最佳的選擇。|
  |我的組織已大量投資於內部部署 Windows Server Active Directory，但是我們想要將身分識別擴充到雲端。| 最常用的 Azure 身分識別解決方案是[混合式身分識別](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview)。 如果您已投資於內部部署 AD DS，可以使用 Azure AD Connect，輕鬆地將身分識別擴充至雲端。|
  |我需要極輕量型的 Azure VM 設定和控制，以符合進行應用程式開發與測試時的內部部署身分識別需求。|如果您需要將 AD DS 運用於極輕量型的 Azure VM 設定控制，或是計畫將舊式的目錄感知內部部署應用程式加以開發或移轉至雲端，[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) 會是不錯的選擇。|

## <a name="next-steps"></a>後續步驟

現在您已了解 Azure 身分識別的概念和可供您使用的選項，可以使用下列資源開始實作您所選擇的選項︰

[測試您對於 Azure 身分識別解決方案的了解](https://aka.ms/aad-understand-quiz)

[深入了解 Azure 概念證明環境中的其他資訊](https://aka.ms/aad-poc)

[在生產環境中部署 Azure AD](https://aka.ms/aad-onboard)

