---

title: "在 Azure Active Directory 預覽版中開始使用授權 | Microsoft Docs"
description: "描述 Azure Active Directory 授權、如何運作、如何開始著手，以及最佳作法，包括 Office 365、Microsoft Intune 和 Azure Active Directory Premium 與 Basic 版本"
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
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: bbad7fbd1395c3982ecba03ab6ba90d833c889a9
ms.lasthandoff: 03/08/2017


---

# <a name="license-yourself-and-your-users-in-azure-active-directory-preview"></a>在 Azure Active Directory 預覽版中進行自身和使用者的授權

> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-licensing-get-started-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) 是 Microsoft 的「身分識別即服務 (IDaaS)」解決方案與平台。 Azure AD 提供許多功能與技術的版本，從可搭配任何 Microsoft 服務 (例如 Office 365、Dynamics、Microsoft Intune 和 Azure) 使用的 Azure AD 免費版 (Azure AD 在此模式中不會產生任何使用費用)，到許多 Azure AD 付費版本，例如 Enterprise Mobility Suite (EMS)、Azure AD Premium (P1 和 P2)、Azure ADBasic，以及 Azure Multi-Factor Authentication (MFA)。 如同許多 Microsoft 線上服務一般，大多數 Azure AD 付費版本都是透過賦予每位使用者權利來提供，就像在 Office 365、Microsoft Intune 和 Azure AD 中一樣。 在這些情況下，服務購買是由一或多個訂用帳戶來表示，而且每個訂用帳戶會在您的租用戶中包含預先購買的授權數目。 每位使用者權利是透過授權指派、在使用者與產品之間建立連結、啟用使用者的服務元件，以及使用一個預付授權來達成。

