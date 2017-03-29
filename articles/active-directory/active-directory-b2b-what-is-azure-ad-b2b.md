---
title: "關於 Azure Active Directory B2B 共同作業預覽 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係。"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4bfd76a00ba694096ef0b045b14beb757ba0809f
ms.lasthandoff: 03/21/2017


---

# <a name="about-the-azure-ad-b2b-collaboration-preview"></a>關於 Azure AD B2B 共同作業預覽
本文討論更新的 Azure Active Directory (Azure AD) B2B 共同作業公開預覽之目標、各項功能和優點。

無法您是 IT 專業人員或資訊工作者，都能使用 Azure AD B2B 共同作業的各項功能。 無論合作夥伴的規模、產業或合規性和控管需求為何，您都可以與身處任何地點之其他組織中的合作夥伴密切合作。 您也能提供對文件、資源及應用程式的存取權，同時又保有您對內部資料的完整控制權。

如果您是開發人員，可以使用 Azure AD B2B API 撰寫應用程式，以安全地結全各個組織。 對於資訊工作者的使用者，可以直接進行程序和瀏覽。

## <a name="how-b2b-collaboration-works"></a>B2B 共同作業如何運作

在目前的預覽版本中，若要與合作夥伴組織建立關聯性，IT 專業人員和資訊工作者可以透過入口網站或邀請管理員 API，一次新增一個或一些使用者。

管理員可以使用 Azure 入口網站 (https://portal.azure.com) 中的新入口網站體驗和 PowerShell 來建立關聯性。

資訊工作者則可以使用 http://myapps.microsoft.com 中的存取面板體驗。

開發人員可以使用 Azure AD B2B 邀請管理員 API 建立應用程式，以新增 B2B 共同作業使用者及自訂邀請和上線工作流程。

通常來說，系統會透過一個邀請和兌換程序來新增 B2B 共同作業使用者。 其運作方式如下：

1. 來自 WoodGrove 的 John Doe 想要使用 Sam Oogle 的 Gmail 地址 (gsamoogle@gmail.com) 來新增這位使用者。

2. John Doe 前往 WoodGrove 入口網站 (portal.azure.com) 或存取面板 (myapps.microsoft.com)，登入並將使用者 Sam Oogle 新增到 WoodGrove 目錄、群組或應用程式。

3. John Doe 指定一個自訂邀請電子郵件來傳送給 Sam Oogle。

4. 當 John Doe 傳送邀請時，系統已在適用於 WoodGrove 的 Azure AD 中建立了使用者 Sam Oogle，如此所示：

  ![portal.azure.com 中的管理員使用者介面](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. 建立使甪者之後，Azure AD 會傳送一封邀請電子郵件給 Sam Oogle：

  ![傳送給 Sam Oogle 的邀請郵件](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. 在邀請中，Sam Oogle 選擇了 [開始使用]。  
Azure 入口網站隨即開啟。

7. Sam Oogle 登入 Azure 入口網站。

8. Azure AD 會用 Sam 權杖中的資訊更新 Azure AD 中的使用者物件，如此所示：

  ![Azure 入口網站中的 Sam Oogle 使用者設定檔](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

9. 現在，使用者 Sam Oogle 已兌換邀請，也能夠存取 WoodGrove 資源。 如同 Azure AD 中的其他使用者，Sam Oogle 也會受到管理員的管理。 使用者清單如此所示：

  ![Azure AD 使用者清單](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>公開預覽功能
依據使用者的意見反應，B2B 共同作業公開預覽更新會提供以下金鑰功能，包括：

* 針對管理員：在 [Azure 入口網站](https://portal.azure.com)中取得使用者介面增強功能。 例如，管理員能夠邀請 B2B 使用者加入目錄、任何群組或應用程式。  

* 針對資訊工作者：在[存取面板](https://myapps.microsoft.com)中取得 B2B 共同作業自助邀請功能。 資訊工作者可以邀請 B2B 共同作業使用者加入他們所管理的任何自助群組或應用程式。

* 允許受邀請的使用者擁有任何電子郵件地址。 無論是 Office 365 或內部部署 Microsoft Exchange 地址、outlook.com 地址或任何社交地址 (Gmail、Yahoo! 等)，使用者都能建立 Azure AD 或 Microsoft 帳戶以存取受邀請的組織。

* 建立專業、租用戶品牌化邀請電子郵件。

* 使用邀請 API 自訂使用者導向。

* 設定適用於邀請組織中 B2B 共同作業使用者的多重要素驗證。

* 將邀請委派給非管理員。

* 提供 B2B 共同作業的 PowerShell 支援。

* 提供稽核和報告功能。

## <a name="help-us-shape-your-features"></a>協助我們設計功能
 
我們會時常聆聽您對改進 B2B 共同作業的意見反應。 我們邀請您在 [Microsoft 技術社群 (英文)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) 參與討論、分享您的使用者案例、最佳作法，以及您對 Azure AD B2B 共同作業的意見
 
我們也邀請您前往 [B2B 共同作業構想 (英文)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) 網站提交您的構想和針對未來的功能投票。

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)
* [針對 Azure Active Directory B2B 共同作業問題進行疑難排解](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 共同作業常見問題 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [在沒有邀請的情況下新增 B2B 共同作業使用者](active-directory-b2b-add-user-without-invite.md)
* [B2B 共同作業使用者稽核和報告](active-directory-b2b-auditing-and-reporting.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)

