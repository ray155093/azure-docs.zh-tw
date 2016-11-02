<properties
    pageTitle="v2.0 端點概觀 | Microsoft Azure"
    description="建置具備 Microsoft 帳戶和 Azure Active Directory 登入之應用程式的簡介。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="dastrock"/>


# <a name="sign-in-microsoft-account-&-azure-ad-users-in-a-single-app"></a>以單一應用程式登入 Microsoft 帳戶和 Azure AD 使用者

在過去，想要同時支援 Microsoft 帳戶和 Azure Active Directory 的應用程式開發人員必須整合這兩個不同的系統。  我們現在將介紹新的驗證 API 版本，可讓您透過 Azure AD 系統，使用這兩種帳戶類型登入使用者。  這個聚合式驗證系統稱為 **v2.0 端點**。  利用 v2.0 端點，一個簡單的整合便可讓您觸達橫跨個人和工作/學校帳戶的數百萬名使用者對象。

使用 v2.0 端點的應用程式也可以利用這其中一種帳戶，從 [Microsoft Graph](https://graph.microsoft.io) 和 [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) 取用 REST API。

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>開始使用
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

從下列清單選擇您最愛的平台，使用我們的開放原始碼程式庫與架構來建置應用程式。  或者，您可以使用 OAuth 2.0 和 OpenID Connect 通訊協定文件，直接傳送及接收通訊協定訊息，而不使用驗證庫。

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="what's-new"></a>新功能
當您想要了解 v2.0 端點可執行哪些動作以及不可執行哪些動作時，此處提供的概念性資訊非常實用。

- 了解 [您可以使用 v2.0 端點建置的應用程式類型](active-directory-v2-flows.md)。
- 了解使用 v2.0 端點的 [限制和條件約束](active-directory-v2-limitations.md) 。
- 我們最近新增了[系統管理員限制範圍](active-directory-v2-scopes.md)和 [OAuth2 用戶端認證授與](active-directory-v2-protocols-oauth-client-creds.md)的支援。  試試看！

## <a name="reference"></a>參考
下列連結有助於深入探索平台：

- 組建 2016： [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- 取得使用 [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 或 [adal](http://stackoverflow.com/questions/tagged/adal) 標籤之 Stack Overflow 的相關說明。
- [v2.0 通訊協定參考](active-directory-v2-protocols.md)
- [v2.0 權杖參考](active-directory-v2-tokens.md)
- [v2.0 程式庫參考](active-directory-v2-libraries.md)
- [v2.0 端點的範圍和同意](active-directory-v2-scopes.md)
- [Microsoft App 註冊入口網站](https://apps.dev.microsoft.com)
- [Office 365 REST API 參考](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)


<!--HONumber=Oct16_HO2-->


