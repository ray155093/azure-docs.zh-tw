---
title: "設定 Azure Active Directory 連線應用程式的裝置型條件式存取原則 | Microsoft Docs"
description: "設定 Azure AD 連線應用程式的裝置型條件式存取原則。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e7e764bdb1e883c28c137292de6a3ef8873e473
ms.openlocfilehash: e4b8622fc6d06be480a2de057070155b12746133
ms.contentlocale: zh-tw
ms.lasthandoff: 12/29/2016

---
# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>設定 Azure Active Directory 連線應用程式的裝置型條件式存取原則
Azure Active Directory (Azure AD) 裝置型條件式存取可協助您保護組織資源，免於遭受下列情況︰

* 來自不明/未受管理裝置的存取嘗試。
* 不符合貴組織安全性原則的裝置。

如需條件式存取的概觀，請參閱 [Azure Active Directory 條件式存取](active-directory-conditional-access.md)。

您可以設定裝置型條件式存取原則來保護下列應用程式︰

* Office 365 SharePoint Online - 用以保護組織的網站和文件
* Office 365 Exchange Online - 用以保護組織的電子郵件
* 連接到 Azure AD 進行驗證的軟體即服務 (SaaS) 應用程式
* 使用 Azure AD 應用程式 Proxy 發佈的內部部署應用程式

若要設定裝置型條件式存取原則，在 Azure 入口網站中，請移至目錄中的特定應用程式。

  ![Azure 入口網站目錄中的應用程式清單](./media/active-directory-conditional-access-policy-connected-applications/01.png "應用程式")

選取應用程式，然後按一下 [設定] 索引標籤以設定條件式存取原則。  

  ![設定應用程式](./media/active-directory-conditional-access-policy-connected-applications/02.png "裝置型存取規則")

若要設定裝置型條件式存取原則，請在 [以裝置為準的存取規則] 區段中，針對 [啟用存取規則] 選取 [開啟]。

裝置型條件式存取原則有三個元件：

* **套用對象**。 要套用原則的使用者範圍。
* **裝置規則**。 裝置必須先符合才能存取應用程式的條件。
* **應用程式強制**。 套用原則的用戶端應用程式 (原生與瀏覽器)。
  
  ![裝置型存取原則的三個元件](./media/active-directory-conditional-access-policy-connected-applications/03.png "裝置型存取規則")

## <a name="select-the-users-the-policy-applies-to"></a>選取要套用原則的使用者
在 [套用對象]  區段中，您可以選取要套用此原則的使用者範圍。

當您建立使用者的存取原則範圍時，會有兩個選項︰

* **所有使用者**。 將原則套用到所有存取應用程式的使用者。
* **群組**。 將原則限制為特定群組成員的使用者。

![將原則套用至所有使用者或某個群組](./media/active-directory-conditional-access-policy-connected-applications/11.png "套用至")

 若要從原則中排除使用者，請選取 [例外] 核取方塊。 當您需要給予特定使用者暫時存取應用程式的權限時，這相當有用。 舉例來說，如果某些使用者有尚未針對條件式存取準備就緒的裝置，請選取此選項。 可能尚未註冊的裝置，或即將不相容的裝置。

## <a name="select-the-conditions-that-devices-must-meet"></a>選取裝置必須符合的條件
使用 [裝置規則]，設定裝置必須符合才能存取應用程式的條件。

您可以設定下列裝置類型的裝置型條件式存取︰

* Windows 10 年度更新版、Windows 8.1 及 Windows 7
* Windows Server 2016、Windows Server 2012 R2、Windows Server 2012 及 Windows Server 2008 R2
* iOS 裝置 (iPad、iPhone)
* Android 裝置

即將推出對 Mac 的支援。

  ![將原則套用至裝置](./media/active-directory-conditional-access-policy-connected-applications/04.png "應用程式")

> [!NOTE]
> 如需有關加入網域與加入 Azure AD 裝置之間差異的資訊，請參閱[在您工作場所中使用 Windows 10 裝置](active-directory-azureadjoin-windows10-devices.md)。
> 
> 

