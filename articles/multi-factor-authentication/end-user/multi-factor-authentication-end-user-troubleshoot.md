---
title: "針對雙步驟驗證進行疑難排解 | Microsoft Docs"
description: "本文件將提供如果使用者使用 Azure Multi-Factor Authentication 遇到問題時，該怎麼辦的資訊。"
services: multi-factor-authentication
keywords: "多重要素驗證用戶端, 驗證的問題, 相互關聯識別碼"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 10b63a859c67b965734e32ec979b26a1ea6cc516
ms.contentlocale: zh-tw
ms.lasthandoff: 06/22/2017

---
# <a name="having-trouble-with-two-step-verification"></a>使用雙步驟驗證遇到困難
本文將討論一些您可能會遇到的雙步驟驗證問題。 如果這裡沒有您遇到的問題，請在註解區段中提供詳細的意見反應，以進行改進。

## <a name="i-lost-my-phone-or-it-was-stolen"></a>我的手機遺失或遭竊
有兩種方式可以取回您的帳戶。 第一種是使用備用驗證電話號碼登入 (如果已設定)。 第二種是要求您的系統管理員清除您的設定。

如果您的手機遺失或遭竊，也建議讓您的系統管理員重設您的應用程式密碼，並清除任何已記住的裝置。 如果您的管理員不確定如何完成這項工作，請將他們指向這篇文章︰[管理使用者和裝置](../multi-factor-authentication-manage-users-and-devices.md)。

### <a name="use-an-alternate-phone-number"></a>使用備用電話號碼
如果您已設定多個驗證選項 (包含不同裝置上的次要電話號碼或驗證器應用程式)，則可以使用其中一個選項來登入。

若要使用備用電話號碼登入，請遵循下列步驟︰

1. 像平常一樣登入。
2. 系統提示進一步驗證您的帳戶時，請選擇 [使用不同的驗證選項]。
   
    ![不同的驗證方式](./media/multi-factor-authentication-end-user-manage/differentverification.png)
3. 選取您具有存取權的電話號碼。
   
    ![備用手機](./media/multi-factor-authentication-end-user-manage/altphone2.png)
4. 取回您的帳戶之後，請[管理您的設定](multi-factor-authentication-end-user-manage-settings.md)以變更您的驗證電話號碼。

> [!IMPORTANT]
> 請務必設定次要驗證電話號碼。 如果您的主要電話號碼和行動應用程式位於相同的電話上，則您的電話遺失或遭竊時需要第三個選項。   

### <a name="clear-your-settings"></a>清除您的設定
如果您尚未設定次要驗證電話號碼，則需要連絡系統管理員來尋求協助。 請他們清除您的帳戶，因此，在下次登入時，系統會提示您重新[設定帳戶](multi-factor-authentication-end-user-first-time.md)。

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>我的手機沒收到簡訊或來電
有幾個原因您可能會嘗試登入，而不是收到簡訊或來電。 如果您的手機過去成功收到過簡訊或來電，則這可能是電話提供者問題，而非帳戶問題。 請確定您的手機收訊良好，而且，如果您嘗試接收簡訊，請確定手機和服務計劃支援簡訊。

如果您已經等待幾分鐘的時間還沒收到文字或通話，則進入您帳戶的最快速方法是嘗試不同的選項。

1. 在等候您驗證的頁面上，選取 [使用不同的驗證選項]。
   
    ![不同的驗證方式](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. 選取您想要使用的電話號碼或傳遞方法。
   
    如果收到多個驗證碼，請使用最新的驗證碼。

如果您未設定其他方法，請連絡系統管理員並要求他們清除您的設定。 下次登入時，系統會提示您再次[設定多重要素驗證](multi-factor-authentication-end-user-first-time.md)。

如果您經常因手機收訊不良而延遲，則建議在智慧型手機上使用 [Microsoft Authenticator 應用程式](microsoft-authenticator-app-how-to.md)。 應用程式可以產生您用來登入的隨機安全驗證碼，而且這些代碼不需要任何手機訊號或網際網路連接。

## <a name="app-passwords-are-not-working"></a>應用程式密碼無效
首先，請確定您輸入的是正確的應用程式密碼。 如果仍無法登入，請嘗試登入並[建立應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)。  如果還是無法登入，請要求系統管理員[刪除您現有的應用程式密碼](../multi-factor-authentication-manage-users-and-devices.md)，然後建立新的密碼。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到我問題的解答。
如果您已嘗試這些疑難排解步驟，但仍發生問題，請連絡系統管理員或設定多因素驗證的人員。 他們應該可以協助您。

您也可以將問題張貼在 [Azure AD 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) 或[連絡支援人員](https://support.microsoft.com/contactus)，我們將會儘快回應您的問題。

如果您連絡支援人員，請包含下列資訊：

* **使用者識別碼** – 您嘗試用來登入的電子郵件地址為何？
* **錯誤的一般描述** – 您實際看到的錯誤訊息。  如果沒有任何錯誤訊息，請詳細說明您所注意到的未預期行為。
* **頁面** – 您看到錯誤時的所在頁面 (包含 URL)。
* **錯誤碼** - 您接收到的特定錯誤碼。
* **工作階段識別碼** - 您接收到的特定工作階段識別碼。
* **相互關聯識別碼** – 當使用者看見此錯誤時所產生的相互關連識別碼。
* **時間戳記** – 您看到錯誤時的精確日期和時間 (包含時區)。

在登入頁面上，可以找到大部分的這項資訊。 未及時驗證登入時，請選取 [檢視詳細資料]。

![登入錯誤詳細資料](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

包含這項資訊有助於我們盡快為您解決問題。

## <a name="related-topics"></a>相關主題
* [管理您的雙步驟驗證設定](multi-factor-authentication-end-user-manage-settings.md)  
* [Microsoft Authenticator 應用程式常見問題集](microsoft-authenticator-app-faq.md)


