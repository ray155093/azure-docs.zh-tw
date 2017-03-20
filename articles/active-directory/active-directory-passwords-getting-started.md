---
title: "開始使用：Azure AD 密碼管理 |Microsoft Docs"
description: "讓使用者重設自己的密碼、探索密碼重設的先決條件，以及啟用密碼回寫以管理 Active Directory 中的內部部署密碼。"
services: active-directory
keywords: "Active directory 密碼管理, 密碼管理, 重設 Azure AD 密碼"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 441caf3cc9a3b9074bd263f4a4c45763967fa580
ms.lasthandoff: 03/15/2017


---
# <a name="getting-started-with-password-management"></a>開始使用密碼管理
> [!IMPORTANT]
> **您有登入問題嗎？** 若是如此， [以下是如何變更和重設密碼的說明](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password)。
>
>

讓您的使用者管理他們自己的雲端 Azure Active Directory 或內部部署 Active Directory 密碼，只要採用幾個簡單的步驟。 確定您符合幾個簡單的先決條件之後，您會在您知悉之前，對整個組織啟用密碼變更和重設。 本文章將會逐步引導您完成下列概念：

* [**開始前閱讀來自從我們客戶的最佳秘訣**](#top-tips-from-our-customers-to-read-before-you-begin)
 * [**最佳秘訣︰文件導覽** - 使用我們的目錄及您瀏覽器的尋找功能來尋找解答](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
 * [**秘訣 1︰授權** - 確定您了解授權需求](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
 * [**秘訣 2︰測試** - 測試使用者而非系統管理員，並與一小組使用者進行試驗](#tip-2-testing---test-with-a-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
 * [**秘訣 3︰部署** - 預先填入使用者的資料，以便他們不需要註冊](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
 * [**祕訣 4︰部署** - 使用密碼重設以排除暫時密碼的需求](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
 * [**祕訣 5︰回寫** - 查看 AAD Connect 電腦上的應用程式事件記錄以疑難排解密碼回寫](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
 * [**祕訣 6︰回寫** - 確保您啟用正確的權限、防火牆規則，以及密碼回寫的連線設定](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
 * [**提示 7：報告** - 查看正在使用 Azure AD SSPR 稽核記錄註冊或重設密碼的人員](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
 * [**祕訣 8：疑難排解** - 讀取我們的疑難排解指南和常見問題集以解決許多問題](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
 * [**秘訣 9：疑難排解** - 如果您仍然需要協助，請包含足夠的資訊以供我們協助您](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)
* [**如何讓使用者重設 Azure Active Directory 密碼**](#enable-users-to-reset-their-azure-ad-passwords)
 * [自助式密碼重設先決條件](#prerequisites)
 * [步驟 1：設定密碼重設原則](#step-1-configure-password-reset-policy)
 * [步驟 2：為您的測試使用者加入連絡人資料](#step-2-add-contact-data-for-your-test-user)
 * [步驟 3：以使用者的身分重設您的密碼](#step-3-reset-your-azure-ad-password-as-a-user)
* [**如何讓使用者重設或變更其內部部署 Active Directory 密碼**](#enable-users-to-reset-or-change-their-ad-passwords)
 * [密碼回寫先決條件](#writeback-prerequisites)
 * [步驟 1：下載最新版本的 Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 * [步驟 2：在 Azure AD Connect 中透過 UI 或 powershell 啟用密碼回寫並且確認](#step-2-enable-password-writeback-in-azure-ad-connect)
 * [步驟 3：設定您的防火牆](#step-3-configure-your-firewall)
 * [步驟 4：設定適當的權限](#step-4-set-up-the-appropriate-active-directory-permissions)
 * [步驟 5：以使用者的身分重設您的 AD 密碼並且確認](#step-5-reset-your-ad-password-as-a-user)

## <a name="top-tips-from-our-customers-to-read-before-you-begin"></a>開始前閱讀來自客戶提供的最佳秘訣
以下是我們看到對部署組織中密碼管理的客戶來說十分實用的一些最佳秘訣。

* [**最佳秘訣︰文件導覽** - 使用我們的目錄及您瀏覽器的尋找功能來尋找解答](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
* [**秘訣 1︰授權** - 確定您了解授權需求](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
* [**秘訣 2︰測試** - 測試使用者而非系統管理員，並與一小組使用者進行試驗](#tip-2-testing---test-with-a-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
* [**秘訣 3︰部署** - 預先填入使用者的資料，以便他們不需要註冊](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
* [**祕訣 4︰部署** - 使用密碼重設以排除暫時密碼的需求](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
* [**祕訣 5︰回寫** - 查看 AAD Connect 電腦上的應用程式事件記錄以疑難排解密碼回寫](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
* [**祕訣 6︰回寫** - 確保您啟用正確的權限、防火牆規則，以及密碼回寫的連線設定](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
* [**提示 7：報告** - 查看正在使用 Azure AD SSPR 稽核記錄註冊或重設密碼的人員](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
* [**祕訣 8：疑難排解** - 讀取我們的疑難排解指南和常見問題集以解決許多問題](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
* [**秘訣 9：疑難排解** - 如果您仍然需要協助，請包含足夠的資訊以供我們協助您](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)

### <a name="top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers"></a>最佳秘訣︰文件導覽 - 使用我們的目錄及您瀏覽器的尋找功能來尋找解答
如果您使用我們任何的文件，我們在目錄中已盡力提供快速連結至可供系統管理員學習的所有有趣地方。 

請查看下列目錄︰ 
* [Azure AD 密碼重設︰文件目錄](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)

### <a name="tip-1-licensing---make-sure-you-understand-the-licensing-requirements"></a>秘訣 1︰授權 - 確定您了解授權需求
為了讓 Azure AD 密碼重設為函式，您必須至少在組織中指派一次授權。 我們不會強制每位使用者自行體驗密碼重設，不過，如果您在沒有指派給使用者授權的情況下使用該功能，則會被視為不符合 Microsoft 授權合約，且必須將授權指派給這些使用者。

以下這些文件可協助您了解密碼重設所需的授權。
* [一般密碼重設授權資訊]()
* [每個功能的重設授權資訊]()
* [密碼回寫的支援案例]()

### <a name="tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users"></a>秘訣 2︰測試 - 測試使用者而非系統管理員，並與一小組使用者進行試驗
當您測試系統管理員時，我們會強制執行系統管理員密碼重設原則，定義如下。  這表示，您不會看見您已為使用者設定之原則的預期結果。

在系統管理 UX 中設定的原則將只套用到使用者，而不會套用到系統管理員。 Microsoft 會為您的系統管理員強制執行強式預設密碼重設原則 - 這可能會與您為使用者所設定的原則不同 - 以確保您的組織保持安全。

#### <a name="administrator-password-reset-policy"></a>系統管理員密碼重設原則
* **適用於** - 任何系統管理員角色 (全域管理員、技術支援系統管理員、密碼管理員等)
* **一個閘道原則適用於...**
 * ...在試用版開始建立前 30 天後**或**
 * ...當虛名網域不存在時**以及** Azure AD Connect 不會同步處理身分識別
 * **_需要_**︰任何**一個**驗證電子郵件、備用電子郵件、驗證電話、行動電話或辦公室電話以顯示目前的值
* **兩個閘道原則適用於...** 
 * ...試用版已經過前 30 天**或**
 * ...當虛名網域存在時**或** 
 * ...您已啟用 Azure AD Connect 同步處理來自內部部署環境的身分識別
 * _**需要**_︰任何**兩個**驗證電子郵件、備用電子郵件、驗證電話、行動電話或辦公室電話以顯示目前的值

### <a name="tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register"></a>秘訣 3︰部署 - 預先填入使用者的資料，以便他們不需要註冊
許多人不知道，您不需要讓使用者重設註冊密碼才可使用功能。  藉由預先為您的使用者設定電話或電子郵件屬性，您可以立即為整個組織啟用密碼重設**而不需要使用者採取任何動作！**

若要了解如何使用 API、PowerShell 或 Azure AD Connect 執行這項操作，請閱讀下列文件︰
* [部署密碼重設而不需要使用者註冊](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [密碼重設使用哪些資料](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords"></a>祕訣 4︰部署 - 使用密碼重設以排除暫時密碼的需求
這是祕訣 3 的附加項目。 一旦您預先設定讓使用者重設密碼，請假設員工是第一次加入貴公司。 您現在可以讓員工瀏覽至 [Azure AD 密碼重設入口網站](https://passwordreset.microsoftonline.com)重設其密碼，而不是通訊他們的暫時密碼。

如果使用者使用 [Windows 10 Azure AD 網域聯結裝置](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy)，它們甚至可以直接從 Windows 10 現成可用的登入畫面進行，不需要動手指即可讓使用者存取全新的電腦:）。

若要了解如何使用 API、PowerShell 或 Azure AD Connect 執行這項操作，請閱讀下列文件。 一旦您預先填入此資料後，只需讓使用者重設其密碼，他們便可立即進入他們的帳戶︰
* [部署密碼重設而不需要使用者註冊](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [密碼重設使用哪些資料](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback"></a>祕訣 5︰回寫 - 查看 AAD Connect 電腦上的應用程式事件記錄以疑難排解密碼回寫
Azure AD Connect 應用程式事件記錄包含一組豐富的記錄資訊，即時說明大部分密碼回寫服務時所發生的事情。 若要存取此記錄，請遵循下列步驟︰

1. 登入您 **Azure AD Connect** 機器
2. 按下**啟動**並輸入**「事件檢視器」**來開啟 [Windows 事件檢視器]
3. 開啟**應用程式**事件記錄
4. 查看來自下列來源的事件︰**PasswordResetService** 或 **ADSync**以深入了解可能發生的問題

如需此記錄可能出現事件的完整清單，以及大量密碼回寫的疑難排解指導方針，請參閱︰
* [疑難排解：密碼回寫](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [回寫事件記錄錯誤碼](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [疑難排解：密碼回寫連線](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [回寫部署 - 步驟 3︰設定您的防火牆](#step-3-configure-your-firewall)
* [回寫部署 - 步驟 4：設定適當的權限](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback"></a>祕訣 6︰回寫 - 確保您啟用正確的權限、防火牆規則，以及密碼回寫的連線設定
為了讓回寫正確運作，您必須確定︰

1. 已使用密碼回寫功能為使用者設定了適當的 **Active Directory 權限**，讓他們有權限在 AD 中修改自己的密碼和帳戶解除鎖定旗標
2. 已開啟適當**防火牆連接埠**，以允許密碼回寫服務使用輸出連線安全地與外界進行通訊
3. 已有金鑰密碼重設服務 URL 的適當**防火牆例外狀況**，例如服務匯流排
4. 您的 **proxy 和防火牆不會刪除閒置的傳出連線**，我們建議 10 分鐘以上的時間

如需疑難排解指引和設定權限的特定指導方針以及密碼回寫的防火牆規則完整清單，請參閱︰
* [疑難排解：密碼回寫](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [回寫事件記錄錯誤碼](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [疑難排解：密碼回寫連線](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [回寫部署 - 步驟 3︰設定您的防火牆](#step-3-configure-your-firewall)
* [回寫部署 - 步驟 4：設定適當的權限](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs"></a>提示 7：報告 - 查看正在使用 Azure AD SSPR 稽核記錄註冊或重設密碼的人員 
一旦密碼重設部署且使用時，下一步是查看它的運作並分析仍然需要註冊的人員、使用者重設時常見的問題，以及您未來的投資報酬率。

使用 Azure AD 密碼重設稽核記錄，您可以從 Azure 入口網站、PowerBI、Azure AD 報告事件 API，或從 PowerShell 進行此動作及更多動作。  若要深入了解如何使用這些報告功能，請參閱︰
* [密碼管理報告概觀](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#overview-of-password-management-reports)
* [如何在 Azure 入口網站中檢視密碼管理報告](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-view-password-management-reports)
* [Azure 入口網站中的自助密碼管理活動類型](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#self-service-password-management-activity-types-in-the-new-azure-portal)
* [如何從 Azure AD 報告和事件 API 擷取密碼管理事件](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
* [如何使用 PowerShell 快速下載密碼重設註冊事件](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-download-password-reset-registration-events-quickly-with-powershell)

### <a name="tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues"></a>祕訣 8：疑難排解 - 讀取我們的疑難排解指南和常見問題集以解決許多問題
您知道密碼重設有一組豐富的疑難排解指引和常見問題集嗎？ 關鍵就在於，如果您有問題，您可以在下列連結中找到答案。

此外，您也可以直接從左瀏覽窗格上的 [Azure Active Directory] -> [使用者與群組] -> [密碼重設] -> [支援與疑難排解] 下找到的密碼管理系統管理使用者經驗，使用 [Azure 入口網站](https://portal.azure.com)中的 [支援與疑難排解] 刀鋒視窗取得一組豐富的疑難排解內容。

密碼重設疑難排解指引的連結和常見問題集︰
* [疑難排解密碼管理](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot)
* [密碼管理常見問題集](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-faq)

### <a name="tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you"></a>秘訣 9：疑難排解 - 如果您仍然需要協助，請包含足夠的資訊以供我們協助您
如果您仍然需要疑難排解的協助，我們隨時為您服務。 您可以開啟支援案例，或連絡您的帳戶管理小組直接與我們接洽。 希望能夠聽到您的意見！

但是，在您接洽之前，請**確定您收集所有下方要求的資訊**，讓我們可以快速協助您！
* [您需要協助時應包含的資訊](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#information-to-include-when-you-need-help)

#### <a name="ways-to-provide-password-reset-feedback"></a>提供密碼重設意見反應的方法
* [功能要求或疑難排解 - Azure AD MSDN 論壇上的貼文](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)
* [功能要求或疑難排解 - StackOverflow 上的貼文](http://stackoverflow.com/questions/tagged/azure-active-directory)
* [功能要求或疑難排解 - 推文@azuread！](https://twitter.com/azuread)
* [僅功能要求 - 在 UserVoice 留下附註](https://feedback.azure.com/forums/169401-azure-active-directory)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>讓使用者重設其 Azure AD 密碼
本節會逐步引導您對 AAD 雲端目錄啟用自助式密碼重設、註冊使用者進行自助式密碼重設，最後以使用者的身分執行自助式密碼重設測試。

* [自助式密碼重設先決條件](#prerequisites)
* [步驟 1：設定密碼重設原則](#step-1-configure-password-reset-policy)
* [步驟 2：為您的測試使用者加入連絡人資料](#step-2-add-contact-data-for-your-test-user)
* [步驟 3：以使用者的身分重設您的密碼](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>先決條件
在您可以啟用及使用自助式密碼重設之前，您必須完成下列先決條件：

* 建立 AAD 租用戶。 如需詳細資訊，請參閱 [開始使用 Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)
* 取得 Azure 訂用帳戶。 如需詳細資訊，請參閱 [Azure AD 租用戶是什麼？](active-directory-administer.md#what-is-an-azure-ad-tenant)。
* 將 AAD 租用戶與 Azure 訂用帳戶產生關聯。 如需詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure AD 產生關聯](https://msdn.microsoft.com/library/azure/dn629581.aspx)。
* 升級至 Azure AD Premium、Basic 或使用 O365 付費的授權。 如需詳細資訊，請參閱 [Azure Active Directory 版本](https://azure.microsoft.com/pricing/details/active-directory/)。

  > [!NOTE]
  > 若要啟用雲端使用者的自助式密碼重設，您必須升級至 Azure AD Premium、Azure AD Basic 或付費的 O365 授權。  若要啟用內部部署使用者的自助式密碼重設，您必須升級至 Azure AD Premium。 如需詳細資訊，請參閱 [Azure Active Directory 版本](https://azure.microsoft.com/pricing/details/active-directory/)。 這項資訊包括如何註冊 Azure AD Premium 或 Basic、如何啟動您的授權方案並啟動您的 Azure AD 存取權，以及如何將存取權指派給系統管理員和使用者帳戶的詳細指示。
  >
  >
* 在 AAD 目錄中建立至少一個系統管理員帳戶和一個使用者帳戶。
* 將 AAD Premium、Basic 或 O365 付費的授權指派給您所建立的系統管理員和使用者帳戶。

### <a name="step-1-configure-password-reset-policy"></a>步驟 1：設定密碼重設原則
若要設定使用者密碼重設原則，請完成下列步驟：

1. 開啟您選擇的瀏覽器並瀏覽至 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，於左側導覽列上找到 [Active Directory 延伸模組]。

   ![Azure AD 中的密碼管理][001]
3. 在 [ **目錄** ] 索引標籤底下，按一下您要設定使用者密碼重設原則的目錄，例如，Wingtip Toys。

    ![][002]
4. 按一下 [設定]  索引標籤。

   ![][003]

5. 在 [設定] 索引標籤底下，向下捲動至 [使用者密碼重設原則] 區段。  這是您設定指定目錄的使用者密碼重設原則各個層面的位置。 *如果看不到 [設定] 索引標籤，請確定您已註冊 Azure Active Directory Premium 或 Basic，並且__指派授權__給設定此功能的系統管理員帳戶。*  

   > [!NOTE]
   > **您設定的原則只適用於您組織中的使用者，不適用於系統管理員**。 基於安全性理由，Microsoft 會為系統管理員控制密碼重設原則。 目前適用於系統管理員的原則需要進行兩項挑戰 - 行動電話和電子郵件地址。

   ![][004]
6. 若要設定使用者密碼重設原則，滑動 [使用者啟用密碼重設] 以切換至 [是] 設定。  這會顯示更多控制項，讓您設定這項功能如何在您的目錄中運作。  放心地自訂您覺得適合的密碼重設。  如果您想要深入了解關於每一項密碼重設原則控制項所執行的項目，請參閱 [自訂：Azure AD 密碼管理](active-directory-passwords-customize.md)。

   ![][005]
7. 視需要為您的租用戶設定使用者密碼重設原則之後，請按一下畫面底部的 [ **儲存** ] 按鈕。

   > [!NOTE]
   > 建議使用兩個挑戰使用者密碼重設原則，這樣您就能看到在最複雜的情況下，功能如何運作。
   >
   >

   ![][006]

   > [!NOTE]
   > **您設定的原則只適用於您組織中的使用者，不適用於系統管理員**。 基於安全性理由，Microsoft 會為系統管理員控制密碼重設原則。 目前適用於系統管理員的原則需要進行兩項挑戰 - 行動電話和電子郵件地址。
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>步驟 2：為您的測試使用者加入連絡人資料
您有幾個選項可用於指定要用於密碼重設的組織中使用者資料。

* 在 [Azure 傳統入口網站](https://manage.windowsazure.com)或 [Office 365 系統管理入口網站](https://portal.microsoftonline.com)編輯使用者
* 使用 AAD Connect 從內部部署 Active Directory 網域將使用者屬性同步處理至 Azure AD
* 使用 Windows PowerShell 編輯使用者屬性
* 引導使用者至位於 [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* 當使用者登入位於 [http://myapps.microsoft.com](http://myapps.microsoft.com) 的存取面板時，要求使用者註冊密碼重設，方法是將 [要求使用者在登入存取面板時註冊] SSPR 組態選項設定為 [是]。

如果您想要深入了解密碼重設使用哪些資料，以及此資料的任何格式需求，請參閱 [密碼重設使用哪些資料？](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)。

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>透過使用者註冊入口網站加入使用者連絡人資料
1. 若要使用密碼重設註冊入口網站，您必須提供組織中的使用者這個頁面的連結 ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup))，或開啟選項要求使用者自動註冊。  一旦使用者按一下此連結，系統會要求他們使用其組織帳戶登入。  完成後，他們會看到下列頁面：

   ![][007]
2. 在這裡，使用者可以提供並確認其行動電話、備用電子郵件地址或安全性問題。  這是驗證行動電話的外觀。

   ![][008]
3. 使用者指定這項資訊之後，頁面會更新以指出該資訊有效 (下方已模糊處理)。  按一下 [完成] 或 [取消] 按鈕，使用者會被帶到存取面板。

   ![][009]
4. 一旦使用者驗證這兩項資訊，他或她的設定檔將會以他或她所提供的資料更新。  在此範例中，[ **辦公室電話** ] 數目已手動指定，因此使用者也可以使用做為連絡方法，以重設其密碼。

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>步驟 3：以使用者的身分重設您的 Azure AD 密碼
您已經設定使用者重設原則，並指定使用者的連絡人詳細資料，這位使用者可以執行自助式密碼重設。

#### <a name="to-perform-a-self-service-password-reset"></a>執行自助式密碼重設
1. 如果您移至像是 [**portal.microsoftonline.com**](http://portal.microsoftonline.com) 的網站，您會看到如下的登入畫面。  按一下 [ **無法存取您的帳戶？** ] 連結，以測試密碼重設 UI。

   ![][011]
2. 按一下 [無法存取您的帳戶？] 之後，您會被帶往新頁面，詢問您要重設密碼的 [使用者識別碼]。  在這裡輸入您的測試 [使用者識別碼]，通過 captcha，然後按 [下一步]。

   ![][012]
3. 因為在此案例中，使用者已指定 [辦公室電話]、[行動電話] 和 [備用電子郵件]，所以您會看到系統給予他或她所有項目做為選項，以通過第一項挑戰。

   ![][013]
4. 在此情況下，首先選擇 [呼叫辦公室電話]。  請注意，當選取以電話為基礎的方法時，系統會要求使用者 [ **確認電話號碼** ]，然後他們才可以重設其密碼。  這是為了防止惡意的個人濫發垃圾郵件給組織中使用者的電話號碼。

   ![][014]
5. 一旦使用者確認其電話號碼之後，按一下 [通話] 將會顯示撥號畫面，且使用者的電話響鈴。  使用者接聽電話之後，會播放訊息，指出 [ **使用者應該按 "#"** ] 以確認他或她的帳戶。  按下此按鍵會自動確認使用者完成第一項挑戰，並前進到第二個驗證步驟的 UI。

   ![][015]
6. 一旦您通過第一項挑戰，UI 會自動更新，從使用者擁有的選擇清單中移除。  在此情況下，因為您先使用您的 [辦公室電話]，所以還剩下 [行動電話] 和 [備用電子郵件] 是有效的選項，可以做為第二個驗證步驟的挑戰。  按一下 [ **寄電子郵件到我的備用電子郵件地址** ] 電子郵件選項。  完成這個動作之後，按下電子郵件將會以電子郵件將檔案寄送到備用電子郵件。

   ![][016]
7. 以下是使用者會看到的電子郵件範例 – 請注意租用戶商標：

   ![][017]
8. 一旦電子郵件送達時，頁面會更新，而且您可以在如下所示的輸入方塊中輸入電子郵件中找到的驗證。  輸入適當的代碼之後，[下一步] 按鈕會亮起，您可以通過第二個驗證步驟。

   ![][018]
9. 一旦您符合組織原則的需求，您就可以選擇新密碼。  密碼是根據其符合 AAD「強式」密碼需求而經過驗證的 (請參閱 [Azure AD 中的密碼原則](https://msdn.microsoft.com/library/azure/jj943764.aspx))，強度驗證器會出現，向使用者表示輸入的密碼是否符合該原則。

   ![][019]
10. 一旦您提供符合組織原則的相符密碼，您的密碼就會重設，您可以立即使用您的新密碼登入。

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>讓使用者重設或變更其 AD 密碼
本章節會引導您透過設定密碼重設，將密碼回寫至內部部署 Active Directory。

* [密碼回寫先決條件](#writeback-prerequisites)
* [步驟 1：下載最新版本的 Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
* [步驟 2：在 Azure AD Connect 中透過 UI 或 powershell 啟用密碼回寫並且確認](#step-2-enable-password-writeback-in-azure-ad-connect)
* [步驟 3：設定您的防火牆](#step-3-configure-your-firewall)
* [步驟 4：設定適當的權限](#step-4-set-up-the-appropriate-active-directory-permissions)
* [步驟 5：以使用者的身分重設您的 AD 密碼並且確認](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>回寫先決條件
在您可以啟用及使用密碼回寫之前，您必須確定完成下列先決條件：

* 您有 Azure AD 租用戶且啟用 Azure AD Premium。  如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。
* 已經在您的租用戶中設定和啟用密碼重設。  如需詳細資訊，請參閱 [讓使用者重設其 Azure AD 密碼](#enable-users-to-reset-their-azure-ad-passwords)
* 您必須擁有至少一個系統管理員帳戶和一個測試使用者帳戶，且具有 Azure AD Premium 授權，可讓您用來測試這項功能。  如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

  > [!NOTE]
  > 請確定您用來啟用密碼回寫的系統管理員帳戶是雲端系統管理員帳戶 (在 Azure AD 中建立)，不是同盟帳戶 (在內部部署 AD 中建立並同步處理至 Azure AD)。
  >
  >
* 您擁有單一或多樹系 AD 內部部署執行 Windows Server 2008、Windows Server 2008 R2、Windows Server 2012 或 Windows Server 2012 R2，且安裝最新的 Service Pack。

  > [!NOTE]
  > 如果您執行舊版 Windows Server 2008 或 2008 R2，您仍然可以使用此功能，但是需要先 [下載及安裝 KB 2386717](https://support.microsoft.com/kb/2386717) ，才能在雲端中強制執行您的本機 AD 密碼原則。
  >
  >
* 您已安裝 Azure AD Connect 工具，且已備妥 AD 環境進行同步處理至雲端。  如需詳細資訊，請參閱 [在雲端中使用內部部署身分識別基礎結構](connect/active-directory-aadconnect.md)

  > [!NOTE]
  > 測試密碼回寫之前，請確定您先在 Azure AD Connect 中完成 AD 和 Azure AD 的完整匯入和完整同步處理。
  >
  >
* 如果您使用 Azure AD Sync 或 Azure AD Connect，則需要開啟 **TCP 443** 輸出 (在某些情況下 **TCP 9350-9354**)。  如需詳細資訊，請參閱 [步驟 3：設定您的防火牆](#step-3-configure-your-firewall) 。 這個案例不再支援使用 DirSync。  如果您仍要使用 DirSync，請升級至最新版的 Azure AD Connect，再部署密碼回寫。

  > [!NOTE]
  > 我們強烈建議使用 Azure AD Sync 或 DirSync 工具的任何人升級至最新版的 Azure AD Connect，以確保擁有最佳使用經驗和發行的新功能。
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>步驟 1：下載最新版本的 Azure AD Connect
密碼回寫可以在 Azure AD Connect 版本或具有版本號碼 **1.0.0419.0911** 或更高版本的 Azure AD Sync 工具中使用。  具有自動帳戶解除鎖定的密碼回寫可以在 Azure AD Connect 版本或具有版本號碼 **1.0.0485.0222** 或更高版本的 Azure AD Sync 工具中使用。 如果您執行較舊的版本，請至少升級至此版本，再繼續作業。 [按一下這裡以下載最新版本的 Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect)。

#### <a name="to-check-the-version-of-azure-ad-sync"></a>檢查 Azure AD Sync 的版本
1. 瀏覽至 *%ProgramFiles%\Azure Active Directory Sync*\**。
2. 尋找 **ConfigWizard.exe** 可執行檔。
3. 以滑鼠右鍵按一下可執行檔，然後從內容功能表選取 [ **屬性** ] 選項。
4. 按一下 [ **詳細資料** ] 索引標籤。
5. 尋找 [ **檔案版本** ] 欄位。

   ![][021]

如果此版本號碼大於或等於 **1.0.0419.0911**，或您安裝 Azure AD Connect，您可以跳到[步驟 2：在 Azure AD Connect 中透過 UI 或 powershell 啟用密碼回寫並且確認](#step-2-enable-password-writeback-in-azure-ad-connect)。

> [!NOTE]
> 如果這是您第一次安裝 Azure AD Connect 工具，建議您依照一些最佳作法以準備環境進行目錄同步處理。  安裝 Azure AD Connect 工具之前，您必須在 [Office 365 系統管理入口網站](https://portal.microsoftonline.com)或 [Azure 傳統入口網站](https://manage.windowsazure.com)中啟動目錄同步作業。  如需詳細資訊，請參閱 [管理 Azure AD Connect](active-directory-aadconnect-whats-next.md)。
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>步驟 2：在 Azure AD Connect 中啟用密碼回寫
您已下載 Azure AD Connect 工具，已準備就緒可以啟用密碼回寫。  您可以使用下列兩種方式之一來執行這個動作。  您可以在 Azure AD Connect 安裝精靈的選用功能畫面中啟用密碼回寫，或者您可以透過 Windows PowerShell 啟用它。

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>在組態精靈中啟用密碼回寫
1. 在您的 [目錄同步處理電腦] 中，開啟 [Azure AD Connect] 組態精靈。
2. 按一下各個步驟，直到您到達 [ **選用功能** ] 組態畫面。
3. 勾選 [ **密碼回寫** ] 選項。

   ![][022]
4. 完成精靈，最後一頁將會摘要說明變更，並且會包含密碼回寫組態變更。

> [!NOTE]
> 您可以隨時停用密碼回寫，方法是重新執行此精靈，並且取消選取此功能，或是將 [Azure 傳統入口網站](https://manage.windowsazure.com)中目錄的 [設定] 索引標籤的 [使用者密碼重設原則] 中的 [將密碼回寫至內部部署目錄] 設定設為 [否]。  如需有關自訂您的密碼重設體驗的詳細資訊，請參閱 [自訂：Azure AD 密碼管理](active-directory-passwords-customize.md)。
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>使用 Windows PowerShell 啟用密碼回寫
1. 在您的 [目錄同步處理電腦] 中，開啟新的 [提升權限的 Windows PowerShell 視窗]。
2. 如果尚未載入模組，輸入 `import-module ADSync` 命令以將 Azure AD Connect Cmdlet 載入到您目前的工作階段。
3. 取得您的系統中的 AAD 連接器清單，方法是執行 `Get-ADSyncConnector` Cmdlet，並將結果儲存在 `$aadConnectorName`，例如 `$connectors = Get-ADSyncConnector|where-object {$\_.name -like "\*AAD"}`
4. 若要取得目前連接器的回寫的目前狀態，請執行下列 Cmdlet：`Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. 執行下列 Cmdlet，以啟用密碼回寫：`Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> 如果系統提示輸入認證，請確定您為 AzureADCredential 指定的系統管理員帳戶是 **雲端系統管理員帳戶 (在 Azure AD 中建立)**，不是同盟帳戶 (在內部部署 AD 中建立並且同步處理至 Azure AD。
>
> [!NOTE]
> 您可以透過 PowerShell 停用密碼回寫，方法是重複上述的相同指示，但是在步驟中傳遞 `$false`，或者將 [Azure 傳統入口網站](https://manage.windowsazure.com)中目錄的 [設定] 索引標籤的 [使用者密碼重設原則] 區段的 [將密碼回寫至內部部署目錄] 設為 [否]。
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>確認組態是否成功
一旦組態成功，您會在 Windows PowerShell 視窗中看到密碼重設回寫已啟用的訊息，或者在組態 UI 中看到成功訊息。

您也可以藉由開啟 [事件檢視器]、瀏覽至應用程式事件記錄檔，並且從來源 **PasswordResetService** 尋找事件 **31005 - OnboardingEventSuccess**，以確認服務是否正確安裝。

  ![][023]

### <a name="step-3-configure-your-firewall"></a>步驟 3：設定您的防火牆
啟用密碼回寫之後，您需要確定執行 Azure AD Connect 的電腦可以連線到 Microsoft 雲端服務以接收密碼回寫要求。 這個步驟包含在您的網路應用裝置 (proxy 伺服器、防火牆等) 中更新連線規則，以允許透過特定網路連接埠的輸出連線到特定 [Microsoft 擁有之 URL 和 IP 位址](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)。 這些變更可能會根據 Azure AD Connect 工具的版本而有所不同。 如需詳細內容，您可以深入了解[密碼回寫的運作方式](active-directory-passwords-learn-more.md#how-password-writeback-works)和[密碼回寫的安全性模型](active-directory-passwords-learn-more.md#password-writeback-security-model)。

#### <a name="why-do-i-need-to-do-this"></a>我為何需要這麼做？

為了使密碼回寫正確運作，執行 Azure AD Connect 的電腦必須能夠建立 **.servicebus.windows.net*和 Azure 所使用之特定 IP 位址的輸出 HTTPS 連線，如 [Microsoft Azure 資料中心 IP 範圍清單](https://www.microsoft.com/download/details.aspx?id=41653)中所定義。

針對 Azure AD Connect 工具 **1.1.443.0** (最新) 和更新版本︰

- 最新版本的 Azure AD Connect 工具需要**輸出 HTTPS** 存取至：
    - *passwordreset.microsoftonline.com*
    - *servicebus.windows.net*

針對 Azure AD Connect 工具版本 **1.0.8667.0** 至 **1.1.380.0**︰

- **選項 1：**允許所有透過連接埠 443 (使用 URL 或 IP 位址) 的輸出 HTTPS 連線。
    - 使用此選項的時機︰
        - 如果您想要最簡單的設定，不需要隨 Azure 資料中心 IP 範圍變更進行更新，請使用此選項。
    - 所需的步驟︰
        - 允許所有透過連接埠 443 使用 URL 或 IP 位址的輸出 HTTPS 連線。
<br><br>
- **選項 2：**允許對特定 IP 範圍和 URL 的輸出 HTTPS 連線
    - 使用此選項的時機︰
        - 如果您在受限制的網路環境中或不方便允許輸出連接，請使用此選項。
        - 在此組態中，如需密碼回寫繼續運作，您必須確保您的網路應用裝置保持來自 Microsoft Azure 資料中心 IP 範圍清單中最新 IP 的每週更新。 這些 IP 範圍可用來做為每星期三 (太平洋時間) 進行更新的 XML 檔案，並於下星期一 (太平洋時間) 生效。
    - 所需的步驟︰
        - 允許所有輸出 HTTPS 連線到 *.servicebus.windows.net
        - 允許所有輸出 HTTPS 連接到 Microsoft Azure 資料中心 IP 範圍清單中的所有 IP，並保持每週更新此組態。

> [!NOTE]
> 如果您已依照上述指示來設定密碼回寫，且未在 Azure AD Connect 事件記錄檔中看到任何錯誤，但在測試時發生連接錯誤，則可能是您環境中的網路應用裝置封鎖 IP 位址的 HTTPS 連接。 例如，當允許 *https://*.servicebus.windows.net* 的連接時，該範圍內的特定 IP 位址連接可能會遭到封鎖。 若要解決此問題，您將需要設定網路環境，以透過連接埠 443 允許所有輸出 HTTPS 連接至任何 URL 或 IP 位址 (上述選項 1)，或與網路小組合作以明確允許 HTTPS 連接至特定的 IP 位址 (上述選項 2)。

**較舊的版本︰**

- 允許透過連接埠 443、9350-9354 和連接埠 5671 的輸出 TCP 連線
- 允許對 *https://ssprsbprodncu-sb.accesscontrol.windows.net/* 的輸出連線

> [!NOTE]
> 如果您是 Azure AD Connect 1.0.8667.0 之前的版本，Microsoft 強烈建議您升級至[最新版的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)，其中包含數項回寫網路增強功能來簡化設定。

一旦已設定網路應用裝置後，請重新開機執行 Azure AD Connect 工具的電腦。

#### <a name="idle-connections-on-azure-ad-connect-114430-and-up"></a>Azure AD Connect 上的閒置連線 (1.1.443.0 和更高版本)
Azure AD Connect 工具會傳送定期 ping/keepalives 至服務匯流排端點，以確保連線保持作用中。 工具偵測到太多的連線應該被終止時，它就會自動增加 ping 至端點的頻率。 將放置的最低「ping 間隔」是每隔 60 秒 1 ping，不過，**我們強烈建議讓 proxy/防火牆允許保存閒置連線至少 2-3 分鐘。** \*針對較舊版本，建議 4 分鐘以上。

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>步驟 4：設定適當的 Active Directory 權限
對於包含使用者 (其密碼將會重設) 的每個樹系，如果 X 為組態精靈 (初始組態期間) 中針對該樹系指定的帳戶，則必須為 X 指定**重設密碼**、**變更密碼**、`lockoutTime` 的**寫入權限**和 `pwdLastSet` 的**寫入權限**、該樹系中每個網域之根物件的擴充權限，或是您希望在 SSPR 中的使用者 OU。  如果您想要將重設權限的範圍設定為只有一組特定的使用者物件，以防萬一不接受針對網域的根目錄進行此動作，您可以使用後面這個選項。 權限應該標示為由所有使用者物件繼承。  

如果您不確定上述指的是哪些帳戶，請開啟 Azure Active Directory Connect 組態 UI，並按一下 [ **檢閱您的方案** ] 選項。  您需要新增權限的帳戶會有紅色底線，如下列螢幕擷取畫面所示。

**<font color="red">請確定針對您系統中各樹系的各個網域設定這項權限，否則密碼回寫將無法正常運作。</font>**

  ![][032]

  設定這些權限將會允許每個樹系的 MA 服務帳戶代表該樹系內的使用者帳戶管理密碼。 如果您沒有指定這些權限，則即使回寫看起來設定正確，使用者在嘗試從雲端管理其內部部署密碼時還是會遇到錯誤。 以下是您如何使用 [ **Active Directory 使用者和電腦** ] 管理嵌入式管理單元以進行作業的詳細步驟：

> [!NOTE]
> 最多可能需要一小時讓這些權限複寫至您的目錄中的所有物件。
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>設定正確權限以執行回寫
1. 以具有適當網域管理權限的帳戶開啟 [ **Active Directory 使用者和電腦** ]。
2. 在 [檢視功能表] 選項中，確定 [進階功能] 已開啟。
3. 在左面板中，以滑鼠右鍵按一下代表網域根目錄的物件。
4. 按一下 [ **安全性** ] 索引標籤。
5. 然後按一下 [ **進階**]。

   ![][024]
6. 按一下 [權限] 索引標籤上的 [新增]。

   ![][025]
7. 選取您想要授與權限的帳戶 (這是與設定該樹系同步處理時所指定的相同帳戶)。
8. 在最上層的下拉式清單中選取 [ **下階使用者物件**]。
9. 在顯示的 [權限項目] 對話方塊中，勾選 [重設密碼]、[變更密碼]、[`lockoutTime` 的寫入權限] 和 [`pwdLastSet` 的寫入權限] 的方塊。

   ![][026]
   ![][027]
   ![][028]
10. 然後在所有開啟的對話方塊中按一下 [ **套用/確定** ]。

### <a name="step-5-reset-your-ad-password-as-a-user"></a>步驟 5：以使用者的身分重設您的 AD 密碼
現在已啟用密碼回寫，您可以測試它是否正常運作，方法是對已同步處理至您的雲端租用戶的使用者帳戶重設密碼。

#### <a name="to-verify-password-writeback-is-working-properly"></a>確認密碼回寫是否正常運作
1. 瀏覽至 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) ，或前往任何組織識別碼登入畫面，然後按一下 [ **無法存取您的帳戶？** ] 連結。

   ![][029]
2. 您現在應該會看到新的頁面，向您要求提供您想要重設密碼的使用者識別碼。 輸入您的測試使用者識別碼，然後繼續完成密碼重設流程。
3. 重設密碼之後，您會看到如下的畫面。 這表示您已成功在您的內部部署和/或雲端目錄中重設您的密碼。

   ![][030]
4. 若要確認作業是否成功或診斷任何錯誤，請移至您的 [目錄同步處理電腦]、開啟 [事件檢視器]、瀏覽至 [應用程式事件記錄檔]，並針對您的測試使用者從來源 **PasswordResetService** 尋找事件 **31002 - PasswordResetSuccess**。

   ![][031]



## <a name="next-steps"></a>後續步驟
以下是所有 Azure AD 密碼重設文件頁面的連結：

* **您來到此處是因為有登入問題嗎？** 若是如此， [以下是如何變更和重設密碼的說明](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password)。
* [**運作方式**](active-directory-passwords-how-it-works.md) - 了解六個不同的服務元件及其功能
* [**自訂**](active-directory-passwords-customize.md) - 了解如何依照組織的需求自訂外觀和服務行為
* [**最佳作法**](active-directory-passwords-best-practices.md) - 了解如何快速部署且有效管理組織的密碼
* [**深入探索**](active-directory-passwords-get-insights.md) - 了解整合式報告功能
* [**常見問題集**](active-directory-passwords-faq.md) - 取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何快速移難排解服務的問題
* [**深入了解**](active-directory-passwords-learn-more.md) - 深入探索服務運作方式的技術細節

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