您有兩個裝置規則選項：

* **所有裝置都必須相容**。 所有存取應用程式的裝置平台都必須相容。 系統會拒絕在不支援裝置型條件式存取的平台上執行的裝置存取。
* **所有選取的裝置都必須相容**。 只有特定裝置平台必須相容。 其他平台，或其他可以存取應用程式的平台都有存取權。
  
  ![設定裝置規則的範圍](./media/active-directory-conditional-access-policy-connected-applications/05.png "應用程式")

如果加入 Azure AD 的裝置在目錄中被 Intune 或與 Azure AD 整合的協力廠商行動裝置管理系統標示為「相容」，該裝置即為相容裝置。

如果已加入網域的裝置符合以下條件，即為相容︰

* 它已向 Azure AD 註冊。 許多組織會將已加入網域的裝置視為信任的裝置。
* 在 Azure AD 中被 System Center Configuration Manager 標示為**相容**。
  
  ![符合](./media/active-directory-conditional-access-policy-connected-applications/06.png "裝置規則")的已加入網域裝置

如果 Windows 個人裝置在目錄中被 Intune 或與 Azure AD 整合的協力廠商行動裝置管理系統標示為「相容」，該裝置即為相容裝置。

非 Windows 裝置如果在目錄中被 Intune 標示為「相容」，即為相容裝置。

> [!NOTE]
> 如需有關如何設定 Azure AD 以不同的管理系統中達到裝置相容性的詳細資訊，請參閱 [Azure Active Directory 條件式存取](active-directory-conditional-access.md)。
> 
> 

您可以針對裝置型存取原則選取一或多個裝置平台。 這包括 Android、iOS、Windows Mobile (Windows 8.1 手機和平板電腦) 及 Windows (所有其他 Windows 裝置，包括所有 Windows 10 裝置)。
只會針對選取的平台進行原則評估。 如果嘗試存取的裝置並未執行其中一個選取的平台，則裝置可以存取應用程式 (如果使用者具有存取權)。 未評估任何裝置的原則。

![選取裝置規則適用的平台](./media/active-directory-conditional-access-policy-connected-applications/07.png "裝置規則")

## <a name="set-policy-evaluation-for-a-type-of-application"></a>設定某種應用程式類型的原則評估
在 [應用程式強制] 區段中，設定原則要針對使用者或裝置存取評估的應用程式類型。

對於要包含的應用程式類型，您有兩個選項：

* 瀏覽器與原生應用程式
* 僅限原生應用程式

![選擇瀏覽器或原生應用程式](./media/active-directory-conditional-access-policy-connected-applications/08.png "應用程式")

若要強制執行應用程式的存取原則，請選取 [適用於瀏覽器與原生應用程式]。 然後，您可以包含︰

* 瀏覽器 (例如，Windows 10 中的 Microsoft Edge或 iOS 中的 Safari)。
* 在任何平台上使用 Active Directory 驗證程式庫 (ADAL) 或在 Windows 10 中使用 Web 帳戶管理員 (WAM) API 的應用程式。

> [!NOTE]
> 如需有關瀏覽器支援以及使用者 (存取裝置型、受憑證授權單位保護的應用程式) 的其他考量，請參閱 [Azure Active Directory 條件式存取](active-directory-conditional-access.md)。
> 
> 

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>保護來自 Exchange Active Sync 應用程式的電子郵件存取
在 Office 365 Exchange Online 應用程式中，您可以使用 Exchange ActiveSync 來封鎖對 Exchange ActiveSync 郵件應用程式的電子郵件存取。

![Exchange ActiveSync 相容性選項](./media/active-directory-conditional-access-policy-connected-applications/09.png "應用程式")

![需要符合規範的裝置才可存取電子郵件](./media/active-directory-conditional-access-policy-connected-applications/10.png "應用程式")

## <a name="next-steps"></a>後續步驟
* [Azure Active Directory 條件式存取](active-directory-conditional-access.md)


