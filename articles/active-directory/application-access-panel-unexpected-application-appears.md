---
title: "應用程式如何出現在存取面板上 | Microsoft Docs"
description: "針對應用程式為什麼出現在存取面板上進行疑難排解"
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
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 2429ebef69aaddf28d10cd77bf4ce9072ea71476
ms.contentlocale: zh-tw
ms.lasthandoff: 04/11/2017

---

<a id="how-applications-appear-on-the-access-panel" class="xliff"></a>

# 應用程式如何出現在存取面板上

存取面板是網頁型入口網站，可讓在 Azure Active Directory (Azure AD) 中具有公司或學校帳戶的使用者，檢視和啟動 Azure AD 系統管理員已授權他們存取的雲端式應用程式。 在 Azure AD 入口網站中可代表使用者設定這些應用程式。 系統管理員可以直接將應用程式佈建給使用者，或使用者所屬的群組，而讓應用程式出現在使用者的存取面板上。

<a id="general-issues-to-check-first" class="xliff"></a>

## 首先檢查的一般問題

-   如果才剛從使用者或使用者所屬的群組移除應用程式，請在幾分鐘之後嘗試登入並再次登出使用者的存取面板，查明是否已移除該應用程式。

-   如果才剛從使用者或使用者所屬的群組移除授權，可能要經過很久，變更才會生效，視群組的大小和複雜度而定。 登入存取面板之前，請多等一些時間。

<a id="problems-related-to-assigning-applications-to-users" class="xliff"></a>

## 指派應用程式給使用者的相關問題

使用者可能因為先前已指派至應用程式，而在存取面板上看見該應用程式。 以下是一些檢查方法︰

-   [檢查使用者是否已指派至應用程式](#check-if-a-user-is-assigned-to-the-application)

-   [檢查使用者是否獲得應用程式相關的授權](#check-if-a-user-is-under-a-license-related-to-the-application)


<a id="check-if-a-user-is-assigned-to-the-application" class="xliff"></a>

### 檢查使用者是否已指派至應用程式

若要檢查使用者是否已指派至應用程式，請依照下列步驟執行：

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 擴充]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式] 以檢視所有應用程式的清單。

6.  **搜尋**相關應用程式的名稱。

7.  按一下 [使用者和群組]。

8.  檢查使用者是否已指派至應用程式。

  * 如果您想要從應用程式移除使用者，請**按一下使用者的資料列**，然後選取 [刪除]。

<a id="check-if-a-user-is-under-a-license-related-to-the-application" class="xliff"></a>

### 檢查使用者是否獲得應用程式相關的授權

若要檢查使用者獲指派的授權，請依照下列步驟執行：

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 擴充]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  按一下瀏覽功能表中的 [使用者和群組]。

5.  按一下 [所有使用者]。

6.  **搜尋**您感興趣的使用者，**按一下資料列**選取該使用者。

7.  按一下 [授權]，查看使用者目前已指派的授權。

   * 如果使用者已指派至 Office 授權，這會讓第一方 Office 應用程式出現在使用者的存取面板上。

<a id="problems-related-to-assigning-applications-to-groups" class="xliff"></a>

## 指派應用程式給群組的相關問題

使用者可能因為屬於已被指派應用程式的群組，而在存取面板上看見該應用程式。 以下是一些檢查方法︰

-   [檢查使用者的群組成員資格](#check-a-users-group-memberships)

-   [檢查使用者是否屬於指派至授權的群組](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

<a id="check-a-users-group-memberships" class="xliff"></a>

### 檢查使用者的群組成員資格

若要檢查群組的成員資格，請依照下列步驟執行：

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 擴充]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  按一下瀏覽功能表中的 [使用者和群組]。

5.  按一下 [所有使用者]。

6.  **搜尋**您感興趣的使用者，**按一下資料列**選取該使用者。

7.  按一下 [群組]。

8.  檢查使用者是否屬於已指派至應用程式的群組。

   * 如果您想要從群組移除使用者，請**按一下群組的資料列**，然後選取 [刪除]。

<a id="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license" class="xliff"></a>

### 檢查使用者是否屬於指派至授權的群組

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 擴充]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  按一下瀏覽功能表中的 [使用者和群組]。

5.  按一下 [所有使用者]。

6.  **搜尋**您感興趣的使用者，**按一下資料列**選取該使用者。

7.  按一下 [群組]。

8.  按一下特定群組的資料列。

9.  按一下 [授權]，查看群組目前已指派的授權。

  * 如果群組已指派至 Office 授權，這可能會讓某些第一方 Office 應用程式出現在使用者的存取面板上。


<a id="if-these-troubleshooting-steps-do-not-the-resolve-the-issue" class="xliff"></a>

## 如果這些疑難排解步驟無法解決問題

使用下列資訊 (若有的話) 開啟支援票證︰

-   相互關聯錯誤 ID

-   UPN (使用者電子郵件地址)

-   租用戶識別碼

-   瀏覽器類型

-   錯誤發生期間的時區和時間/時間範圍

-   Fiddler 追蹤

<a id="next-steps" class="xliff"></a>

## 後續步驟
[使用 Azure Active Directory 管理應用程式](active-directory-enable-sso-scenario.md)

