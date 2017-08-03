---
title: "如何在 Azure AD 中管理同盟憑證 | Microsoft Docs"
description: "了解如何自訂同盟憑證的到期日期，以及如何更新即將到期的憑證。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 4b859d631617fc3d375711876cf23e201662e43a
ms.contentlocale: zh-tw
ms.lasthandoff: 07/12/2017

---
# <a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理同盟單一登入的憑證
本文涵蓋各種與 Azure Active Directory 建立憑證，以對 SaaS 應用程式建立同盟單一登入 (SSO) 相關的常見問題和相關資訊。 您可以從 Azure AD 應用程式資源庫，或使用非資源庫的應用程式範本新增這些應用程式。 應用程式必須使用同盟單一登入選項進行設定。

本文只與利用 **SAML 同盟**設定為使用 **Azure AD 單一登入**的應用程式有關，如下列範例所示：

![Azure AD 單一登入](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>為資源庫和非資源庫應用程式自動產生的憑證
當您從資源庫新增應用程式，並設定 SAML 型登入時，Azure AD 會為應用程式產生 3 年的有效憑證。 您可以從 [SAML 簽署憑證] 區段下載此憑證。 對於資源庫應用程式，本區段會視應用程式的需求而定，顯示下載 [憑證] 或 [同盟中繼資料] 的選項。

![Azure AD 單一登入](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="how-to-customize-the-expiration-date-for-your-federation-certificate-and-roll-over-the-new-certificate"></a>如何自訂同盟憑證的到期日期及變換新的憑證
根據預設，憑證會設定為三年之後到期。 您可以遵循以下步驟，為憑證選擇不同的到期日期：包含的螢幕擷取畫面使用 Salesforce 作為範例，但是這些步驟可以套用在任何同盟的 SaaS 應用程式。

1. 在 [Azure Active Directory](https://aad.portal.azure.com) 管理入口網站中，按一下 [企業應用程式] 導覽窗格，然後按一下 [概觀] 頁面中的 [新增應用程式]，如下所示：

   ![開啟 SSO 組態精靈。](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. 搜尋資源庫應用程式，並選取您想要新增的應用程式。 如果找不到所需的應用程式，則使用 [非資源庫應用程式] 選項來新增應用程式。 這項功只適用於 **Azure AD Premium (P1 和 P2)** SKU。

    ![Azure AD 單一登入](./media/active-directory-sso-certs/add_gallery_application.png)

3. 按一下左側導覽窗格中的 [單一登入] 連結，然後將 [單一登入模式] 變更為 [SAML 型登入]。 這會為您的應用程式產生三年的有效憑證。

4. 若要建立新憑證，請按一下 [SAML 簽署憑證] 區段中的 [建立新憑證] 連結。

    ![產生新的憑證](./media/active-directory-sso-certs/create_new_certficate.png)

5. 建立新憑證連結會開啟日曆控制項，且立即設定多達三年的任何日期和時間。 選取的日期和時間會是新憑證的到期日期和時間。 按一下 [儲存] 按鈕以儲存您的憑證。

    ![下載，然後上傳憑證](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. 新憑證現在可供下載。 按一下 [憑證] 連結，以便下載它。 此時，您的憑證不在作用中。 當您想要變換此憑證時，屆時按一下 [產生新憑證] 作用中核取方塊，然後按一下 [儲存]。 從該時間點以後，Azure AD 會開始使用新憑證來簽署回應。

7.  若要了解如何將憑證上傳至特定 SaaS 應用程式，請按一下頁面上的 [檢視應用程式組態教學課程] 連結。

## <a name="how-to-renew-a-certificate-that-will-soon-expire"></a>如何更新即將到期的憑證
下面顯示的更新步驟在理想情況下應該不會讓您的使用者有任何明顯的停機時間。 本節中使用的螢幕擷取畫面採用 Salesforce 做為範例，但這些步驟可以套用到任何同盟的 SaaS 應用程式。

1. 在 **Azure Active Directory** 應用程式的 [單一登入] 頁面上，為您的應用程式產生新憑證。 按一下 [SAML 簽署憑證] 區段中的 [建立新憑證] 連結，即可達成。

    ![產生新的憑證](./media/active-directory-sso-certs/create_new_certficate.png)

2. 選取新憑證的所需到期日期與時間，按一下 [儲存] 按鈕。

3. 下載 [SAML 簽署憑證] 選項中的憑證。 將新的憑證上傳至 SaaS 應用程式的單一登入組態畫面。 若要了解如何為特定 SaaS 應用程式執行這項操作，請按一下頁面上的 [檢視應用程式組態教學課程] 連結。
   
4. 若要在 Azure AD 上啟用新憑證，選取 [產生新憑證] 作用中核取方塊，然後按一下頁面頂端的 [儲存] 按鈕。 這會在 Azure AD 端變換新憑證。 憑證的狀態會從 [新增] 變更為 [作用中]。 從該時間點以後，Azure AD 會開始使用新憑證來簽署回應。 
   
    ![產生新的憑證](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>相關文章
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)
* [SAML 型單一登入疑難排解](active-directory-saml-debugging.md)
