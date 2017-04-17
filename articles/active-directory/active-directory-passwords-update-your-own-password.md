---
title: "Azure AD：密碼重設 |Microsoft Docs"
description: "使用自助式密碼重設以重新取得您帳戶的存取權"
services: active-directory
keywords: "Active directory 密碼管理, 密碼管理, Azure AD 自助式密碼重設, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9b7735bbe8d3b2ffc78eba8f1489d70a02fe7342
ms.lasthandoff: 04/12/2017


---
# <a name="help-i-forgot-my-password"></a>忘記密碼的說明

步驟 1...別慌張

如果您適用下列案例，我們可以協助

* 您不確定如何存取您的帳戶，且不記得您的密碼
* 未指派密碼且您的系統管理員將您傳送到此處

## <a name="unlock-your-account"></a>解除鎖定您的帳戶

如果您到此處解除鎖定您的帳戶，請遵循下列步驟。 當您在步驟 6 中看到 [選擇新密碼]，您可以解除鎖定或變更您的密碼並解除鎖定。

## <a name="reset-my-password"></a>重設密碼

若要取回您的帳戶，請遵循下列步驟。
1. 從任何工作或學校登入頁面按一下 [無法存取您的帳戶嗎?] 連結，然後按一下 [工作或學校帳戶]，或直接移至[密碼重設頁面](https://passwordreset.microsoftonline.com/)

    ![無法存取您的帳戶嗎？][Login]

2. 輸入您的工作或學校**使用者識別碼**並證明您不是機器人，方法為傳遞 CAPTCHA 挑戰並輸入您看到的文字，然後按 [下一步]
3. 根據系統管理員設定的方式而定，您會看到下列一或多個項目
    * **寄電子郵件到我的備用電子郵件地址** - 將含有 6 位數代碼的電子郵件傳送到您的備用電子郵件或驗證電子郵件 (由您選擇)。
    * **傳簡訊到我的行動電話** - 將含有 6 位數代碼的簡訊傳送到您的行動電話或驗證電話 (由您選擇)。
    * **撥打我的行動電話**撥打您的「行動電話」或「驗證電話」(由您選擇)。 按下 # 鍵來驗證呼叫。
    * **撥打我的辦公室電話**撥打您的辦公室電話。 按下 # 鍵來驗證呼叫。
    * **回答我的安全性問題**顯示您預先登錄要回答的安全性問題。
4. 以挑戰資訊的答案填入必要欄位，然後按 [下一步]

    ![確認驗證資料][Verification]

5. 系統管理員可能會要求額外的驗證步驟，您可能必須以不同的選項重複執行步驟 4
6. 在 [選擇新密碼] 頁面上，輸入符合貴組織需求的新密碼並確認您的選擇，然後按一下 [完成]

    ![變更密碼][Change]

7. 當您看到**您的密碼已經重設**時，可以使用您的新密碼登入。

    ![您的密碼已經重設][Complete]

> [!NOTE]
> 如果您的系統管理員尚未啟用此功能，即會顯示「連絡您的系統管理員」連結，讓您的系統管理員可以透過電子郵件或其本身的 web 入口網站提供協助。
>

使用這個方法來解除鎖定或重設密碼之後，您可能會收到來自像是「Microsoft 在此代表貴組織」帳戶的電子郵件確認此程序已完成。 如果您收到這樣的電子郵件，而不是使用自助式密碼重設以重新取得您帳戶的存取權，請連絡您的系統管理員。

## <a name="change-my-password"></a>變更我的密碼

如果您已經知道您的密碼並需要加以變更，請嘗試下列的步驟

### <a name="change-your-password-from-the-office-365-portal"></a>從 Office 365 入口網站變更您的密碼

1. 按一下您右上方的設定檔，然後按一下 [檢視帳戶]
2. **安全性與隱私權**
3. **密碼**
4. 輸入您的舊密碼，然後設定並確認新密碼
5. **提交**

### <a name="change-your-password-from-the-azure-access-panel"></a>從 Azure 存取面板變更您的密碼

1. 使用您的現有密碼，登入 [Azure 存取入口網站](https://myapps.microsoft.com/)
2. 按一下您右上方的設定檔，然後按一下 [設定檔]
3. **變更密碼**
4. 輸入您的舊密碼，然後設定並確認新密碼
5. **提交**

## <a name="next-steps"></a>後續步驟

* [如何註冊使用自助式密碼重設](active-directory-passwords-reset-register.md)
* [密碼重設註冊頁面](http://aka.ms/ssprsetup)
* [密碼重設入口網站](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "登入頁面 [無法存取您的帳戶嗎?]"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "確認驗證資料"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "變更密碼"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "密碼已經重設"

