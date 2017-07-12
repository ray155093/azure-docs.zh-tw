---
title: "安裝應用程式存取面板的瀏覽器延伸模組時出現的錯誤 | Microsoft Docs"
description: "如何修正在安裝存取面板的瀏覽器延伸模組時所遇到的常見錯誤"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 759631bca9b29134098dba55ef07513d0ee42549
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017

---

<a id="problem-installing-the-application-access-panel-browser-extension" class="xliff"></a>

# 安裝應用程式存取面板的瀏覽器延伸模組時出現的錯誤

存取面板是網頁型入口網站，可讓在 Azure Active Directory (Azure AD) 中具有公司或學校帳戶的使用者，檢視和啟動 Azure AD 系統管理員已授權他們存取的雲端式應用程式。 具有 Azure AD 版本的使用者也可以透過存取面板使用自助群組和應用程式管理功能。 存取面板與 Azure 入口網站分開，使用者不需要具備 Azure 訂用帳戶也能使用。

若要在存取面板中使用密碼單一登入 (SSO)，必須在使用者的瀏覽器中安裝存取面板延伸模組。 當使用者選取已設定密碼 SSO 的應用程式時，就會自動下載此存取面板延伸模組。

<a id="meeting-browser-requirements-for-the-access-panel" class="xliff"></a>

## 符合存取面板的瀏覽器需求

存取面板需要支援 JavaScript 且已啟用 CSS 的瀏覽器。 若要在存取面板中使用密碼單一登入 (SSO)，必須在使用者的瀏覽器中安裝存取面板延伸模組。 當使用者選取已設定密碼 SSO 的應用程式時，就會自動下載此存取面板延伸模組。

若是密碼 SSO，則使用者的瀏覽器可以是：

-   Internet Explorer 8、9、10、11 -- 在 Windows 7 或更新版本上

-   Chrome - 在 Windows 7 或更新版本，和在 MacOS X 或更新版本上

-   Firefox 26.0 或更新版本 - 在 Windows XP SP2 或更新版本，和在 Mac OS X 10.6 或更新版本上

**注意**：當瀏覽器延伸開始支援 Edge 時，Windows 10 中的 Edge 就能使用密碼型 SSO 延伸模組。

<a id="how-to-install-the-access-panel-browser-extension" class="xliff"></a>

## 如何安裝存取面板瀏覽器延伸模組

若要安裝存取面板的瀏覽器延伸模組，請遵循下列步驟：

1.  在其中一種支援的瀏覽器中開啟[存取面板](https://myapps.microsoft.com)，然後在您的 Azure AD 中以**使用者**身分登入。

2.  按一下存取面板中的 [密碼-SSO 應用程式]。

3.  在要求安裝軟體的提示中，選取 [立即安裝]。

4.  系統會根據您的瀏覽器將您導向至下載連結。 將延伸模組**新增**到瀏覽器中。

5.  如果您的瀏覽器要求，請選取 [啟用] 或 [允許] 該延伸模組。

6.  安裝之後，**重新啟動**瀏覽器工作階段。

7.  登入存取面板，並查看您是否可以**啟動**密碼-SSO 應用程式

您可能也會從下列直接連結中下載適用於 Chrome 和 Firefox 的延伸模組：

-   [Chrome 存取面板延伸模組](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 存取面板延伸模組](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

<a id="setting-up-a-group-policy-for-internet-explorer" class="xliff"></a>

## 設定適用於 Internet Explorer 的群組原則

您可以設定一個群組原則，以允許您在使用者電腦上遠端安裝適用於 Internet Explorer 的存取面板延伸模組。

必要條件包括：

-   您已設定了 [Active Directory 網域服務](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)，並且已將使用者的電腦加入網域。

-   您必須擁有「編輯設定」權限，才能編輯群組原則物件 (GPO)。 根據預設，下列安全性群組的成員擁有此權限：網域系統管理員、企業系統管理員及群組原則建立者擁有者。 [深入了解](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

如需如何設定群組原則並將它部署到使用者的逐步指示，請遵循[如何使用群組原則部署 Internet Explorer 的存取面板延伸模組](active-directory-saas-ie-group-policy.md)教學課程。

<a id="troubleshoot-the-access-panel-in-internet-explorer" class="xliff"></a>

## 進行 Internet Explorer 中存取面板的疑難排解

如需存取診斷工具和設定 IE 延伸模組的逐步指示，請遵循[疑難排解 Internet Explorer 的存取面板延伸模組](active-directory-saas-ie-troubleshooting.md)指南。

<a id="if-these-troubleshooting-steps-do-not-resolve-the-issue" class="xliff"></a>

## 如果這些疑難排解步驟無法解決問題

使用下列資訊 (若有的話) 開啟支援票證︰

-   相互關聯錯誤 ID

-   UPN (使用者電子郵件地址)

-   TenantID

-   瀏覽器類型

-   錯誤發生期間的時區和時間/時間範圍

-   Fiddler 追蹤

<a id="next-steps" class="xliff"></a>

## 後續步驟
[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

