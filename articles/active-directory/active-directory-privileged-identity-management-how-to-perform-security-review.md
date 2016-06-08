<properties
   pageTitle="如何執行安全性檢閱 | Microsoft Azure"
   description="了解如何使用 Azure Privileged Identity Management 應用程式來執行檢閱。"
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

# Azure AD Privileged Identity Management：如何執行安全性檢閱

Azure Active Directory (AD) Privileged Identity Management 簡化了企業管理特殊權限身分識別的方式，和存取 Azure AD 以及如 Office 365 或 Microsoft Intune 等其他 Microsoft 線上服務資源的方式。

如果您已獲指派系統管理角色，貴組織的特殊權限角色管理員可能會要求您定期檢閱，並確認您仍需要該角色來執行工作。您可能會收到包含連結的電子郵件，或請直接移至 [Azure 入口網站](https://portal.azure.com)。請遵循本文中的步驟，執行獲指派角色的自我檢閱。

如果您是對安全性檢閱感興趣的特殊權限角色管理員，請在[如何開始安全性檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)中取得更多詳細資訊。

## 加入 Privileged Identity Management 應用程式

您可以使用 [Azure 入口網站](https://portal.azure.com/)中的 Azure AD Privileged Identity Management (PIM) 應用程式來執行檢閱。如果入口網站中沒有 Azure AD Privileged Identity Management 應用程式，請遵循這些步驟操作。

1. 如果您尚未登入 [Azure 入口網站](https://portal.azure.com/)，請先登入。
2. 如果貴組織有多個目錄，請按一下 Azure 入口網站右上角的使用者名稱，選取您要操作的目錄。
3. 選取左側導覽中的**新增**圖示。
4. 從功能表中選取 [安全性 + 識別]。
5. 選取 [Azure AD Privileged Identity Management]。
6. 保留勾選 [釘選到儀表板] 核取方塊，然後按一下 [建立] 按鈕。Privileged Identity Management 應用程式隨即開啟。


## 核准或拒絕存取

存取不會變更，直到[完成檢閱](active-directory-privileged-identity-management-how-to-complete-review.md)。此程序只會針對變更角色存取權的使用者建置檢查清單。至少選取一位使用者之後，即會啟用 [核准存取] 和 [拒絕存取] 按鈕。

1. 在 PIM 應用程式中，按一下主功能表上的 [檢閱系統管理存取權]。隨即會出現安全性檢閱清單。
2. 在清單中，選取您要變更存取權的 [使用者]。
3. 針對您選取的使用者按一下 [核准存取] 或 [拒絕存取]。通知會出現在 Azure 入口網站的主功能表上，檢閱清單中選取的名稱便會消失 (變更篩選選項即可再度顯示名稱)。關閉 [檢閱 Azure AD 角色] 刀鋒視窗。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->