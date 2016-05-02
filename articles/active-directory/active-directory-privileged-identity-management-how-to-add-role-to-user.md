<properties
   pageTitle="如何新增或移除使用者角色 | Microsoft Azure"
   description="了解如何使用 Azure Active Directory Privileged Identity Management 應用程式來將角色新增到特殊權限身分識別。"
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
   ms.date="04/18/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management：如何新增或移除使用者角色

使用 Azure Active Directory (AD)，全域系統管理員 (或公司系統管理員) 可以更新哪些使用者獲**永久**指派 Azure AD 的角色。做法是使用 PowerShell Cmdlet，如 `Add-MsolRoleMember` 和 `Remove-MsolRoleMember`。或者可以依[在 Azure Active Directory (Azure AD) 中指派系統管理員角色](active-directory-assign-admin-roles.md)中所述，使用 Azure 傳統入口網站。

Azure AD Privileged Identity Management 應用程式也允許安全性系統管理員指派永久的角色。不過，它也讓系統管理員新增或移除**暫時**指派給這些角色的候選人員。候選人員可以在有需要時啟用角色，一等他們完成工作，權限即到期。

## 在 Azure 入口網站管理使用 PIM 的角色

在貴組織中，您可以在 Azure AD 中指派使用者不同的系統管理角色。這些角色指派控制使用者可以在 Azure AD、Office 365 和其他 Microsoft 服務和應用程式執行哪些工作，像是新增和移除使用者或變更服務設定。如需可用角色的詳細資訊，請參閱 [Roles in Azure AD PIM](active-directory-privileged-identity-management-roles.md) (Azure AD PIM 中的角色)。

若要使用 Privileged Identity Management 新增或移除使用者角色，請叫出 PIM 儀表板，然後按一下 [系統管理員角色的使用者] 按鈕，或從角色資料表中選取特定角色 (如全域系統管理員)。

> [AZURE.NOTE] 如果您還沒有在 Azure 入口網站中啟用 PIM，請移至[開始使用 Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) 以取得詳細資訊。

如果您想要讓其他使用者存取 PIM 本身，請參閱 [How to give access to manage Azure AD Privileged Identity Management (如何提供管理 Azure AD Privileged Identity Management 的存取權)](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)，進一步了解 PIM 需要使用者具有哪些角色。

## 將使用者新增至角色
一旦瀏覽至 [角色] 刀鋒視窗，請在 Azure AD PIM 儀表板中選取角色，或按一下 [系統管理員角色的使用者] 按鈕。

1. 按一下 [新增]。
  - 如果您是按一下角色表格中的使用者角色而瀏覽到此處，則該角色將是已經選取的。  
  - 按一下 [選取角色]，從角色清單中選擇一個角色。例如，[密碼管理員]。
2. 在 [選取使用者] 刀鋒視窗中搜尋使用者。如果使用者在目錄中，則他們的帳戶就會在您輸入時出現。
3. 從搜尋結果清單中選取使用者，然後按一下 [完成]。
4. 按一下 [確定] 以儲存您的選取項目。您選取的使用者會出現在清單中，而角色預設是暫時性的。

  >[AZURE.NOTE] 如果想要讓角色變成永久，請按一下清單中的使用者。該使用者的資訊即會出現在新的刀鋒視窗中。在使用者資訊功能表中選取 [變成永久]。如果使用者無法註冊為 Azure Multi-Factor Authentication (MFA) 或使用 Microsoft 帳戶，則您需要執行此動作。系統會要求暫時系統管理員在啟用啟用期間註冊 MFA。

現在，您已指派使用者暫時的角色，請讓他們根據 [Azure AD Privileged Identity Management：如何啟用或停用角色](active-directory-privileged-identity-management-how-to-activate-role.md)中的指示啟用角色。

## 從角色移除使用者

您可以從暫時角色指派中移除使用者，但請務必隨時保留至少一個永久的全域系統管理員使用者。

請遵循下列步驟移除角色中的特定使用者︰

1. 請在 Azure AD PIM 儀表板中選取角色，或按一下 [系統管理員角色的使用者] 按鈕，瀏覽至 [角色] 清單中的角色。
2. 按一下使用者清單中的使用者。
3. 按一下 [移除]。會出現訊息要求您確認。
4. 按一下 [是]，即會從使用者中移除角色。

如果您不確定哪些使用者仍然需要其角色指派，請參閱 [Azure AD Privileged Identity Management：如何開始安全性檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->