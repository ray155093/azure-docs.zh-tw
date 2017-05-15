---
title: "Azure AD：SSPR 註冊 | Microsoft Docs"
description: "註冊 Azure AD 自助式密碼重設的驗證資料"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: bd0175558a94a146a365e8c1a32522f1e54c184e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---
# <a name="register-for-self-service-password-reset"></a>註冊自助式密碼重設

身為一般使用者，您可以重設密碼或解除鎖定您的帳戶，而不需使用自助式密碼重設 (SSPR) 與人員對話。 在您可以使用這項功能之前，您必須註冊驗證方法，或確認您系統管理員已填入的預先定義驗證方法。

## <a name="register-or-confirm-authentication-data-with-sspr"></a>使用 SSPR 註冊或確認驗證資料

1. 開啟您裝置的 web 瀏覽器並移至[密碼重設註冊頁面](http://aka.ms/ssprsetup)
2. 輸入您的使用者名稱和您系統管理員所提供的密碼
3. 視您的 IT 人員如何如何設定而定，下列一或多個選項可供您設定及驗證。 如果您的系統管理員有權限使用資訊，他們可能會為您填入部分資訊。
    * 只有您的系統管理員可以設定辦公室電話
    * [驗證電話] 應設定為另一個您可存取的電話號碼，如可以接收簡訊或電話的行動電話。
    * [驗證電子郵件] 應設定為您可以存取而不需要重設密碼的替代電子郵件地址。
    * [安全性問題] 可提供一份您系統管理員已核准的問題清單以供您回答。 您不可以使用相同的問題或回答一次以上。
4. 提供並確認您的系統管理員所需的資訊。 如果可使用多個選項，我們建議您註冊多個方法，以當另一種方法無法使用時提供彈性 (範例︰出差而無法存取您的辦公室電話)

    ![註冊驗證方法，並按一下 [完成]][Register]

5. 當完成步驟 4 時，選擇 [完成]現在您便可以在未來需要時使用自助式密碼重設。

如果您在驗證電話或驗證電子郵件中輸入資料，在全域目錄中會看不見。 您和您的系統管理員是唯一可以看到此資料的人員。 只有您可以查看您安全性問題的答案。

系統管理員在一段時間後可能會要求您確認驗證方法，確定您仍有適當的已註冊方法。

## <a name="next-steps"></a>後續步驟

* [如何使用自助式密碼重設變更您的密碼](active-directory-passwords-update-your-own-password.md)
* [密碼重設註冊頁面](http://aka.ms/ssprsetup)
* [密碼重設入口網站](https://passwordreset.microsoftonline.com/)
* [無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "顯示已註冊方法及完成按鈕的密碼重設註冊頁面"


