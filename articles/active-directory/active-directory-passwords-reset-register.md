---
title: "Azure AD：自助式密碼重設註冊 | Microsoft Docs"
description: "註冊自助式密碼重設的驗證資料"
services: active-directory
keywords: "Active directory 密碼管理, 密碼管理, Azure AD 自助式密碼重設, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 0f10ad4904b10a9554e949e77f0192edcb8f624f
ms.lasthandoff: 04/12/2017


---
# <a name="register-for-self-service-password-reset"></a>註冊自助式密碼重設

作為一般使用者，如果您的系統管理員已啟用，您可以重設密碼或解除鎖定您的帳戶，而不需使用自助式密碼重設 (SSPR) 與人員對話。 在您可以使用這項功能之前，您必須註冊驗證方法，或確認您系統管理員已填入的預先定義驗證方法。

## <a name="register-or-confirm-authentication-data-with-sspr"></a>使用 SSPR 註冊或確認驗證資料

1. 開啟您裝置的 web 瀏覽器並移至[密碼重設註冊頁面](http://aka.ms/ssprsetup)
2. 輸入您的使用者名稱和您系統管理員所提供的密碼
3. 根據您系統管理員已核准的選項，如果您需要重設密碼，您會看到一或多個下列項目可供您設定或確認以便使用
    * 辦公室電話 - 只有您的系統管理員才可以設定這個選項
    * 驗證電話 - 這個選項應該設為您可存取的另一個電話號碼，如可以接收文字或呼叫的行動電話 (您的系統管理員可能會使用您的行動電話號碼填入此項目，如果他們已經有使用該資訊的權限)
    * 驗證電子郵件 - 這個選項應該設定為您可以存取而不需要重設密碼的替代電子郵件地址
    * 安全性問題 - 此選項可提供一份您系統管理員已核准的問題清單以供您回答。 您不可以針對多個問題使用相同的答案。
4. 提供並確認您的系統管理員所需的資訊。 如果可使用多個選項，我們建議您註冊多個方法，以當另一種方法無法使用時提供彈性 (範例︰出差而無法存取您的辦公室電話)

    ![註冊驗證方法，並按一下 [完成]][Register]

5. 當完成步驟 4 時，選擇 [完成]現在您便可以在未來需要時使用自助式密碼重設。

如果您在驗證電話或驗證電子郵件中輸入資料，在全域目錄中會看不見。 您和您的系統管理員是唯一可以看到此資料的人員。 只有您可以查看您安全性問題的答案。

系統管理員在一段時間後可能會要求您確認驗證方法，確定您仍有適當的已註冊方法。

## <a name="next-steps"></a>後續步驟

* [如何使用自助式密碼重設變更您的密碼](active-directory-passwords-update-your-own-password.md)
* [密碼重設註冊頁面](http://aka.ms/ssprsetup)
* [密碼重設入口網站](https://passwordreset.microsoftonline.com/)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "顯示已註冊方法及完成按鈕的密碼重設註冊頁面"