[立即試用 Azure AD Premium。](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

如需 Azure AD 服務功能的一般概觀，請參閱 [什麼是 Azure AD](https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/)。 如需詳細資訊，請參閱「服務等級協定」頁面。

> [!NOTE]
> Azure 預付型訂用帳戶則不同：雖然也會在您的目錄中表示，但是這些訂用帳戶可以建立 Azure 資源，而且會將資源對應到您的付款方法。 在這種情況下，訂用帳戶「沒有」相關聯的授權計數。 使用者與訂用帳戶的關聯就是使用者管理訂用帳戶資源的存取權，是透過授與使用者權限來達成，讓他們能夠操作與訂用帳戶對應的 Azure 資源。

## <a name="how-does-azure-ad-licensing-work"></a>Azure AD 授權如何運作？

以授權為基礎的 Azure AD 服務的運作方式，是在您的 Azure AD 目錄/服務租用戶中啟用訂用帳戶。 一旦訂用帳戶啟用，服務功能就可以由目錄/服務系統管理員管理，並由授權的使用者使用。

當您購買或啟用 Enterprise Mobility Suite、Azure AD Premium 或 Azure AD Basic 時，您的目錄會隨著訂用帳戶更新，包括其有效期間和預付授權。 您的訂用帳戶資訊，包括已指派或可使用的授權數目，可在 Azure 入口網站中特定目錄的 Azure Active Directory &gt; [授權] 圖格下取得。 這也是管理您的授權指派最佳的位置。

每個訂用帳戶都包含一或多個服務方案，每個方案都會對應服務類型包含的功能等級。例如，Azure AD、Azure MFA、Microsoft Intune、Exchange Online 或 SharePoint Online。  Azure AD 授權管理「不」需要服務方案等級管理。 這與 Office 365 不同，Office 365 是依賴此進階組態模式來管理內含服務的存取權。 而 Azure AD 則是依賴服務組態來啟用功能和管理個別的權限。

> [!IMPORTANT]
> Azure AD Premium 和 Basic，以及 Enterprise Mobility Suite 訂用帳戶，會受限於其佈建的目錄/租用戶。 訂用帳戶無法分割至不同目錄，或用來賦予權利給其他目錄中的使用者。 在目錄之間可以移動訂用帳戶，但是需要提交支援票證，如果是直接購買的情況，則需要取消並重新購買。
>
> 透過大量授權購買 Azure AD 或 Enterprise Mobility Suite 時，如果協議包含其他 Microsoft Online Services (例如 Office 365)，將會自動啟用訂用帳戶。

### <a name="assigning-licenses"></a>指派授權

取得訂用帳戶之後，您只需要設定付費功能，但是要使用 Azure AD 的付費功能，就必須將授權分配給適當的人員。 一般而言，應存取 Azure AD 付費功能或者透過 Azure AD 付費功能管理的人員，都必須被指派授權。 授權指派是一種使用者與購買的服務 (例如 Azure AD Premium、Basic 或 Enterprise Mobility Suite) 之間的對應。

管理您的目錄中哪些使用者應該擁有授權很簡單。 只要在 Azure 入口網站中指派授權給群組，或是透過入口網站、PowerShell 或 API 直接將授權指派給適當的人員即可。 將授權指派給群組時，則會對所有群組成員指派授權。 如果群組新增或移除使用者，也會對他們指派或移除適當的授權。 群組指派可以利用您可用的任何群組管理功能，而且與以群組為基礎的應用程式指派一致。 使用這個方法，您可以設定規則讓您的目錄中所有使用者都會自動被指派，確保有適當職稱的每個人都獲得授權，或者甚至委派決策權給組織中的其他管理人員。 

如需指派授權給群組的詳細討論，包括進階案例和 Office 365 授權案例，請參閱[這篇文章](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-assignment-azure-portal)。

## <a name="getting-started-with-azure-ad-licensing"></a>開始使用 Azure AD 授權

要開始使用 Azure AD 很簡單。您註冊免費的 Azure 試用版之後，就可以建立您的目錄。 [深入了解以組織身分註冊](https://azure.microsoft.com/en-us/documentation/articles/sign-up-organization/)。 以下可協助您確定您的目錄最適合您可能會使用或打算使用的其他 Microsoft 服務，以及您取得服務的目標。

以下是幾種最佳作法︰

- 如果您已經在使用任何 Microsoft 的組織服務，您就已經有 Azure AD 目錄。 在此情況下，您應該為其他服務繼續使用相同的目錄，以便可以跨服務使用核心身分識別管理 (包括佈建和混合式單一登入 (SSO)。 您的使用者將會擁有單一登入經驗，而且將從跨服務更豐富的功能受益。 因此，如果您決定為員工購買 Azure AD 付費服務，我們建議您使用相同的目錄來進行。

- 如果您打算為不同一組使用者 (夥伴、客戶等等) 使用 Azure AD，或如果您想要評估 Azure AD 服務，且想要隔離實際執行服務進行，或如果您想要為您的服務設定沙箱環境，我們建議您先透過 Azure 傳統入口網站建立新的目錄。 深入了解在 Azure 傳統入口網站中建立新的 Azure AD 目錄。 您的帳戶將會建立新的目錄，成為具有全域系統管理員權限的外部使用者。 當您使用此帳戶登入 Azure 入口網站時，您將會看到這個目錄，並且能夠存取所有的目錄管理工作。

> [!NOTE]
> Azure AD 支援「外部使用者」，這是在 Azure AD 執行個體中，使用 Microsoft 帳戶 (MSA) 或另一個目錄中的 Azure AD 身分識別所建立的使用者帳戶。 雖然我們忙著將此功能擴充到所有 Microsoft 的組織服務，但是目前在某些服務體驗中不支援這些帳戶。例如，Office 365 系統管理入口網站目前不支援這些使用者。 因此，使用 Microsoft 帳戶的外部使用者將無法存取 Office 365 系統管理入口網站，而來自其他 Azure AD 目錄的外部使用者將會被忽略。 在後者的情況中，只有使用者的本機帳戶，最初建立使用者的 Azure AD 或 Office 365 目錄，才能透過這些體驗存取。

如之前所述，Azure AD 有不同的付費版本。 這些版本在其購買可用性有些微的差異：

|  產品       |              EA/VL  | 開啟  | CSP |  MPN 使用權利  | 直接購買 |  試用版 |
|  -------------- | ------------- | ------- | ------ | ----- | ---------------- | ----------------- | ------- |
|  Enterprise Mobility Suite  | X   |    X  |    X  |     X | &nbsp;  | X |
|  Azure AD Premium P2     |    X    |   X   |   X   | &nbsp;  |  X  |   X  
|  Azure AD Premium P1     |    X   |    X    |  X   |  &nbsp; |  X  |  &nbsp; |             
|  Azure AD Basic          |    X   |    X   |  X  |  X  | &nbsp; | &nbsp;  |

### <a name="select-one-or-more-license-trials"></a>選取一或多個授權試用版

您可以在 Azure Active Directory &gt; [快速啟動] 下啟用 Azure AD Premium 或 Enterprise Mobility Suite 試用版訂用帳戶。

![選取授權試用版](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

試用版授權便會出現在 [授權] 刀鋒視窗中。

### <a name="assign-licenses-to-users-and-groups"></a>將授權指派給使用者和群組

一旦訂用帳戶啟用之後，您應該指派一個授權給自己，然後重新整理瀏覽器以確保您可以看到所有的功能。 下一個步驟是將授權指派給需要存取或要包含在 Azure AD 付費功能中的使用者。 如先前在[指派授權](#assigning-licenses)中所述，執行這項操作最好的方式是，識別代表目標對象的群組，並且將授權指派給群組；如此一來，在群組的週期期間新增到群組或從群組移除的使用者，就會分別被指派授權或移除授權。

> [!NOTE]
> 某些 Microsoft 服務無法在所有位置使用。系統管理員必須先指定使用者的 “Usage location” (使用位置) 屬性，才可以將授權指派給使用者。 這可以在 Azure 入口網站中 [使用者] &gt; [設定檔] &gt; [設定] 區段下設定。 使用群組授權指派時，未指定使用位置的任何使用者在指派期間會繼承目錄的位置。

若要指派授權給群組或個別使用者，在 Azure Active Directory &gt; [授權] &gt; [所有產品] 下，選取一或多個產品，然後按一下命令列上的 [指派] 按鈕。

![選取要指派的授權](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

這會帶出新的刀鋒視窗，您可以在其中選擇多個使用者或群組，以及選擇性地停用產品中的服務方案。 最上方的搜尋列可以用來搜尋使用者和群組名稱。

![選取要指派授權的使用者或群組](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

指派授權給群組時，在所有使用者都繼承授權之前可能需要一些時間，取決於群組中的使用者數目。 處理的狀態可以在群組刀鋒視窗的 [授權] 圖格底下找到。

![授權指派狀態](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

在 Azure AD 授權指派期間，可能會發生指派錯誤，但在管理 Azure AD 和 EMS 產品時則相對較少發生。 可能發生的指派錯誤僅限於：
- 指派衝突：使用者先前已被指派一個與最新授權不相容的授權。 在此情況下，若要指派新的授權，必須先移除目前這一個授權。
- 超過可用的授權數目：當指派群組中的使用者數目超過可用的授權數目時，使用者的指派狀態將會反映因缺少授權的指派失敗。

如需可用群組的授權指派詳細資訊，請參閱這篇文章。

### <a name="view-assigned-licenses"></a>檢視已指派的授權

已指派和可使用的授權摘要檢視會顯示在 Azure Active Directory 下&gt; [授權] &gt; [所有產品]。

![檢視授權摘要](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

按一下特定產品時，會顯示可用的已指派使用者和群組的詳細清單。 [授權的使用者] 顯示目前耗用授權的所有使用者，包括授權是直接指派給使用者或從群組繼承。

![檢視授權詳細資料](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

同樣地，[授權的群組] 顯示已指派授權的所有群組。 按一下這些檢視中的使用者或群組，這會開啟 [授權] 刀鋒視窗，其中顯示指派給該物件的所有授權。

### <a name="removing-a-license"></a>移除授權

若要移除授權，移至使用者或群組，然後開啟 [授權] 圖格。 選取授權，並按一下 [移除]。

![移除授權](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

請注意，無法直接移除使用者從群組那裡繼承的授權。 反而是要將使用者從繼承授權的群組之中移除。

### <a name="extending-trials"></a>延長試用期

透過 Office 365 入口網站可以自助延長客戶的試用期。 客戶系統管理員可以瀏覽至 Office 入口網站 (存取權取決於 Office 入口網站的權限)，然後選取 Azure AD Premium 試用版。 按一下**延長試用期**連結就會啟動延長程序。 必須輸入信用卡，但不會被收費。

![在 Azure 入口網站中延長試用期](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>後續步驟

若要深入了解透過群組管理授權的進階案例，請閱讀這篇文章。

現在您可能已經準備好設定和使用某些 Azure AD Premium 功能。

* [自助式密碼重設](active-directory-manage-passwords.md)
* [自助式群組管理](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect 健康情況](active-directory-aadconnect-health.md)
* [群組指派給應用程式](active-directory-manage-groups.md)
* [將授權指派給群組](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [直接購買 Azure AD Premium 授權](http://aka.ms/buyaadp)

