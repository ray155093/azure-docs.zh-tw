<properties
   pageTitle="如何啟用或停用角色 | Microsoft Azure"
   description="了解如何利用 Azure Privileged Identity Management 應用程式，啟用特殊權限身分識別的角色。"
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

# Azure AD Privileged Identity Management：如何啟用或停用角色

Azure Active Directory (AD) Privileged Identity Management 簡化了企業管理特殊權限身分識別的方式，和存取 Azure AD 以及如 Office 365 或 Microsoft Intune 等其他 Microsoft 線上服務資源的方式。

本文適用對象是必須在 Azure AD Privileged Identity Management (PIM) 中啟用其角色的系統管理員。它會逐步引導您，在需要權限時啟用角色，並在完成時停用角色。

如果您已獲指派系統管理角色，您可以在必須執行需要該角色的工作時啟用該角色。例如，如果您有時只需要管理 Office 365，貴組織的特殊權限角色管理員就不會想讓您成為永久的系統管理員。他們反倒會讓您成為 Azure AD 的全域管理員或 Exchange Online 系統管理員角色的候選人員。這表示，當您需要這些權限時，您可以要求暫時的角色指派，擁有預定時段內的 Office 365 管理控制權。


## 加入 Privileged Identity Management 應用程式

在 [Azure 入口網站](https://portal.azure.com/)中使用 Azure AD Privileged Identity Management 應用程式要求「啟用」角色，即使您要在另一個網站或透過 PowerShell 操作。如果您在 Azure 入口網站沒有 Azure AD Privileged Identity Management 應用程式，請遵循下列步驟操作。

1. 如果您尚未登入 [Azure 入口網站](https://portal.azure.com/)，請先登入。
2. 如果貴組織有多個目錄，請按一下 Azure 入口網站右上角的使用者名稱，選取您要操作的目錄。
3. 按一下左側導覽列中的 [新增] 圖示。
4. 從 [建立] 功能表中選取 [安全性 + 身分識別]。
5. 選取 [Azure AD Privileged Identity Management]。
6. 選取 [釘選到儀表板]，然後按一下 [建立] 按鈕。Privileged Identity Management 應用程式隨即開啟。

## 啟用角色

當您需要執行角色時，您可以使用 Azure AD Privileged Identity Management 應用程式中的 [啟用我的角色] 按鈕，要求啟用。


1. 登入 [Azure 入口網站](https://portal.azure.com/)，選取 [Azure AD Privileged Identity Management] 圖格。
2. 選取 [啟用我的角色]。畫面會隨即顯示已指派給您的角色清單。
3. 選取您想要啟用的角色。
4. 選取 [**啟用**]。[要求啟用角色] 刀鋒視窗隨即出現。
5. 如全域管理員之類的某些角色，需要有 Multi-Factor Authentication (MFA) 才能啟用角色。如果登入時並未執行 MFA，您可能必須先執行它才能啟用角色。
6. 在啟用要求的文字欄位中輸入啟用原因。特殊權限角色管理員可能也會要求您提供問題票證號碼。
7. 選取 [確定]。角色現已啟用，角色變更也會出現在 Microsoft Online Services 中。

## 停用角色

角色一經啟用，就會在達到時間限制時自動停用。

如果您提早完成作業，也可以在 Azure AD Privileged Identity Management 應用程式中手動停用角色。選取 [啟用我的角色]，找到目前不再需要的角色，然後選取 [停用]。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

如果您有興趣進一步了解 Azure AD Privileged Identity Management，下列連結會提供詳細資訊。

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->