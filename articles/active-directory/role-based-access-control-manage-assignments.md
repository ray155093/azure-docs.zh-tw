<properties
	pageTitle="檢視 Azure 資源存取權指派 |Microsoft Azure"
	description="檢視及管理 Azure 入口網站中任何使用者或群組的所有角色型存取控制指派"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="jeffsta"/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="09/21/2016"
	ms.author="kgremban"/>

# 在 Azure 入口網站 - 公開預覽中檢視存取使用者和群組的工作分派

您可以使用 Azure Active Directory 預覽版本中的角色型存取控制 (RBAC) 來管理 Azure 資源的存取權。[預覽版有何功能？](active-directory-preview-explainer.md)

使用 RBAC 指派的存取是精細的，因為有兩種方式可以限制權限︰

- **範圍︰**RBAC 角色指派範圍設定為特定的訂用帳戶、資源群組或資源。取得單一資源存取權的使用者無法在相同的訂用帳戶中存取任何其他資源。
- **角色︰**在指派的範圍內，會指派角色以進一步縮小存取範圍。角色可為高層級，例如擁有者或特定，例如虛擬機器讀取器。

只能從指派範圍內的訂用帳戶、資源群組或資源指派角色。但是，您可以在單一位置檢視指定使用者或群組的存取權指派。

取得[使用角色指派來管理 Azure 訂用帳戶資源的存取權](role-based-access-control-configure.md)的詳細資訊。

##  檢視存取權指派

若要尋找單一使用者或群組的存取權指派，請在 [Azure 入口網站](http://portal.azure.com) 的 Azure Active Directory 中開始。

1. 選取 **Azure Active Directory**。如果您的瀏覽清單上看不到這個選項，選取 [更多服務]，然後向下捲動以尋找 **Azure Active Directory**。
2. 選取 [使用者和群組]，然後選取 [所有使用者]或 [所有群組]。在此範例中，我們將重點放在個別使用者。![在 Azure Active Directory 中管理使用者和群組 - 螢幕擷取畫面](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. 依名稱或使用者名稱搜尋使用者。
4. 在使用者刀鋒視窗上選取 [Azure 資源]。隨即出現該使用者的所有存取權指派。

### 檢視指派的讀取權限

此頁面只會顯示您擁有讀取權限的存取權指派。例如，您擁有訂用帳戶 A 的讀取權限，並移至 Azure 資源刀鋒視窗檢查使用者的指派。您可以看到她的訂用帳戶 A 存取權指派，但不會看到她在訂用帳戶 B 上也擁有存取權指派。

## 刪除存取權指派

從這個刀鋒視窗中，您可以刪除已直接指派給使用者或群組的存取權指派。如果從父群組繼承存取權指派，您需要移至資源或訂用帳戶，並在那裡管理指派。

1. 從使用者或群組的所有存取權指派清單中，選取您想要刪除的項目。
2. 選取 [移除] 然後選取 [是] 以確認。![移除存取權指派 - 螢幕擷取畫面](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## 相關主題

- 取得[使用角色指派來管理 Azure 訂用帳戶資源的存取權](role-based-access-control-configure.md)的詳細資訊。
- 參閱 [RBAC 內建角色](role-based-access-built-in-roles.md)

<!---HONumber=AcomDC_0928_2016-->