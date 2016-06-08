<properties
   pageTitle="如何授與 PIM 存取權 |Microsoft Azure"
   description="了解如何使用 Azure Active Directory Privileged Identity Management 擴充功能為使用者加入角色，以便他們可以管理 PIM。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# 如何授與存取權以管理 Azure AD Privileged Identity Management

組織中第一個啟用 Azure AD Privileged Identity Management (PIM) 的使用者必須是全域系統管理員。但是，其他全域系統管理員預設沒有 PIM 存取權，所以他們無法管理暫時性指派。若要授與 PIM 存取權，第一位使用者可以將特殊權限角色管理員角色指派給其他使用者。這項指派必須在 PIM 內完成，而且無法透過 PowerShell 或其他入口網站變更。

> [AZURE.NOTE] 管理 Azure AD PIM 需要 Azure MFA。因為 Microsoft 帳戶無法註冊 Azure MFA，所以使用 Microsoft 帳戶登入的使用者無法存取 Azure AD PIM。

請確保特殊權限角色管理員角色中永遠至少有兩位使用者，以防一位使用者遭到鎖定或他們的帳戶遭刪除。

## 授與另一位使用者存取權以管理 PIM

1. 登入 [Azure 入口網站](https://portal.azure.com/)，在儀表板上選取 [Azure AD Privileged Identity Management] 應用程式。
2. 選取**特殊權限角色管理員**角色。該角色的目前使用者清單隨即出現。
3. 按一下 [加入] 即會顯示精靈刀鋒視窗。該角色已選取。
4. 按一下 [選取使用者] 即開啟使用者清單刀鋒視窗。
5. 在搜尋欄位中輸入使用者名稱。如果使用者在目錄中，則他們的帳戶就會在您輸入時出現。
6. 從搜尋結果中選取使用者，然後按一下 [完成]。
7. 按一下 [確定] 以儲存您的選取項目。您選取的使用者會出現在清單中，而角色預設是暫時性的。

  - 如果想要讓角色變成永久，請按一下清單中的使用者。該使用者的資訊即會出現在新的刀鋒視窗中。在使用者資訊功能表中選取 [變成永久]。

8. 將[開始使用 Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) 的 Azure 文件連結傳送給使用者。


## 移除其他使用者管理 PIM 的存取權限

將某人自特殊權限角色管理員角色移除之前，請確保該角色仍有兩位指派的使用者。

1. 在 PIM 儀表板中，按一下 [特殊權限角色管理員] 角色。該角色的目前使用者清單隨即出現。
2. 按一下使用者清單中的使用者。
3. 按一下 [移除]。隨即會看到確認訊息。
4. 按一下 [是]，即會從使用者中移除角色。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->