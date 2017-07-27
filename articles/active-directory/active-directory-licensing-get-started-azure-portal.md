---

title: "在 Azure Active Directory 中開始使用授權 | Microsoft Docs"
description: "Azure Active Directory 授權的運作方式、如何以最佳做法來開始進行"
services: active-directory
keywords: "Azure AD 授權"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/30/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b62571e446aa0680c653d0a9d109207af26ad786
ms.contentlocale: zh-tw
ms.lasthandoff: 07/01/2017


---

# <a name="license-yourself-and-your-users-in-azure-active-directory"></a>在 Azure Active Directory 中進行自身和使用者的授權

> [!div class="op_single_selector"]
> * [Azure 入口網站指示](active-directory-licensing-get-started-azure-portal.md)
> * [取得 Azure 傳統入口網站的資訊](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) 是 Microsoft 的「身分識別即服務 (IDaaS)」解決方案與平台。 Microsoft 在不同的服務版本中提供 Azure AD：

* Azure Active Directory Free，任何 Microsoft 服務 (例如 Office 365、Dynamics、Microsoft Intune 或 Azure) 均提供此版本。 在此模式中，Azure AD 不會產生任何使用費用。

* Azure AD 付費版本，例如：
  - Enterprise Mobility + Security 
  - Azure AD Premium (P1 和 P2)
  - Azure AD Basic
  - Azure Multi-Factor Authentication

如同許多 Microsoft 線上服務一般，大多數 Azure AD 付費版本都是透過賦予每位使用者權利來提供，就像在 Office 365、Microsoft Intune 和 Azure AD 中一樣。 在這些案例中，所購買的服務會以一或多個訂用帳戶來表示，而且每個訂用帳戶都會在您的租用戶中包含某些預先購買的授權。 您可透過下列方式來賦予每位使用者權利：

* 指派授權。 
* 讓使用者與產品建立連結。
* 為使用者啟用服務元件。
* 取用其中一個預付授權。

