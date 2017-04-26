---
title: "使用 Office 365 帳戶註冊 Azure | Microsoft Docs"
description: "了解如何使用 Office 365 帳戶建立 Azure 訂用帳戶"
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d62ec023605573e4b0804da00c75f65c8dea5173
ms.lasthandoff: 04/12/2017


---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>使用 Office 365 帳戶註冊 Azure 訂用帳戶
如果您有 Office 365 訂用帳戶，請使用 Office 365 帳戶來建立 Azure 訂用帳戶。 您可以使用 Office 365 使用者名稱和密碼來登入 [Azure 入口網站](https://portal.azure.com/)，而不需具有 Azure 訂用帳戶。 但是，如果您想要設定虛擬機器或使用其他 Azure 服務，您必須為您的帳戶建立 Azure 訂用帳戶。 如果您所屬的組織想要使用 Azure 服務，請將您組織中的其他人新增至已建立的訂用帳戶。  

如果您已經有 Office 365 帳戶和 Azure 訂用帳戶，請參閱[將 Office 365 租用戶關聯至 Azure 訂用帳戶](billing-add-office-365-tenant-to-azure-subscription.md)。

## <a name="get-an-azure-subscription-by-using-your-office-365-account"></a>使用 Office 365 帳戶取得 Azure 訂用帳戶

若要註冊 Azure，請使用 Azure 免費連結，並使用 Office 365 使用者名稱和密碼來登入 Azure。 

1. 移至 [Azure.com](https://azure.microsoft.com/)。 
2. 按一下 [開始免費使用]。
3. 使用 Office 365 使用者名稱和密碼來登入。 您使用的帳戶不需具備系統管理員權限。 如果您有一個以上的 Office 365 帳戶，請務必使用您想要與 Azure 訂用帳戶建立關聯之 Office 365 帳戶的認證。 

   ![顯示登入頁面的螢幕擷取畫面。](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

4. 輸入必要資訊並完成註冊程序。

    ![顯示註冊表單的螢幕擷取畫面。](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- 如果您需要將組織中的其他人新增至 Azure 訂用帳戶，請參閱[開始在 Azure 入口網站中進行存取管理](../active-directory/role-based-access-control-what-is.md)。 
- 如果您想要新增系統管理員來協助管理訂用帳戶，請參閱[新增或變更負責管理訂用帳戶或服務的 Azure 系統管理員角色](billing-add-change-azure-subscription-administrator.md)。

## <a id="more-about-subs">深入了解 Azure 和 Office 365 訂用帳戶</a>
Office 365 和 Azure 均使用 Azure AD 服務來管理使用者和訂用帳戶。 Azure 目錄就像您可在其中將使用者和訂用帳戶分組的容器。 若要將相同的使用者帳戶用於 Azure 和 Office 365 訂用帳戶，您需要確定訂用帳戶是建立在相同的目錄中。 請記住下列幾點：

* 訂用帳戶會建立在目錄之下。
* 使用者所屬目錄。
* 訂用帳戶會落在建立訂用帳戶之使用者的目錄中。 所以 Office 365 訂用帳戶會繫結至與 Azure 訂用帳戶相同的帳戶。
* Azure 訂用帳戶是由目錄中的個別使用者所擁有。
* Office 365 訂用帳戶是由目錄本身所擁有。 在目錄中擁有適當權限的使用者可以管理這些訂用帳戶。

![顯示目錄、使用者和訂用帳戶關聯性的螢幕擷取畫面。](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

如需詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](../active-directory/active-directory-how-subscriptions-associated-directory.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。 
