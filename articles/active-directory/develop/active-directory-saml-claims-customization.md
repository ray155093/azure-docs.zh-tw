---
title: "在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e89a06de6232afef579c32d51137ddf577917436
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告
目前 Azure Active Directory 支援 Azure AD 應用程式庫中數千個預先整合的應用程式，有 150 個以上的應用程式支援使用 SAML 2.0 通訊協定的單一登入。 當使用者使用 SAML 透過 Azure AD 驗證應用程式時，Azure AD 會將權杖傳送給應用程式 (透過 HTTP 302 重新導向)。 然後，應用程式會驗證並使用權杖將使用者登入，而不會提示輸入使用者名稱和密碼。 這些 SAML 權杖包含關於使用者的資訊片段 (稱為「宣告」)。

在身分識別交談中，「宣告」是身分識別提供者在為使用者發出的權杖中關於使用者說明的資訊。 在 [SAML 權杖](http://en.wikipedia.org/wiki/SAML_2.0)中，此資料通常包含在 SAML 屬性陳述式，使用者的唯一識別碼則通常表示在 SAML Subject 中。

根據預設，Azure AD 會發出 SAML 權杖給您的應用程式，其包含一個 NameIdentifier 宣告，具有使用者在 Azure AD 中的使用者名稱的值。 此值可唯一識別使用者。 SAML 權杖也包含額外的宣告，其包含使用者的電子郵件地址、名字和姓氏。

若要檢視或編輯簽發給應用程式之 SAML 權杖中的宣告，請在 Azure 傳統入口網站中開啟應用程式記錄，並選取應用程式下方的 [屬性] 索引標籤。

![[屬性] 索引標籤][1]

編輯在 SAML 權杖中簽發的宣告有兩個可能原因：
* 應用程式是設計為要求不同的宣告 URI 組或宣告值 
* 您的應用程式已部署為要求 NameIdentifier 宣告必須是 Azure Active Directory 中儲存之 username (也稱為使用者主體名稱) 以外的項目。 

您可以編輯任何預設的宣告值。 選取每當您將滑鼠游標移到 SAML 權杖屬性表格的其中一個資料列上方時，會顯示於右側的鉛筆形狀圖示。 您也可以使用 **X** 圖示移除宣告 (除了 NameIdentifier 以外)，並使用 [新增使用者屬性] 按鈕新增宣告。

## <a name="editing-the-nameidentifier-claim"></a>編輯 NameIdentifier 宣告
要解決使用不同的使用者名稱部署應用程式的問題，請按一下 NameIdentifier 宣告旁的鉛筆圖示。 這個動作會提供包含數個不同選項的對話方塊：

![編輯使用者屬性][2]

在 [屬性值] 功能表中，選取 [user.mail]，將 NameIdentifier 宣告設定為使用者在目錄中的電子郵件地址。 或者，選取 **user.onpremisessamaccountname**設為已從內部部署 Azure AD 同步處理的使用者 SAM 帳戶名稱。

您也可以使用特殊的 ExtractMailPrefix() 函式，從電子郵件地址或使用者主要名稱將網域尾碼移除。 然後，只有使用者名稱的第一個部分會通過 (例如，"joe_smith" 而不是 joe_smith@contoso.com)。

![編輯使用者屬性][3]

## <a name="adding-claims"></a>新增宣告
新增宣告時，您可以指定屬性名稱 (不必根據 SAML 規格嚴格遵循 URI 模式)。 將值設定為儲存在目錄中的任何使用者屬性。

![新增使用者屬性][4]

例如，您必須傳送使用者在其組織內所屬的部門作為宣告 (例如，銷售)。 您可以輸入應用程式預期的任何宣告值，然後選取 **user.department** 作為值。

如果指定的使用者沒有針對選取的屬性儲存的值，則權杖中不會發出該宣告。

**注意：**只有在使用 [Azure AD Connect 工具](../active-directory-aadconnect.md)從內部部署的 Active Directory 同步處理使用者資料時，才支援 **user.onpremisesecurityidentifier** 和 **user.onpremisesamaccountname**。

## <a name="next-steps"></a>後續步驟
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](../active-directory-apps-index.md)
* [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](../active-directory-saas-custom-apps.md)
* [SAML 型單一登入疑難排解](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ../media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ../media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ../media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ../media/active-directory-saml-claims-customization/claimscustomization4.png
