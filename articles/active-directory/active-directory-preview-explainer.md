---
title: "Azure Active Directory 預覽版說明 | Microsoft Docs"
description: "說明傳統入口網站中的 Azure Active Directory 與 Azure 入口網站中的 Azure Active Directory 預覽版之間差異的主題。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e717c11c-e7c9-4565-8e47-1950905e5b3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 3b31bd036d9c3ff8036b314b93cbddd94874ff63
ms.openlocfilehash: 8f7a679dc5b5726107503a9f7363ab162b1770d0


---
# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Azure 入口網站中的 Azure Active Directory 管理體驗預覽
Azure 入口網站目前提供預覽版的 Azure Active Directory (Azure AD) 管理體驗。 您可以用您目錄的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com) 來試試這項體驗。 然後，從服務清單中選取 [Azure Active Directory] (如果可以看到的話)，或是選取 [更多服務]  來檢視所有服務的清單。 您不需要有 Azure 訂用帳戶，即可使用 Azure 入口網站中的 Azure AD 管理體驗。

## <a name="learn-about-what-you-can-do-in-the-preview-experience"></a>了解您可以在預覽版體驗中執行哪些操作
預覽版體驗可讓您在 Azure 入口網站中管理許多目錄資源，例如使用者、群組、應用程式及目錄設定。 我們正在改善這項體驗，使其包括 [Azure 傳統入口網站](https://manage.windowsazure.com)中 Azure AD 管理體驗現有的所有功能。 在那之前，有一些目錄管理工作，您仍然必須在傳統入口網站中完成。

## <a name="manage-the-same-azure-ad-tenants"></a>管理相同的 Azure AD 租用戶
預覽版體驗的讀寫目標是與傳統入口網站及「Office 365 系統管理中心」相同的 Azure Active Directory 租用戶。 在任何這些入口網站中所做的變更都會反映在所有其他入口網站。

## <a name="use-the-same-authorization-logic"></a>使用相同的授權邏輯
預覽版體驗使用與現有 Active Directory 用戶端相同的授權邏輯。 系統會根據使用者的目錄角色 (例如全域管理員、使用者管理員及密碼管理員) 授與使用者變更目錄資源的權限。 在 Azure 資源或 Azure 訂用帳戶上擁有角色並不會授與使用者管理目錄資源的權限。 如需有關 Azure AD 管理角色的詳細資訊，請參閱[在 Azure Active Directory 中指派系統管理員角色](active-directory-assign-admin-roles.md)。

預覽版體驗是針對全域管理員最佳化的體驗。 如果您是以全域管理員以外的使用者身分登入來使用預覽版體驗，可能無法享有完整的體驗。 例如，您可能可以選取一個按鈕來開始工作，但這工作卻是您無法在目錄中完成的。 我們很快就會改進這項體驗。

## <a name="next-steps"></a>後續步驟
您可以在 [Azure AD 意見反應論壇](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc)的「管理入口網站」區段中，提供有關預覽版體驗的意見反應。



<!--HONumber=Feb17_HO3-->


