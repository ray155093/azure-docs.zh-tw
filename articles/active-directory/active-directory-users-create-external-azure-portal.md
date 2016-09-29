<properties
	pageTitle="在 Azure Active Directory 預覽版中從其他目錄或合作夥伴公司新增使用者 | Microsoft Azure"
	description="說明如何在 Azure Active Directory 中新增使用者或變更使用者資訊，包括外部使用者和來賓使用者。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>

# 在 Azure Active Directory 預覽版中從其他目錄或合作夥伴公司新增使用者

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-users-create-external-azure-portal.md)
- [Azure 傳統入口網站](active-directory-create-users-external.md)

本文說明如何在 Azure Active Directory (Azure AD) 預覽版中從其他目錄或從合作夥伴公司新增使用者。[預覽版有何功能？](active-directory-preview-explainer.md) 如需有關新增您組織內使用者以及新增具有 Microsoft 帳戶之使用者的資訊，請參閱[將新的使用者新增到 Azure Active Directory](active-directory-users-create-azure-portal.md)。新增的使用者預設不會有系統管理員權限，但是您可以隨時指派角色給他們。

## 新增使用者

1.  使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com)。

2.  選取 [更多服務]，在文字方塊中輸入「使用者和群組」，然後選取 **Enter**。

    ![開啟使用者管理](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  在 [使用者和群組] 刀鋒視窗上，選取 [使用者]，然後選取 [新增]。

    ![選取 [新增] 命令](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. 在 [使用者] 刀鋒視窗中，於 [名稱] 中提供顯示名稱，並於 [使用者名稱] 中提供使用者的登入名稱。

5. 複製或記下產生的使用者密碼，以便在此程序完成後，將它提供給使用者。

6. (選擇性) 選取 [設定檔] 來新增使用者的名字和姓氏、職稱，以及部門名稱。

		![Opening the user profile](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

	- 選取 [群組] 以將使用者新增到一個或多個群組中。

		![將使用者新增到群組中](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

	- 選取 [組織角色] 以將使用者指派給 [角色] 清單中的角色。如需有關使用者和系統管理員角色的詳細資訊，請參閱[在 Azure AD 中指派系統管理員角色](active-directory-assign-admin-roles.md)。

		![將使用者指派給角色](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. 選取 [建立]。

8. 將產生的密碼安全地散發給新使用者，以便讓使用者可以登入。

> [AZURE.IMPORTANT] 如果您的組織使用多個網域，當您新增使用者帳戶時，請注意下列問題：
>
> - 若要跨網域新增具有相同使用者主體名稱 (UPN) 的使用者帳戶，請「先」新增 geoffgrisso@contoso.onmicrosoft.com，「再」新增 geoffgrisso@contoso.com。
> - 請「勿」先新增 geoffgrisso@contoso.com 再新增 geoffgrisso@contoso.onmicrosoft.com。此順序很重要，事後想要復原會很麻煩。

如果您變更其身分識別已與您的內部部署 Active Directory 服務同步處理之使用者的資訊，您就無法在 Azure 傳統入口網站中變更使用者資訊。若要變更此使用者資訊，請使用您的內部部署 Active Directory 管理工具。


## 後續步驟

- [新增使用者](active-directory-users-create-azure-portal.md)
- [在新 Azure 入口網站中重設使用者的密碼](active-directory-users-reset-password-azure-portal.md)
- [在 Azure AD 中將使用者指派給角色](active-directory-users-assign-role-azure-portal.md)
- [變更使用者的工作資訊](active-directory-users-work-info-azure-portal.md)
- [管理使用者設定檔](active-directory-users-profile-azure-portal.md)
- [在 Azure AD 中刪除使用者](active-directory-users-delete-user-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->