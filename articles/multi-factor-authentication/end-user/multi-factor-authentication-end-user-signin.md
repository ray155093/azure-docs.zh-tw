---
title: "搭配雙步驟驗證的 Azure MFA 登入體驗 | Microsoft Docs"
description: "本頁面將指引您移至何處才能看到 Azure MFA 可用的各種登入方法。"
keywords: "使用者驗證, 登入經驗, 使用行動電話登入, 使用辦公室電話登入"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 63951136d4f7bc2eb81c24ddfd58e696fc31cf37


---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 的登入體驗
> [!NOTE]
> 以下文件顯示一般登入體驗。  如需登入的說明，請參閱 [使用 Azure Multi-Factor Authentication 時碰到困難](multi-factor-authentication-end-user-manage-settings.md)
>
>

## <a name="what-will-your-sign-in-experience-be"></a>您的登入體驗將會如何？
視您登入及使用 Multi-Factor Authentication 的方式而定，您的體驗將會有所不同。  本節將提供登入時預期情況的資訊。  選擇最符合您的情況的一項：

| 您在做什麼？ | 說明 |
|:--- |:--- |
| [使用行動或辦公室電話登入](#signing-in-with-mobile-or-office-phone) |這是使用行動或辦公室電話登入時所能預期的情況。 |
| [使用通知登入 Microsoft 驗證器應用程式](#signing-in-with-the-microsoft-authenticator-app-using-notification) |這是搭配使用 Microsoft 驗證器應用程式與通知時所能預期的情況。 |
| [使用驗證碼登入 Microsoft 驗證器應用程式](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |這是搭配使用 Microsoft 驗證器應用程式與驗證碼時所能預期的情況。 |
| [使用替代方法登入](#signing-in-with-an-alternate-method) |這會顯示想要使用替代方法時所能預期的情況。 |

## <a name="signing-in-with-mobile-or-office-phone"></a>使用行動或辦公室電話登入
下列資訊將說明搭配行動電話或辦公室電話使用 Multi-Factor Authentication 的經驗。

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>透過辦公室或行動電話進行登入
* 使用您的使用者名稱和密碼登入應用程式或服務，例如 Office 365。  
* Microsoft 將會打電話給您。  
* 請接聽電話並按 # 鍵。  
* 您現在應已登入。  

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>使用通知登入 Microsoft 驗證器應用程式
下列資訊將說明當您接獲通知時，將 Multi-Factor Authentication 使用於 Microsoft 驗證器應用程式的經驗。

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>使用傳送的通知登入 Microsoft 驗證器應用程式
* 使用您的使用者名稱和密碼登入應用程式或服務，例如 Office 365。
* Microsoft 會傳送通知。

![Microsoft 傳送通知](./media/multi-factor-authentication-end-user-signin/notify.png)

* 請接聽電話並按驗證鍵。  如果您的公司需要 PIN，系統將會在這裡要求您提供它。

![Verify](./media/multi-factor-authentication-end-user-signin/phone2.png)

![設定](./media/multi-factor-authentication-end-user-first-time/scan3.png)

* 您現在應已登入。

## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>使用驗證碼登入 Microsoft 驗證器應用程式
下列資訊將說明當您搭配驗證碼使用 Multi-Factor Authentication 時，將 Multi-Factor Authentication 使用於 Microsoft 驗證器應用程式的經驗。

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>使用驗證碼登入 Microsoft 驗證器應用程式
* 使用您的使用者名稱和密碼登入應用程式或服務，例如 Office 365。
* Microsoft 將會提示您輸入驗證碼。

![輸入驗證碼](./media/multi-factor-authentication-end-user-signin/verify3.png)

* 在電話上開啟 Microsoft 驗證器應用程式，並在登入的方塊中輸入驗證碼。

![取得驗證碼](./media/multi-factor-authentication-end-user-signin/phone3.png)

* 您現在應已登入。

## <a name="signing-in-with-an-alternate-method"></a>使用替代方法登入
下一節將說明當主要方法無法使用時，如何使用替代方法進行登入。

### <a name="to-sign-in-with-an-alternate-method"></a>使用替代方法進行登入
* 使用您的使用者名稱和密碼登入應用程式或服務，例如 Office 365。
* 選取使用不同的驗證選項。  您將會看見各種不同的選項。 您所看到的個數將以您設定的個數為基礎。

![使用替代方法](./media/multi-factor-authentication-end-user-signin/alt.png)

* 選擇替代方法並且登入。



<!--HONumber=Dec16_HO4-->


