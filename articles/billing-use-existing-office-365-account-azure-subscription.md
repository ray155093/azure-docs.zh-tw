---
title: "在 Office 365 和 Azure 訂用帳戶之間共用單一 Azure AD 租用戶 | Microsoft Docs"
description: "了解如何與 Azure 訂用帳戶分享 Office 365 Azure AD 租用戶和其使用者，反之亦然"
services: 
documentationcenter: 
author: JiangChen79
manager: mbaldwin
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: bf9cf92a53bd2db9970c03463cb417310e78224e
ms.openlocfilehash: edf236a3c9a28552b8c6831a8c7f57bab73c41ed


---
# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>搭配使用現有的 Office 365 帳戶與 Azure 訂用帳戶，反之亦然
案例︰您已經擁有 Office 365 訂用帳戶，並且已做好 Azure 訂用帳戶的準備，不過您想要將現有的 Office 365 使用者帳戶用於 Azure 訂用帳戶。 或者，您是 Azure 訂閱者，並且想要為現有 Azure Active Directory 中的使用者取得 Office 365 訂用帳戶。 本文將說明兼得魚與熊掌是多麼容易的事。

> [!NOTE]
> 本文不適用於 Enterprise 合約 (EA) 客戶。 如果您對於本文任何內容有需要進一步的協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
>
>

