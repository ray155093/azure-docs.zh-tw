---
title: "存取面板簡介 | Microsoft Docs"
description: "了解如何使用各種存取面板 (網頁瀏覽器、Android 應用程式、iPhone 和 iPad 應用程式) 存取 SaaS 應用程式。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c5a0f3f958d42d514a95477c1e39f02c78307d4d
ms.lasthandoff: 04/06/2017


---
# <a name="what-is-the-access-panel"></a>何謂存取面板？
存取面板是網頁型入口網站。 它可讓在 Azure Active Directory (Azure AD) 中具有公司或學校帳戶的使用者，檢視和啟動 Azure AD 系統管理員已授權他們存取的雲端式應用程式。 具有 Azure AD 版本的使用者也可以透過存取面板使用自助式群組管理功能。

存取面板與 Azure 入口網站分開，使用者不需要具備 Azure 訂用帳戶也能使用。

![存取面板][1]

存取面板可讓使用者編輯某些設定檔設定，包括以下功能：

* 變更與公司或學校帳戶相關聯的密碼。
* 編輯密碼重設設定。
* 編輯與 Multi-Factor Authentication 相關的連絡人和喜好設定的設定 (系統管理員已要求使用此功能的帳戶)。
* 檢視帳戶詳細資料，例如，使用者識別碼、替代電子郵件及行動電話和辦公室電話號碼
* 檢視和啟動 Azure AD 系統管理員已授權存取的雲端式應用程式。 如需以使用者觀點為主的存取面板詳細資訊，請參閱[使用存取面板](https://msdn.microsoft.com/library/azure/dn756411.aspx)。
* 自我管理群組。 更具體地說，系統管理員可以在 Azure AD 中建立及管理安全性群組，並要求安全性群組成員資格。 如需詳細資訊，請參閱 [Azure AD 中使用者的自助群組管理](active-directory-accessmanagement-self-service-group-management.md)與[管理群組](active-directory-manage-groups.md)。

## <a name="accessing-the-access-panel"></a>存取存取面板
使用者可以使用網頁瀏覽器瀏覽以下 URL 以存取「存取面板」： <br>
**http://myapps.microsoft.com**

如果您已經為您的登入頁面設定自訂商標，在預設情況下，您可以將您組織的網域附加到 URL 結尾以載入此商標： <br>
**http://myapps.microsoft.com/contosobuild.com**

在此情況下，您可以使用已在 Azure 入口網站中，您目錄的 [網域] 索引標籤下設定的任何作用中或已驗證的網域名稱，如以下螢幕擷取畫面所示：

![Wingtip Toys 網域名稱][2]  

務必將此 URL 散佈給要登入與 Azure AD 整合的應用程式的所有使用者。

## <a name="authentication"></a>驗證
若要前往存取面板，必須透過 Azure AD 中的公司或學校帳戶驗證使用者。 Azure AD 可以直接驗證使用者。 或者，如果組織已經使用 Active Directory 同盟服務 (ADFS) 或其他技術設定同盟，則可由 Windows Server Active Directory 驗證使用者。

如果使用者具備 Azure 或 Office 365 的訂用帳戶，而且已經在使用 Azure 入口網站或 Office 365 應用程式，他們會看到應用程式清單，而不需要再次登入。 對於未經過驗證的使用者，系統會提示使用他們在 Azure AD 中的帳戶的使用者名稱和密碼登入。 如果組織已設定同盟，則輸入使用者名稱已經足夠。

驗證之後，使用者就能夠與已由系統管理員整合到目錄的應用程式互動。 若要了解如何整合應用程式與 Azure AD，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="web-browser-requirements"></a>網頁瀏覽器需求
存取面板至少需要有支援 JavaScript 且啟用 CSS 的瀏覽器。 若要讓使用者透過密碼單一登入 (SSO) 登入應用程式，必須在使用者的瀏覽器中安裝存取面板延伸模組。 當使用者選取已設定密碼 SSO 的應用程式時，就會自動下載此存取面板延伸模組。

存取面板延伸模組目前適用於 Internet Explorer 8 和更新版本、Edge、Chrome 與 Firefox 瀏覽器。

## <a name="mobile-app-support"></a>行動應用程式支援
Azure Active Directory 團隊發行「我的應用程式」行動應用程式。 當使用者安裝此應用程式時，可以在 iOS 和 Android 裝置上登入密碼 SSO 應用程式。

> [!NOTE]
> 使用者不需要外掛程式或行動應用程式，就能在幾乎任何裝置的任何網頁瀏覽器上，登入支援與 Azure AD 同盟的應用程式 (包括 Salesforce、Google Apps、Dropbox、Box、Concur、Workday、Office 365 和其他 70 種以上的應用程式)。 同時也不需要在行動裝置上使用「我的應用程式」行動應用程式，就能[體驗其餘存取面板](https://myapps.microsoft.com/)的功能。
>
>

### <a name="my-apps-for-android"></a>My Apps for Android
執行 Android 4.1 版和更新版本的任何 Android 裝置支援 My Apps for Android。 現在已發佈於 [Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.myapps)。

![My Apps for Android 畫面][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>My Apps for iPhone 和 My Apps for iPad
執行 iOS 版本 7 和更新版本的任何 iPhone 或 iPad 均支援 My Apps for iOS。 現在已發佈於 Apple App Store。

![My Apps for iOS 畫面][4]    

## <a name="tips-for-testing-the-user-experience"></a>測試使用者體驗的秘訣
如果您是 Azure 系統管理員，而且已使用目錄中的帳戶登入 Azure 入口網站，則會自動以目前的系統管理員帳戶登入存取面板。 在此情況下，您可以看到已指派給此帳戶的所有應用程式。

若要以「不同」使用者帳戶測試：

1. 按一下 Azure 入口網站或存取面板右上角的使用者功能表，並選取 [登出]。 這會將您登出 Azure AD。
2. 前往[存取面板](http://myapps.microsoft.com)。
3. 在登入頁面上，針對您在目錄中想要測試的帳戶，輸入使用者名稱和密碼。

## <a name="starting-applications"></a>啟動應用程式
存取面板上可能出現許多種應用程式。

### <a name="office-365-applications"></a>Office 365 應用程式
如果組織使用 Office 365 應用程式，且使用者已取得其授權，則 Office 365 應用程式會出現在使用者的存取面板上。

當使用者按一下 Office 365 應用程式的應用程式圖格時，系統會將他們重新導向至該應用程式，並自動登入。

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>已設定同盟 SSO 的 Microsoft 及第三方應用程式
系統管理員可以在 Azure 入口網站的 Active Directory 區段中新增應用程式，並將 SSO 模式設定為 [Azure AD 單一登入]。 只有當系統管理員已明確授權該使用者存取應用程式時，使用者才會看到這些應用程式。

當使用者按一下其中一個應用程式的圖格時，系統會將他們重新導向至該應用程式，並自動登入。

### <a name="password-based-sso-without-identity-provisioning"></a>不含身分識別佈建的密碼 SSO
系統管理員可以在 Azure 入口網站的 Active Directory 區段中新增應用程式，並將 SSO 模式設定為 [密碼單一登入]。 目錄中的所有使用者會看到已設定為此模式的所有應用程式。

使用者第一次按一下其中一個應用程式的圖格時，系統會提示他們在 Internet Explorer 或 Chrome 中安裝密碼 SSO 外掛程式。 安裝之後，使用者可能需要重新啟動網頁瀏覽器。 當使用者返回存取面板，再次按一下應用程式圖格時，系統會提示他們針對應用程式輸入使用者名稱和密碼。 使用者輸入使用者名稱和密碼之後，這些認證會安全地儲存在 Azure AD，並連結至他們在 Azure AD 中的帳戶。

使用者下次按一下應用程式圖格時，將會自動登入應用程式。 使用者不需要再次輸入認證，或再次安裝密碼 SSO 外掛程式。

如果使用者的認證已在目標第三方應用程式中變更，那麼使用者也必須更新他們儲存在 Azure AD 中的認證。 若要更新認證，使用者必須選取應用程式圖格上的圖示，然後選取 [更新認證]，以重新輸入該應用程式的使用者名稱與密碼。

### <a name="password-based-sso-with-identity-provisioning"></a>含身分識別佈建的密碼 SSO
系統管理員可以在 Azure 入口網站的 Active Directory 區段中新增應用程式，並將 SSO 模式設定為 [密碼單一登入]，同時佈建身分識別。

使用者第一次按一下其中一個應用程式的應用程式圖格時，系統會提示他們在 Internet Explorer 或 Chrome 中安裝密碼 SSO 外掛程式。 安裝之後，使用者可能需要重新啟動網頁瀏覽器。 當使用者返回存取面板，再次按一下應用程式圖格時，會自動登入應用程式。

有些應用程式可能會要求使用者在第一次登入時變更密碼。 如果使用者的認證已在目標第三方應用程式中變更，那麼使用者也必須更新他們儲存在 Azure AD 中的認證。 若要更新認證，使用者必須選取應用程式圖格上的圖示，然後選取 [更新認證]，以重新輸入該應用程式的使用者名稱與密碼。

### <a name="application-with-existing-sso-solutions"></a>含現有 SSO 解決方案的應用程式
在為應用程式設定 SSO 方面，Azure 入口網站提供第三個選項：**現有的單一登入**。 這個選項可讓系統管理員建立應用程式的連結，並將連結放在選定使用者的存取面板上。

例如，如果應用程式設定為使用 AD FS 2.0 來驗證使用者，系統管理員可以使用 [現有的單一登入] 選項，在存取面板上建立該應用程式的連結。 當使用者存取連結時，將會透過 AD FS 2.0 或應用程式提供的任何現有 SSO 解決方案來驗證他們。

## <a name="related-articles"></a>相關文章
* [Azure Active Directory 中應用程式管理的文件索引](active-directory-apps-index.md)
* [如何整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [單一登入及使用 Azure Active Directory 管理應用程式存取的簡介](active-directory-appssoaccess-whatis.md)
* [SaaS 應用程式的自動化使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