[立即試用 Azure AD Premium。](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

如需 Azure AD 服務功能的一般概觀，請參閱[什麼是 Azure AD？](active-directory-whatis.md)。 如需詳細資訊，請參閱[服務等級協定頁面](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/)。

> [!NOTE]
> Azure 隨用隨付訂用帳戶能夠建立 Azure 資源，然後將這些資源對應到您的付款方式。 這種訂用帳戶沒有相關聯的授權計數。 當您授權使用者操作對應至訂用帳戶的 Azure 資源時，使用者便會與訂用帳戶產生關聯，並可管理訂用帳戶的資源。

## <a name="how-does-azure-active-directory-licensing-work"></a>Azure Active Directory 授權的運作方式為何？

以授權為基礎的 Azure AD 服務可透過在 Azure AD 服務租用戶中啟用訂用帳戶來開始運作。 在訂用帳戶啟用後，服務的功能便會由 Azure AD 系統管理員管理並供授權使用者使用。

### <a name="manage-subscription-information"></a>管理訂用帳戶資訊

當您購買 Enterprise Mobility + Security、Azure AD Premium 或 Azure AD Basic 時，您的租用戶會隨著訂用帳戶更新，包括其有效期間和預付授權。 您可透過 Azure 入口網站取得您的訂用帳戶資訊 (包括已指派或可使用的授權數目)：在 [Azure Active Directory] 底下，開啟特定目錄的 [授權] 圖格。 [授權] 刀鋒視窗也是最適合用來管理授權指派的位置。

每個訂用帳戶都包含一或多個服務方案，例如 Azure AD、Multi-Factor Authentication、Intune、Exchange Online 或 SharePoint Online。  Azure AD 授權的管理「不」需要進行服務方案等級的管理。 Office 365 則不同，因為 Office 365 依賴此進階組態模式來管理內含服務的存取權。 Azure AD 依賴服務內組態來啟用功能和管理個別權限。

> [!IMPORTANT]
> Azure AD Premium、Azure AD Basic 和 Enterprise Mobility + Security 訂用帳戶會受制於其佈建的目錄/租用戶。 訂用帳戶無法分割至不同目錄，或用來賦予權利給其他目錄中的使用者。 您可以在不同目錄之間移動訂用帳戶，但需要提交支援票證，或者，您也可以先取消再重新購買，來直接購買訂用帳戶。
>
> 當您透過大量授權訂用帳戶購買 Azure AD 或 Enterprise Mobility + Security 時，如果合約內包含其他 Microsoft 線上服務 (例如 Office 365)，該服務將會自動啟用。 

### <a name="assign-licenses"></a>指派授權

雖然只要取得訂用帳戶就能設定付費功能，但您仍必須對使用者發放 Azure AD 付費功能的授權。 應存取 Azure AD 付費功能或者透過 Azure AD 付費功能管理的人員，都必須獲得授權。 授權指派是一種使用者與購買的服務 (例如 Azure AD Premium、Basic 或 Enterprise Mobility + Security) 之間的對應。


您可以透過下列方式來管理目錄中應該擁有授權的使用者： 

* 在 [Azure 入口網站](active-directory-licensing-whatis-azure-portal.md)中對群組指派授權。
* 透過入口網站、PowerShell 或 API 直接將授權指派給使用者。 

當您對群組指派授權時，所有群組成員都會獲得授權。 如果在群組中新增或移除使用者，則系統也會指派或移除適當的授權。 群組指派可以利用您可用的任何群組管理功能，而且與以群組為基礎的應用程式指派一致。

您可以使用[以群組為基礎的授權指派](active-directory-licensing-whatis-azure-portal.md)來設定規則，例如：
* 目錄中的所有使用者都自動取得授權
* 具有適當職稱的所有人員都取得授權
* 您可以將決定權委派給組織中的其他管理員 (方法是使用[自助服務群組](active-directory-accessmanagement-self-service-group-management.md))

如需指派授權給群組的詳細討論，包括進階案例和 Office 365 授權案例，請參閱[在 Azure Active Directory 中透過群組成員資格對使用者指派授權](active-directory-licensing-group-assignment-azure-portal.md)。

## <a name="get-started-with-azure-ad-licensing"></a>開始使用 Azure AD 授權

Azure AD 很容易就能開始使用。 在註冊 [Azure 免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/)的過程中，您就可以建立您的目錄。

下列最佳做法會協助確保您的租用戶可配合您可能取用的其他 Microsoft 服務以及您的服務使用目標：

- 如果您已使用 Microsoft 的任何組織服務，您就已經有 Azure AD 租用戶。 其他服務最好也使用相同的租用戶，以便可以跨服務使用核心身分識別管理 (包括佈建和混合式單一登入 (SSO))。 使用單一登入時，您的使用者將可受惠於各項服務的豐富功能。 因此，如果您決定為員工購買 Azure AD 付費服務，我們建議您再次使用相同的租用戶。

- 如果您有下列打算，則建議您在 Azure 入口網站中使用新的租用戶：
  - 對一組不同的使用者 (例如夥伴或客戶) 使用 Azure AD。
  - 在與生產服務隔離的環境中評估 Azure AD 服務。
  - 為服務設置沙箱環境。

  新的目錄將會使用您的帳戶以外部使用者身分來建立，而且此身分具有全域系統管理員權限。 當您使用此帳戶登入 Azure 入口網站時，您將會看到這個租用戶，並且能夠存取所有管理工作。

> [!NOTE]
> Azure AD 支援「來賓使用者」，也就是 Azure AD 租用戶中的使用者帳戶，這些帳戶是透過 Microsoft 帳戶或另一個租用戶中的 Azure AD 身分識別所建立。 Office 365 系統管理入口網站目前不支援這些使用者。 具有 Microsoft 帳戶的來賓使用者完全無法存取 Office 365 系統管理入口網站，而來自其他 Azure AD 租用戶的來賓使用者則會遭到忽略。 若為後者，只有使用者的本機帳戶 (在最初用來建立使用者的 Azure AD 或 Office 365 租用戶中) 可供存取。

### <a name="select-one-or-more-license-trials"></a>選取一或多個授權試用版

您可以在 [Azure Active Directory] &gt; [快速啟動] 下啟用 Azure AD Premium 或 Enterprise Mobility + Security 試用版訂用帳戶。

![選取授權試用版](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

試用版授權可在 [授權] 刀鋒視窗中取得。

### <a name="assign-licenses-to-users-and-groups"></a>將授權指派給使用者和群組

訂用帳戶啟用後，您應該對自己指派授權。 然後重新整理瀏覽器，以確保您會看到所有功能。 下一個步驟是將授權指派給需要存取 Azure AD 功能的使用者。 如[指派授權](#assign-licenses)所述，有個簡單的方法可以指派授權，那就是找出代表所要對象的群組，然後對該群組指派授權。 如此一來，在群組的生命週期中，於群組內新增或移除的使用者將會分別獲得授權或遭到移除授權。

> [!NOTE]
> 並非所有位置都可使用某些 Microsoft 服務。 系統管理員必須先指定使用者的 [使用位置] 屬性，才能將授權指派給使用者。 您可以在 Azure 入口網站的 [使用者] &gt; [設定檔] &gt; [設定] 下設定此屬性。 在使用群組授權指派時，未指定使用位置的使用者皆會繼承目錄的位置。

若要指派授權，請在 [Azure Active Directory] &gt; [授權] &gt; [所有產品] 下，選取一或多個產品，然後選取命令列上的 [指派]。

![選取要指派的授權](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

您可以使用 [使用者和群組] 刀鋒視窗來選擇多個使用者或群組，也可以停用產品中的服務方案。 使用最上方的搜尋方塊來搜尋使用者和群組名稱。

![選取要指派授權的使用者或群組](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

在指派授權給群組時，所有使用者需要一些時間才能繼承授權，時間長短取決於群組中的使用者數目。 您可以在 [群組] 刀鋒視窗的 [授權] 圖格底下查看處理狀態。

![授權指派狀態](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

在 Azure AD 授權指派期間，可能會發生指派錯誤，但在管理 Azure AD 和 Enterprise Mobility + Security 產品時則相對較少發生。 可能發生的指派錯誤僅限於：
- 指派衝突：使用者先前已被指派一個與最新授權不相容的授權。 在此情況下，若要指派新的授權，您必須先移除目前這一個授權。
- 超過可用的授權數目：當指派群組中的使用者數目超過可用的授權數目時，使用者的指派狀態將會反映因缺少授權的指派失敗。

#### <a name="azure-ad-b2b-collaboration-licensing"></a>Azure AD B2B 共同作業授權

B2B 共同作業可讓您邀請來賓使用者加入您的 Azure AD 租用戶，讓他們能夠存取您開放使用的 Azure AD 服務與任何 Azure 資源。  

邀請 B2B 使用者並將他們指派給 Azure AD 中的應用程式並無須付費。 B2B 共同作業使用者可以免費使用最多 10 個應用程式 (每位來賓使用者) 和 3 份基本報告。 如果來賓使用者在夥伴的 Azure AD 租用戶中獲得任何適當的授權，則他們也會在您的租用戶中獲得授權。

雖非必要，但如果您想要讓他們存取 Azure AD 付費功能，那麼這些 B2B 來賓使用者必須獲得適當的 Azure AD 授權。 具有 Azure AD 付費授權的邀請方租用戶，可以將 B2B 共同作業使用者權限指派給另外五位受邀加入租用戶的來賓使用者。 如需這方面的案例和資訊，請參閱 [B2B 共同作業授權指引](active-directory-b2b-licensing.md)。

### <a name="view-assigned-licenses"></a>檢視已指派的授權

已指派授權和可用授權的摘要檢視會顯示在 [Azure Active Directory] &gt; [授權] &gt; [所有產品] 下。

![檢視授權摘要](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

選取特定產品時，會顯示可用的已指派使用者和群組的詳細清單。 [授權的使用者] 清單會顯示目前取用授權的所有使用者，以及授權是直接指派給使用者或是從群組繼承。

![檢視授權詳細資料](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

同樣地，[授權的群組] 清單會顯示已指派授權的所有群組。 選取使用者或群組可開啟 [授權] 刀鋒視窗，其中會顯示已對該物件指派的所有授權。

### <a name="remove-a-license"></a>移除授權

若要移除授權，請移至使用者或群組，然後開啟 [授權] 圖格。 選取授權，並按一下 [移除]。

![移除授權](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

您無法直接移除使用者從群組那裡繼承的授權。 反而是要將使用者從繼承授權的群組之中移除。

### <a name="extend-trials"></a>延長試用期

客戶可以透過 Office 365 入口網站，以自行註冊的方式延長試用期。 客戶的系統管理員可以移至 Office 入口網站 (存取權取決於 Office 入口網站的權限)，然後選取 Azure AD Premium 試用版。 按一下**延長試用期**連結就會啟動延長程序。 您必須輸入信用卡，但不會被收費。

![在 Azure 入口網站中延長試用期](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>後續步驟

若要深入了解透過群組管理授權的進階案例，請閱讀[將授權指派給群組](active-directory-licensing-group-assignment-azure-portal.md)一文。

以下是有關如何設定及使用其他 Azure AD 付費功能的資訊：

* [自助式密碼重設](active-directory-manage-passwords.md)
* [自助式群組管理](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [直接購買 Azure AD Premium 授權](http://aka.ms/buyaadp)

