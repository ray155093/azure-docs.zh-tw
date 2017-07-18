---
title: "開始使用 Azure AD Privileged Identity Management | Microsoft Docs"
description: "了解如何在 Azure 入口網站中使用 Azure Active Directory Privileged Identity Management 應用程式來管理特殊權限身分識別。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 17cdff033cc3dbb199d11c3b8ac1acbc92499877
ms.contentlocale: zh-tw
ms.lasthandoff: 07/10/2017

---
# <a name="start-using-azure-ad-privileged-identity-management"></a>開始使用 Azure AD Privileged Identity Management
您可以利用 Azure Active Directory (AD) Privileged Identity Management 來管理、控制和監視組織內的存取行為。 此範圍包括存取 Azure AD 中的資源和其他 Microsoft 線上服務，例如 Office 365 或 Microsoft Intune。

本文會告訴您如何將 Azure AD PIM 應用程式加入 Azure 入口網站儀表板中。

## <a name="add-the-privileged-identity-management-application"></a>加入 Privileged Identity Management 應用程式
使用 Azure AD Privileged Identity Management 之前，您需要先將應用程式加入 Azure 入口網站儀表板中。

1. 以目錄的全域系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/) 。
2. 如果貴組織有多個目錄，請選取 Azure 入口網站右上角的使用者名稱。 選取您要在其中使用 PIM 的目錄。
3. 選取 [更多服務] 並使用 [篩選器] 文字方塊來搜尋 [Azure AD Privileged Identity Management]。
4. 選取 [釘選到儀表板]，然後按一下 [建立]。 Privileged Identity Management 應用程式隨即開啟。

如果您是目錄中使用 Azure AD Privileged Identity Management 的第一人，則會自動獲指派目錄中的「安全性系統管理員」和「特殊權限角色管理員」角色。 只有特殊權限角色管理員才能管理使用者的角色指派。 此外，您還可以選擇執行[安全性精靈](active-directory-privileged-identity-management-security-wizard.md) 引導您完成初始探索和指派體驗。

## <a name="navigate-to-your-tasks"></a>瀏覽至您的工作
Azure AD Privileged Identity Management 設定完畢後，您會在每次開啟應用程式時看到導覽刀鋒視窗。 使用此刀鋒視窗來完成您的身分識別管理工作。

![PIM 的最上層工作 - 螢幕擷取畫面](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

* **我的角色**會帶您前往已指派給您的角色清單。 您可以在此區段中啟動任何您具備資格的角色。
* **核准要求 (預覽)** 會顯示目錄中來自使用者的擱置啟用要求清單。 [深入了解。](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
* **擱置要求 (預覽)** 會顯示任何已設為啟動的目前要求。
* **檢閱存取**會帶您前往任何需要完成的擱置存取權檢閱 (無論您是在檢閱自己還是他人的存取權)。
* [管理] 區段下的 **Azure AD Directory 角色**是一個儀表板，可讓特殊權限角色管理員用來管理角色指派、變更角色啟用設定、開始存取權檢閱等。 對於任何不是特殊權限角色管理員的人員，此儀表板中的選項會停用。

## <a name="next-steps"></a>後續步驟
[Azure AD Privileged Identity Management 概觀](active-directory-privileged-identity-management-configure.md) 包含如何管理貴組織系統管理存取的更多詳細資訊。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