## <a name="quick-guidance"></a>簡要指引
* 如果您已經擁有 Office 365 訂用帳戶，並且想要註冊 Azure，請使用 [使用組織帳戶登入] 選項。 接著再使用 Office 365 帳戶繼續進行 Azure 註冊程序。 請參閱[本文稍後的詳細步驟](#s1)。
* 如果您已經擁有 Azure 訂用帳戶，並且想要取得 Office 365 訂用帳戶，請使用 Azure 帳戶登入 Office 365。 接著再繼續進行註冊步驟。 完成註冊後，系統會將 Office 365 訂用帳戶新增至 Azure 訂用帳戶隸屬的同一個 Azure Active Directory 執行個體。 如需詳細資訊，請參閱[本文稍後的詳細步驟](#s2)一節。

> [!NOTE]
> 若要取得 Office 365 訂用帳戶，註冊所用的帳戶必須是 Azure Active Directory 租用戶中全域管理員或帳務管理員目錄角色的成員。 [了解如何判斷 Azure Active Directory 中的角色](#how-to-know-your-role-in-your-azure-active-directory)。
>
>

若要了解將訂用帳戶新增至帳戶時的情況，請參閱本文章後續的背景資訊。

## <a name="detailed-steps"></a>詳細步驟
<a id="s1"></a>

### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>案例 1：計劃購買 Azure 的 Office 365 使用者
在此案例中，我們假設 Kelley Wall 這位使用者擁有 Office 365 訂用帳戶，並且計劃訂閱 Azure。 除此之外，還有 Jane 和 Tricia 兩位作用中使用者。 Kelley 的帳戶是 Admin@contoso.onmicrosoft.com。

![Office 365 使用者系統管理中心](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

若要註冊 Azure，請遵循下列步驟︰

1. 前往 [Azure.com](https://azure.microsoft.com/)註冊 Azure。 按一下 [免費試用]。 在下一頁按一下 [立即開始] 。

    ![免費試用 Azure。](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)
2. 按一下 [使用組織帳戶登入] 。

    ![登入 Azure。](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)
3. 登入 Office 365 帳戶。 在本例中，我們應登入 Kelley 的 Office 365 帳戶。

    ![登入 Office 365 帳戶。](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)
4. 填寫資訊並完成註冊程序。

    ![填寫資訊，然後完成註冊。](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![按一下 [開始管理我的服務]。](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

這樣就準備好了。 在 Azure 入口網站中，您應該會看到同一位使用者出現。 若要確認，請依照下列步驟執行：

1. 在上述螢幕擷取畫面中按一下 [開始管理我的服務]。
2. 依序按一下 [瀏覽] 和 [Active Directory]。

    ![依序按一下 [瀏覽] 和 [Active Directory]。](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. 按一下 [使用者] 。

    ![[使用者] 索引標籤](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)
4. 系統會如預期般列出包括 Kelley 在內的所有使用者。

    ![使用者清單](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>

### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>案例 2：計劃購買 Office 365 的 Azure 使用者
在此案例中，Kelley Wall 這名使用者的 admin@contoso.onmicrosoft.com. Kelley 想要訂閱 Office 365，並且想要使用與 Azure 相同的目錄。

> [!NOTE]
> 若要取得 Office 365 訂用帳戶，登入所用的帳戶必須是 Azure Active Directory 租用戶中全域管理員或帳務管理員目錄角色的成員。 [了解如何得知 Azure Active Directory 中的角色](#how-to-know-your-role-in-your-azure-active-directory)。
>
>

![Azure 入口網站訂用帳戶設定](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Azure 入口網站 Active Directory 使用者](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

若要訂閱 Office 365，請遵循下列步驟︰

1. 移至 [Office 365 產品頁面](https://products.office.com/business)，然後選取合適的計劃。
2. 選取計劃之後，以下頁面將會出現。 請勿填寫表單。 按一下位於頁面右上角的 [登入]。

    ![Office 365 試用頁面](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)
3. 登入您的帳戶認證。 在本範例中，我們應登入 Kelley 的帳戶。

    ![Office 365 登入](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)
4. 按一下 [立即試用] 。

    ![確認您的 Office 365 訂單。](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)
5. 在訂單收據頁面上，按一下 [繼續] 。

    ![Office 365 訂單收據](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

這樣就準備好了。 在 Office 365 系統管理中心，您應該會看到來自 Contoso 目錄的相同使用者顯示為作用中使用者。 若要確認，請依照下列步驟執行：

1. 開啟 Office 365 系統管理中心。
2. 展開 [使用者]，然後按一下 [作用中使用者]。

    ![Office 365 系統管理中心使用者](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>如何得知您在 Azure Active Directory 中的角色
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 依序按一下 [瀏覽] 和 [Active Directory]。

    ![Azure 入口網站中的 Active Directory](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. 按一下 [使用者] 。

    ![Azure 入口網站預設 Active Directory 使用者](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)
4. 按一下使用者。 在此範例中，使用者是 Kelley Wall。

    請注意 [組織角色] 欄位。

    ![Azure 入口網站使用者身分識別](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>有關 Azure 和 Office 365 訂用帳戶的背景資訊
Office 365 和 Azure 均使用 Azure Active Directory 服務來管理使用者和訂用帳戶。 請將 Azure 目錄當做為使用者和訂用帳戶分組的容器。 若要將相同的使用者帳戶用於 Azure 和 Office 365 訂用帳戶，您需要確定訂用帳戶是建立在相同的目錄中。 請記住下列幾點：

* 訂用帳戶乃建立在目錄之下，而非目錄建立在訂用帳戶之下。
* 使用者隸屬於目錄，而不是目錄歸使用者所有。
* 訂用帳戶會落在建立訂用帳戶之使用者的目錄中。 如此一來，當您使用 Azure 訂用帳戶的帳戶來建立 Office 365 訂用帳戶時，Office 365 訂用帳戶會與同一個帳戶連結。

![背景資訊](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

如需詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](active-directory/active-directory-how-subscriptions-associated-directory.md)。

> [!NOTE]
> Azure 訂用帳戶是由目錄中的個別使用者所擁有。
>
> [!NOTE]
> Office 365 訂用帳戶是由目錄本身所擁有。 如果目錄中的使用者擁有必要權限，他們就可以操作這些訂用帳戶。
>
>

## <a name="next-steps"></a>後續步驟
您在過去分開取得 Azure 和 Office 365 訂用帳戶，而且想要能夠從 Azure 訂用帳戶存取 Office 365 租用戶，請參閱[將 Office 365 租用戶與 Azure 訂用帳戶產生關聯](billing-add-office-365-tenant-to-azure-subscription.md)。

> [!NOTE]
> 如果您仍有問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
>
>



<!--HONumber=Nov16_HO3-->


