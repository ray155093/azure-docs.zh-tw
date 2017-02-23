---
title: "Office 365 外部共用語 Azure Active Directory B2B 共同作業 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業的宣告對應參考資料"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 716d19be09085db91c17d7ed76422e05d164d137


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 外部共用與 Azure Active Directory B2B 共同作業

Office365 (OneDrive、SharePoint Online、整合群組等) 中的外部共用與使用 Azure Active Directory (Azure AD) B2B 共同作業在技術上而言是相同的。 所有外部共用 (OneDrive/ SharePoint Online 除外)，包括「整合群組中的來賓」都已經在使用 Azure AD B2B 共同作業邀請 API 來進行共用。

OneDrive/SharePoint Online (ODSP) 有不同的邀請管理員，因為外部共用的 ODSP 支援在 Azure AD 網狀架構受到繼承支援之前就已開始。 隨著時間的經過，ODSP 外部共用已發展出數個功能，而且已經有數百萬個使用者在使用該產品的內建共用模式。 我們與 ODSP 合作以將該技術引進到 Azure AD B2B 邀請 API (在此文件中的指稱)，以整合所有端對端優點，並將 Azure AD 開發體驗中的所有創新與之整合。 同時，ODSP 外部共用的運作方式與 Azure AD B2B 的運作方式有些微差異：

1. ODSP 會在使用者兌換其邀請之後，將使用者新增到目錄。 因此，在使用者兌換之前，您不會在 Azure AD 入口網站中實際看到該使用者。 若使用者同時收到另一個組織的邀請，會產生新的邀請。 然而，使用 B2B 共同作業時，我們會在建立邀請的同時就新增使用者，因此使用者會顯示在系統中。

2. ODSP 中的兌換體驗在這一點來講與 B2B 共同作業有所差異。

3. 然而，當使用者兌換邀請時，看起來則相同。

4. 您可以從 ODSP 共用對話方塊看到收到 B2B 共同作業邀請的使用者。 當收到 ODSP 邀請的使用者兌換其邀請之後，也會出現在 Azure AD 中。

5. 若要以受系統管理員控制的方式使用 ODSP 中的外部共用來搭配 B2B 共同作業，請將 ODSP 外部共用選項設定為 [只允許與已位於目錄中的外部使用者共用]。 使用者可以移至外部共用網站，並從系統管理員新增的外部共同作業者之間選擇。 系統管理員可以透過 B2B 共同作業邀請 API 來新增外部共同作業者。

![ODSP 外部共用設定](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)
* [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
* [委派 B2B 共同作業邀請](active-directory-b2b-delegate-invitations.md)
* [動態群組與 B2B 共同作業](active-directory-b2b-dynamic-groups.md)
* [B2B 共同作業程式碼與 PowerShell 範例](active-directory-b2b-code-samples.md)
* [設定適用於 B2B 共同作業的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
* [B2B 共同作業使用者權杖](active-directory-b2b-user-token.md)
* [B2B 共同作業使用者宣告對應](active-directory-b2b-claims-mapping.md)
* [B2B 共同作業目前的限制](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


