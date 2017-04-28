---
title: "將新的使用者新增到 Azure Active Directory 預覽版 | Microsoft Docs"
description: "說明如何在 Azure Active Directory 中新增新的使用者或變更使用者資訊。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: f0e7890ba4c7ef281558886f8cb8bcc429944f06
ms.openlocfilehash: 9c0c15c0bf876110fcf9dd01a7930d3b226dfbc4
ms.lasthandoff: 02/15/2017


---
# <a name="add-new-users-to-azure-active-directory-preview"></a>將新的使用者新增到 Azure Active Directory 預覽版
> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-users-create-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-create-users.md)
>
>

本文說明如何透過 Azure Active Directory (Azure AD) 預覽版，在您的組織中新增新的使用者。 [預覽版有何功能？](active-directory-preview-explainer.md)

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [更多服務]，在文字方塊中輸入「使用者和群組」，然後選取 **Enter**。

   ![開啟使用者管理](./media/active-directory-users-create-azure-portal/create-users-user-management.png)
3. 在 [使用者和群組] 刀鋒視窗上，選取 [所有使用者]，然後選取 [新增]。

   ![選取 [新增] 命令](./media/active-directory-users-create-azure-portal/create-users-add-command.png)
4. 輸入使用者的詳細資料，例如「名稱」和「使用者名稱」。 使用者名稱的網域名稱部分必須是初始預設網域名稱 "foo.onmicrosoft.com" 網域名稱，或是已驗證的非同盟網域名稱，例如"contoso.com"。
5. 複製或記下產生的使用者密碼，以便在此程序完成後，將它提供給使用者。
6. (選擇性) 您可以開啟使用者的 [設定檔] 刀鋒視窗、[群組] 刀鋒視窗或 [目錄角色] 刀鋒視窗，然後在其中填入資訊。 如需有關使用者和系統管理員角色的詳細資訊，請參閱 [在 Azure AD 中指派系統管理員角色](active-directory-assign-admin-roles.md)。
7. 在 [使用者] 刀鋒視窗上，選取 [建立]。
8. 將產生的密碼安全地散發給新使用者，以便讓使用者可以登入。

### <a name="next-steps"></a>後續步驟
* [新增外部使用者](active-directory-users-create-external-azure-portal.md)
* [在新 Azure 入口網站中重設使用者的密碼](active-directory-users-reset-password-azure-portal.md)
* [變更使用者的工作資訊](active-directory-users-work-info-azure-portal.md)
* [管理使用者設定檔](active-directory-users-profile-azure-portal.md)
* [在 Azure AD 中刪除使用者](active-directory-users-delete-user-azure-portal.md)
* [在 Azure AD 中將使用者指派給角色](active-directory-users-assign-role-azure-portal.md)

