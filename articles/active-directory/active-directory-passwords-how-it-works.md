---
title: "運作方式：Azure AD 密碼管理 | Microsoft Docs"
description: "了解 Azure AD 密碼管理的不同元件，包括使用者註冊、重設及變更其密碼的位置，以及系統管理員設定、報告及啟用內部部署 Active Directory 密碼管理的位置。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: b9f7c86415a2606cb969427a362b4d2feef183fb
ms.lasthandoff: 04/13/2017


---
# <a name="how-password-management-works-in-azure-active-directory"></a>Azure Active Directory 中的密碼管理運作方式
> [!IMPORTANT]
> **您來到此處是因為有登入問題嗎？** 若是如此， [以下是如何變更和重設密碼的說明](active-directory-passwords-update-your-own-password.md#reset-my-password)。

Azure Active Directory (Azure AD) 中的密碼管理由下列邏輯元件組成：

* **密碼管理組態入口網站**：您可以在 [Azure 入口網站](https://manage.windowsazure.com)中，瀏覽至租用戶目錄的 [設定] 索引標籤，以控制如何在租用戶中管理密碼的不同面向。
* **使用者註冊入口網站**：使用者可以透過此 Web 入口網站自行註冊密碼重設。
* **使用者密碼重設入口網站**：使用者可以根據系統管理員控制的密碼重設原則，使用各種查問來重設自己的密碼
* **使用者密碼變更入口網站**：使用者可以透過此 Web 入口網站，輸入舊密碼並選取新的密碼，隨時變更自己的密碼。
* **密碼管理報告**：您可以在 [Azure 入口網站](https://manage.windowsazure.com)中，瀏覽至租用戶目錄的 [報告] 索引標籤中的 [活動報告] 區段，以檢視和分析租用戶中的密碼重設和註冊活動。
* **Azure AD Connect 的密碼回寫元件**：安裝 Azure AD Connect 以啟用從雲端管理同盟或密碼同步使用者的密碼，您可以選擇性地啟用密碼回寫功能。

## <a name="password-management-configuration-portal"></a>密碼管理組態入口網站
您可以在 [Azure 入口網站](https://manage.windowsazure.com)中，瀏覽至特定目錄的 [設定] 索引標籤中的 [使用者密碼重設原則] 區段，以設定該目錄的密碼管理原則。 您可以從此組態頁面控制如何在您的組織中管理密碼的許多層面，包括：

* 啟用和停用目錄中所有使用者的密碼重設。
* 設定使用者必須通過才能重設其密碼的查問數目 (一個或兩個)。
* 從下列選項中，設定您想要對組織中的使用者啟用的特定查問類型：
  * 行動電話 (透過文字或語音通話的驗證碼)
  * 辦公室電話 (語音通話)
  * 備用電子郵件 (透過電子郵件的驗證碼)
  * 安全性問題 (以知識為基礎的驗證)
* 設定使用者必須註冊才能使用安全性問題驗證方法的問題數目 (只有在已啟用安全性問題時才會看見)。
* 設定使用者在重設期間必須提供才能使用安全性問題驗證方法的問題數目 (只有在已啟用安全性問題時才會看見)。
* 使用在註冊密碼重設 (只有在已啟用安全性問題時才會看見) 時，使用者可以選擇的預先定義、當地語系化等安全性問題。
* 定義在註冊密碼重設 (只有在已啟用安全性問題時才會看見) 時，使用者可以選擇的自訂安全性問題。
* 當使用者前往應用程式[存取面板](http://myapps.microsoft.com)時，要求使用者註冊密碼重設。
* 要求使用者在經過可設定的天數之後，重新確認先前已註冊的資料 (只有在已啟用強制執行註冊時才會看見)。
* 提供自訂技術支援中心電子郵件或 URL，在使用者遇到重設密碼的問題時向其顯示。
* 啟用或停用密碼回寫功能 (當使用 Azure AD Connect 部署密碼回寫時)。
* 檢視密碼回寫代理程式的狀態 (當使用 Azure AD Connect 部署密碼回寫時)。
* 當使用者已重設自己的密碼時，對使用者啟用電子郵件通知 (位於 [Azure 入口網站](https://manage.windowsazure.com)的 [通知] 區段)。
* 當其他系統管理員重設自己的密碼時，對系統管理員啟用電子郵件通知 (位於 [Azure 入口網站](https://manage.windowsazure.com)的 [通知]區段)。
* 使用租用戶商標自訂功能，在使用者密碼重設入口網站和密碼重設電子郵件中，展示您組織的標誌和名稱做為商標 (位於 [Azure 入口網站](https://manage.windowsazure.com)的 [目錄屬性] 區段)。

若要深入了解在您的組織中設定密碼管理，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

## <a name="user-registration-portal"></a>使用者註冊入口網站
您必須以正確的驗證資料更新使用者的雲端使用者帳戶，確保他們可以通過您所定義的相應密碼重設查問次數，然後才能使用密碼重設。 您也可以使用 Azure 或 Office Web 入口網站、DirSync/Azure AD Connect 或 Windows PowerShell，代表使用者定義此驗證資訊。

不過，如果您想讓使用者註冊自己的資料，我們也提供網頁讓使用者前往新增這項資訊。 此頁面可讓使用者根據其組織中啟用的密碼重設原則，指定驗證資訊。 此資料經過驗證之後會儲存在雲端使用者帳戶中，供日後復原帳戶時使用。

以下是註冊入口網站的外觀：

  ![][001]

如需詳細資訊，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)和[最佳作法：Azure AD 密碼管理](active-directory-passwords-best-practices.md)。

## <a name="user-password-reset-portal"></a>使用者密碼重設入口網站
當您啟用自助式密碼重設、設定組織的自助式密碼重設原則，並確定您的使用者在目錄中有適當的連絡資料之後，組織中的使用者就能從使用公司或學校帳戶登入的任何網頁，自動重設自己的密碼 (例如 [portal.microsoftonline.com](https://portal.microsoftonline.com))。 在這類頁面中，使用者會看到「**無法存取您的帳戶嗎？**」連結。

  ![][002]

按一下此連結會開啟自助式密碼重設入口網站。

  ![][003]

若要深入了解使用者如何重設自己的密碼，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

## <a name="user-password-change-portal"></a>使用者密碼變更入口網站
如果使用者想要變更自己的密碼，他們隨時可以使用密碼變更入口網站這樣做。 使用者可以透過存取面板設定檔頁面，或按一下 Office 365 應用程式中的 [變更密碼] 連結，前往密碼變更入口網站。 當使用者的密碼過期時，登入時會自動要求他們變更密碼。

  ![][004]

在這兩種情況下，如果已啟用密碼回寫，且使用者為同盟或密碼已同步處理，則這些變更的密碼會回寫至您的內部部署 Active Directory。

以下是密碼變更入口網站的外觀：

  ![][005]

若要深入了解使用者如何變更自己的內部部署 Active Directory 密碼，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

## <a name="password-management-reports"></a>密碼管理報告
當您瀏覽至 [報告] 索引標籤，在 [活動記錄] 區段下查看時，您會看到兩個密碼管理報告：[密碼重設活動] 和 [密碼重設註冊活動]。 您可以使用這兩份報告，查看您組織中註冊和使用密碼重設的使用者。

以下是這些報告在 [Azure 入口網站](https://manage.windowsazure.com)中的外觀：

  ![][006]

如需詳細資訊，請參閱[取得深入資訊：Azure AD 密碼管理報告](active-directory-passwords-get-insights.md)。

## <a name="password-writeback-component-of-azure-ad-connect"></a>Azure AD Connect 的密碼回寫元件
如果您的組織中的使用者密碼源自於您的內部部署環境 (透過同盟或密碼同步處理)，您可以安裝最新版的 Azure AD Connect，以便直接從雲端更新這些密碼。 這表示當使用者忘記或想要修改 Azure AD 密碼時，可以直接在網路上修改。 以下是 Azure AD Connect 安裝精靈中，密碼回寫的位置：

  ![][007]

如需 Azure AD Connect 的詳細資訊，請參閱[開始使用：Azure AD Connect](active-directory-aadconnect.md)。 如需密碼回寫的詳細資訊，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)。


## <a name="next-steps"></a>後續步驟

若要深入了解，請參閱下列 Azure AD 密碼重設頁面︰

* **您來到此處是因為有登入問題嗎？** 若是如此，請[變更和重設您自己的密碼](active-directory-passwords-update-your-own-password.md#reset-my-password)。
* [**開始使用**](active-directory-passwords-getting-started.md)：了解如何讓使用者重設和變更雲端或內部部署密碼。
* [**自訂**](active-directory-passwords-customize.md)：了解如何依照組織的需求自訂服務的外觀和行為。
* [**最佳作法**](active-directory-passwords-best-practices.md)：了解如何在組織中快速部署和有效管理密碼。
* [**取得深入資訊**](active-directory-passwords-get-insights.md)：了解整合式報告功能。
* [**常見問題集**](active-directory-passwords-faq.md)：取得常見問題的解答。
* [**疑難排解**](active-directory-passwords-troubleshoot.md)：了解如何針對服務的問題快速進行疑難排解。
* [**深入了解**](active-directory-passwords-learn-more.md)：深入探索服務運作方式的技術細節。

[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

