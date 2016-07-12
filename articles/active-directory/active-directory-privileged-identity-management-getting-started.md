<properties
   pageTitle="開始使用 Azure AD Privileged Identity Management | Microsoft Azure"
   description="了解如何在 Azure 入口網站中使用 Azure Active Directory Privileged Identity Management 應用程式來管理特殊權限身分識別。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/29/2016"
   ms.author="kgremban"/>

# 開始使用 Azure AD Privileged Identity Management


Azure Active Directory (AD) Privileged Identity Management 可讓您管理、控制和監視特殊權限身分識別，以及存取 Azure AD 和 Office 365 或 Microsoft Intune 等其他 Microsoft Online 服務的資源。

本文會告訴您如何將 Azure AD PIM 應用程式加入 Azure 入口網站儀表板中。

## 加入 Privileged Identity Management 應用程式

使用 Azure AD Privileged Identity Management 之前，您需要先將應用程式加入 Azure 入口網站儀表板中。

1. 以目錄的全域系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 如果貴組織有多個目錄，請按一下 Azure 入口網站右上角的使用者名稱，選取您要使用 PIM 的目錄。
3. 選取 [新增] > [安全性 + 身分識別] > [Azure AD Privileged Identity Management]。

    ![在入口網站中啟用 PIM][1]

4. 選取 [釘選到儀表板]，然後按一下 [建立]。Privileged Identity Management 應用程式隨即開啟。


如果您是在目錄中使用 Azure AD Privileged Identity Management 的第一個人，則[安全性精靈](active-directory-privileged-identity-management-security-wizard.md)會引導您完成初始指派體驗。之後，您就會自動成為目錄的第一個**安全性系統管理員**和**特殊權限角色管理員**。只有特殊權限角色管理員可以存取這個應用程式來管理其他管理員的存取權。

如果您已被指派一或多個角色，您可選擇 [啟用我的角色]。如果您是特殊權限角色管理員角色，您也會看到 [管理特殊權限角色] 的選項。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

[Azure AD Privileged Identity Management 概觀](active-directory-privileged-identity-management-configure.md)包含如何管理貴組織系統管理存取的更多詳細資訊。

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

<!---HONumber=AcomDC_0706_2016-->