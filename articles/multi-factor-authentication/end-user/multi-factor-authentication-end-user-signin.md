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
ms.date: 02/02/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c0af680c83a72a9f2a415999141f9913c8b724a4
ms.openlocfilehash: 85e0212ed9d7bde001f9b5cd4776d34ad1f01816


---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 的登入體驗
> [!NOTE]
> 本文的目的逐步解說典型的登入體驗。 如需登入的說明或疑難排解問題，請參閱 [使用 Azure Multi-Factor Authentication 時碰到困難](multi-factor-authentication-end-user-troubleshoot.md)。

## <a name="what-will-your-sign-in-experience-be"></a>您的登入體驗將會如何？
視您登入及使用 Multi-Factor Authentication 的方式而定，您的體驗將會有所不同。 選擇最符合您的情況的一個選項：

| 您要如何登入？ | 
| --- |
| [使用行動或辦公室電話登入](#signing-in-with-mobile-or-office-phone) |
| [從 Microsoft 驗證器應用程式使用通知](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [從 Microsoft 驗證器應用程式使用驗證碼](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [使用替代方法，因為現在無法使用我慣用的方法](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-mobile-or-office-phone"></a>使用行動或辦公室電話登入
下列資訊將說明搭配行動電話或辦公室電話使用 Multi-Factor Authentication 的經驗。

1. 使用您的使用者名稱和密碼登入應用程式或服務，例如 Office 365。  
2. Microsoft 將會打電話給您。  
3. 請接聽電話並按 # 鍵。  
4. 您現在應已登入。  

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>使用 Microsoft 驗證器應用程式登入 
下列資訊將說明使用 Microsoft 驗證器進行雙步驟驗證的體驗。 應用程式有兩種不同的使用方式。 您可以在裝置上接收推送通知，或是開啟應用程式以取得驗證碼。

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>從 Microsoft 驗證器應用程式使用通知登入
1. 使用您的使用者名稱和密碼登入應用程式或服務，例如 Office 365。
2. Microsoft 會將通知傳送到您裝置上的 Microsoft 驗證器應用程式。

  ![Microsoft 傳送通知](./media/multi-factor-authentication-end-user-signin/notify.png)

3. 在電話上開啟通知，然後選取 [驗證] 鍵。 如果貴公司要求 PIN，在此處輸入。
4. 您現在應已登入。

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>使用驗證碼登入 Microsoft 驗證器應用程式

如果您使用 Microsoft 驗證器應用程式取得驗證碼，那麼開啟應用程式時，您會在您的帳戶名稱下面看到一個數字。 這個數字每&30; 秒會變更一次，所以您不會使用相同的數字兩次。 當系統要求您輸入驗證碼時，開啟應用程式並使用當下所顯示的任何數字。 

1. 使用您的使用者名稱和密碼登入應用程式或服務，例如 Office 365。
2. Microsoft 將會提示您輸入驗證碼。

  ![輸入驗證碼](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. 在電話上開啟 Microsoft 驗證器應用程式，並在登入的方塊中輸入驗證碼。
4. 您現在應已登入。

## <a name="signing-in-with-an-alternate-method"></a>使用替代方法登入
有時候您設定做為慣用驗證方法的電話或裝置不在手邊。 這種情況就是為什麼我們建議您為帳戶設定備份方法。 下一節將說明當主要方法無法使用時，如何使用替代方法進行登入。

1. 使用您的使用者名稱和密碼登入應用程式或服務，例如 Office 365。
2. 選取 [使用不同的驗證選項]。 您會看到不同的驗證選項，這取決於您設定了幾種驗證方法。

  ![使用替代方法](./media/multi-factor-authentication-end-user-signin/alt.png)

3. 選擇替代方法並且登入。

## <a name="next-steps"></a>後續步驟

如果您碰到雙步驟驗證登入的問題，可從[使用 Azure Multi-Factor Authentication 時碰到困難](multi-factor-authentication-end-user-troubleshoot.md)獲得詳細資訊。

了解如何[管理雙步驟驗證設定](multi-factor-authentication-end-user-manage-settings.md)。

了解如何[開始使用 Microsoft 驗證器應用程式](microsoft-authenticator-app-how-to.md)，如此一來，因此您可以使用通知登入，而不是經由文字訊息和來電。 


<!--HONumber=Feb17_HO1-->


