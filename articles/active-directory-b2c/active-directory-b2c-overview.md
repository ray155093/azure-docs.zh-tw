---
title: "Azure Active Directory B2C︰概觀 | Microsoft Docs"
description: "使用 Azure Active Directory B2C 開發取用者導向應用程式"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: f37fa3a212a0dca46c76b3928cf78756722f0340
ms.openlocfilehash: 2c6765e28f40210730823a9af0b6fb80a5ddc4d1


---
# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active Directory B2C：在您的應用程式中註冊與登入取用者
Azure Active Directory B2C 是適用於取用者導向 Web 與行動應用程式的全方位雲端身分識別管理解決方案。 其為高可用性的全域服務，可處理數億萬規模的消費者身分識別。 Azure Active Directory B2C 建置於企業級安全平台，確保您的應用程式、商務和取用者受到安全防護。

在過去，應用程式開發人員若想要註冊並讓取用者登入其應用程式，他們會編寫自己的程式碼。 而且他們會使用內部部署資料庫或系統來儲存使用者名稱和密碼。 Azure Active Directory B2C 為開發人員提供更理想的做法，透過安全且以標準為基礎的平台以及豐富的可延伸原則組合，協助將取用者身分識別管理整合至應用程式。 當您使用 Azure Active Directory B2C 時，您的取用者可以使用現有的社交帳戶 (Facebook、Google、Amazon、LinkedIn) 註冊應用程式，或是建立新的認證 (電子郵件地址與密碼，或使用者名稱與密碼)；後者稱為「本機帳戶」。

## <a name="get-started"></a>開始使用
若要建置可接受取用者註冊與登入的應用程式，您必須先使用 Azure Active Directory B2C 租用戶註冊該應用程式。 使用 [建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)中概述的步驟來建立您自己的租用戶。

您可透過以下方式針對 Azure Active Directory B2C 服務撰寫應用程式：選擇直接傳送通訊協定訊息、使用 [OAuth 2.0 或 Open ID Connect](active-directory-b2c-reference-protocols.md)，或是使用我們的程式庫為您執行工作。 在下表中選擇您喜愛的平台以開始使用。

[!INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>新功能
請不時返回此處查看，瞭解關於 Azure Active Directory B2C 未來變更的相關資訊。 我們也會使用 @AzureAD 發佈任何更新的相關推文。

* 瞭解關於 [可延伸原則架構](active-directory-b2c-reference-policies.md) ，以及您在應用程式中可建立和使用的原則類型資訊。
* 將我們的 [服務部落格](https://blogs.msdn.microsoft.com/azureadb2c/) 設為標籤，以取得次要服務問題、更新、狀態和補救措施的通知。 也請持續監視 [Azure 狀態儀表板](https://azure.microsoft.com/status/) 。
* 目前 [服務限制和條件約束](active-directory-b2c-limitations.md)。
* 最後是使用 Azure AD B2C 和 ASP.NET Core 的[程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c)。

## <a name="how-to-articles"></a>操作說明文章
瞭解如何使用特定 Azure Active Directory B2C 功能：

* 設定您要在取用者導向應用程式中使用的 [Facebook](active-directory-b2c-setup-fb-app.md)、[Google+](active-directory-b2c-setup-goog-app.md)、[Microsoft 帳戶](active-directory-b2c-setup-msa-app.md)、[Amazon](active-directory-b2c-setup-amzn-app.md) 和 [LinkedIn](active-directory-b2c-setup-li-app.md)。
* [使用自訂屬性來收集取用者相關資訊](active-directory-b2c-reference-custom-attr.md)。
* [在取用者導向應用程式中啟用 Azure Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)。
* [設定取用者的自助式密碼重設](active-directory-b2c-reference-sspr.md)。
* [自訂其提供之註冊、登入和其他取用者導向頁面的外觀](active-directory-b2c-reference-ui-customization.md) 。
* [使用 Azure Active Directory 圖形 API 來以程式設計方式建立、讀取、更新和刪除取用者](active-directory-b2c-devquickstarts-graph-dotnet.md) 。

## <a name="next-steps"></a>後續步驟
以下連結有助於深入探索服務：

* 請參閱 [Azure Active Directory B2C 價格資訊](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。
* 檢閱 Azure Active Directory B2C 的[程式碼範例](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c)。 
* 使用 [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 或 [adal](http://stackoverflow.com/questions/tagged/adal) 標記，取得 Stack Overflow 的相關說明。
* 透過 [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/)告訴我們您的想法 -- 我們冀望瞭解您的看法！ 請在您的文章標題中使用 "AzureADB2C:" 字詞，以方便我們尋找。
* 檢閱 [Azure AD B2C 通訊協定參考](active-directory-b2c-reference-protocols.md)。
* 檢閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)。
* 閱讀 [Azure Active Directory B2C 常見問題集](active-directory-b2c-faqs.md)。
* [提出 Azure Active Directory B2C 的支援要求](active-directory-b2c-support.md)。

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新
我們鼓勵您造訪 [此頁面](https://technet.microsoft.com/security/dd252948) 並訂閱資訊安全摘要報告警示，以在安全性事件發生時收到通知。




<!--HONumber=Feb17_HO1-->


