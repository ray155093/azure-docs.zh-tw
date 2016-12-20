---
title: "將自訂網域名稱新增到 Azure Active Directory 預覽版 | Microsoft Docs"
description: "如何在 Azure Active Directory 中新增您公司的網域名稱，以及如何確認網域名稱。"
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: d97e57c6-578a-4929-8fb8-42e858a711c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: caec8549c3ea92d49184d8bc183aa4980f30a44b


---
# <a name="add-a-custom-domain-name-to-azure-active-directory-preview"></a>將自訂網域名稱新增到 Azure Active Directory 預覽版
> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-domains-add-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-add-domain.md)
> 
> 

您已有一或多個您的組織用來經營業務的網域名稱，而且您的使用者使用您公司的網域名稱來登入公司網路。 使用 Azure Active Directory (Azure AD) 預覽版，您可以將您的公司網域名稱新增到 Azure AD。 [預覽版有何功能？](active-directory-preview-explainer.md) 這可讓您在目錄中指派您的使用者熟悉的使用者名稱，例如 ‘alice@contoso.com.’ 此程序很簡單：

1. 在目錄中新增自訂網域名稱
2. 在網域名稱註冊機構中新增網域名稱的 DNS 項目
3. 驗證 Azure AD 中的自訂網域名稱

## <a name="how-do-i-add-a-domain-name"></a>如何新增網域名稱？
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [更多服務]，在文字方塊中輸入 **Azure Active Directory**，然後選取 **Enter**。
   
   ![開啟使用者管理](./media/active-directory-domains-add-azure-portal/user-management.png)
3. 在 [directory-name] 刀鋒視窗上，選取 [網域名稱]。
4. 在 [*directory-name* - 網域名稱] 刀鋒視窗上，選取 [新增] 命令。
   
   ![選取 [新增] 命令](./media/active-directory-domains-add-azure-portal/add-command.png)
5. 在 [網域名稱] 刀鋒視窗上，於方塊中輸入您的自訂網域名稱 (例如 'contoso.com')，然後選取 [新增網域]。 請務必包含 .com、.net 或其他最上層的擴充。
6. 在 [domainname] 刀鋒視窗 (亦即所開啟且標題中含有新網域名稱的刀鋒視窗) 上，取得 Azure AD 將用來驗證貴組織是否擁有自訂網域名稱的 DNS 項目資訊。
   
   ![取得 DNS 項目資訊](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

既然您已新增網域名稱，Azure AD 必須確認您的組織擁有該網域名稱。 您必須先在 DNS 區域檔案中新增該網域名稱的 DNS 項目，Azure AD 才可以進行此確認。 這項工作是在該網域名稱的網域名稱註冊機構網站上進行。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>在網域名稱註冊機構中新增網域的 DNS 項目
利用 Azure AD 使用您自訂網域名稱的下一個步驟，就是更新網域的 DNS 區域檔案。 這可讓 Azure AD 確認您的組織擁有該自訂網域名稱。

1. 登入網域的網域名稱註冊機構。 如果您無法存取以更新 DNS 項目，請要求具有此存取權的人員或小組完成步驟 2 並在完成時通知您。
2. 透過新增 Azure AD 提供給您的 DNS 項目來更新網域的 DNS 區域檔案。 此 DNS 項目可讓 Azure AD 確認您擁有網域。 DNS 項目不會變更任何行為，例如郵件路由或 Web 裝載。

如需新增此 DNS 項目的說明，請參閱 [在常用 DNS 註冊機構新增 DNS 項目的指示](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>使用 Azure AD 確認網域名稱
一旦新增了 DNS 項目，您就可以使用 Azure AD 確認網域名稱。

只有在 DNS 記錄傳播完成之後，才能驗證網域名稱。 通常此傳播只需要幾秒鐘，但有時可能需要一個小時以上。 如果驗證第一次不成功，請稍後再試。

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [瀏覽]，在文字方塊中輸入「使用者管理」，然後選取 **Enter**。
   
   ![開啟使用者管理](./media/active-directory-domains-add-azure-portal/user-management.png)
3. 在 [使用者管理 - 網域名稱]  刀鋒視窗上，選取您想要驗證的未驗證網域名稱。
4. 在 [domainname] 刀鋒視窗 (也就是所開啟且標題中含有新網域名稱的刀鋒視窗) 上，選取 [驗證] 來完成驗證。

現在您可以[指派包含自訂網域名稱的使用者名稱](active-directory-users-create-azure-portal.md)。

## <a name="troubleshooting"></a>疑難排解
如果無法確認自訂網域名稱，請嘗試下列方法。 我們會從最常見的原因來開始逐一介紹到最不常見的原因。

1. **等候一小時**。 DNS 記錄必須在 Azure AD 確認網域之後傳播。 這可能需要一個小時以上。
2. **確定已輸入正確的 DNS 記錄**。 請在該網域的網域名稱註冊機構網站上完成這個步驟。 如果 DNS 項目不在 DNS 區域檔案中，或如果與 Azure AD 提供您的 DNS 項目不完全相符，則 Azure AD 無法確認網域名稱。 如果您無法在網域名稱註冊機構上存取以更新網域的 DNS 記錄，請與組織內具有此存取權的個人或團隊共用 DNS 項目，並請他們新增 DNS 項目。
3. **從 Azure AD 中的另一個目錄刪除網域名稱**。 網域名稱只能在單一目錄中確認。 如果網域名稱先前在另一個目錄中確認過，則必須先在那裡將其刪除後，才可在新的目錄中確認。 若要了解如何刪除網域名稱，請參閱 [管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)。    

## <a name="add-more-custom-domain-names"></a>新增更多的自訂網域名稱
如果您的組織使用多個自訂網域名稱，例如 ‘contoso.com’ 和 ‘contosobank.com’，您最多可以新增 900 個網域名稱。 請使用本文中的相同步驟來新增每個網域名稱。

## <a name="next-steps"></a>後續步驟
[管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)




<!--HONumber=Nov16_HO3-->


